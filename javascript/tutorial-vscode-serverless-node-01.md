---
title: Implantar o Azure Functions no Node.js usando o Visual Studio Code
description: Parte 1 do tutorial, introdução e pré-requisitos.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 915ab0cb07fe6f4cf74d812c022d5e0e9d266177
ms.sourcegitcommit: 1bd9ec6a4115e9162e33b76a933869788e6ab702
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80439161"
---
# <a name="deploy-azure-functions-from-visual-studio-code"></a>Implantar o Azure Functions usando o Visual Studio Code

Neste tutorial, você usa Visual Studio Code e a extensão Azure Functions para criar e implantar um aplicativo Azure Functions escrito com JavaScript.

## <a name="walkthrough-video"></a>Vídeo explicativo

Assista a este vídeo para ter uma explicação completa do conteúdo neste artigo.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Deploy-Azure-Functions-from-Visual-Studio-Code/player]

## <a name="prerequisites"></a>Pré-requisitos

- Uma [assinatura do Azure](#azure-subscription).
- [Visual Studio Code](https://code.visualstudio.com/).
- A [extensão do Azure Functions](vscode:extension/ms-azuretools.vscode-azurefunctions)
- [Node.js e npm](https://nodejs.org/en/download), o gerenciador de pacotes do Node.js.

> <a class="tutorial-install-extension-btn" href="vscode:extension/ms-azuretools.vscode-azurefunctions">Instalar a extensão do Azure Functions</a>

### <a name="azure-subscription"></a>Assinatura do Azure

Se você ainda não tiver uma assinatura do Azure, [inscreva-se agora](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-functions-extension&mktingSource=vscode-tutorial-functions-extension) para obter uma conta gratuita com US$ 200 em créditos Azure a fim de experimentar qualquer combinação de serviços.

## <a name="sign-in-to-azure"></a>Entrar no Azure

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

## <a name="install-the-azure-functions-core-tools"></a>Instalação das ferramentas básicas do Azure Functions

Para habilitar a depuração local, você precisa instalar o [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools), o que pode ser feito diretamente no Visual Studio Code.

1. Inicie o Visual Studio Code.

1. Abra a **Paleta de Comandos** (**F1**), insira **Azure Functions: Instalar ou Atualizar o Azure Functions Core Tools** e pressione **Enter** para executar o comando.

1. Para verificar a instalação, selecione o comando de menu **Terminal** > **Novo Terminal** no VS Code e execute o comando `func`. O comando deve mostrar uma saída semelhante à abaixo (juntamente com as informações de uso).

    <pre>
                      %%%%%%
                     %%%%%%
                @   %%%%%%    @
              @@   %%%%%%      @@
           @@@    %%%%%%%%%%%    @@@
         @@      %%%%%%%%%%        @@
           @@         %%%%       @@
             @@      %%%       @@
               @@    %%      @@
                    %%
                    %

    Azure Functions Core Tools (2.4.419 Commit hash: c9c1724d002bd90b2e6b41393915ea3a26bcf0ce)
    Function Runtime Version: 2.0.12332.0
    </pre>

> [!div class="nextstepaction"]
> [Instalei os pré-requisitos](tutorial-vscode-serverless-node-02.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azurefunctions&step=getting-started)
