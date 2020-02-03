---
title: Migrar seus aplicativos WebLogic para Máquinas Virtuais do Azure
description: Este guia descreve as informações das quais você deve estar ciente quando deseja migrar um aplicativo WebLogic existente para ser executado em Máquinas Virtuais do Azure.
author: edburns
ms.author: edburns
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 4abc2383d0ff2e14cf550d96b87c75bfa6fab72e
ms.sourcegitcommit: 367780fe48d977c82cb84208c128b0bf694b1029
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76830694"
---
# <a name="migrate-weblogic-applications-to-azure-virtual-machines"></a>Migrar seus aplicativos WebLogic para Máquinas Virtuais do Azure

Este guia descreve as informações das quais você deve estar ciente quando deseja migrar um aplicativo WebLogic existente para ser executado em Máquinas Virtuais do Azure.

## <a name="pre-migration"></a>Pré-migração

### <a name="define-what-you-mean-by-migration-complete"></a>Defina o que você quer dizer por "migração concluída"

Este guia e as ofertas do Azure Marketplace correspondentes são um ponto de partida para acelerar a migração de suas cargas de trabalho do WebLogic Server para o Azure. É importante definir o escopo do seu esforço de migração. Por exemplo, você está fazendo um "lift and shift" rigoroso de sua infraestrutura existente para Máquinas Virtuais do Azure? Nesse caso, pode estar tentado a fazer um "lift and improve" ao migrar.

É melhor se ater ao "lift and shift" puro na medida do possível, fazendo as alterações necessárias detalhadas neste guia. Defina o que você quer dizer por "migração concluída" para saber quando chegou a essa etapa. Quando você tiver atingido a "migração concluída", poderá tirar um instantâneo de suas Máquinas Virtuais, conforme descrito em [Criar um instantâneo](/azure/virtual-machines/windows/snapshot-copy-managed-disk). Depois de verificar que é possível restaurar com êxito com base no seu instantâneo, é mais seguro fazer as melhorias sem medo de perder o progresso da migração feito até agora.

### <a name="determine-whether-the-pre-built-marketplace-offers-are-a-good-starting-point"></a>Determinar se as ofertas predefinidas do Marketplace são um bom ponto de partida

