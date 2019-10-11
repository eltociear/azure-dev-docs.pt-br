---
title: Transmitir logs do Serviço de Aplicativo do Azure dentro do Visual Studio Code
description: Parte 4 do tutorial, exibir ou acompanhar logs.
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: kraigb
ms.openlocfilehash: 8484efc27120d374738a933244a3fce71c7c6acb
ms.sourcegitcommit: c04984b6367e922dbc5973af44f8cd0ca81ce157
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71686212"
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
