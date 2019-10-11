---
title: Criar o aplicativo Node.js estático no Visual Studio Code
description: Parte 2 do tutorial, criar o aplicativo de exemplo
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: kraigb
ms.openlocfilehash: 566d166a69bbbee59726b8e381bee4a24077d8c6
ms.sourcegitcommit: c04984b6367e922dbc5973af44f8cd0ca81ce157
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71685974"
---
# <a name="create-the-app"></a>Crie o aplicativo

[Etapa anterior: Introdução e pré-requisitos](tutorial-vscode-static-website-node-01.md)

Nesta etapa, você usa a CLI do utilitário React, [create-react-app](https://github.com/facebook/create-react-app) para criar um aplicativo React simples que pode ser implantado no Azure. Como alternativa, você pode usar Angular, Vue, outra estrutura ou qualquer pasta que contenha alguns arquivos HTML. Se você já tiver um aplicativo pronto para implantar, poderá pular para [Criar uma conta de armazenamento do Azure](tutorial-vscode-static-website-node-03.md).

1. Use a ferramenta create-react-app para realizar o scaffold de um novo aplicativo React chamado `my-react-app` executando o seguinte comando:

    ```bash
    npm create react-app my-react-app
    ```

1. Compile o aplicativo alternando para a nova pasta e executando `npm run build`:

    ```bash
    cd my-react-app
    npm run build
    ```

1. Agora você deve ter uma pasta *compilar* na pasta *my-react-app*. A pasta *compilar* contém os arquivos HTML, CSS e JavaScript que você implanta no Armazenamento do Azure.

1. Execute o aplicativo usando o comando a seguir:

    ```bash
    npm start
    ```

1. Abra um navegador para [http://localhost:3000](http://localhost:3000) para verificar se o aplicativo está em execução:

    ![O aplicativo React de exemplo em execução](media/static-website/local-app.png)

1. Pare o servidor pressionando **Ctrl**+**C** no terminal ou no prompt de comando.

> [!div class="nextstepaction"]
> [Criei o aplicativo](tutorial-vscode-static-website-node-03.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=create-app)
