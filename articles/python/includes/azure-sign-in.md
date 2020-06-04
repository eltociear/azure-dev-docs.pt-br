---
ms.openlocfilehash: cbcc292bf0b59adf35789bbd24ca37b0301a2ad7
ms.sourcegitcommit: efab6be74671ea4300162e0b30aa8ac134d3b0a9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84268801"
---
Depois de instalar a extensão do Azure, entre na sua conta do Azure navegando até o gerenciador do **Azure**, selecione **Entrar no Azure** e siga os prompts. (Se você tiver várias extensões do Azure instaladas, selecione uma para a área na qual você está trabalhando, como Serviço de Aplicativo, Functions etc.)

![Entrar no Azure por meio do VS Code](../media/deploy-azure/sign-in-to-azure-through-visual-studio-code.png)

Depois de entrar, verifique se o endereço de email da sua conta do Azure (ou "Conectado") aparece na Barra de Status e suas assinaturas aparecem no gerenciador do **Azure**:

![Barra de status do Visual Studio Code mostrando a conta do Azure](../media/deploy-azure/azure-account-status-bar-in-visual-studio-code.png)

![Gerenciador do Serviço de Aplicativo do Azure do Visual Studio Code mostrando assinaturas](../media/deploy-azure/view-azure-subscription-in-visual-studio-code-app-service-explorer.png)

> [!NOTE]
> Se você vir o erro **"Não é possível encontrar a assinatura com o nome [ID da assinatura]"** , talvez seja porque você está atrás de um proxy e não consegue acessar a API do Azure. Configure as variáveis de ambiente `HTTP_PROXY` e `HTTPS_PROXY` com as informações de proxy no terminal:
>
> ```cmd
> # Windows
> set HTTPS_PROXY=https://username:password@proxy:8080
> set HTTP_PROXY=http://username:password@proxy:8080
> ```
>
> ```bash
> # macOS/Linux
> export HTTPS_PROXY=https://username:password@proxy:8080
> export HTTP_PROXY=http://username:password@proxy:8080
> ```
