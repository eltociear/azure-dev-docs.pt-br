---
title: 'Passo a passo, parte 7: autenticar aplicativos Python com os serviços do Azure'
description: Um exame do código do ponto de extremidade da API do aplicativo principal, que usa o ponto de extremidade de API de terceiros e grava uma mensagem no Armazenamento de Filas do Azure.
ms.date: 08/24/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: e026eca0216147c6614582e0cd070cee81daf99c
ms.sourcegitcommit: 324da872a9dfd4c55b34739824fc6a6598f2ae12
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379454"
---
# <a name="part-7-main-application-api-endpoint"></a>Parte 7: ponto de extremidade da API do aplicativo principal

[Página anterior: código de inicialização do aplicativo principal](walkthrough-tutorial-authentication-06.md)

A API */api/v1/getcode* do aplicativo gera uma resposta JSON que contém um código alfanumérico e um carimbo de data/hora.

Primeiro, o decorador `@app.route` informa ao Flask que a função `get_code` lida com solicitações para a URL */api/v1/getcode*.

```python
@app.route('/api/v1/getcode', methods=['GET'])
def get_code():
```

Em seguida, chamamos a API de terceiros, a URL da qual está em `number_url`, fornecendo a chave de acesso que recuperamos do cofre de chaves no cabeçalho.

```python
    headers = {
        'Content-Type': 'application/json',
        'x-functions-key': access_key
        }

    r = requests.get(url = number_url, headers = headers)

    if (r.status_code != 200):
        return "Could not get you a code.", r.status_code
```

