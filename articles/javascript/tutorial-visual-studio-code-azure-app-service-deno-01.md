---
title: Implantar aplicativos Deno para o Serviço de Aplicativo do Azure na CLI do Azure
description: Parte 1 do tutorial, introdução e pré-requisitos.
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: 817efd11f5eaf072d10a54efa083823e9b73e356
ms.sourcegitcommit: 553da4e9aa988e5bb823364244ea81961cee5bc7
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85791165"
---
# <a name="deploy-to-azure-app-service-using-visual-studio-code"></a>Implantar no Serviço de Aplicativo do Azure usando o Visual Studio Code

Neste tutorial, você implantará um aplicativo Deno no Serviço de Aplicativo do Azure (no Linux ou Windows) usando a CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie um gratuitamente](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-appservice-deno&mktingSource=vscode-tutorial-appservice-deno)
- [Visual Studio Code](https://code.visualstudio.com/)
- [Deno](https://deno.land/#installation)
- Ter a CLI do Azure instalada e conectada

## <a name="install-or-run-in-azure-cloud-shell"></a>Instalar ou executar no Azure Cloud Shell

A maneira mais fácil começar a usar a CLI do Azure é executando-a em um ambiente do Azure Cloud Shell por meio do seu navegador. Para saber mais sobre o Cloud Shell, consulte [Início Rápido para Bash no Azure Cloud Shell](/azure/cloud-shell/quickstart).

Quando você estiver pronto para instalar a CLI, confira as [instruções de instalação](/cli/azure/install-azure-cli).

Depois de instalar a CLI pela primeira vez, verifique se ela está instalada e se você tem a versão correta, executando `az --version`.

> [!NOTE]
> Se você estiver usando o modelo de implantação clássico do Azure, [instale a CLI clássica do Azure](/cli/azure/install-classic-cli).

## <a name="sign-in"></a>Entrar

Antes de usar os comandos da CLI com uma instalação local, é preciso entrar com [az login](/cli/azure/reference-index#az-login).

[!INCLUDE [interactive-login](../azure-cli/includes/interactive-login.md)]

Depois de entrar, você deve ver uma lista de assinaturas associadas à sua conta do Azure. A informação de assinatura com `isDefault: true` é a assinatura ativada no momento depois de entrar. Para selecionar outra assinatura, use o comando [az account set](/cli/azure/account#az-account-set) com a ID da assinatura para a qual alternar. Para obter mais informações sobre a seleção da assinatura, confira [Usar várias assinaturas do Azure](/cli/azure/manage-azure-subscriptions-azure-cli).

> [!div class="nextstepaction"]
> [Eu instalei e fiz logon na CLI do Azure](tutorial-visual-studio-code-azure-app-service-deno-02.md) [Eu encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=deno-deployment-azureappservice&step=getting-started)

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [tutorial-next-steps](includes/tutorial-next-steps.md)]

> [!div class="nextstepaction"]
> [Terminei](node-howto-deploy-web-app.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=deno-deployment-azureappservice&step=clean-up-resources)
