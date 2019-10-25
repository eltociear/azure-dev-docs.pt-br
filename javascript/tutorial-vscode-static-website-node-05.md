---
title: Implantar alterações em um site do Node.js estático usando o Visual Studio Code
description: Parte 5 do tutorial, fazer alterações e reimplantar
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: buhollan
ms.openlocfilehash: 73d27549e5ff1acb294f8e65fa610d0b46f56914
ms.sourcegitcommit: 2757d8bd0cc045b7d02f430d44de859f9de853f4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72587264"
---
# <a name="make-changes-and-redeploy"></a>Fazer alterações e implantar novamente

[Etapa anterior: Implantar no Armazenamento do Azure](tutorial-vscode-static-website-node-04.md)

Nesta etapa, você faz uma alteração simples ao código-fonte do aplicativo e reimplanta o site para experimentar o fluxo de trabalho de implantação de ponta a ponta.

# <a name="angulartabangular"></a>[Angular](#tab/angular)

1. No Visual Studio Code, abra o arquivo _src/app/app.component.html_ e altere a linha 305 para corresponder ao seguinte:

    ```html
    <span>Welcome To Azure</span>
    ```

1. Em um terminal ou prompt de comando, execute `npm run build`.

1. No VS Code, clique com o botão direito do mouse na pasta _dist/my-static-site_ atualizada e, novamente, escolha **Implantar no Site Estático**. Escolha sua conta de armazenamento e confirme que você deseja implantar suas alterações. (A extensão do Azure exclui automaticamente os arquivos antigos antes de implantar as alterações para evitar problemas de cache.)

1. Quando a implantação for concluída, atualize o site no navegador para observar as alterações:

    ![Alterações no aplicativo após a reimplantação](media/static-website/updated-azure-app-angular.png)

# <a name="reacttabreact"></a>[React](#tab/react)

1. No Visual Studio Code, abra a linha 11 do arquivo de alteração _src/app.js_ para corresponder ao seguinte:

    ```html
    <h1 className="App-title">Welcome to Azure!</h1>
    ```

1. Em um terminal ou prompt de comando, execute `npm run build`.

1. No VS Code, clique com o botão direito do mouse na pasta _compilar_ atualizada e, novamente, escolha **Implantar para o Site Estático**. Escolha sua conta de armazenamento e confirme que você deseja implantar suas alterações. (A extensão do Azure exclui automaticamente os arquivos antigos antes de implantar as alterações para evitar problemas de cache.)

1. Quando a implantação for concluída, atualize o site no navegador para observar as alterações:

    ![Alterações no aplicativo após a reimplantação](media/static-website/updated-azure-app-react.png)

# <a name="vuetabvue"></a>[Vue](#tab/vue)

1. No Visual Studio Code, abra a linha 11 da alteração do arquivo _src/App.vue_ para corresponder ao seguinte:

    ```html
    <HelloWorld msg="Welcome to Azure!" />
    ```

1. Em um terminal ou prompt de comando, execute `npm run build`.

1. No VS Code, clique com o botão direito do mouse na pasta _dist_ e, novamente, escolha **Implantar no Site Estático**. Escolha sua conta de armazenamento e confirme que você deseja implantar suas alterações. (A extensão do Azure exclui automaticamente os arquivos antigos antes de implantar as alterações para evitar problemas de cache.)

1. Quando a implantação for concluída, atualize o site no navegador para observar as alterações:

    ![Alterações no aplicativo após a reimplantação](media/static-website/updated-azure-app-vue.png)

---

> [!div class="nextstepaction"]
> [Implantei as alterações](tutorial-vscode-static-website-node-06.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=code-change)
