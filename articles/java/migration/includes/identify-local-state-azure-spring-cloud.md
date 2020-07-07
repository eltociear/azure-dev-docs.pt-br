---
author: yevster
ms.author: yebronsh
ms.date: 5/28/2020
ms.openlocfilehash: 70f54f911d97febf30b78888e6922e2d4ac3eb54
ms.sourcegitcommit: 81577378a4c570ced1e9c6765f4a9eee8453c889
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512615"
---
#### <a name="identify-local-state"></a>Identificar o estado local

Em ambientes PaaS, não é garantido que nenhum aplicativo esteja sendo executado exatamente uma vez em determinado momento. Mesmo quando você configurar um aplicativo para ser executado em uma só instância, uma instância duplicada poderá ser criada nos seguintes casos:

* O aplicativo precisa ser relocado para um host físico devido a uma falha ou uma atualização do sistema.
* O aplicativo está sendo atualizado.

Em qualquer um desses casos, a instância original permanecerá em execução até que a nova instância tenha terminado a inicialização. Isso tem as seguintes implicações potencialmente significativas para o aplicativo:

* Não é possível garantir que nenhum [singleton](https://en.wikipedia.org/wiki/Singleton_pattern) seja verdadeiramente único.
* Todos os dados que não foram persistidos no armazenamento externo provavelmente serão perdidos muito antes do que seriam em um servidor físico ou uma VM individual.

Antes de fazer a migração para o Azure Spring Cloud, verifique se o código não contém o estado local que não deve ser perdido nem duplicado. Se o estado local existir, altere o código para armazenar esse estado fora do aplicativo. Os aplicativos prontos para a nuvem normalmente armazenam o estado do aplicativo em localizações como as seguintes:

* [Cache Redis do Azure](/azure/azure-cache-for-redis/cache-java-get-started)
* [Azure Cosmos DB](/azure/cosmos-db/create-sql-api-java)
* Outro banco de dados externo, como [SQL do Azure](/azure/azure-sql/azure-sql-iaas-vs-paas-what-is-overview), [BD do Azure para MySQL](/azure/mysql/overview) ou [BD do Azure para PostgreSQL](/azure/postgresql/overview).
