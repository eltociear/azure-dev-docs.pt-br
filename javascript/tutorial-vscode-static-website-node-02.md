---
title: Criar o aplicativo Node.js estático no Visual Studio Code
description: Parte 2 do tutorial, criar o aplicativo de exemplo
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: buhollan
ms.openlocfilehash: b57789ca26851c27a3a68c9d095f327ea64164cf
ms.sourcegitcommit: 2757d8bd0cc045b7d02f430d44de859f9de853f4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72587253"
---
# <a name="create-the-app"></a>Crie o aplicativo

[Etapa anterior: Introdução e pré-requisitos](tutorial-vscode-static-website-node-01.md)

Nesta etapa, você usará a CLI (Interface de Linha de Comando) para [Angular](https://cli.angular.io/), [React](https://github.com/facebook/create-react-app) ou [Vue](https://cli.vuejs.org/) para criar um aplicativo simples que pode ser implantado no Azure. Como alternativa, você pode usar qualquer outra estrutura JavaScript que produza um conjunto de arquivos estáticos ou qualquer pasta que contenha arquivos HTML, CSS ou JavaScript. Se você já tiver um aplicativo pronto para implantar, poderá pular para [Criar uma conta de armazenamento do Azure](tutorial-vscode-static-website-node-03.md).

# <a name="angulartabangular"></a>[Angular](#tab/angular)

1. Use a CLI para realizar o scaffold de um novo aplicativo chamado “my-static-app” executando o seguinte comando:

    ```bash
    npx @angular/cli new my-static-app
    ```

    Quando a CLI solicitar perguntas de configuração, pressione enter para selecionar as opções padrão.

1. Compile o aplicativo alternando para a nova pasta e executando `npm run build`:

    ```bash
    cd my-static-app
    npm run build
    ```

1. Agora você deve ter uma pasta _dist_ na pasta _my-static-app_. Dentro dessa pasta _dist_, haverá uma pasta com o mesmo nome que o seu projeto – _my-static-app_. A pasta _build/my-static-app_ contém os arquivos HTML, CSS e JavaScript que você implanta no Armazenamento do Azure.

1. Execute o aplicativo usando o comando a seguir:

    ```bash
    npm start
    ```

1. Abra um navegador para [http://localhost:3000](http://localhost:3000) para verificar se o aplicativo está em execução:

    ![O aplicativo Angular de exemplo em execução](media/static-website/local-app-angular.png)

1. Pare o servidor pressionando **Ctrl**+**C** no terminal ou no prompt de comando.

# <a name="reacttabreact"></a>[React](#tab/react)

1. Use a CLI para realizar o scaffold de um novo aplicativo chamado “my-static-app” executando o seguinte comando:

    ```bash
    npx create-react-app my-static-app
    ```

1. Compile o aplicativo alternando para a nova pasta e executando `npm run build`:

    ```bash
    cd my-static-app
    npm run build
    ```

1. Agora você deve ter uma pasta _build_ na pasta _my-static-app_. A pasta _compilar_ contém os arquivos HTML, CSS e JavaScript que você implanta no Armazenamento do Azure.

1. Execute o aplicativo usando o comando a seguir:

    ```bash
    npm start
    ```

1. Abra um navegador para [http://localhost:3000](http://localhost:3000) para verificar se o aplicativo está em execução:

    ![O aplicativo React de exemplo em execução](media/static-website/local-app-react.png)

1. Pare o servidor pressionando **Ctrl**+**C** no terminal ou no prompt de comando.

# <a name="vuetabvue"></a>[Vue](#tab/vue)

1. Use a CLI para realizar o scaffold de um novo aplicativo chamado “my-static-app” executando o seguinte comando:

    ```bash
    npx @vue/cli create my-static-app
    ```

Quando a CLI solicitar perguntas de configuração, pressione enter para selecionar as opções padrão.

1. Compile o aplicativo alternando para a nova pasta e executando `npm run build`:

    ```bash
    cd my-static-app
    npm run build
    ```

1. Agora você deve ter uma pasta _dist_ na pasta _my-static-app_. A pasta _dist_ contém os arquivos HTML, CSS e JavaScript que você implanta no Armazenamento do Azure.

1. Execute o aplicativo usando o comando a seguir:

     ```bash
     npm run serve
     ```

1. Abra um navegador para [http://localhost:8080](http://localhost:8080) para verificar se o aplicativo está em execução:

    ![O aplicativo Vue de exemplo em execução](media/static-website/local-app-vue.png)

1. Pare o servidor pressionando **Ctrl**+**C** no terminal ou no prompt de comando.

---

> [!div class="nextstepaction"]
> [Criei o aplicativo](tutorial-vscode-static-website-node-03.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=create-app)
