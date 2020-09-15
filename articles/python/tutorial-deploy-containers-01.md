---
title: 'Tutorial: Implantar contêineres do Docker no Serviço de Aplicativo do Azure com o Visual Studio Code'
description: Etapa 1 do tutorial, trabalhar com contêineres, introdução e pré-requisitos.
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: f0fb983a596ca1828809d1d829af5517e8af66df
ms.sourcegitcommit: 9e282fc2ec967bee181c3034e7e70b28ae308905
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89473551"
---
# <a name="tutorial-deploy-docker-containers-to-azure-app-service-with-visual-studio-code"></a>Tutorial: Implantar contêineres do Docker no Serviço de Aplicativo do Azure com o Visual Studio Code

Este artigo orienta você ao longo do processo de usar o Visual Studio Code para implantar uma imagem de contêiner de um registro de contêiner no [Serviço de Aplicativo do Azure](/azure/app-service/), tudo isso no Visual Studio Code.

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

Algum problema? Envie um problema do GitHub usando a seção de comentários "Esta página" na parte inferior da página.
