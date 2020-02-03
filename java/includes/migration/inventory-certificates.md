---
author: yevster
ms.author: yebronsh
ms.date: 1/24/2020
ms.openlocfilehash: 3cf4edcf12d7fe72c0fa5a0216d1a8c97a2f2e59
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76825855"
---
### <a name="inventory-certificates"></a>Inventariar os certificados

Documente todos os certificados usados para pontos de extremidade SSL públicos ou comunicação com bancos de dados de back-end e outros sistemas. Você pode exibir todos os certificados nos servidores de produção executando o seguinte comando:

```bash
keytool -list -v -keystore <path to keystore>
```
