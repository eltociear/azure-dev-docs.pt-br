---
author: mnriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: 6f35e42736d538c8bb755a84cd0b082052645b6e
ms.sourcegitcommit: 850856d3fa2ddd8f96616ee6a1f092d8e0aedab3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90738440"
---
### <a name="determine-whether-jaas-is-in-use"></a>Determinar se o JAAS está sendo usado

Se seu aplicativo estiver usando o JAAS, você precisará capturar como o JAAS está configurado. Se estiver usando um banco de dados, você poderá convertê-lo em um domínio JAAS no WildFly. Caso se trate de uma implementação personalizada, você precisará validar que ela pode ser usada no WildFly.
