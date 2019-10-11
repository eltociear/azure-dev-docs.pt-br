---
title: 'Tutorial: Implantar aplicativos Python no Serviço de Aplicativo do Azure no Linux usando o Visual Studio Code'
description: 'Tutorial, etapa 1: introdução, pré-requisitos e entrada no Azure.'
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: kraigb
ms.custom: seo-python-october2019
ms.openlocfilehash: 24f615f5f456276b1ed78fc431e3cdd929e2d1cd
ms.sourcegitcommit: bed07b313eeab51281d1a6d4eba67a75524b2f57
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72172519"
---
# <a name="tutorial-deploy-python-apps-to-azure-app-service-on-linux-from-visual-studio-code"></a>Tutorial: Implantar aplicativos Python no Serviço de Aplicativo do Azure no Linux usando o Visual Studio Code

Este artigo explica passo-a-passo como usar o Visual Studio Code para implantar um aplicativo Python no Serviço de Aplicativo do Azure no Linux usando a extensão do [Serviço de Aplicativo do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice).

Se você encontrar problemas com alguma das etapas deste tutorial, adoraríamos ouvir os detalhes. Use o link **Encontrei um problema** no final de cada artigo para enviar comentários.

> [!TIP]
> O [Serviço de Aplicativo do Azure no Linux](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro), atualmente em versão prévia para Python, executa o código-fonte em um contêiner predefinido do Docker. Esse contêiner executa aplicativos com o Python 3.7, usando o servidor Web [Gunicorn](https://gunicorn.org). As características desse contêiner são descritas em [Configurar aplicativos Python para o Serviço de Aplicativo no Linux](https://docs.microsoft.com/azure/app-service/containers/how-to-configure-python). A própria definição de contêiner está no [github.com/Azure-App-Service/python](https://github.com/Azure-App-Service/python/tree/master/3.7).

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
- A extensão do [Serviço de Aplicativo do Azure](vscode:extension/ms-azuretools.vscode-azureappservice), que fornece interação com o Serviço de Aplicativo do Azure de dentro do VS Code. Para obter informações gerais, explore o [Tutorial da extensão do Serviço de Aplicativo](https://code.visualstudio.com/tutorials/app-service-extension/getting-started) e visite o [repositório do GitHub vscode-azureappservice](https://github.com/Microsoft/vscode-azureappservice).

## <a name="sign-in-to-azure"></a>Entrar no Azure

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

> [!div class="nextstepaction"]
> [Entrei no Azure](tutorial-deploy-app-service-on-linux-02.md)

[Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-python&step=01-verify-prerequisites)
