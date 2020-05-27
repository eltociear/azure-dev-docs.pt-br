---
title: Gerenciar entidades de serviço locais para o desenvolvimento do Azure
description: Como gerenciar entidades de serviço criadas para o desenvolvimento local usando o portal do Azure ou a CLI do Azure.
ms.date: 05/12/2020
ms.topic: conceptual
ms.openlocfilehash: b2e0c913b08c98994226d7a9de39ae83ae50ec21
ms.sourcegitcommit: 2cdf597e5368a870b0c51b598add91c129f4e0e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/14/2020
ms.locfileid: "83404879"
---
# <a name="how-to-manage-service-principals"></a>Como gerenciar entidades de serviço

Para fins de segurança, é sempre bom gerenciar cuidadosamente como o código do aplicativo está autorizado a acessar e modificar os recursos do Azure. Ao testar o código localmente, você sempre deve usar uma *entidade de serviço* local em vez de executar como um usuário totalmente privilegiado, conforme descrito em [Configurar seu ambiente de desenvolvimento Python local - Autenticação](configure-local-development-environment.md#configure-authentication).

Ao longo do tempo, provavelmente será necessário excluir, renomear ou gerenciar de outra maneira essas entidades de serviço e, para isso, você pode usar o portal do Azure ou a CLI do Azure.

## <a name="basics-of-azure-authorization"></a>Noções básicas da autorização do Azure

Sempre que seu código tenta executar alguma operação nos recursos do Azure (que você faz por meio de classes no SDK do Azure), o Azure garante que o aplicativo esteja autorizado a executar essa ação. Você usa o [portal do Azure](https://portal.azure.com) ou a CLI do Azure para conceder permissões específicas com base em função ou recursos à identidade do aplicativo. (Esse procedimento evita a concessão de permissões excessivas ao aplicativo que poderiam ser exploradas se a segurança do seu aplicativo for comprometida.)

Quando implantado no Azure, a identidade do aplicativo normalmente é igual ao nome que você dá ao aplicativo no serviço que o hospeda (como Serviço de Aplicativo do Azure, Azure Functions, uma máquina virtual, etc., quando a identidade gerenciada está habilitada). Porém, quando o código é executado localmente, esse serviço de hospedagem não é envolvido, portanto, você precisa apresentar um substituto adequado ao Azure.

Para essa finalidade, você usa uma *entidade de serviço* local, que é outro nome para uma identidade de aplicativo, em oposição a uma identidade de usuário. A entidade de serviço tem um nome, um identificador de "locatário" (essencialmente uma ID para sua organização), um identificador de aplicativo ou "cliente" e um segredo/senha. Essas credenciais são suficientes para autenticar a identidade com o Azure, que pode então verificar se essa identidade está autorizada a acessar qualquer recurso específico.

Cada desenvolvedor deve ter sua própria entidade de serviço protegida em sua conta de usuário na sua estação de trabalho. Ela nunca deve ser armazenada em um repositório de controle do código-fonte. Se uma entidade de serviço for roubada ou comprometida, você poderá excluí-la facilmente no portal do Azure para revogar todas as suas permissões e depois recriar a entidade de serviço para esse desenvolvedor.

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

- [Autenticar com o Azure usando o SDK do Azure](azure-sdk-authenticate.md)
