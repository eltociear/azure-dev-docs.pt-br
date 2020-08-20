---
title: 'Tutorial: Implantar aplicativos Python no Serviço de Aplicativo do Azure no Linux usando o Visual Studio Code'
description: 'Tutorial, etapa 1: introdução, pré-requisitos e entrada no Azure.'
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: 255e94d863f0c144ff4de402a2d775a21645b95a
ms.sourcegitcommit: 815cf2acff71e849735f7afce54723f03ffa5df3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88501371"
---
# <a name="tutorial-deploy-python-apps-to-azure-app-service-on-linux-from-visual-studio-code"></a>Tutorial: Implantar aplicativos Python no Serviço de Aplicativo do Azure no Linux usando o Visual Studio Code

Este artigo explica passo-a-passo como usar o Visual Studio Code para implantar um aplicativo Python no Serviço de Aplicativo do Azure no Linux usando a extensão do [Serviço de Aplicativo do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice).

Se você encontrar problemas com alguma das etapas deste tutorial, adoraríamos ouvir os detalhes. Use o link **Encontrei um problema** no final de cada artigo para enviar comentários.

Para assistir a um vídeo de demonstração, confira <a href="https://www.youtube.com/watch?v=dNVvFttc-sA&feature=youtu.be&ocid=AID3006292" target="_blank">Criar WebApps com o VS Code e o Serviço de Aplicativo do Azure</a> (youtube.com) do PyCon 2020 virtual.

> [!TIP]
> O [Serviço de Aplicativo do Azure no Linux](/azure/app-service/overview#app-service-on-linux) executa o código-fonte em um contêiner predefinido do Docker. Esse contêiner executa aplicativos com o Python 3.7, usando o servidor Web [Gunicorn](https://gunicorn.org). As características desse contêiner são descritas em [Configurar aplicativos Python para o Serviço de Aplicativo no Linux](/azure/app-service/configure-language-python). A própria definição de contêiner está no [github.com/Azure-App-Service/python](https://github.com/Azure-App-Service/python/tree/master/3.7).

## <a name="prerequisites"></a>Pré-requisitos

- Uma [assinatura do Azure](#azure-subscription).
- [Visual Studio Code com a extensão do Serviço de Aplicativo do Azure](#visual-studio-code-python-and-the-azure-app-service-extension).
- Um ambiente do Python

### <a name="azure-subscription"></a>Assinatura do Azure

Se você ainda não tiver uma assinatura do Azure, [inscreva-se agora](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-appservice-extension&mktingSource=vscode-tutorial-appservice-extension) para obter uma conta gratuita com US$ 200 em créditos Azure a fim de experimentar qualquer combinação de serviços.

### <a name="visual-studio-code-python-and-the-azure-app-service-extension"></a>Visual Studio Code, Python e a extensão do Serviço de Aplicativo do Azure

Instale o seguinte software:

- [Visual Studio Code](https://code.visualstudio.com/).
- O Python e a extensão do [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python), conforme descrito em [Tutorial de Python no VS Code – Pré-requisitos](https://code.visualstudio.com/docs/python/python-tutorial).
- A extensão do [Serviço de Aplicativo do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice), que fornece interação com o Serviço de Aplicativo do Azure de dentro do VS Code. Para obter informações gerais, explore o [Tutorial da extensão do Serviço de Aplicativo](https://code.visualstudio.com/tutorials/app-service-extension/getting-started) e visite o [repositório do GitHub vscode-azureappservice](https://github.com/Microsoft/vscode-azureappservice).

## <a name="sign-in-to-azure"></a>Entrar no Azure

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

> [!div class="nextstepaction"]
> [Entrei no Azure – prossiga para a etapa 2 >>>](tutorial-deploy-app-service-on-linux-02.md)

[Está com problemas? Fale conosco.](https://aka.ms/FlaskVSCQuickstartHelp)
