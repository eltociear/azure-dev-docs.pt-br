---
title: 'Etapa 4: Transmitir logs do Serviço de Aplicativo do Azure para um contêiner dentro do Visual Studio Code'
description: 'Tutorial, parte 4: exibição de logs do Serviço de Aplicativo do Azure para monitorar seu comportamento.'
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: 47316324010d5a74568bb55be508128917702780
ms.sourcegitcommit: 69933dcce571b2686897b295b7822e207d944617
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2020
ms.locfileid: "90772639"
---
# <a name="4-stream-logs-from-azure-app-service-for-a-container"></a>4: Transmitir logs do Serviço de Aplicativo do Azure para um contêiner

[Etapa anterior: fazer alterações e reimplantar](tutorial-deploy-containers-03.md)

Use este procedimento para transmitir logs de um Serviço de Aplicativo do Azure de um contêiner para o Visual Studio Code.

De dentro do VS Code, você pode exibir (ou "acompanhar") os logs do site em execução no Serviço de Aplicativo do Azure, que captura qualquer saída para o console – como de instruções `print` – e as direciona para o painel **Saída** do VS Code.

1. Localize o aplicativo no gerenciador do **Azure: Serviço de Aplicativo**, clique com o botão direito do mouse no aplicativo e selecione **Iniciar Streaming de Logs**.

1. Responda **Sim** quando questionado se deseja habilitar o registro em log e reinicie o aplicativo. Depois que o aplicativo for reiniciado, o painel Saída do VS Code será aberto com uma conexão ao streaming de log.

1. Após alguns segundos, você verá uma mensagem na saída indicando que você está conectado ao serviço de streaming de log:

    <pre>
    Connecting to log stream...
    2018-09-27T20:14:26  Welcome, you are now connected to log-streaming service.

    2018-09-27 20:14:59.269 INFO  - Starting container for site

    2018-09-27 20:14:59.270 INFO  - docker run -d -p 24138:8000 --name vsdocs-django-sample-container_0 -e WEBSITES_PORT=8000 -e WEBSITE_SITE_NAME=vsdocs-django-sample-container -e WEBSITE_AUTH_ENABLED=False -e WEBSITE_ROLE_INSTANCE_ID=0 -e WEBSITE_INSTANCE_ID=02c705ae24eaf5f298e553a9c2724b9fe4485707c2d1c36137cd02931091e561 -e HTTP_LOGGING_ENABLED=1 vsdocsregistry.azurecr.io/python-sample-vscode-django-tutorial:latest

    2018-09-27 20:15:06.216 INFO  - Container vsdocs-django-sample-container_0 for site vsdocs-django-sample-container initialized successfully.
    </pre>

1. Navegue dentro do aplicativo a fim de ver saídas adicionais para várias solicitações HTTP.

> [!div class="nextstepaction"]
> [Vejo os logs – prossiga para a etapa 5 >>>](tutorial-deploy-containers-05.md)

