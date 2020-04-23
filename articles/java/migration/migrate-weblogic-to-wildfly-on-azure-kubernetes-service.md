---
title: Migrar aplicativos WebLogic para o WildFly no Serviço de Kubernetes do Azure
description: Este guia descreve as informações das quais você deve estar ciente quando deseja migrar um aplicativo WebLogic existente para ser executado no WildFly em um contêiner do Serviço de Kubernetes do Azure.
author: mriem
ms.author: manriem
ms.topic: conceptual
ms.date: 2/28/2020
ms.openlocfilehash: d17551aeb1041415e2c5b6d5fd8a43d3b7b670aa
ms.sourcegitcommit: 0af39ee9ff27c37ceeeb28ea9d51e32995989591
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81673382"
---
# <a name="migrate-weblogic-applications-to-wildfly-on-azure-kubernetes-service"></a>Migrar aplicativos WebLogic para o WildFly no Serviço de Kubernetes do Azure

Este guia descreve as informações das quais você deve estar ciente quando deseja migrar um aplicativo WebLogic existente para ser executado no WildFly em um contêiner do Serviço de Kubernetes do Azure.

## <a name="before-you-start"></a>Antes de começar

Se você não puder atender a nenhum dos requisitos de pré-migração, confira o guia de migração complementar:

* [Migrar seus aplicativos WebLogic para Máquinas Virtuais do Azure](migrate-weblogic-to-virtual-machines.md)

## <a name="pre-migration"></a>Pré-migração

[!INCLUDE [inventory-server-capacity-aks](includes/inventory-server-capacity-aks.md)]

[!INCLUDE [inventory-all-secrets](includes/inventory-all-secrets.md)]

[!INCLUDE [inventory-all-certificates](includes/inventory-all-certificates.md)]

[!INCLUDE [inventory-jndi-resources](includes/inventory-jndi-resources.md)]

### <a name="determine-whether-session-replication-is-used"></a>Determinar se a replicação de sessão é usada

Se seu aplicativo depender da replicação de sessão, com ou sem o Oracle Coherence*Web, você terá duas opções:

* Refatore seu aplicativo para usar um banco de dados para gerenciamento de sessão.
* Refatore seu aplicativo para externalizar a sessão para o serviço Redis do Azure. Para obter mais informações, consulte [Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview).

[!INCLUDE [document-datasources](includes/document-datasources.md)]

[!INCLUDE [determine-whether-weblogic-has-been-customized](includes/determine-whether-weblogic-has-been-customized.md)]

[!INCLUDE [determine-whether-a-connection-to-on-premises-is-needed](includes/determine-whether-a-connection-to-on-premises-is-needed.md)]

[!INCLUDE [determine-whether-jms-queues-or-topics-are-in-use](includes/determine-whether-jms-queues-or-topics-are-in-use.md)]

[!INCLUDE [determine-whether-you-are-using-your-own-custom-created-shared-java-ee-libraries](includes/determine-whether-you-are-using-your-own-custom-created-shared-java-ee-libraries.md)]

[!INCLUDE [determine-whether-osgi-bundles-are-used](includes/determine-whether-osgi-bundles-are-used.md)]

[!INCLUDE [determine-whether-your-application-contains-os-specific-code](includes/determine-whether-your-application-contains-os-specific-code.md)]

[!INCLUDE [determine-whether-oracle-service-bus-is-in-use](includes/determine-whether-oracle-service-bus-is-in-use.md)]

[!INCLUDE [determine-whether-your-application-is-composed-of-multiple-wars](includes/determine-whether-your-application-is-composed-of-multiple-wars.md)]

[!INCLUDE [determine-whether-your-application-is-packaged-as-an-ear](includes/determine-whether-your-application-is-packaged-as-an-ear.md)]

<!-- AKS-specific extension of the last INCLUDE. -->
> [!NOTE]
> Se você deseja ser capaz de dimensionar cada um dos seus aplicativos Web independentemente para um melhor uso dos recursos do AKS, divida o EAR em aplicativos Web separados.
<!-- end extension -->

