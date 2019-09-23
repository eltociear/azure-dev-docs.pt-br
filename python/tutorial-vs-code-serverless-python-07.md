---
title: Adicionar uma associação de armazenamento para o Azure Functions no Python com o Visual Studio Code
description: 'Tutorial, etapa 7: adição de uma associação no Python para gravar mensagens no armazenamento do Azure.'
services: functions
author: kraigb
manager: barbkess
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: kraigb
ms.openlocfilehash: 8fdb78024cc6ac1df0ee961fc424878f5f634cf2
ms.sourcegitcommit: 74e28a479c87a3a53592646420b78e69852dd86a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/16/2019
ms.locfileid: "71019934"
---
# <a name="add-a-binding-to-write-messages-to-azure-storage"></a>Adicionar uma associação para gravar mensagens no armazenamento do Azure

[Etapa anterior: implantar uma segunda função](tutorial-vs-code-serverless-python-06.md)

Uma _Associação_ permite conectar o código da sua função a recursos como o armazenamento do Azure, sem gravar nenhum código de acesso a dados. Uma associação é definida no arquivo *function.json* e pode representar tanto entrada quanto saída. Uma função pode usar várias associações de entrada e de saída, mas apenas um gatilho. Para saber mais, confira [Conceitos de gatilhos e de associações do Azure Functions](/azure/azure-functions/functions-triggers-bindings.md).

Nesta seção, você adicionará uma associação de armazenamento à função HttpExample criada anteriormente neste tutorial. A função usa essa associação para gravar mensagens no armazenamento com cada solicitação. O armazenamento em questão usa a mesma conta de armazenamento padrão empregada pelo aplicativo de funções. No entanto, se você planeja fazer uso intenso do armazenamento, considere a possibilidade de criar uma conta separada.

1. Sincronize as configurações remotas para seu projeto do Azure Functions no arquivo *local.settings.json*, abrindo a Paleta de Comandos e selecionando **Azure Functions: Baixar Configurações Remotas**. Abra o *local.settings.json* e verifique se ele contém um valor para `AzureWebJobsStorage`. O valor é a cadeia de conexão para a conta de armazenamento.

1. Na pasta `HttpExample`, clique com o botão direito do mouse em *function.json* e selecione **Adicionar associação**:

    ![Comando Adicionar associação, no gerenciador do Visual Studio Code](media/tutorial-vs-code-serverless-python/add-binding-command.png)

1. Nos prompts a seguir, no Visual Studio Code, selecione ou forneça os seguintes valores:

    | Prompt | Valor a ser fornecido |
    | --- | --- |
    | Definir direção da associação | obre |
    | Selecionar associação com direção de saída | Armazenamento de Filas do Azure |
    | O nome usado para identificar essa associação no seu código | msg |
    | A fila à qual a mensagem será enviada | outqueue |
    | Selecione a configuração do *local.settings.json* (solicitando a conexão de armazenamento) | AzureWebJobsStorage |

1. Depois de fazer essas seleções, verifique se a seguinte associação foi adicionada ao arquivo *function.json*:

    ```json
        {
          "type": "queue",
          "direction": "out",
          "name": "msg",
          "queueName": "outqueue",
          "connection": "AzureWebJobsStorage"
        }
    ```

1. Agora que a associação está configurada, você pode usá-la no código da função. Novamente, a associação recém-definida aparece no código como um argumento para a função `main` no *\_\_init\_\_.py*. Por exemplo, você pode modificar o arquivo *\_\_init\_\_.py*, no HttpExample, para corresponder ao seguinte, que mostra o uso do argumento `msg` para gravar uma mensagem com carimbo de data/hora com o nome usado na solicitação. Os comentários explicam as alterações específicas:

    ```python
    import logging
    import datetime  # MODIFICATION: added import
    import azure.functions as func

    # MODIFICATION: the added binding appears as an argument; func.Out[func.QueueMessage]
    # is the appropriate type for an output binding with "type": "queue" (in function.json).
    def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> func.HttpResponse:
        logging.info('Python HTTP trigger function processed a request.')

        name = req.params.get('name')
        if not name:
            try:
                req_body = req.get_json()
            except ValueError:
                pass
            else:
                name = req_body.get('name')

        if name:
            # MODIFICATION: write the a message to the message queue, using msg.set
            msg.set(f"Request made for {name} at {datetime.datetime.now()}")

            return func.HttpResponse(f"Hello {name}!")
        else:
            return func.HttpResponse(
                 "Please pass a name on the query string or in the request body",
                 status_code=400
            )
    ```

1. Para testar essas alterações localmente, inicie o depurador novamente no Visual Studio Code pressionando F5 ou selecionando o comando de menu **Depurar** > **Iniciar Depuração**. Como antes, agora a janela **Saída** deve mostrar os dois pontos de extremidade do seu projeto.

1. Em um navegador, visite a URL `http://localhost:7071/api/HttpExample?name=VS%20Code` para criar uma solicitação ao ponto de extremidade HttpExample, que também deve gravar uma mensagem na fila.

1. Para verificar se a mensagem foi gravada na fila "outqueue" (conforme denominação na associação), você pode usar um dos três métodos seguintes:

    1. Entre no [portal do Azure](https://portal.azure.com) e navegue até o grupo de recursos que contém o projeto de funções. Dentro desse grupo de recursos, localize e navegue até a conta de armazenamento do projeto e, em seguida, navegue até **Filas**. Nessa página, navegue até "outqueue", que deve exibir todas as mensagens registradas em log.

    1. Navegue e examine a fila com o Gerenciador de Armazenamento do Azure, que se integra ao Visual Studio – conforme descrito em [Conectar o Functions ao Armazenamento do Azure usando o Visual Studio Code](/azure/azure-functions/functions-add-output-binding-storage-queue-vs-code.md) – especialmente a seção [Examinar a fila de saída](/azure/azure-functions/functions-add-output-binding-storage-queue-vs-code.md#examine-the-output-queue).

    1. Use a CLI do Azure para consultar a fila de armazenamento, conforme descrito em [Consultar a fila de armazenamento](/azure/azure-functions/functions-add-output-binding-storage-queue-python.md#query-the-storage-queue).

1. Para testar na nuvem, reimplante o código usando **Implantar no Aplicativo de Funções**, no gerenciador do **Azure: Functions**. Se solicitado, selecione o Aplicativo de Funções criado anteriormente. Após a conclusão da implantação (demora alguns minutos!), a janela **Saída** mostra novamente os pontos de extremidade públicos com os quais você pode repetir os testes.

> [!div class="nextstepaction"]
> [Adicionei uma associação de armazenamento](tutorial-vs-code-serverless-python-08.md)

[Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=07-storage-binding)
