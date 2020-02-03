---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: cc634f89170f4db6f961da91e3eb3abe7393539d
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76830984"
---
### <a name="validate-that-the-supported-java-version-works-correctly"></a>Validar se a versão Java com suporte funciona corretamente

Todos os caminhos de migração do WebLogic para o Azure exigem uma versão específica do Java, que varia para cada caminho. Você precisará validar que seu aplicativo pode ser executado corretamente usando essa versão com suporte.

Para obter a versão atual, entre no servidor de produção e execute este comando:

```bash
java -version
```

> [!NOTE]
> Ao migrar para o WebLogic em máquinas virtuais do Azure, os requisitos para as versões específicas do Java são determinados pelo Java pré-instalado nas máquinas virtuais.
