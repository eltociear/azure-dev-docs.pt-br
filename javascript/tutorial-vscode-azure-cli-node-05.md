---
title: Transmitir logs do Serviço de Aplicativo do Azure
description: Parte 5 do tutorial, exibir logs
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 8a173bbb7f53de2189e0ecb99b851d77704ff92d
ms.sourcegitcommit: e77f8f652128b798dbf972078a7b460ed21fb5f8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74466758"
---
# <a name="stream-logs-from-app-service"></a>Transmitir logs do Serviço de Aplicativo

[Etapa anterior: Implantar o aplicativo](tutorial-vscode-azure-cli-node-04.md)

Nesta etapa, você exibe (ou "acompanha") os logs do Serviço de Aplicativo em execução. Todas as chamadas para `console.log` no código do site são exibidas no terminal.

1. Execute o comando a seguir para iniciar o registro em log, substituindo `<your_app_name>` pelo nome de seu Serviço de Aplicativo:

    ```bash
    az webapp log tail --name <your_app_name>
    ```

1. Após alguns segundos, deverá ser exibida uma mensagem indicando que você está conectado ao serviço de streaming de log.

    ```bash
    2019-09-25T13:39:23  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours. Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).
    ```

1. Atualize a página algumas vezes no navegador para gerar uma saída adicional:

    ```bash
    GET / 304 2.327 ms - -
    GET / 304 0.957 ms - -
    GET / 304 2.435 ms - -
    ```

1. Pressione **Ctrl**+**C** para encerrar a sessão de log.

> [!div class="nextstepaction"]
> [Vejo os logs](tutorial-vscode-azure-cli-node-06.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=tailing-logs)
