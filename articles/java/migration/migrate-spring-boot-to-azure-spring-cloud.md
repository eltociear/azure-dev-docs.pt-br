---
title: Migrar aplicativos Spring Boot para o Azure Spring Cloud
description: Este guia descreve as informações das quais você deve estar ciente quando deseja migrar um aplicativo Spring Boot existente para execução no Azure Spring Cloud.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 5/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: 4d2f84c6c77294c9a2d25028608e2feb712599f8
ms.sourcegitcommit: 4036ac08edd7fc6edf8d11527444061b0e4531ef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89062035"
---
# <a name="migrate-spring-boot-applications-to-azure-spring-cloud"></a>Migrar aplicativos Spring Boot para o Azure Spring Cloud

Este guia descreve as informações das quais você deve estar ciente quando deseja migrar um aplicativo Spring Boot existente para execução no Azure Spring Cloud.

## <a name="pre-migration"></a>Pré-migração

Antes de tudo, para garantir uma migração bem-sucedida, conclua as etapas de avaliação e de inventário descritas nas seções a seguir.

Se você não puder atender a nenhum desses requisitos de pré-migração, confira os seguintes guias de migração complementares:

* Migrar aplicativos JAR executáveis para contêineres no Serviço de Kubernetes do Azure (diretrizes planejadas)
* Migrar aplicativos JAR executáveis para Máquinas Virtuais do Azure (diretrizes planejadas)

### <a name="inspect-application-components"></a>Inspecionar os componentes de aplicativo

[!INCLUDE [identify-local-state](includes/identify-local-state-azure-spring-cloud.md)]

[!INCLUDE [static-content-azure-spring-cloud](includes/determine-whether-and-how-the-file-system-is-used-azure-spring-cloud.md)]

#### <a name="determine-whether-any-of-the-services-contain-os-specific-code"></a>Determinar se algum dos serviços contém o código específico do SO

[!INCLUDE [determine-whether-your-application-contains-os-specific-code](includes/determine-whether-your-application-contains-os-specific-code-no-title.md)]

[!INCLUDE [switch-to-a-supported-platform-azure-spring-cloud](includes/switch-to-a-supported-platform-azure-spring-cloud.md)]

[!INCLUDE [identify-spring-boot-versions](includes/identify-spring-boot-versions.md)]

Para os aplicativos que usam o Spring Boot 1.x, siga o [Guia de migração do Spring Boot 2.0](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.0-Migration-Guide) para atualizá-los para uma versão do Spring Boot com suporte. Para obter as versões com suporte, confira [Preparar um aplicativo Spring Java para a implantação](/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment#spring-boot-and-spring-cloud-versions).

[!INCLUDE [identify-logs-metrics-apm-azure-spring-cloud.md](includes/identify-logs-metrics-apm-azure-spring-cloud.md)]

### <a name="inventory-external-resources"></a>Recursos externos de inventário

Identifique recursos externos, como fontes de dados, agentes de mensagens JMS e URLs de outros serviços. Em aplicativos Spring Boot, normalmente é possível encontrar a configuração desses recursos na pasta *src/main/directory*, em um arquivo geralmente chamado de *application.properties* ou de *application.yml*.

[!INCLUDE [inventory-databases-spring-boot](includes/inventory-databases-spring-boot.md)]

[!INCLUDE [identify-jms-brokers-in-spring](includes/identify-jms-brokers-in-spring.md)]

Depois de identificar os agentes em uso, localize as configurações correspondentes. Em aplicativos Spring Boot, normalmente é possível encontrá-las nos arquivos *application.properties* e *application.yml* no diretório de aplicativo.

[!INCLUDE [jms-broker-settings-examples-in-spring](includes/jms-broker-settings-examples-in-spring.md)]

[!INCLUDE [identify-external-caches-azure-spring-cloud](includes/identify-external-caches-azure-spring-cloud.md)]

[!INCLUDE [inventory-identity-providers-spring-boot.md](includes/inventory-identity-providers-spring-boot.md)]

#### <a name="identify-any-clients-relying-on-a-non-standard-port"></a>Identificar qualquer cliente que dependa de uma porta não padrão

O Azure Spring Cloud substitui a configuração `server.port` no aplicativo implantado. Se qualquer cliente dos clientes depender do aplicativo estar disponível em uma porta diferente de 443, você precisará modificá-los.

#### <a name="all-other-external-resources"></a>Todos os outros recursos externos

É inviável que este guia documente todas as dependências externas possíveis. Após a migração, é sua responsabilidade verificar se você pode satisfazer todas as dependência externas do aplicativo.

[!INCLUDE [inventory-configuration-sources-and-secrets-spring-boot](includes/inventory-configuration-sources-and-secrets-spring-boot.md)]

[!INCLUDE [inspect-the-deployment-architecture-spring-boot](includes/inspect-the-deployment-architecture-spring-boot.md)]

## <a name="migration"></a>Migração

[!INCLUDE [migrate-steps-spring-boot-azure-spring-cloud](includes/migrate-steps-spring-boot-azure-spring-cloud.md)]

## <a name="post-migration"></a>Após a migração

[!INCLUDE [post-migration-spring-boot-azure-spring-cloud](includes/post-migration-spring-boot-azure-spring-cloud.md)]
