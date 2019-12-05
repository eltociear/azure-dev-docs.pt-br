---
title: Criar um aplicativo Node.js para implantar no Azure usando a CLI do Azure
description: Parte 2 do tutorial, criar o código do aplicativo.
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: dba089c58cf6413263348b7bdeb975852c2e6a2f
ms.sourcegitcommit: e77f8f652128b798dbf972078a7b460ed21fb5f8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74467184"
---
# <a name="create-the-app-code-using-express"></a>Criar o código do aplicativo usando o Expresso

[Etapa anterior: Introdução e pré-requisitos](tutorial-vscode-azure-cli-node-01.md)

Nesta etapa, você cria um aplicativo Node.js simples com o [Expresso](https://www.expressjs.com) usando o [Gerador do Expresso](https://expressjs.com/en/starter/generator.html).

1. Use o seguinte comando para executar o Gerador Expresso e realizar o scaffold de um novo aplicativo Expresso chamado "myExpressApp". (Os parâmetros `--view pug --git` instruem o gerador a usar o mecanismo de modelo [pug](https://pugjs.org/api/getting-started.html), anteriormente conhecido como Jade, e criar um arquivo *.gitignore*.)

    ```bash
    npx express-generator myExpressApp --view pug –git
    ```

1. Navegue até a pasta do aplicativo e instale as dependências do aplicativo executando os seguintes comandos:

    ```bash
    cd myExpressApp
    npm install
    ```

1. Inicie o servidor do aplicativo executando o seguinte comando:

    ```bash
    npm start
    ```

1. Abra um navegador para [http://localhost:3000](http://localhost:3000) para ver o aplicativo em execução:

    ![Como executar o aplicativo expresso localmente](media/azure-cli/local-app.png)

1. Ao você terminar de testar o aplicativo, pare o servidor pressionando **Ctrl**+**C** no terminal em que você executou o `npm start`.

> [!div class="nextstepaction"]
> [Criei o aplicativo](tutorial-vscode-azure-cli-node-03.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=express)
