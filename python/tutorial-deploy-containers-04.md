---
title: 'Etapa 4: Transmitir logs do Serviço de Aplicativo do Azure para um contêiner dentro do Visual Studio Code'
description: 'Tutorial, parte 4: exibição de logs do Serviço de Aplicativo do Azure para monitorar seu comportamento.'
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: seo-python-october2019
ms.openlocfilehash: 4f448945c0e1d1eda7870eb633d644535da7be0e
ms.sourcegitcommit: 44d1abfb836f90b8731d7ea5d5a5af09245b2b89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2020
ms.locfileid: "77422221"
---
# <a name="4-stream-logs-from-azure-app-service-for-a-container"></a>4: Transmitir logs do Serviço de Aplicativo do Azure para um contêiner

[Etapa anterior: fazer alterações e reimplantar](tutorial-deploy-containers-03.md)

Use este procedimento para transmitir logs de um Serviço de Aplicativo do Azure de um contêiner para o Visual Studio Code.

De dentro do VS Code, você pode exibir (ou "acompanhar") os logs do site em execução no Serviço de Aplicativo do Azure, que captura qualquer saída para o console – como de instruções `print` – e as direciona para o painel **Saída** do VS Code.

1. Localize o aplicativo no gerenciador do **Azure: Serviço de Aplicativo**, clique com o botão direito do mouse no aplicativo e selecione **Iniciar Streaming de Logs**.

1. Responda **Sim** quando questionado se deseja habilitar o registro em log e reinicie o aplicativo. Depois que o aplicativo for reiniciado, o painel Saída do VS Code será aberto com uma conexão ao streaming de log.

1. Após alguns segundos, será exibida uma mensagem indicando que você está conectado ao serviço de streaming de log.

    ```bash
    Connecting to log stream...
    2018-09-27T20:14:26  Welcome, you are now connected to log-streaming service.

    2018-09-27 20:14:59.269 INFO  - Starting container for site

    2018-09-27 20:14:59.270 INFO  - docker run -d -p 24138:8000 --name vsdocs-django-sample-container_0 -e WEBSITES_PORT=8000 -e WEBSITE_SITE_NAME=vsdocs-django-sample-container -e WEBSITE_AUTH_ENABLED=False -e WEBSITE_ROLE_INSTANCE_ID=0 -e WEBSITE_INSTANCE_ID=02c705ae24eaf5f298e553a9c2724b9fe4485707c2d1c36137cd02931091e561 -e HTTP_LOGGING_ENABLED=1 vsdocsregistry.azurecr.io/python-sample-vscode-django-tutorial:latest

    2018-09-27 20:15:06.216 INFO  - Container vsdocs-django-sample-container_0 for site vsdocs-django-sample-container initialized successfully.
    ```

1. Navegue dentro do aplicativo a fim de ver saídas adicionais para várias solicitações HTTP.

> [!div class="nextstepaction"]
> [Vejo os logs – prossiga para a etapa 5 >>>](tutorial-deploy-containers-05.md)

[Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-containers&step=04-stream-logs)