A Oracle e a Microsoft fizeram parceria para trazer um conjunto de modelos de solução do Azure para o Azure Marketplace e fornecer um ponto de partida sólido para migrar para o Azure. Consulte a documentação do [Middleware do Oracle Fusion](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/wlazu/) para ver a lista de ofertas e escolher a que mais se aproxima da sua implantação existente. Você pode ver a lista de ofertas [na documentação da Oracle](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/wlazu/select-required-oracle-weblogic-server-offer-azure-marketplace.html#GUID-187739C5-EE7A-47C6-B3BA-C0A0333DC398)

Se nenhuma das ofertas existentes for um bom ponto de partida, será necessário reproduzir a implantação manualmente usando recursos de Máquina Virtual do Azure. Para obter mais informações, consulte [O que é IaaS?](https://azure.microsoft.com/overview/what-is-iaas/).

### <a name="determine-whether-the-weblogic-version-is-compatible"></a>Determinar se a versão do WebLogic é compatível

Sua versão existente do WebLogic deve ser compatível com a versão nas ofertas de IaaS. Essa consulta mostrará as ofertas para [a versão 12.2.1.3 do WebLogic](https://azuremarketplace.microsoft.com/marketplace/apps?search=oracle%20weblogic%2012.2.1.3&page=1). Se a versão existente do WebLogic não for compatível com essa versão, você terá que reproduzir a implantação manualmente usando os recursos de IaaS do Azure. Para obter mais informações, consulte [a documentação do Azure](https://azure.microsoft.com/overview/what-is-iaas/).

[!INCLUDE [inventory-server-capacity-virtual-machines](includes/migration/inventory-server-capacity-virtual-machines.md)]

[!INCLUDE [inventory-all-secrets](includes/migration/inventory-all-secrets.md)]

[!INCLUDE [inventory-all-certificates](includes/migration/inventory-all-certificates.md)]

[!INCLUDE [validate-that-the-supported-java-version-works-correctly](includes/migration/validate-that-the-supported-java-version-works-correctly.md)]

[!INCLUDE [inventory-jndi-resources](includes/migration/inventory-jndi-resources.md)]

[!INCLUDE [domain-configuration](includes/migration/domain-configuration.md)]

[!INCLUDE [determine-whether-session-replication-is-used](includes/migration/determine-whether-session-replication-is-used.md)]

[!INCLUDE [document-datasources](includes/migration/document-datasources.md)]

[!INCLUDE [determine-whether-weblogic-has-been-customized](includes/migration/determine-whether-weblogic-has-been-customized.md)]

[!INCLUDE [determine-whether-management-over-rest-is-used](includes/migration/determine-whether-management-over-rest-is-used.md)]

[!INCLUDE [determine-whether-a-connection-to-on-premises-is-needed](includes/migration/determine-whether-a-connection-to-on-premises-is-needed.md)]

[!INCLUDE [determine-whether-jms-queues-or-topics-are-in-use-virtual-machines](includes/migration/determine-whether-jms-queues-or-topics-are-in-use-virtual-machines.md)]

[!INCLUDE [determine-whether-you-are-using-your-own-custom-created-shared-java-ee-libraries](includes/migration/determine-whether-you-are-using-your-own-custom-created-shared-java-ee-libraries.md)]

[!INCLUDE [determine-whether-osgi-bundles-are-used](includes/migration/determine-whether-osgi-bundles-are-used.md)]

[!INCLUDE [determine-whether-your-application-contains-os-specific-code](includes/migration/determine-whether-your-application-contains-os-specific-code.md)]

[!INCLUDE [determine-whether-oracle-service-bus-is-in-use](includes/migration/determine-whether-oracle-service-bus-is-in-use.md)]

[!INCLUDE [determine-whether-your-application-is-composed-of-multiple-wars](includes/migration/determine-whether-your-application-is-composed-of-multiple-wars.md)]

[!INCLUDE [determine-whether-your-application-is-packaged-as-an-ear](includes/migration/determine-whether-your-application-is-packaged-as-an-ear.md)]

[!INCLUDE [identify-all-outside-processes-and-daemons-running-on-the-production-servers](includes/migration/identify-all-outside-processes-and-daemons-running-on-the-production-servers.md)]

[!INCLUDE [determine-whether-wlst-is-used](includes/migration/determine-whether-wlst-is-used.md)]

[!INCLUDE [validate-whether-and-how-the-file-system-is-used](includes/migration/validate-whether-and-how-the-file-system-is-used.md)]

[!INCLUDE [determine-the-network-topology](includes/migration/determine-the-network-topology.md)]

[!INCLUDE [account-for-the-use-of-jca-adapters-and-resource-adapters](includes/migration/account-for-the-use-of-jca-adapters-and-resource-adapters.md)]

[!INCLUDE [account-for-the-use-of-custom-security-providers-and-jaas](includes/migration/account-for-the-use-of-custom-security-providers-and-jaas.md)]

[!INCLUDE [determine-whether-weblogic-clustering-is-used](includes/migration/determine-whether-weblogic-clustering-is-used.md)]

[!INCLUDE [determine-whether-the-java-ee-application-client-feature-is-used](includes/migration/determine-whether-the-java-ee-application-client-feature-is-used.md)]

## <a name="migration"></a>Migração

### <a name="select-a-weblogic-on-azure-virtual-machines-offer"></a>Selecionar uma oferta do WebLogic em Máquinas Virtuais do Azure

As ofertas a seguir estão disponíveis para o WebLogic em Máquinas Virtuais do Azure.

Durante a implantação de uma oferta, você deverá escolher o tamanho da Máquina Virtual para seus nós do WebLogic Server. É importante considerar todos os aspectos do dimensionamento (memória, processador, disco) ao escolher o tamanho da VM. Para obter mais informações, consulte [a documentação para as ofertas](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/wlazu/deploy-oracle-weblogic-server-administration-server-single-node.html) e também a [documentação do Azure para o dimensionamento de máquinas virtuais](/azure/cloud-services/cloud-services-sizes-specs)

#### <a name="weblogic-server-single-node-with-no-admin-server"></a>WebLogic Server de um único nó sem servidor de administração

Esta oferta cria uma única VM e instala o WebLogic nela, mas não configura domínios, o que é útil para cenários em que você tem uma configuração de domínio altamente personalizada.

#### <a name="weblogic-server-single-node-with-admin-server"></a>WebLogic Server de um único nó com servidor de administração

Esta oferta provisiona uma única VM e instala o WebLogic Server 12.1.2.3 nela. Ele cria um domínio e inicializa o servidor de administração.

#### <a name="weblogic-server-n-node-cluster"></a>Cluster de N nós do WebLogic Server

Esta oferta cria um cluster altamente disponível de VMs do WebLogic Server.

#### <a name="weblogic-server-n-node-dynamic-cluster"></a>Cluster dinâmico de N nós do WebLogic Server

Esta oferta cria um cluster dinâmico altamente disponível e escalonável de VMs do WebLogic Server

### <a name="provision-the-offer"></a>Provisionar a oferta

Depois de selecionar a oferta inicial, siga as instruções na [documentação das ofertas](https://wls-eng.github.io/arm-oraclelinux-wls/) para provisionar essa oferta. Lembre-se de escolher o nome de domínio que corresponda ao nome de domínio existente. Você pode até mesmo corresponder a senha de domínio com sua senha de domínio existente.

### <a name="migrate-the-domains"></a>Migrar os domínios

Depois de provisionar a oferta, você pode examinar a configuração de domínio e seguir [este guia](https://support.oracle.com/knowledge/Middleware/2336356_1.html) para obter detalhes sobre como migrar os domínios.

### <a name="connect-the-databases"></a>Conectar os bancos de dados

Depois de fazer a migração dos domínios, você pode conectar os bancos de dados seguindo as instruções [na documentação da oferta](https://wls-eng.github.io/arm-oraclelinux-wls/#connecting-a-database-to-a-cluster). Essas instruções ajudarão você a considerar os segredos de banco de dados e cadeias de caracteres de acesso envolvidos.

### <a name="account-for-keystores"></a>Considerar os repositórios de chaves

Você deve considerar a migração de todos os repositórios de chaves SSL usados pelo seu aplicativo. Para obter mais informações, consulte [Configurando repositórios de chaves](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/secmg/identity_trust.html#GUID-7F03EB9C-9755-430B-8B86-17199E0C01DC).

### <a name="connect-the-jms-sources"></a>Conectar as fontes JMS

Depois de conectar os bancos de dados, você pode configurar o JMS seguindo as instruções em [Middleware do Fusion Administrando recursos JMS para o Oracle WebLogic Server](https://docs.oracle.com/middleware/12213/wls/JMSAD/toc.htm) na documentação do WebLogic.

### <a name="account-for-logging"></a>Considerar o registro em log

A configuração de registro em log existente deve ser transferida quando o domínio é migrado. Para obter mais informações, consulte [Configurar os níveis do agente java.util.logging](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/wlach/taskhelp/logging/ConfigureJavaLoggingLevels.html) e [Configurando arquivos de log e filtrando mensagens de log para o Oracle WebLogic Server](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/wllog/index.html)

### <a name="migrating-your-applications"></a>Migrando seus aplicativos

As técnicas usadas para implantar aplicativos da equipe de desenvolvimento em servidores de teste, de preparo e de produção variam muito conforme o caso. Em alguns casos, há uma plataforma de CI/CD altamente evoluída que faz com que os aplicativos sejam implantados no WebLogic Server. Em outros casos, o processo pode ser mais manual. Um dos benefícios de usar as Máquinas Virtuais do Azure para migrar aplicativos WebLogic para a nuvem é que os processos existentes continuarão a funcionar.

Você precisará configurar o grupo de segurança de rede provisionado pela oferta para permitir o acesso do pipeline de CI/CD ou do sistema de implantação manual. Para obter mais informações, consulte [Grupos de segurança](/azure/virtual-network/security-overview) na documentação do Azure.

### <a name="testing"></a>Testando

Todos os testes no contêiner em relação a aplicativos devem ser configurados para acessar os novos servidores em execução no Azure. Assim como acontece com as preocupações com o CI/CD, você deve garantir que as regras de segurança de rede necessárias permitam que seus testes acessem os aplicativos implantados no Azure. Para obter mais informações, consulte [Grupos de segurança](/azure/virtual-network/security-overview) na documentação do Azure.

## <a name="post-migration"></a>Após a migração

Depois de atingir as metas de migração que você definiu na etapa de [pré-migração](#pre-migration), execute um teste de aceitação de ponta a ponta para verificar se tudo funciona conforme o esperado. Alguns tópicos para aprimoramentos após a migração incluem, mas certamente não estão limitados ao seguinte:

* Usar o Armazenamento do Microsoft Azure para fornecer conteúdo estático montado nas máquinas virtuais. Para obter mais informações, consulte [Anexar ou desanexar um disco de dados em uma máquina virtual](/azure/lab-services/devtest-lab-attach-detach-data-disk).

* Implante seus aplicativos em seu cluster WebLogic migrado com o Azure DevOps. Para obter mais informações, consulte a [documentação de introdução do Azure DevOps](/azure/devops/get-started/?view=azure-devops).

* Aprimore sua topologia de rede com serviços avançados de balanceamento de carga. Para obter mais informações, consulte [Usando os serviços de balanceamento de carga no Azure](/azure/traffic-manager/traffic-manager-load-balancing-azure).

* Aproveite as identidades gerenciadas do Azure para segredos gerenciados e atribua acesso baseado em função aos recursos do Azure. Para obter mais informações, consulte [O que são identidades gerenciadas para recursos do Azure?](/azure/active-directory/managed-identities-azure-resources/overview).

* Integre a autenticação e autorização do WebLogic Java EE com o Azure Active Directory. Para obter mais informações, consulte o [guia de introdução Integrando o Azure Active Directory](/azure/active-directory/manage-apps/plan-an-application-integration).

* Use o Azure Key Vault para armazenar qualquer informação que funcione como um "segredo". Para saber mais, consulte [Conceitos básicos do Azure Key Vault](/azure/key-vault/basic-concepts).
