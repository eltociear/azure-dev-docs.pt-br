---
author: burkeholland
ms.service: app-service
ms.topic: include
ms.date: 03/31/2020
ms.author: buhollan
ms.openlocfilehash: c95177d6b4cb101b764acd8f8dad54f937a495eb
ms.sourcegitcommit: 553da4e9aa988e5bb823364244ea81961cee5bc7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85791304"
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

1. Teste o aplicativo abrindo um navegador e acessando `http://localhost:3000`. O site deve aparecer da seguinte maneira:

    ![Executar o aplicativo Express](../media/deploy-azure/express.png)

1. Selecione **Ctrl**+**C** no terminal para interromper o servidor.
 
