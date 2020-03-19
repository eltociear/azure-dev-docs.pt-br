---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: bda7ba230d8287101fc8f3f298b08777bb6e1c61
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897463"
---
### <a name="determine-whether-ejb-timers-are-in-use"></a>Determinar se temporizadores EJB estão sendo usados

Se seu aplicativo usar temporizadores EJB, você precisará validar que o código do temporizador EJB pode ser disparado pela instância do WildFly de maneira independente. Essa validação é necessária porque, no cenário de implantação do Serviço de Kubernetes do Azure, cada temporizador EJB será disparado em sua própria instância do WildFly.
