---
title: Implantar um site do Node.js estático no Azure usando o Visual Studio Code
description: Parte 1 do tutorial, introdução e pré-requisitos.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: c63ff76f3572a7815b385b08b05e223eb681fbbe
ms.sourcegitcommit: 553da4e9aa988e5bb823364244ea81961cee5bc7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85791252"
---
# <a name="deploy-a-static-website-to-azure-from-visual-studio-code"></a>Implantar um site estático no Azure usando o Visual Studio Code

Neste tutorial, você vai criar e implantar um site estático no Azure usando o [Armazenamento do Azure](https://docs.microsoft.com/azure/storage). Um site estático é composto por HTML, CSS, JavaScript e outros arquivos estáticos, como imagens ou fontes. Um site estático normalmente é um SPA (ou [aplicativo de página única](https://en.wikipedia.org/wiki/Single-page_application)) escrito com Angular, React ou Vue. No entanto, você cria o aplicativo, hospeda e serve esses arquivos diretamente do _armazenamento_, em vez de usando um servidor Web. Hospedar no armazenamento é mais simples e mais barato do que manter um servidor Web.

## <a name="walkthrough-video"></a>Vídeo explicativo

Assista a este vídeo para ter uma explicação completa do conteúdo neste artigo.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Deploy-static-website-to-Azure-from-Visual-Studio-Code/player]

> [!NOTE]
> Se você tiver seu próprio código de servidor, como um aplicativo Node.js/Expresso, siga o [tutorial do Serviço de Aplicativo](tutorial-vscode-azure-app-service-node-01.md), em vez disso.

## <a name="prerequisites"></a>Prerequisites

- Uma [assinatura do Azure](#azure-subscription).
- [Visual Studio Code](https://code.visualstudio.com/).
- A [extensão de Armazenamento do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage).
- [Node.js e npm](https://nodejs.org/en/download), o gerenciador de pacotes do Node.js. (Esse requisito é usado apenas para gerar um projeto de exemplo. Você não precisará instalar o Node.js se já tiver o código do aplicativo.)

> <a class="tutorial-install-extension-btn" href="https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage">Instalar a extensão do Armazenamento do Azure</a>

### <a name="azure-subscription"></a>Assinatura do Azure

Se você ainda não tiver uma assinatura do Azure, [inscreva-se agora](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-static-website&mktingSource=vscode-tutorial-static-website) para obter uma conta gratuita com US$ 200 em créditos Azure a fim de experimentar qualquer combinação de serviços.

## <a name="sign-in-to-azure"></a>Entrar no Azure

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

> [!div class="nextstepaction"]
> [Instalei os pré-requisitos](tutorial-vscode-static-website-node-02.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-staticwebsite&step=getting-started)