[!INCLUDE [identify-all-outside-processes-and-daemons-running-on-the-production-servers](includes/identify-all-outside-processes-and-daemons-running-on-the-production-servers.md)]

### <a name="validate-that-the-supported-java-version-works-correctly"></a>Validar se a versão Java com suporte funciona corretamente

O uso do WildFly no Serviço de Kubernetes do Azure requer uma versão específica do Java. Portanto, você precisará validar se seu aplicativo pode ser executado corretamente usando essa versão com suporte. Essa validação será especialmente importante se o servidor atual estiver usando um JDK compatível (como Oracle JDK ou IBM OpenJ9).

Para determinar a sua versão atual, entre no servidor de produção e execute o seguinte comando:

```bash
java -version
```

[!INCLUDE [determine-whether-your-application-relies-on-scheduled-jobs](includes/determine-whether-your-application-relies-on-scheduled-jobs.md)]

### <a name="determine-whether-weblogic-scripting-tool-wlst-is-used"></a>Determinar se a WLST (WebLogic Scripting Tool) é usada

Se você usar a WLST para realizar a implantação atualmente, precisará avaliar o que ela está fazendo. Se a WLST estiver alterando parâmetros (runtime) do seu aplicativo como parte da implantação, você precisará verificar se esses parâmetros estão em conformidade com uma das seguintes opções:

* Eles são externalizados como configurações do aplicativo.
* Eles são inseridos em seu aplicativo.
* Eles estão usando a CLI do JBoss durante a implantação.

Se a WLST estiver fazendo mais do que o mencionado acima, você terá que realizar um trabalho adicional durante a migração.

### <a name="determine-whether-your-application-uses-weblogic-specific-apis"></a>Determinar se seu aplicativo usa APIs específicas do WebLogic

Se seu aplicativo usar APIs específicas do WebLogic, você precisará refatorá-lo para remover essas dependências. Por exemplo, se você usou uma classe mencionada na [Referência de API Java para o Oracle WebLogic Server](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/wlapi/index.html?overview-summary.html), você usou uma API específica do WebLogic em seu aplicativo.

[!INCLUDE [determine-whether-your-application-uses-entity-beans](includes/determine-whether-your-application-uses-entity-beans.md)]

[!INCLUDE [determine-whether-the-java-ee-application-client-feature-is-in-use-aks](includes/determine-whether-the-java-ee-application-client-feature-is-in-use-aks.md)]

### <a name="determine-whether-a-deployment-plan-was-used"></a>Determinar se um plano de implantação foi usado

Se seu aplicativo tiver sido implantado usando um plano de implantação, você precisará avaliar o que o plano de implantação está fazendo. Se o plano de implantação for uma implantação direta, então você poderá implantar seu aplicativo Web sem nenhuma alteração. Se o plano de implantação for mais elaborado, você precisará determinar se pode usar a CLI do JBoss para configurar adequadamente seu aplicativo como parte da implantação. Se não for possível usar a CLI do JBoss, você precisará refatorar seu aplicativo de modo que um plano de implantação não seja mais necessário.

[!INCLUDE [determine-whether-ejb-timers-are-in-use](includes/determine-whether-ejb-timers-are-in-use.md)]

### <a name="validate-whether-and-how-the-file-system-is-used"></a>Validar se e como o sistema de arquivos é usado

Qualquer uso do sistema de arquivos no servidor de aplicativos exigirá reconfiguração ou, em casos raros, alterações de arquitetura. O sistema de arquivos pode ser usado por módulos compartilhados do WebLogic ou pelo seu código do aplicativo. Você pode identificar alguns ou todos os cenários descritos nas seções a seguir.

#### <a name="read-only-static-content"></a>Conteúdo estático somente leitura

Se seu aplicativo estiver servindo conteúdo estático no momento, você precisará de um local alternativo para ele. Talvez você queira considerar a movimentação de conteúdo estático para o Armazenamento de Blobs do Azure e a adição da CDN do Azure para downloads extremamente rápidos, globalmente. Para obter mais informações, confira [Hospedagem de site estático no Armazenamento do Microsoft Azure](/azure/storage/blobs/storage-blob-static-website) e [Início rápido: Integrar uma conta de armazenamento do Azure à CDN do Azure](/azure/cdn/cdn-create-a-storage-account-with-cdn).

