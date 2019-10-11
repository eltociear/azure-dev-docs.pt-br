---
title: Implantar alterações em um site do Node.js estático usando o Visual Studio Code
description: Parte 5 do tutorial, fazer alterações e reimplantar
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: kraigb
ms.openlocfilehash: 986d2a0f8999d79dfd1d856ed20a053c495a3765
ms.sourcegitcommit: c04984b6367e922dbc5973af44f8cd0ca81ce157
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71685934"
---
# <a name="make-changes-and-redeploy"></a>Fazer alterações e implantar novamente

[Etapa anterior: Implantar no Armazenamento do Azure](tutorial-vscode-static-website-node-04.md)

Nesta etapa, você faz uma alteração simples ao código-fonte do aplicativo e reimplanta o site para experimentar o fluxo de trabalho de implantação de ponta a ponta.

1. No Visual Studio Code, abra a linha 11 do arquivo de alteração *src/app.js* para corresponder ao seguinte:

    ```js
    <h1 className="App-title">Welcome to Azure!</h1>
    ```

1. Em um terminal ou prompt de comando, execute `npm run build`.

1. No VS Code, clique com o botão direito do mouse na pasta *compilar* atualizada e, novamente, escolha **Implantar para o Site Estático**. Escolha sua conta de armazenamento e confirme que você deseja implantar suas alterações. (A extensão do Azure exclui automaticamente os arquivos antigos antes de implantar as alterações para evitar problemas de cache.)

1. Quando a implantação for concluída, atualize o site no navegador para observar as alterações:

    ![Alterações no aplicativo após a reimplantação](media/static-website/updated-azure-app.png)

> [!div class="nextstepaction"]
> [Implantei as alterações](tutorial-vscode-static-website-node-06.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=code-change)
