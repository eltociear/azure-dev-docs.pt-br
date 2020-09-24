---
ms.openlocfilehash: 4215099ae39963448b7a94d389ded0c9096b1c67
ms.sourcegitcommit: 69933dcce571b2686897b295b7822e207d944617
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2020
ms.locfileid: "90772669"
---
Você pode excluir o grupo de recursos usando o [portal do Azure](https://portal.azure.com) ou a CLI do Azure:

- No portal, selecione **Grupos de recursos** no painel de navegação esquerdo, selecione o grupo de recursos criado no processo deste tutorial e, em seguida, use o comando **Excluir grupo de recursos**.

- Execute o seguinte comando da CLI do Azure (localmente ou usando o [Cloud Shell](/azure/cloud-shell/overview)), substituindo `<resource_group>` pelo nome do grupo usado neste tutorial:

    ```azurecli
    az group delete --no-wait --name <resource_group>
    ```
