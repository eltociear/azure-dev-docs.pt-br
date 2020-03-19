---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: 99140db00e7a0a72b96db19b4dab9d7476694ab2
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897514"
---
### <a name="determine-whether-jaas-is-in-use"></a>Determinar se o JAAS está sendo usado

Se seu aplicativo estiver usando o JAAS, você precisará capturar como o JAAS está configurado. Se estiver usando um banco de dados, você poderá convertê-lo em um domínio JAAS no WildFly. Caso se trate de uma implementação personalizada, você precisará validar que ela pode ser usada no WildFly.
