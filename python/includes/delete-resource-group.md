---
ms.openlocfilehash: 2f54e918e7126123ada68b696ea96f4d5f3dbb83
ms.sourcegitcommit: a8073315f751631ab983618fa9f812eb95d8b2dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76125229"
---
Você pode excluir o grupo de recursos usando o [portal do Azure](https://portal.azure.com) ou a CLI do Azure:

- No portal, selecione **Grupos de recursos** no painel de navegação esquerdo, selecione o grupo de recursos criado no processo deste tutorial e, em seguida, use o comando **Excluir grupo de recursos**.

- Execute o seguinte comando da CLI do Azure (localmente ou usando o [Cloud Shell](/cloud-shell/overview)), substituindo `<resource_group>` pelo nome do grupo usado neste tutorial:

    ```azurecli
    az group delete --name <resource_group>
    ```
