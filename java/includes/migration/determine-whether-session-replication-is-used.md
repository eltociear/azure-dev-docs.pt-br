---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 26d0422b9ea569b5657a5b7841319e77ce0f1684
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78894149"
---
### <a name="determine-whether-session-replication-is-used"></a>Determinar se a replicação de sessão é usada

Se seu aplicativo depender da replicação de sessão, com ou sem o Oracle Coherence*Web, você terá três opções:

* O Coherence*Web pode ser executado junto com um WebLogic Server nas máquinas virtuais do Azure, mas você deve configurar manualmente essa opção depois de provisionar a oferta. Se você estiver usando o Coherence de forma independente, também poderá executá-lo em uma máquina virtual do Azure, mas deverá configurar manualmente essa opção depois de provisionar a oferta.
* Refatore seu aplicativo para usar um banco de dados para gerenciamento de sessão.
* Refatore seu aplicativo para externalizar a sessão para o serviço Redis do Azure. Para obter mais informações, consulte [Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview).

Para todas essas opções, é uma boa ideia saber como o WebLogic faz a replicação de estado de sessão HTTP. Para obter mais informações, consulte [Replicação de estado de sessão HTTP](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/clust/failover.html#GUID-E13D8142-66BA-46A1-854F-4FC6F82992DD) na documentação da Oracle.
