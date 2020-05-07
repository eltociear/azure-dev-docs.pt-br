---
title: Transmitir logs do Serviço de Aplicativo do Azure dentro do Visual Studio Code
description: Parte 4 do tutorial, exibir ou acompanhar logs.
ms.topic: conceptual
ms.date: 03/04/2020
ms.openlocfilehash: e6c1f4f87a28b33dac51d6ffc59c0cd9dfdc429d
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "78893514"
---
# <a name="stream-logs-from-azure-app-service"></a>Transmitir logs do Serviço de Aplicativo do Azure

[Etapa anterior: Implantar o site](tutorial-vscode-azure-app-service-node-03.md)

Nesta etapa, você aprenderá a exibir ou "acompanhar" qualquer saída que o aplicativo em execução gerar por meio de chamadas a `console.log`. Essa saída aparece na janela **Saída** no Visual Studio Code.

1. No gerenciador do **Serviço de Aplicativo do Azure**, clique com o botão direito do mouse no nó do aplicativo e selecione **Iniciar o Streaming de Logs**.

    ![Exibir logs de streaming](media/deploy-azure/start-streaming-logs.png)

1. Quando solicitado, escolha habilitar o registro em log e reinicie o aplicativo.

    ![Prompt para habilitar registro em log e reiniciar](media/deploy-azure/enable-restart.png)

1. Depois que o aplicativo for reiniciado, a janela **Saída** do VS Code será aberta com uma conexão com o fluxo de log que mostra a saída.

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).
    </pre>

1. Atualize a página da Web algumas vezes no navegador para ver saída de log adicional.

> [!div class="nextstepaction"]
> [Vejo os logs](tutorial-vscode-azure-app-service-node-05.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azureappservice&step=tailing-logs)
