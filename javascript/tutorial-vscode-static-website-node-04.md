---
title: Implantar os arquivos do aplicativo Node.js estático no Armazenamento do Azure usando o Visual Studio Code
description: Parte 4 do tutorial, implantar os arquivos no Armazenamento do Azure
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: buhollan
ms.openlocfilehash: 2683de33fd8ddc7148991d85e3d67f1eb93a5f44
ms.sourcegitcommit: e77f8f652128b798dbf972078a7b460ed21fb5f8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74466654"
---
# <a name="deploy-the-website-to-azure-storage"></a>Implantar o site para o Armazenamento do Azure

[Etapa anterior: Criar uma conta de Armazenamento](tutorial-vscode-static-website-node-03.md)

Nesta etapa, você usa o Visual Studio Code para implantar os arquivos do site estático criados nas etapas anteriores para o Armazenamento do Azure, que hospeda e serve esses arquivos.

# <a name="angulartabangular"></a>[Angular](#tab/angular)

1. No Visual Studio Code, vá para o gerenciador do **Armazenamento do Azure**, expanda sua assinatura, expanda o nó da conta de Armazenamento do Azure criada na etapa anterior e, em seguida, expanda o nó **Contêineres de Blob**. É no contêiner `$web` que você implanta o código do aplicativo.

   ![Nós de Armazenamento do Azure no gerenciador de Armazenamento do Azure](media/static-website/storage-nodes.png)

1. Selecione o gerenciador de **Arquivos**, clique com o botão direito do mouse na pasta _dist/my-static-app_ e escolha **Implantar no Site Estático**:

    ![Comando Implantar para o Site Estático](media/static-website/deploy-build-angular.png)

1. Quando solicitado, escolha a conta de Armazenamento que você criou anteriormente.

1. Quando a implantação for concluída, uma mensagem será exibida com um botão **Navegar para o site**. Selecione esse botão para abrir o ponto de extremidade primário do código do aplicativo implantado.

    ![Mensagem de implantação concluída](media/static-website/deployment-complete.png)

    ![Site estático em execução no Azure](media/static-website/azure-app-angular.png)

# <a name="reacttabreact"></a>[React](#tab/react)

1. No Visual Studio Code, vá para o gerenciador do **Armazenamento do Azure**, expanda sua assinatura, expanda o nó da conta de Armazenamento do Azure criada na etapa anterior e, em seguida, expanda o nó **Contêineres de Blob**. É no contêiner `$web` que você implanta o código do aplicativo.

   ![Nós de Armazenamento do Azure no gerenciador de Armazenamento do Azure](media/static-website/storage-nodes.png)

1. Selecione o gerenciador de **Arquivos**, clique com o botão direito do mouse na pasta _compilar_ e escolha **Implantar para o Site Estático**:

    ![Comando Implantar para o Site Estático](media/static-website/deploy-build-react.png)

1. Quando solicitado, escolha a conta de Armazenamento que você criou anteriormente.

1. Quando a implantação for concluída, uma mensagem será exibida com um botão **Navegar para o site**. Selecione esse botão para abrir o ponto de extremidade primário do código do aplicativo implantado.

    ![Mensagem de implantação concluída](media/static-website/deployment-complete.png)

    ![Site estático em execução no Azure](media/static-website/azure-app-react.png)

# <a name="vuetabvue"></a>[Vue](#tab/vue)

1. No Visual Studio Code, vá para o gerenciador do **Armazenamento do Azure**, expanda sua assinatura, expanda o nó da conta de Armazenamento do Azure criada na etapa anterior e, em seguida, expanda o nó **Contêineres de Blob**. É no contêiner `$web` que você implanta o código do aplicativo.

   ![Nós de Armazenamento do Azure no gerenciador de Armazenamento do Azure](media/static-website/storage-nodes.png)

1. Selecione o gerenciador de **Arquivos**, clique com o botão direito do mouse na pasta _dist_ e escolha **Implantar no Site Estático**:

    ![Comando Implantar para o Site Estático](media/static-website/deploy-build-vue.png)

1. Quando solicitado, escolha a conta de Armazenamento que você criou anteriormente.

1. Quando a implantação for concluída, uma mensagem será exibida com um botão **Navegar para o site**. Selecione esse botão para abrir o ponto de extremidade primário do código do aplicativo implantado.

    ![Mensagem de implantação concluída](media/static-website/deployment-complete.png)

    ![Site estático em execução no Azure](media/static-website/azure-app-vue.png)

---

> [!div class="nextstepaction"]
> [Meu site está no Azure](tutorial-vscode-static-website-node-05.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=create-storage)
