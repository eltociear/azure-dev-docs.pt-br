---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 3427cc445333c9851a760a607c402e689c7b6ba4
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81673152"
---
### <a name="inventory-all-certificates"></a>Inventariar todos os certificados

Documente todos os certificados usados para pontos de extremidade SSL públicos. Você pode exibir todos os certificados nos servidores de produção executando o seguinte comando:

```bash
keytool -list -v -keystore <path to keystore>
```
