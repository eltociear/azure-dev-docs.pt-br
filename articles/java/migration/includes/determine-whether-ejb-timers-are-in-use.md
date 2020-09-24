---
author: mnriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: c77a5ae60807cf25415ab86dab9d9891abab13f9
ms.sourcegitcommit: 850856d3fa2ddd8f96616ee6a1f092d8e0aedab3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90738445"
---
### <a name="determine-whether-ejb-timers-are-in-use"></a>Determinar se temporizadores EJB estão sendo usados

Se seu aplicativo usar temporizadores EJB, você precisará validar que o código do temporizador EJB pode ser disparado pela instância do WildFly de maneira independente. Essa validação é necessária porque, no cenário de implantação do Serviço de Kubernetes do Azure, cada temporizador EJB será disparado em sua própria instância do WildFly.
