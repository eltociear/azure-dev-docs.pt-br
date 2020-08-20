---
title: Implantar aplicativos Node.js para o Serviço de Aplicativo do Azure usando a CLI do Azure
description: Parte 1 do tutorial, introdução e pré-requisitos.
ms.topic: conceptual
ms.date: 09/24/2019
ms.custom: devx-track-javascript
ms.openlocfilehash: 51fb29a80ba69dd59ca18dd94eaaeb6f09e53074
ms.sourcegitcommit: 0699b984b85782b1c441289fa756f285eae853c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88218070"
---
# <a name="deploy-to-azure-app-service-using-the-azure-cli"></a>Implantar no Serviço de Aplicativo do Azure usando a CLI do Azure

Neste tutorial, você implanta um aplicativo Node.js para o Serviço de Aplicativo do Azure usando a [CLI (interface de linha de comando) do Azure](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest), que é executada em todos os sistemas operacionais. Com a CLI, você pode criar recursos do Azure, configurar um pipeline de implantação entre um repositório Git e o Azure e exibir a saída `console.log` do aplicativo.

## <a name="prerequisites"></a>Prerequisites

- Uma [assinatura do Azure](#azure-subscription).
- [Node.js e npm 6.x ou superior](https://nodejs.org/en/download), o gerenciador de pacotes do Node.js.
- [Git](https://git-scm.com/downloads), após o qual o comando `git --version` deve mostrar um número de versão.
- O [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

Como alternativa, você pode usar a [extensão da CLI do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli), que fornece colorização de sintaxe, IntelliSense (conclusões) e snippets ao escrever scripts da CLI do Azure.

Uma segunda alternativa é o [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), que pode ser usado no Visual Studio Code com a [extensão de Conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

### <a name="azure-subscription"></a>Assinatura do Azure

Se você ainda não tiver uma assinatura do Azure, [inscreva-se agora](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-node-git&mktingSource=vscode-tutorial-node-git) para obter uma conta gratuita com US$ 200 em créditos Azure a fim de experimentar qualquer combinação de serviços.

### <a name="sign-in-to-the-azure-cli"></a>Fazer logon na CLI do Azure

Depois que a CLI do Azure for instalada, execute o seguinte comando em um terminal ou prompt de comando:

```azurecli
az login
```

O comando abre uma janela do navegador na qual você será solicitado a fazer logon no Azure. Quando você estiver conectado, a janela do terminal mostrará a saída JSON com os detalhes de sua assinatura.

## <a name="check-npm-version"></a>Verificar a versão do npm

Se você já tiver o Node.js instalado, execute o comando `node -v` e verifique se a versão é 10.x ou superior. Se você tiver uma versão mais antiga, [atualize](https://nodejs.org/en/download/) para a versão mais recente de LTS ("suporte de longo prazo").

> [!div class="nextstepaction"]
> [Instalei os pré-requisitos](tutorial-vscode-azure-cli-node-02.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=getting-started)
