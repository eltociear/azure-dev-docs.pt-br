---
author: yevster
ms.author: yebronsh
ms.date: 4/15/2020
ms.openlocfilehash: f2bc0442aafcfffc963d4e5a6da18085d9205a22
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990158"
---
#### <a name="identify-external-caches"></a>Identificar os caches externos

Identifique os caches externos em uso. Frequentemente, o Redis é usado por meio do Spring Data Redis. Para obter informações de configuração, confira a [Documentação do Spring Data Redis](https://spring.io/projects/spring-data-redis).

Determine se os dados de sessão estão sendo armazenados em cache por meio do [Spring Session](https://spring.io/projects/spring-session) pesquisando a respectiva configuração (no [Java](https://docs.spring.io/spring-session/docs/current/reference/html5/#httpsession-redis-jc) ou no [XML](https://docs.spring.io/spring-session/docs/current/reference/html5/#httpsession-redis-xml)).
