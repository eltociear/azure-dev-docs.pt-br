---
ms.custom: devx-track-javascript
ms.openlocfilehash: 45c027d261a091e6c5e2abaa1ea1d4f55f5ad23f
ms.sourcegitcommit: 0699b984b85782b1c441289fa756f285eae853c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2020
ms.locfileid: "90773079"
---
Depois de instalar a extensão do Azure no VS Code, entre em sua conta do Azure navegando até o gerenciador do **Azure**, selecione **Entrar no Azure** e siga os prompts. (Se você tiver várias extensões do Azure instaladas, selecione uma para a área na qual você está trabalhando, como Serviço de Aplicativo, Functions etc.)

![Entrar no Azure por meio do VS Code](../media/deploy-azure/azure-sign-in.png)

Depois de entrar, verifique se o endereço de email da sua conta do Azure (ou "Conectado") aparece na Barra de Status e suas assinaturas aparecem no gerenciador do **Azure**:

![Barra de Status do VS Code mostrando a conta do Azure](../media/deploy-azure/azure-account-status-bar.png)

![Gerenciador do Azure do VS Code mostrando assinaturas](../media/deploy-azure/azure-subscription-view.png)

> [!NOTE]
> Se você vir o erro **"Não é possível encontrar a assinatura com o nome [ID da assinatura]"** , talvez seja porque você está atrás de um proxy e não consegue acessar a API do Azure. Configure as variáveis de ambiente `HTTP_PROXY` e `HTTPS_PROXY` com as informações de proxy no terminal:
>
> # <a name="bash"></a>[Bash](#tab/bash)
>
> ```bash
> export HTTPS_PROXY=https://username:password@proxy:8080
> export HTTP_PROXY=http://username:password@proxy:8080
> ```
>
> # <a name="powershell"></a>[PowerShell](#tab/powershell)
>
> ```powershell
> $env: HTTPS_PROXY = "https://username:password@proxy:8080"
> $env: HTTP_PROXY = "http://username:password@proxy:8080"
> ```
>
> # <a name="cmd"></a>[Cmd](#tab/cmd)
>
> ```cmd
> set HTTPS_PROXY=https://username:password@proxy:8080
> set HTTP_PROXY=http://username:password@proxy:8080
> ```
>
> ---
