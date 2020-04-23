---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: d56ba6d8f101b1d90468a436f0a111f78a19fc83
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81673402"
---
### <a name="determine-whether-weblogic-has-been-customized"></a>Determinar se o WebLogic foi personalizado

Determine quais das personalizações a seguir foram feitas e capture o que foi feito.

* Os scripts de inicialização foram alterados? Esses scripts incluem *setDomainEnv*, *commEnv*, *startWebLogic* e *stopWebLogic*.
* Parâmetros específicos foram passados para a JVM?
* JARs foram adicionados ao classpath do servidor?
