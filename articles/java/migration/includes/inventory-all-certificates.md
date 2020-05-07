---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: 3427cc445333c9851a760a607c402e689c7b6ba4
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "81673152"
---
### <a name="inventory-all-certificates"></a>Inventariar todos os certificados

Documente todos os certificados usados para pontos de extremidade SSL públicos. Você pode exibir todos os certificados nos servidores de produção executando o seguinte comando:

```bash
keytool -list -v -keystore <path to keystore>
```
