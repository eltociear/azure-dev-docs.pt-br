---
ms.openlocfilehash: 5b817be30b4835525ad2e25b5e3975fba1217f01
ms.sourcegitcommit: 945e92dae2fa4521eebdc049c65273ae6b5470ee
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71813787"
---
Depois de instalar a extensão do Azure, entre na sua conta do Azure navegando até o gerenciador do **Azure**, selecione **Entrar no Azure** e siga os prompts. (Se você tiver várias extensões do Azure instaladas, selecione uma para a área na qual você está trabalhando, como Serviço de Aplicativo, Functions etc.)

![Entrar no Azure por meio do VS Code](../media/deploy-azure/azure-sign-in.png)

Depois de entrar, verifique se o endereço de email da sua conta do Azure (ou "Conectado") aparece na Barra de Status e suas assinaturas aparecem no gerenciador do **Azure**:

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