#### <a name="dynamically-published-static-content"></a>Conteúdo estático publicado dinamicamente

Se o aplicativo permitir conteúdo estático que é carregado/produzido pelo aplicativo, mas não puder ser alterado após sua própria criação, você poderá usar o Armazenamento de Blobs do Azure e a CDN do Azure, conforme descrito acima, com uma função do Azure para lidar com uploads e atualização de CDN. Fornecemos uma implementação de exemplo para seu uso em [Carregar conteúdo estático e fazer o pré-carregamento desse conteúdo pela CDN com o Azure Functions](https://github.com/Azure-Samples/functions-java-push-static-contents-to-cdn).

#### <a name="dynamic-or-internal-content"></a>Conteúdo dinâmico ou interno

Para arquivos que são frequentemente escritos e lidos pelo o aplicativo (como arquivos de dados temporários) ou arquivos estáticos que são visíveis somente para o aplicativo, você pode montar compartilhamentos do Armazenamento do Azure como volumes persistentes. Para obter mais informações, confira [Criar e usar dinamicamente um volume persistente com Arquivos do Azure no Serviço de Kubernetes do Azure](/azure/aks/azure-files-dynamic-pv).

### <a name="determine-whether-jca-connectors-are-used"></a>Determinar se conectores JCA são usados

Se seu aplicativo usar conectores JCA, você precisará validar se o conector JCA pode ser usado no WildFly. Se a implementação do JCA estiver vinculada ao WebLogic, você precisará refatorar seu aplicativo para remover essa dependência. Se puder ser usado, você precisará adicionar os JARs ao classpath do servidor e colocar os arquivos de configuração necessários no local correto nos diretórios do servidor do WildFly para que ele esteja disponível.

[!INCLUDE [determine-whether-your-application-uses-a-resource-adapter](includes/determine-whether-your-application-uses-a-resource-adapter.md)]

[!INCLUDE [determine-whether-jaas-is-in-use](includes/determine-whether-jaas-is-in-use.md)]

### <a name="determine-whether-weblogic-clustering-is-used"></a>Determinar se o clustering do WebLogic é usado

É provável que você tenha implantado seu aplicativo em vários WebLogic Servers para conseguir alta disponibilidade. O Serviço de Kubernetes do Azure é capaz de dimensionar, mas se você tiver usado a API de Cluster do WebLogic, precisará refatorar seu código para eliminar o uso dessa API.

[!INCLUDE [perform-in-place-testing](includes/perform-in-place-testing.md)]

## <a name="migration"></a>Migração

[!INCLUDE [provision-azure-container-registry-and-azure-kubernetes-service](includes/provision-azure-container-registry-and-azure-kubernetes-service.md)]

[!INCLUDE [create-a-docker-image-for-wildfly](includes/create-a-docker-image-for-wildfly.md)]

[!INCLUDE [build-and-push-the-docker-image-to-azure-container-registry](includes/build-and-push-the-docker-image-to-azure-container-registry.md)]

[!INCLUDE [provision-a-public-ip-address](includes/provision-a-public-ip-address.md)]

[!INCLUDE [deploy-to-aks](includes/deploy-to-aks.md)]

### <a name="configure-persistent-storage"></a>Configurar um armazenamento persistente

Se seu aplicativo exigir armazenamento não volátil, configure um ou mais [Volumes Persistentes](/azure/aks/azure-disks-dynamic-pv).

[!INCLUDE [migrate-scheduled-jobs-aks](includes/migrate-scheduled-jobs-aks.md)]

## <a name="post-migration"></a>Após a migração

Agora que você migrou seu aplicativo para o Serviço de Kubernetes do Azure, você deve verificar se ele funciona conforme o esperado. Após fazer isso, confira as seguintes recomendações para tornar seu aplicativo mais nativo de nuvem.

[!INCLUDE [recommendations-wildfly-on-aks](includes/recommendations-wildfly-on-aks.md)]