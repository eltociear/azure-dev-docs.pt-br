---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: bda7ba230d8287101fc8f3f298b08777bb6e1c61
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "81672902"
---
### <a name="determine-whether-ejb-timers-are-in-use"></a>Determinar se temporizadores EJB estão sendo usados

Se seu aplicativo usar temporizadores EJB, você precisará validar que o código do temporizador EJB pode ser disparado pela instância do WildFly de maneira independente. Essa validação é necessária porque, no cenário de implantação do Serviço de Kubernetes do Azure, cada temporizador EJB será disparado em sua própria instância do WildFly.
