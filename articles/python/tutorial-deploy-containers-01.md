---
title: 'Tutorial: Implantar contêineres do Docker no Serviço de Aplicativo do Azure com o Visual Studio Code'
description: Etapa 1 do tutorial, introdução e pré-requisitos.
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: seo-python-october2019
ms.openlocfilehash: 1e86b06b87ed2a0471f38102d1ad5bca8d0dfcb1
ms.sourcegitcommit: 7474de4884bce076ce33ca77ae3584ba1598bbc6
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2020
ms.locfileid: "85069403"
---
# <a name="tutorial-deploy-docker-containers-to-azure-app-service-with-visual-studio-code"></a>Tutorial: Implantar contêineres do Docker no Serviço de Aplicativo do Azure com o Visual Studio Code

Este artigo orienta você ao longo do processo de usar o Visual Studio Code para implantar uma imagem de contêiner de um registro de contêiner no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/containers/), tudo isso no Visual Studio Code.

Se você encontrar problemas com alguma das etapas deste tutorial, adoraríamos ouvir os detalhes. Use o link **Encontrei um problema** no final de cada artigo para enviar comentários.

Para assistir a um vídeo de demonstração relacionado, confira <a href="https://www.youtube.com/watch?v=t79HDLC5kQA&feature=youtu.be&ocid=AID3006292" target="_blank">Aplicativos Django em contêineres de desenvolvimento do VS Code</a> (youtube.com) do PyCon 2020 virtual.

## <a name="prerequisites"></a>Pré-requisitos

- Uma [conta do Azure](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension)
- [Visual Studio Code](https://code.visualstudio.com/)
- Um contêiner adequado que foi carregado em um registro de contêiner. Os detalhes sobre como criar um contêiner com um aplicativo Web Python podem ser encontrados em [Python em contêineres](https://code.visualstudio.com/docs/containers/quickstart-python).
- A [extensão do Serviço de Aplicativo do Azure para VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice).
- A [extensão do Docker para VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker).

## <a name="sign-in-to-azure"></a>Entrar no Azure

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

> [!div class="nextstepaction"]
> [Entrei no Azure – prossiga para a etapa 2 >>>](tutorial-deploy-containers-02.md)

[Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-containers&step=01-verify-prerequisites)