A propriedade `x-functions-key` do cabeçalho diz especificamente como o Azure Functions (onde esse exemplo de API de terceiros é implantado) espera que uma chave de acesso apareça em um cabeçalho. Para obter mais informações, confira [Gatilho HTTP do Azure Functions – Chaves de autorização](/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#authorization-keys). Se a chamada da API falhar por algum motivo, retornaremos uma mensagem de erro e o código de status.

Supondo que a chamada à API tenha sucesso e retorne um valor numérico, criamos um código mais complexo usando esse número mais alguns caracteres aleatórios (usando nossa própria função `random_char`).

```python
    data = r.json()
    chars1 = random_char(3)
    chars2 = random_char(3)
    code_value = f"{chars1}-{data['value']}-{chars2}"
    code = { "code": code_value, "timestamp" : str(datetime.utcnow()) }
```

A variável `code` aqui contém a resposta JSON completa para a API do aplicativo, que inclui o valor do código, bem como um carimbo de data/hora. Um exemplo de resposta seria `{"code":"ojE-161-pTv","timestamp":"2020-04-15 16:54:48.816549"}`.

No entanto, antes de retornar essa resposta, escrevemos uma mensagem com ela na nossa fila de armazenamento usando o método [`send_message`](/python/api/azure-storage-queue/azure.storage.queue.queueclient?view=azure-python#send-message-content----kwargs-) do cliente de Filas:

```python
    queue_client.send_message(code)

    return jsonify(code)
```

## <a name="processing-queue-messages"></a>Processando mensagens da fila

As mensagens armazenadas na fila podem ser exibidas e gerenciadas por meio do [portal do Azure](/azure/storage/queues/storage-quickstart-queues-portal#view-message-properties) ou com o comando [`az storage message get`](/cli/azure/storage/message?view=azure-cli-latest#az-storage-message-get) da CLI do Azure. O repositório de exemplo inclui um script (*test.cmd* e *test.sh*) para solicitar um código do ponto de extremidade do aplicativo e, em seguida, verificar a fila de mensagens. Também há um script para limpar a fila usando o comando [`az storage message clear`](/cli/azure/storage/message?view=azure-cli-latest#az-storage-message-clear).

Normalmente, um aplicativo como este exemplo teria outro processo que efetua pull de maneira assíncrona de mensagens da fila para processamento adicional. Como mencionado anteriormente, a resposta gerada por esse ponto de extremidade de API pode ser usada em outro lugar do aplicativo com autenticação de usuário de dois fatores. Nesse caso, o aplicativo deve invalidar o código após um determinado período, por exemplo, 10 minutos. Uma forma simples de fazer essa tarefa seria manter uma tabela de códigos de autenticação de dois fatores válidos, usados no procedimento de logon do usuário. Então, o aplicativo teria um processo simples de observação de fila com a seguinte lógica (em pseudocódigo):

<pre>
pull a message from the queue and retrieve the code.

if (code is already in the table):
    remove the code from the table, thereby invalidating it
else:
    add the code to the table, making it valid
    call queue_client.send_message(code, visibility_timeout=600)
</pre>

Esse pseudocódigo emprega o parâmetro opcional `visibility_timeout` do método [`send_message`](/python/api/azure-storage-queue/azure.storage.queue.queueclient?view=azure-python#send-message-content----kwargs-), que especifica o número de segundos antes que a mensagem se torne visível na fila. Como o tempo limite padrão é zero, as mensagens inicialmente gravadas pelo ponto de extremidade de API tornam-se imediatamente visíveis para o processo de observação da fila. Como resultado, esse processo os armazena imediatamente na tabela de códigos válidos. Ao enfileirar a mesma mensagem novamente com o tempo limite, o processo sabe que receberá o código de novo 10 minutos depois e, nesse momento, removerá ele da tabela.

## <a name="implementing-the-main-app-api-endpoint-in-azure-functions"></a>Implementando o ponto de extremidade de API do aplicativo principal no Azure Functions

O código mostrado anteriormente neste artigo usa a estrutura da Web do Flask para criar o ponto de extremidade de API. Como o Flask precisa ser executado com um servidor Web, esse código deve ser implantado no Serviço de Aplicativo do Azure ou em uma máquina virtual.

Uma opção de implantação alternativa é o ambiente sem servidor do Azure Functions. Nesse caso, todo o código de inicialização e o código do ponto de extremidade de API estariam contidos na mesma função, associada a um gatilho HTTP. Assim como acontece com o Serviço de Aplicativo, você usa as [configurações do aplicativo de funções](/azure/azure-functions/functions-how-to-use-azure-function-app-settings#settings) para criar variáveis de ambiente para o seu código.

Uma parte da implementação que se torna mais fácil é a autenticação com o Armazenamento de Filas. Em vez de obter um objeto `QueueClient` usando a URL da fila e um objeto de credencial, você cria uma *associação de armazenamento de fila* para a função. A associação manipula toda a autenticação em segundo plano. Com essa associação, a função recebe um objeto do cliente pronto para uso como parâmetro. Para obter mais informações e código de exemplo, confira [Conectar o Azure Functions ao Armazenamento de Filas do Azure](/azure/azure-functions/functions-add-output-binding-storage-queue-cli?tabs=bash%2Cbrowser&pivots=programming-language-python).

## <a name="next-steps"></a>Próximas etapas

Por meio deste exemplo, você aprendeu como os aplicativos são autenticados com outros serviços do Azure e como os aplicativos podem usar o Azure Key Vault para armazenar quaisquer outros segredos necessários para APIs de terceiros.

O mesmo padrão demonstrado aqui com o Azure Key Vault e o Armazenamento do Microsoft Azure se aplica a todos os outros serviços do Azure. A etapa crucial é você definir as permissões de função corretas para o aplicativo na página do serviço no portal do Azure ou por meio da CLI do Azure. (Confira [Como atribuir permissões de função](how-to-assign-role-permissions.md)). Verifique a documentação do serviço para saber se você precisa configurar outra política de acesso.

Lembre-se sempre de que você precisa atribuir as mesmas funções e políticas de acesso a qualquer entidade de serviço que você esteja usando para o desenvolvimento local.

Resumindo, ao concluir este passo a passos você poderá aplicar seus conhecimentos a qualquer número de outros serviços do Azure e a qualquer número de outros serviços externos.

Um assunto que não abordamos aqui é a autenticação de *usuários*. Para explorar essa área para aplicativos Web, comece com [Autenticar e autorizar usuários de ponta a ponta no Serviço de Aplicativo do Azure](/azure/app-service/tutorial-auth-aad?pivots=platform-linux).

## <a name="see-also"></a>Confira também

- [Como autenticar e autorizar aplicativos Python no Azure](azure-sdk-authenticate.md)
- Exemplo passo a passo: [github.com/Azure-Samples/python-integrated-authentication](https://github.com/Azure-Samples/python-integrated-authentication)
- [Documentação do Azure Active Directory](/azure/active-directory)
- [Documentação do Azure Key Vault](/azure/key-vault)
