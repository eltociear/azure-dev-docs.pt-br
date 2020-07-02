---
title: Criar o Serviço de Aplicativo do Azure no Visual Studio Code
description: 'Parte 2 do tutorial: criar o aplicativo Node.js e executá-lo localmente'
ms.topic: conceptual
ms.date: 03/04/2020
ms.openlocfilehash: 86d3801b31f1a0c5fb988940a7c9f550a991f0d2
ms.sourcegitcommit: 553da4e9aa988e5bb823364244ea81961cee5bc7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85791098"
---
# <a name="create-and-run-a-local-nodejs-app"></a>Criar e executar um aplicativo Node.js local

[Etapa anterior: Introdução e pré-requisitos](tutorial-vscode-azure-app-service-node-01.md)

Nesta etapa, você criará um aplicativo Node.js simples usando o Gerador de aplicativos do Express. Você executará o aplicativo localmente.

1. Em um terminal ou prompt de comando, navegue até um local em que você deseja criar a pasta do aplicativo.

1. Execute o comando a seguir para criar um aplicativo Express denominado *expressApp1* usando o Gerador do Express. (Os parâmetros `--view pug --git` instruem o gerador a usar o mecanismo de modelo [pug](https://pugjs.org/api/getting-started.html), anteriormente conhecido como Jade, e criar um arquivo *.gitignore*.)

    ```bash
    npx express-generator expressApp1 --view pug -–git
    ```

1. Navegue até a pasta do aplicativo:

    ```bash
    cd expressApp1
    ```

1. Instale as dependências do aplicativo:

    ```bash
    npm install
    ```

1. Inicie o servidor:

    ```bash
    npm start
    ```

1. Teste o aplicativo abrindo um navegador em `http://localhost:3000`. O site deve aparecer da seguinte maneira:

    ![Executar aplicativo Express](media/deploy-azure/express.png)

1. Pressione **Ctrl**+**C** no terminal para parar o servidor.

> [!div class="nextstepaction"]
> [Criei o aplicativo Node.js](tutorial-vscode-azure-app-service-node-03.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azureappservice&step=create-app)
