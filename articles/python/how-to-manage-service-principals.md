---
title: Gerenciar entidades de serviço locais para o desenvolvimento do Azure
description: Como gerenciar entidades de serviço criadas para o desenvolvimento local usando o portal do Azure ou a CLI do Azure.
ms.date: 08/18/2020
ms.topic: conceptual
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: b6d3ffbb7e78b7c4f2405e5363446c1906913aa9
ms.sourcegitcommit: 800c5e05ad3c0b899295d381964dd3d47436ff90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88614512"
---
# <a name="how-to-manage-service-principals"></a>Como gerenciar entidades de serviço

Conforme descrito em [Como autenticar um aplicativo](azure-sdk-authenticate.md), geralmente, as entidades de serviço são usadas para identificar um aplicativo com o Azure, exceto ao usar a identidade gerenciada.

Ao longo do tempo, provavelmente, você precisará excluir, renomear ou, de outro modo, gerenciar essas entidades de serviço e, para isso, você poderá usar o portal do Azure ou a CLI do Azure.

## <a name="manage-service-principals-using-the-azure-portal"></a>Gerenciar entidades de serviço usando o portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com).

1. Para navegar até a página do **Azure Active Directory**, use o ícone na página inicial do portal ou procure por "Azure Active Directory" na barra de pesquisa do portal.

    ![Procurar Azure Active Directory na portal do Azure](media/how-to-manage-service-principals/azure-ad-portal-search.png)

1. Selecione **Gerenciar** > **Registros de aplicativo** no menu de navegação à esquerda. Suas entidades de serviço de desenvolvimento local aparecem na lista:

    ![Registros de aplicativo no Azure Active Directory](media/how-to-manage-service-principals/azure-ad-app-registrations.png)

1. Selecione uma das entidades de serviço para navegar até sua página de propriedades, onde você pode examinar os valores de ID, renomear ou excluir a entidade de serviço e obter várias URLs de ponto de extremidade.

1. O processo para autorizar o acesso de uma entidade de serviço a um recurso específico normalmente depende do serviço em questão. Para obter mais informações, consulte a documentação desse serviço. Por exemplo, os artigos [Autorização para armazenamento de blob](/azure/storage/common/storage-auth-aad-rbac-portal) e [Autorização para armazenamento de filas](/azure/storage/common/storage-auth-aad-rbac-portal) descrevem o processo em parte do Armazenamento do Microsoft Azure.

## <a name="manage-service-principals-using-the-azure-cli"></a>Gerenciar entidades de serviço usando a CLI do Azure

Com a CLI do Azure, você pode executar muitas das operações em entidades de serviço que podem ser executadas por meio do Portal do Azure:

- Criar, exibir, atualizar e excluir entidades de serviço: comando [az ad sp](/cli/azure/ad/sp?view=azure-cli-latest). Veja também [Criar uma entidade de serviço do Azure com a CLI do Azure](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).
- Gerenciar atribuições de função: comando [az role assignment](/cli/azure/role/assignment?view=azure-cli-latest).

Consulte também:

- [Autenticar com o Azure usando as bibliotecas do Azure](azure-sdk-authenticate.md)
