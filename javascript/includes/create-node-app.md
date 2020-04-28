---
author: burkeholland
ms.service: app-service
ms.topic: include
ms.date: 03/31/2020
ms.author: buhollan
ms.openlocfilehash: 6cb841a9cf9563c3b7b3db86d907eac757c5d31d
ms.sourcegitcommit: 36e02e96b955ed0531f98b9c0f623f4acb508661
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82072661"
---
1. Em um prompt de comando do terminal, vá até o local em que você deseja criar a pasta do aplicativo.

1. Execute o comando a seguir para criar um aplicativo Express denominado `myexpressapp` usando o Gerador Express. Os parâmetros `--git --view pug` instruem o gerador a criar um arquivo .gitignore e usar o mecanismo de modelo [Pug](https://pugjs.org/api/getting-started.html), que antes era conhecido como Jade.

    ```bash
    npx express-generator myexpressapp --git --view pug
    ```

1. Vá para a pasta do aplicativo:

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

1. Teste o aplicativo abrindo um navegador e indo para [http://localhost:3000](http://localhost:3000). O site deve aparecer da seguinte maneira:

    ![Executar o aplicativo Express](../media/deploy-azure/express.png)

1. Selecione **Ctrl**+**C** no terminal para interromper o servidor.
 
