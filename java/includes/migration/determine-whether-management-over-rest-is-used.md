---
author: edburns
ms.author: edburns
ms.date: 1/21/2020
ms.openlocfilehash: a67bdb749231c39e1b64d7b2a982ad4c0946cc25
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76830734"
---
### <a name="determine-whether-management-over-rest-is-used"></a>Determinar se o gerenciamento em REST é usado

Se o ciclo de vida do seu aplicativo incluir o uso do gerenciamento em REST, você precisará capturar quais portas são usadas para acessar a API REST e determinar como elas são autenticadas e expostas. Após a migração, você precisará garantir que essas mesmas portas e mecanismos de autenticação sejam expostos para que o ciclo de vida do aplicativo possa funcionar de maneira semelhante a antes da migração. Para saber mais, consulte [Administrando o Oracle WebLogic Server com serviços de gerenciamento RESTful](https://docs.oracle.com/middleware/12213/wls/WLRUR/title.htm).
