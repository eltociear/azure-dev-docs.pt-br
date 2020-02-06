---
ms.openlocfilehash: 8f757c030849cb89eea36d74b55867dcc2ff98a6
ms.sourcegitcommit: 6fa28ea675ae17ffb9ac825415e2e26a3dfe7107
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2020
ms.locfileid: "77013898"
---
Você pode excluir o grupo de recursos usando o [portal do Azure](https://portal.azure.com) ou a CLI do Azure:

- No portal, selecione **Grupos de recursos** no painel de navegação esquerdo, selecione o grupo de recursos criado no processo deste tutorial e, em seguida, use o comando **Excluir grupo de recursos**.

- Execute o seguinte comando da CLI do Azure (localmente ou usando o [Cloud Shell](/azure/cloud-shell/overview)), substituindo `<resource_group>` pelo nome do grupo usado neste tutorial:

    ```azurecli
    az group delete --name <resource_group>
    ```
