---
title: Fazer alterações ao código do aplicativo e reimplantar no Azure
description: Parte 6 do tutorial, fazer alterações e reimplantar
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: kraigb
ms.openlocfilehash: f656e7414d6b7b6cecc28d69f108bfe92eb82894
ms.sourcegitcommit: c04984b6367e922dbc5973af44f8cd0ca81ce157
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71686125"
---
# <a name="make-changes-and-redeploy"></a>Fazer alterações e implantar novamente

[Etapa anterior: Transmitir logs](tutorial-vscode-azure-cli-node-05.md)

Nesta etapa, você faz alterações ao código do aplicativo, confirma-as para o repositório Git local e reimplanta o site enviando-o por push para o Azure.

1. Na pasta `myExpressApp`, abra o arquivo *views/index.pug* e altere a mensagem na linha 5 para `p Welcome to Azure!`.

    ![Como editar o arquivo index.pug](media/azure-cli/editpugfile.png)

1. Salve o arquivo.

1. Em um terminal ou em um prompt de comando, confirme as alterações no git executando o seguinte comando:

    ```bash
    git commit -a -m "Edited message"
    ```

1. Envie por push as alterações para o Git remoto chamado Azure criado anteriormente:

    ```bash
    git push azure master
    ```

1. Como o Serviço de aplicativo já está conectado ao repositório Git, a saída do comando mostra que as alterações são publicadas automaticamente no Azure: 

    ```output
    Enumerating objects: 7, done.
    Counting objects: 100% (7/7), done.
    Delta compression using up to 4 threads
    Compressing objects: 100% (4/4), done.
    Writing objects: 100% (4/4), 405 bytes | 202.00 KiB/s, done.
    Total 4 (delta 2), reused 0 (delta 0)
    remote: Updating branch 'master'.
    remote: Updating submodules.
    remote: Preparing deployment for commit id '9fd89a25b6'.
    remote: Generating deployment script.
    remote: Running deployment command...
    remote: Handling node.js deployment.
    remote: Creating app_offline.htm
    remote: KuduSync.NET from: 'D:\home\site\repository' to: 'D:\home\site\wwwroot'
    remote: Copying file: 'views\index.pug'
    remote: Deleting app_offline.htm
    remote: Using start-up script bin/www from package.json.
    remote: Generated web.config.
    remote: The package.json file does not specify node.js engine version constraints.
    remote: The node.js application will run with the default node.js version 6.9.5.
    remote: Selected npm version 3.10.10
    remote: ..
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://msdocs-node-cli.scm.azurewebsites.net/msdocs-node-cli.git
       a98bad8..9fd89a2  master -> master
    ```

1. Atualize o aplicativo no navegador para ver tais alterações:

    ![Alterações publicadas visíveis no navegador](media/azure-cli/remote-app-changes.png)

> [!div class="nextstepaction"]
> [Vejo minhas alterações](tutorial-vscode-azure-cli-node-07.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=publishing-changes)
