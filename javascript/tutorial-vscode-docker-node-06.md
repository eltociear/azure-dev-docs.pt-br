---
title: Transmita logs de um aplicativo Node.js em contêineres usando o Visual Studio Code
description: Parte 5 do tutorial, transmitir logs para o Visual Studio Code
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: kraigb
ms.openlocfilehash: 9df23cb6aac013006cf0f21871f16eededdcb816
ms.sourcegitcommit: c04984b6367e922dbc5973af44f8cd0ca81ce157
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71685992"
---
# <a name="stream-logs-into-visual-studio-code"></a>Transmitir logs para o Visual Studio Code

[Etapa anterior: Fazer alterações e reimplantar](tutorial-vscode-docker-node-05.md)

Nesta etapa, você aprenderá a exibir ou "acompanhar" qualquer saída que o site em execução gere por meio de chamadas a `console.log`. Essa saída aparece na janela **Saída** no Visual Studio Code.

1. No gerenciador do **Serviço de Aplicativo do Azure**, clique com o botão direito do mouse no nó do aplicativo e selecione **Iniciar o Streaming de Logs**.

    ![Exibir logs de streaming](media/deploy-containers/stream-logs-command.png)

1. Quando solicitado, escolha habilitar o registro em log e reinicie o aplicativo.

    ![Prompt para habilitar registro em log e reiniciar](media/deploy-azure/enable-restart.png)

1. Depois que o aplicativo for reiniciado, o painel **Saída** no Visual Studio Code será aberto com uma conexão com o fluxo de log, começando com a mensagem `Starting Live Log Stream`.

> [!div class="nextstepaction"]
> [Vejo os logs](tutorial-vscode-docker-node-07.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-docker-extension&step=tailing-logs)