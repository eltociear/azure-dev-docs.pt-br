---
author: burkeholland
ms.service: app-service
ms.topic: include
ms.date: 03/31/2020
ms.author: buhollan
ms.openlocfilehash: 59eea52aab3f2b1941a3accb5848bc4ad92d2992
ms.sourcegitcommit: f89c59f772364ec717e751fb59105039e6fab60c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80740503"
---
1. Em um terminal ou prompt de comando, navegue até um local em que você deseja criar a pasta do aplicativo.

1. Execute o comando a seguir para criar um aplicativo Express denominado *myexpressapp* usando o Gerador do Express. Os parâmetros `--git --view pug` informam ao gerador para criar um arquivo *.gitignore*. e para usar o mecanismo de modelo [pug](https://pugjs.org/api/getting-started.html), anteriormente conhecido como Jade.

    ```bash
    npx express-generator myexpressapp --git --view pug
    ```

1. Navegue até a pasta do aplicativo:

    ```bash
    cd myexpressapp
    ```

1. Instale as dependências do aplicativo:

    ```bash
    npm install
    ```

1. Inicie o servidor:

    ```bash
    npm start
    ```

1. Teste o aplicativo abrindo um navegador para [http://localhost:3000](http://localhost:3000). O site deve aparecer da seguinte maneira:

    ![Executar aplicativo Express](../media/deploy-azure/express.png)

1. Selecione **CTRL**+**C** no terminal para interromper o servidor.
