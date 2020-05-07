---
title: Transmita logs de um aplicativo Node.js em contêineres usando o Visual Studio Code
description: Parte 7 do tutorial, transmitir logs para o Visual Studio Code
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 10ccf13cddfc7bb1ed7f226629072cb9baeea3a1
ms.sourcegitcommit: 756e4873f904db954a56c20ebb2f1f5116ee4596
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "80740634"
---
# <a name="stream-logs-into-visual-studio-code"></a>Transmitir logs para o Visual Studio Code

[Etapa anterior: Fazer alterações e reimplantar](tutorial-vscode-docker-node-06.md)

Nesta etapa, você aprenderá a exibir ou "acompanhar" qualquer saída que o site em execução gere por meio de chamadas a `console.log`. Essa saída aparece na janela **Saída** no Visual Studio Code.

1. No gerenciador do **Serviço de Aplicativo do Azure**, clique com o botão direito do mouse no nó do aplicativo e selecione **Iniciar o Streaming de Logs**.

    ![Exibir logs de streaming](media/deploy-containers/stream-logs-command.png)

1. Quando solicitado, escolha habilitar o registro em log e reinicie o aplicativo.

    ![Prompt para habilitar registro em log e reiniciar](media/deploy-azure/enable-restart.png)

1. Depois que o aplicativo for reiniciado, o painel **Saída** no Visual Studio Code será aberto com uma conexão com o fluxo de log, começando com a mensagem `Starting Live Log Stream`.

> [!div class="nextstepaction"]
> [Vejo os logs](tutorial-vscode-docker-node-08.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-docker-extension&step=tailing-logs)
