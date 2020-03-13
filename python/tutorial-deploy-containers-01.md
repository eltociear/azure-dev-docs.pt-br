---
title: 'Tutorial: Implantar contêineres do Docker no Serviço de Aplicativo do Azure com o Visual Studio Code'
description: Etapa 1 do tutorial, introdução e pré-requisitos.
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: seo-python-october2019
ms.openlocfilehash: b01c45d1f61e021bb025dec4980a7a10eae6185b
ms.sourcegitcommit: 21ddeb9bd9abd419d143dc2ca8a7c821a1758cf9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79089969"
---
# <a name="tutorial-deploy-docker-containers-to-azure-app-service-with-visual-studio-code"></a>Tutorial: Implantar contêineres do Docker no Serviço de Aplicativo do Azure com o Visual Studio Code

Este artigo orienta você ao longo do processo de usar o Visual Studio Code para implantar uma imagem de contêiner de um registro de contêiner no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/containers/), tudo isso no Visual Studio Code.

Se você encontrar problemas com alguma das etapas deste tutorial, adoraríamos ouvir os detalhes. Use o link **Encontrei um problema** no final de cada artigo para enviar comentários.

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
