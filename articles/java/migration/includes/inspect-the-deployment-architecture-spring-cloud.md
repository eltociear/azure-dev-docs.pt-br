---
author: yevster
ms.author: yebronsh
ms.date: 5/4/2020
ms.openlocfilehash: 7b2ad87dfe2e2c7358737ff02ec52b97b1332ae7
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990128"
---
### <a name="inspect-the-deployment-architecture"></a>Inspecionar a arquitetura de implantação

#### <a name="document-hardware-requirements-for-each-service"></a>Documentar os requisitos de hardware para cada serviço

Para cada um dos serviços do Spring Cloud (não incluindo o servidor de configuração, o registro nem o gateway), documente as seguintes informações:

* O número de instâncias em execução.
* O número de CPUs alocadas para cada instância.
* A quantidade de RAM alocada para cada instância.

#### <a name="document-geo-replicationdistribution"></a>Documentar a replicação geográfica/distribuição

Determine se os aplicativos Spring Cloud estão atualmente distribuídos entre várias regiões ou datacenters. Documente os requisitos de tempo de atividade/SLA (Contrato de Nível de Serviço) para os aplicativos que você está migrando.

#### <a name="identify-clients-that-bypass-the-service-registry"></a>Identificar os clientes que ignoram o registro de serviço

Identifique aplicativos cliente que invoquem qualquer um dos serviços a serem migrados sem usar o Spring Cloud Service Registry. Após a migração, essas invocações não serão mais possíveis. Atualize esses clientes para usar o [Spring Cloud OpenFeign](https://spring.io/projects/spring-cloud-openfeign) antes da migração.
