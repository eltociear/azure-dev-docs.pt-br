---
ms.openlocfilehash: c9d46571c22c9bee49352dd4c1f492b2c9ff60a8
ms.sourcegitcommit: aa2c66b0fecce51862cc9115f68d39c770f0b2ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77709823"
---
Depois de instalar a extensão do Azure, entre na sua conta do Azure navegando até o gerenciador do **Azure**, selecione **Entrar no Azure** e siga os prompts. (Se você tiver várias extensões do Azure instaladas, selecione uma para a área na qual você está trabalhando, como Serviço de Aplicativo, Functions etc.)

![Entrar no Azure por meio do VS Code](../media/deploy-azure/azure-sign-in.png)

Depois de entrar, verifique se o endereço de email da sua conta do Azure (ou "Conectado") aparece na Barra de Status e suas assinaturas aparecem no gerenciador do **Azure**:

![Barra de Status do VS Code mostrando a conta do Azure](../media/deploy-azure/azure-account-status-bar.png)

![Gerenciador do Azure do VS Code mostrando assinaturas](../media/deploy-azure/azure-subscription-view.png)

> [!NOTE]
> Se você vir o erro **"Não é possível encontrar a assinatura com o nome [ID da assinatura]"** , talvez seja porque você está atrás de um proxy e não consegue acessar a API do Azure. Configure as variáveis de ambiente `HTTP_PROXY` e `HTTPS_PROXY` com as informações de proxy no terminal:
>
> ```bash
> # MacOS/Linux
> export HTTPS_PROXY=https://username:password@proxy:8080
> export HTTP_PROXY=http://username:password@proxy:8080
> ```
>
> ```cmd
> # Windows
> set HTTPS_PROXY=https://username:password@proxy:8080
> set HTTP_PROXY=http://username:password@proxy:8080
> ```
