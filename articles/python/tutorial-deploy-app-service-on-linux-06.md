---
title: 'Etapa 6: Transmitir logs do Serviço de Aplicativo do Azure para o VS Code'
description: Etapa 6 do tutorial, transmitindo logs de aplicativo para o Visual Studio Code
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: e6955e849fbec2d3d3c3546b00fb3745b6a0b8f3
ms.sourcegitcommit: 980efe813d1f86e7e00929a0a3e1de83514ad7eb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87983548"
---
# <a name="6-stream-logs-from-azure-app-service-into-visual-studio-code"></a>6: Transmitir logs do Serviço de Aplicativo do Azure dentro do Visual Studio Code

[Etapa anterior: implantar o aplicativo](tutorial-deploy-app-service-on-linux-05.md)

Use este procedimento para transmitir logs de um Serviço de Aplicativo do Azure para o Visual Studio Code.

1. No Visual Studio Code, abra o explorador **Azure: Serviço de Aplicativo**, clique com o botão direito do mouse no Serviço de Aplicativo e selecione **Iniciar a transmissão de logs**:

   ![Iniciar a transmissão de logs do gerenciador do Serviço de Aplicativo](media/deploy-azure/start-streaming-logs-in-visual-studio-code.png)

1. Quando for solicitado que você habilite o registro em log de arquivos e reinicie o aplicativo Web, selecione **Sim**. Enquanto o aplicativo é reiniciado, a janela **Saída** no VS Code mostra o progresso. Habilitar o registro em log é um processo único.

1. Depois que o registro em log estiver habilitado, clique com o botão direito do mouse no Serviço de Aplicativo e, novamente, selecione **Iniciar a transmissão de logs**. A janela **Saída** no VS Code exibe "Iniciando Transmissão de Logs ao Vivo" e a saída do log começa a aparecer. Tente atualizar o aplicativo Web no navegador para gerar mais informações de log.

1. Para interromper a transmissão de logs (sem desabilitar o registro em log), clique com o botão direito do mouse no aplicativo no explorador **Azure: Serviço de Aplicativo** e selecione **Interromper transmissão de logs**.

> [!div class="nextstepaction"]
> [Vejo os logs – prossiga para a etapa 7 >>>](tutorial-deploy-app-service-on-linux-07.md)

[Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-python&step=06-stream-logs)
