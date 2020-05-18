---
author: yevster
ms.author: yebronsh
ms.date: 5/4/2020
ms.openlocfilehash: 430f4fef9c512f91da4fd95f04320ddd127ea784
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990248"
---
### <a name="inventory-configuration-sources-and-secrets"></a>Inventariar as fontes e os segredos de configuração

#### <a name="inventory-passwords-and-secure-strings"></a>Inventariar as senhas e proteger as cadeia de caracteres

Verifique todas as propriedades e os arquivos de configuração, assim como as variáveis de ambiente, nas implantações de produção em busca de senhas e cadeias de caracteres secretas. Em um aplicativo Spring Boot, normalmente é possível encontrar essas cadeias de caracteres no arquivo *application.properties* ou no *application.yml*.

[!INCLUDE [inventory-certificates-h4](inventory-certificates-h4.md)]
