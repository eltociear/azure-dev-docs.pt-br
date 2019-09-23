---
ms.openlocfilehash: d64a5c908915b5d020f27e1c501aee852f04ae38
ms.sourcegitcommit: 74e28a479c87a3a53592646420b78e69852dd86a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/16/2019
ms.locfileid: "71019654"
---
Depois de instalar a extensão do Azure, entre na sua conta do Azure navegando até o gerenciador do **Azure: Serviço de Aplicativo**, selecione **Entrar no Azure** e siga os prompts.

![Entrar no Azure por meio do VS Code](../media/deploy-azure/azure-sign-in.png)

Depois de entrar, verifique se o endereço de email da sua conta do Azure aparece na Barra de Status e as assinaturas aparecem no gerenciador do **Azure: Serviço de Aplicativo**:

![Barra de Status do VS Code mostrando a conta do Azure](../media/deploy-azure/azure-account-status-bar.png)

![Gerenciador do Serviço de Aplicativo do Azure do VS Code mostrando assinaturas](../media/deploy-azure/azure-subscription-view.png)

> [!NOTE]
> Se você vir o erro **"Não é possível encontrar a assinatura com o nome [ID da assinatura]"** , talvez seja porque você está atrás de um proxy e não consegue acessar a API do Azure. Configure as variáveis de ambiente `HTTP_PROXY` e `HTTPS_PROXY` com as informações de proxy no terminal:
>
> ```sh
> # macOS/Linux
> export HTTPS_PROXY=https://username:password@proxy:8080
> export HTTP_PROXY=http://username:password@proxy:8080
>
> # Windows
> set HTTPS_PROXY=https://username:password@proxy:8080
> set HTTP_PROXY=http://username:password@proxy:8080
> ```
