---
ms.openlocfilehash: 8f757c030849cb89eea36d74b55867dcc2ff98a6
ms.sourcegitcommit: 1bd9ec6a4115e9162e33b76a933869788e6ab702
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80441351"
---
Você pode excluir o grupo de recursos usando o [portal do Azure](https://portal.azure.com) ou a CLI do Azure:

- No portal, selecione **Grupos de recursos** no painel de navegação esquerdo, selecione o grupo de recursos criado no processo deste tutorial e, em seguida, use o comando **Excluir grupo de recursos**.

- Execute o seguinte comando da CLI do Azure (localmente ou usando o [Cloud Shell](/azure/cloud-shell/overview)), substituindo `<resource_group>` pelo nome do grupo usado neste tutorial:

    ```azurecli
    az group delete --name <resource_group>
    ```
