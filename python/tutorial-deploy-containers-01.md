---
title: Implantar contêineres do Docker no Serviço de Aplicativo do Azure com o Visual Studio Code
description: Etapa 1 do tutorial, introdução e pré-requisitos.
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: kraigb
ms.openlocfilehash: 0135761f8294b3fbbb8fe821540b46126c107109
ms.sourcegitcommit: d6575ac86449380b5a9c6c66aa722cb33ed53438
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71186184"
---
# <a name="deploy-containers-to-azure-app-service"></a>Implantar contêineres no Serviço de Aplicativo do Azure

Este tutorial orienta você ao longo do processo de usar o Visual Studio Code para implantar uma imagem de contêiner de um registro de contêiner no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/containers/), tudo isso no Visual Studio Code.

Se você encontrar problemas com alguma das etapas deste tutorial, adoraríamos ouvir os detalhes. Use o link **Encontrei um problema** no final de cada artigo para enviar comentários.

## <a name="prerequisites"></a>Pré-requisitos

- Uma [conta do Azure](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension)
- [Visual Studio Code](https://code.visualstudio.com/)
- Um contêiner adequado que foi carregado em um registro de contêiner. Por exemplo, detalhes de como criar um contêiner com um aplicativo Web Python e adicioná-lo a um registro podem ser encontrados em [Criar um contêiner](https://code.visualstudio.com/docs/python/tutorial-create-containers).
- A [extensão do Serviço de Aplicativo do Azure para VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice).
- A [extensão do Docker para VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker).

## <a name="sign-in-to-azure"></a>Entrar no Azure

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

> [!div class="nextstepaction"]
> [Entrei no Azure](tutorial-deploy-containers-02.md)

[Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-containers&step=01-verify-prerequisites)
