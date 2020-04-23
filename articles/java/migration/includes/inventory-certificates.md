---
author: yevster
ms.author: yebronsh
ms.date: 1/24/2020
ms.openlocfilehash: 3cf4edcf12d7fe72c0fa5a0216d1a8c97a2f2e59
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81672142"
---
### <a name="inventory-certificates"></a>Inventariar os certificados

Documente todos os certificados usados para pontos de extremidade SSL públicos ou comunicação com bancos de dados de back-end e outros sistemas. Você pode exibir todos os certificados nos servidores de produção executando o seguinte comando:

```bash
keytool -list -v -keystore <path to keystore>
```
