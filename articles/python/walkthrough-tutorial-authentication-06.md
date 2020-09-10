---
title: 'Passo a passo, parte 6: autenticar aplicativos Python com os serviços do Azure'
description: Um exame do código de inicialização do aplicativo principal, que configura o objeto DefaultAzureCredential e os objetos de cliente necessários ao ponto de extremidade de API.
ms.date: 08/24/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 17d5e007c65572ef301a4aa682260af6f492a626
ms.sourcegitcommit: 5ab6e90e20a87f9a8baea652befc74158a9b6613
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89614278"
---
# <a name="part-6-main-app-startup-code"></a>Parte 6: código de inicialização do aplicativo principal

[Página anterior: dependências, elementos de importação e variáveis de ambiente](walkthrough-tutorial-authentication-05.md)

O código de inicialização do aplicativo, que segue as instruções `import`, inicializa variáveis diferentes usadas nas funções que manipulam as solicitações HTTP.

Primeiro, criamos o objeto de aplicativo do Flask e recuperamos a URL de ponto de extremidade de API de terceiros da variável de ambiente:

```python
app = Flask(__name__)

number_url = os.environ["THIRD_PARTY_API_ENDPOINT"]
```

Em seguida, obtemos o objeto [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python
), que é a credencial recomendada para uso ao autenticar com os serviços do Azure. Confira [Como autenticar aplicativos Python](azure-sdk-authenticate.md#authenticate-with-defaultazurecredential).

```python
credential = DefaultAzureCredential()
```

Quando executado localmente, `DefaultAzureCredential` procura as variáveis de ambiente `AZURE_TENANT_ID`, `AZURE_CLIENT_ID` e `AZURE_CLIENT_SECRET` que contêm informações para sua entidade de serviço local. Quando executado na nuvem, `DefaultAzureCredential` usa automaticamente a entidade de serviço registrada para o aplicativo, que normalmente está contida na identidade gerenciada.

O código a seguir recupera a chave de acesso da API de terceiros do Azure Key Vault. No script de provisionamento, o Key Vault é criado usando [`az keyvault create`](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) e o segredo é armazenado com [`az keyvault secret set`](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set).

O próprio recurso do Key Vault é acessado por meio de uma URL, que é carregada da variável de ambiente `KEY_VAULT_URL`.

```python
key_vault_url = os.environ["KEY_VAULT_URL"]
```

Para se conectar ao cofre de chaves, devemos criar um objeto de cliente adequado. Como queremos recuperar um segredo, usamos o [`SecretClient`](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python), que requer a URL do cofre de chaves e o objeto de credencial que representa a identidade sob a qual o aplicativo está sendo executado.

```python
keyvault_client = SecretClient(vault_url=key_vault_url, credential=credential)
```

A criação do objeto `SecretClient` não autentica a credencial de maneira alguma. A `SecretClient` é simplesmente um constructo do lado do cliente que gerencia internamente a URL do recurso e a credencial. A autenticação e a autorização acontecem somente quando você invoca uma operação por meio do cliente, como [`get_secret`](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#get-secret-name--version-none----kwargs-), que gera uma chamada à API REST para o recurso do Azure.

```python
api_secret_name = os.environ["THIRD_PARTY_API_SECRET_NAME"]
vault_secret = keyvault_client.get_secret(api_secret_name)

# The "secret" from Key Vault is an object with multiple properties. The key we
# want for the third-party API is in the value property. 
access_key = vault_secret.value
```

Mesmo que a identidade do aplicativo esteja autorizada a acessar o cofre de chaves, ela ainda deve estar especificamente autorizada a acessar segredos.  Caso contrário, a chamada à `get_secret` falhará. Por esse motivo, o script de provisionamento define uma política de acesso "obter segredos" para o aplicativo usando o comando [`az keyvault set-policy`](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) da CLI do Azure. Para obter mais informações, confira [Autenticação do Key Vault](/azure/key-vault/general/authentication), bem como [Conceder ao aplicativo acesso ao Key Vault](/azure/key-vault/general/managed-identity#grant-your-app-access-to-key-vault). O último artigo mostra como definir uma política de acesso usando o portal do Azure. (O artigo também é escrito para identidade gerenciada, mas se aplica igualmente a uma entidade de serviço local usada no desenvolvimento.)

Por fim, o código do aplicativo configura o objeto de cliente por meio do qual podemos gravar mensagens em uma Fila de Armazenamento do Microsoft Azure. A URL da Fila está na variável de ambiente `STORAGE_QUEUE_URL`.

```python
queue_url = os.environ["STORAGE_QUEUE_URL"]
queue_client = QueueClient.from_queue_url(queue_url=queue_url, credential=credential)
```

Assim como ocorre com Key Vault, usamos um objeto de cliente específico das bibliotecas do Azure ([`QueueClient`](/python/api/azure-storage-queue/azure.storage.queue.queueclient?view=azure-python)) – bem como o método [`from_queue_url`](/python/api/azure-storage-queue/azure.storage.queue.queueclient?view=azure-python#from-queue-url-queue-url--credential-none----kwargs-) dele – para se conectar ao recurso localizado na URL em questão. Mais uma vez, a tentativa de criar esse objeto de cliente valida que a identidade do aplicativo representada pela credencial está autorizada a acessar a fila. Conforme observado anteriormente, essa autorização foi concedida por meio da atribuição da função "Colaborador de Dados da Fila de Armazenamento" ao aplicativo principal.

Supondo que todo esse código de inicialização tenha sido realizado com sucesso, o aplicativo terá todas as variáveis internas implementadas para dar suporte ao ponto de extremidade de API */api/v1/getcode*.

> [!div class="nextstepaction"]
> [Parte 7 – Ponto de extremidade de API do aplicativo principal >>>](walkthrough-tutorial-authentication-07.md)
