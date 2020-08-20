---
author: burkeholland
ms.service: app-service
ms.topic: include
ms.date: 03/31/2020
ms.author: buhollan
ms.custom: devx-track-javascript
ms.openlocfilehash: 9db8df165bd4d0f42694ff1e752878c8f055bce8
ms.sourcegitcommit: 0699b984b85782b1c441289fa756f285eae853c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88218405"
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
 
