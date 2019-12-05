---
title: Criar o Serviço de Aplicativo do Azure no Visual Studio Code
description: Parte 2 do tutorial, criar o aplicativo Node.js
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 96c786b7cc8112c36c0aff06761417a97e30bf44
ms.sourcegitcommit: e77f8f652128b798dbf972078a7b460ed21fb5f8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74466804"
---
# <a name="create-your-nodejs-application"></a>Crie seu aplicativo Node.js

[Etapa anterior: Introdução e pré-requisitos](tutorial-vscode-azure-app-service-node-01.md)

Nesta etapa, você cria um aplicativo Node.js simples usando o gerador de aplicativo Expresso que você então pode implantar no Azure.

Você também pode usar o aplicativo do [tutorial do Node.js do Visual Studio Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial); nesse caso, você pode pular para [Implantar o aplicativo](tutorial-vscode-azure-app-service-node-03.md).

1. Em um terminal ou prompt de comando, use o seguinte comando para executar o Gerador Expresso e realizar o scaffold de um novo aplicativo Expresso chamado "myExpressApp". (Os parâmetros `--view pug --git` instruem o gerador a usar o mecanismo de modelo [pug](https://pugjs.org/api/getting-started.html), anteriormente conhecido como Jade, e criar um arquivo *.gitignore*.)

    ```bash
    npx express-generator myExpressApp --view pug -–git
    ```

1. Instale as dependências do aplicativo executando `npm install` na pasta do aplicativo:

    ```bash
    cd myExpressApp
    npm install
    ```

1. Inicie o servidor executando `npm start`:

    ```bash
    npm start
    ```

1. Teste o aplicativo abrindo um navegador para [http://localhost:3000](http://localhost:3000). O site deve aparecer da seguinte maneira:

    ![Executar aplicativo Express](media/deploy-azure/express.png)

> [!div class="nextstepaction"]
> [Criei o aplicativo Node.js](tutorial-vscode-azure-app-service-node-03.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azureappservice&step=create-app)
