---
title: Transmitir logs do Serviço de Aplicativo do Azure para o VS Code
description: Etapa 6 do tutorial, transmitindo logs de aplicativo para o Visual Studio Code
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: kraigb
ms.openlocfilehash: 39ae534580e5057b1ddcf93588be94ad67f2a9b4
ms.sourcegitcommit: 74e28a479c87a3a53592646420b78e69852dd86a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/16/2019
ms.locfileid: "71020164"
---
# <a name="stream-logs-from-the-running-app"></a>Transmitir logs do aplicativo em execução

[Etapa anterior: implantar o aplicativo](tutorial-deploy-app-service-on-linux-05.md)

1. No Visual Studio Code, abra o explorador **Azure: Serviço de Aplicativo**, clique com o botão direito do mouse no Serviço de Aplicativo e selecione **Iniciar a transmissão de logs**:

   ![Comando Iniciar a transmissão de logs](media/deploy-azure/start-streaming-logs-command.png)

1. Quando for solicitado que você habilite o registro em log de arquivos e reinicie o aplicativo Web, selecione **Sim**. Enquanto o aplicativo é reiniciado, a janela **Saída** no VS Code mostra o progresso. Habilitar o registro em log é um processo único.

1. Depois que o registro em log estiver habilitado, clique com o botão direito do mouse no Serviço de Aplicativo e, novamente, selecione **Iniciar a transmissão de logs**. A janela **Saída** no VS Code exibe "Iniciando Transmissão de Logs ao Vivo" e a saída do log começa a aparecer. Tente atualizar o aplicativo Web no navegador para gerar mais informações de log.

1. Para interromper a transmissão de logs (sem desabilitar o registro em log), clique com o botão direito do mouse no aplicativo no explorador **Azure: Serviço de Aplicativo** e selecione **Interromper transmissão de logs**.

> [!div class="nextstepaction"]
> [Estou vendo os logs](tutorial-deploy-app-service-on-linux-07.md)

[Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-python&step=06-stream-logs)
