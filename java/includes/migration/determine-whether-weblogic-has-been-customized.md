---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: d56ba6d8f101b1d90468a436f0a111f78a19fc83
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76830844"
---
### <a name="determine-whether-weblogic-has-been-customized"></a>Determinar se o WebLogic foi personalizado

Determine quais das personalizações a seguir foram feitas e capture o que foi feito.

* Os scripts de inicialização foram alterados? Esses scripts incluem *setDomainEnv*, *commEnv*, *startWebLogic* e *stopWebLogic*.
* Parâmetros específicos foram passados para a JVM?
* JARs foram adicionados ao classpath do servidor?
