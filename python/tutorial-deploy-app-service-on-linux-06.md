---
title: 'Tutorial: Transmitir logs do Serviço de Aplicativo do Azure para o VS Code'
description: Etapa 6 do tutorial, transmitindo logs de aplicativo para o Visual Studio Code
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: kraigb
ms.custom: seo-python-october2019
ms.openlocfilehash: f4aac132e9c01a0c428e243e06e811357defc2aa
ms.sourcegitcommit: bed07b313eeab51281d1a6d4eba67a75524b2f57
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72172228"
---
# <a name="tutorial-stream-logs-from-azure-app-service-into-visual-studio-code"></a>Tutorial: Transmitir logs do Serviço de Aplicativo do Azure dentro do Visual Studio Code

[Etapa anterior: implantar o aplicativo](tutorial-deploy-app-service-on-linux-05.md)

1. No Visual Studio Code, abra o explorador **Azure: Serviço de Aplicativo**, clique com o botão direito do mouse no Serviço de Aplicativo e selecione **Iniciar a transmissão de logs**:

   ![Comando Iniciar a transmissão de logs](media/deploy-azure/start-streaming-logs-command.png)

1. Quando for solicitado que você habilite o registro em log de arquivos e reinicie o aplicativo Web, selecione **Sim**. Enquanto o aplicativo é reiniciado, a janela **Saída** no VS Code mostra o progresso. Habilitar o registro em log é um processo único.

1. Depois que o registro em log estiver habilitado, clique com o botão direito do mouse no Serviço de Aplicativo e, novamente, selecione **Iniciar a transmissão de logs**. A janela **Saída** no VS Code exibe "Iniciando Transmissão de Logs ao Vivo" e a saída do log começa a aparecer. Tente atualizar o aplicativo Web no navegador para gerar mais informações de log.

1. Para interromper a transmissão de logs (sem desabilitar o registro em log), clique com o botão direito do mouse no aplicativo no explorador **Azure: Serviço de Aplicativo** e selecione **Interromper transmissão de logs**.

> [!div class="nextstepaction"]
> [Estou vendo os logs](tutorial-deploy-app-service-on-linux-07.md)

[Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-python&step=06-stream-logs)
