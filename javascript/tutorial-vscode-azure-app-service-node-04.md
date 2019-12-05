---
title: Transmitir logs do Serviço de Aplicativo do Azure dentro do Visual Studio Code
description: Parte 4 do tutorial, exibir ou acompanhar logs.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: cc140d7751f9b014f1a16065fd4c65b481c7d1ae
ms.sourcegitcommit: e77f8f652128b798dbf972078a7b460ed21fb5f8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74466808"
---
# <a name="stream-logs-from-azure-app-service"></a>Transmitir logs do Serviço de Aplicativo do Azure

[Etapa anterior: Implantar o site](tutorial-vscode-azure-app-service-node-03.md)

Nesta etapa, você aprenderá a exibir ou "acompanhar" qualquer saída que o site em execução gere por meio de chamadas a `console.log`. Essa saída aparece na janela **Saída** no Visual Studio Code.

1. No gerenciador do **Serviço de Aplicativo do Azure**, clique com o botão direito do mouse no nó do aplicativo e selecione **Iniciar o Streaming de Logs**.

    ![Exibir logs de streaming](media/deploy-azure/view-logs.png)

1. Quando solicitado, escolha habilitar o registro em log e reinicie o aplicativo.

    ![Prompt para habilitar registro em log e reiniciar](media/deploy-azure/enable-restart.png)

1. Depois que o aplicativo for reiniciado, a janela **Saída** do VS Code será aberta com uma conexão com o fluxo de log que mostra a saída.

    ```bash
    Connecting to log-streaming service...
    2019-09-20 17:33:51.428 INFO  - Container msdocs-vscode-node_2 for site msdocs-vscode-node initialized successfully.
    2019-09-20 17:33:56.500 INFO  - Container logs
    ```

1. Atualize a página da Web algumas vezes no navegador para ver saída de log adicional.

> [!div class="nextstepaction"]
> [Vejo os logs](tutorial-vscode-azure-app-service-node-05.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azureappservice&step=tailing-logs)
