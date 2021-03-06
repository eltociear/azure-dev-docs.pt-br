---
title: Migrar seus aplicativos WebLogic para Máquinas Virtuais do Azure
description: Este guia descreve as informações das quais você deve estar ciente quando deseja migrar um aplicativo WebLogic existente para ser executado em Máquinas Virtuais do Azure.
author: edburns
ms.author: edburns
ms.topic: conceptual
ms.date: 1/27/2020
ms.custom: devx-track-java
ms.openlocfilehash: 3b6d9847cc72f246587a36b74173521736a6cc9a
ms.sourcegitcommit: b923aee828cd4b309ef92fe1f8d8b3092b2ffc5a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88052245"
---
# <a name="migrate-weblogic-server-applications-to-azure-virtual-machines"></a>Migrar aplicativos do WebLogic Server para as Máquinas Virtuais do Azure

Este guia descreve as informações das quais você deve estar ciente quando deseja migrar um aplicativo WebLogic existente para ser executado em Máquinas Virtuais do Azure.  Para obter uma visão geral das soluções do WebLogic Server disponíveis no Azure Marketplace, confira [O que é o Oracle WebLogic Server no Azure?](/azure/virtual-machines/workloads/oracle/oracle-weblogic)

## <a name="pre-migration"></a>Pré-migração

Antes de tudo, para garantir uma migração bem-sucedida, conclua as etapas de avaliação e de inventário descritas nas seções a seguir.

### <a name="define-what-you-mean-by-migration-complete"></a>Defina o que você quer dizer por "migração concluída"

Este guia e as ofertas do Azure Marketplace correspondentes são um ponto de partida para acelerar a migração de suas cargas de trabalho do WebLogic Server para o Azure. É importante definir o escopo do seu esforço de migração. Por exemplo, você está fazendo um "lift and shift" rigoroso de sua infraestrutura existente para Máquinas Virtuais do Azure? Nesse caso, pode estar tentado a fazer um "lift and improve" ao migrar.

É melhor se ater ao "lift and shift" puro na medida do possível, fazendo as alterações necessárias detalhadas neste guia. Defina o que você quer dizer por "migração concluída" para saber quando chegou a essa etapa. Quando você tiver atingido a "migração concluída", poderá tirar um instantâneo de suas Máquinas Virtuais, conforme descrito em [Criar um instantâneo](/azure/virtual-machines/windows/snapshot-copy-managed-disk). Depois de verificar que é possível restaurar com êxito com base no seu instantâneo, é mais seguro fazer as melhorias sem medo de perder o progresso da migração feito até agora.

### <a name="determine-whether-the-pre-built-marketplace-offers-are-a-good-starting-point"></a>Determinar se as ofertas predefinidas do Marketplace são um bom ponto de partida

A Oracle e a Microsoft fizeram parceria para trazer um conjunto de modelos de solução do Azure para o Azure Marketplace e fornecer um ponto de partida sólido para migrar para o Azure. Consulte a documentação do [Middleware do Oracle Fusion](https://docs.oracle.com/en/middleware/standalone/weblogic-server/wlazu/) para ver a lista de ofertas e escolher a que mais se aproxima da sua implantação existente. É possível ver a lista de ofertas no artigo de visão geral [O que é o Oracle WebLogic Server no Azure?](/azure/virtual-machines/workloads/oracle/oracle-weblogic)

Se nenhuma das ofertas existentes for um bom ponto de partida, será necessário reproduzir a implantação manualmente usando recursos de Máquina Virtual do Azure. Para obter mais informações, consulte [O que é IaaS?](https://azure.microsoft.com/overview/what-is-iaas/).

### <a name="determine-whether-the-weblogic-version-is-compatible"></a>Determinar se a versão do WebLogic é compatível

Sua versão existente do WebLogic deve ser compatível com a versão nas ofertas de IaaS. Essa consulta mostrará as ofertas para [a versão 12.2.1.3 do WebLogic](https://azuremarketplace.microsoft.com/marketplace/apps?search=oracle%20weblogic%2012.2.1.3&page=1). Se a versão existente do WebLogic não for compatível com essa versão, você terá que reproduzir a implantação manualmente usando os recursos de IaaS do Azure. Para obter mais informações, consulte [a documentação do Azure](https://azure.microsoft.com/overview/what-is-iaas/).

[!INCLUDE [inventory-server-capacity-virtual-machines](includes/inventory-server-capacity-virtual-machines.md)]

[!INCLUDE [inventory-all-secrets](includes/inventory-all-secrets.md)]

[!INCLUDE [inventory-all-certificates](includes/inventory-all-certificates.md)]

[!INCLUDE [validate-that-the-supported-java-version-works-correctly-weblogic](includes/validate-that-the-supported-java-version-works-correctly-weblogic.md)]

[!INCLUDE [inventory-jndi-resources](includes/inventory-jndi-resources.md)]

[!INCLUDE [inspect-your-domain-configuration](includes/inspect-your-domain-configuration.md)]

[!INCLUDE [determine-whether-session-replication-is-used](includes/determine-whether-session-replication-is-used.md)]

[!INCLUDE [document-datasources](includes/document-datasources.md)]

[!INCLUDE [determine-whether-weblogic-has-been-customized](includes/determine-whether-weblogic-has-been-customized.md)]

[!INCLUDE [determine-whether-management-over-rest-is-used](includes/determine-whether-management-over-rest-is-used.md)]

[!INCLUDE [determine-whether-a-connection-to-on-premises-is-needed](includes/determine-whether-a-connection-to-on-premises-is-needed.md)]

[!INCLUDE [determine-whether-jms-queues-or-topics-are-in-use-virtual-machines](includes/determine-whether-jms-queues-or-topics-are-in-use-virtual-machines.md)]

[!INCLUDE [determine-whether-you-are-using-your-own-custom-created-shared-java-ee-libraries](includes/determine-whether-you-are-using-your-own-custom-created-shared-java-ee-libraries.md)]

[!INCLUDE [determine-whether-osgi-bundles-are-used](includes/determine-whether-osgi-bundles-are-used.md)]

[!INCLUDE [determine-whether-your-application-contains-os-specific-code](includes/determine-whether-your-application-contains-os-specific-code.md)]

[!INCLUDE [determine-whether-oracle-service-bus-is-in-use](includes/determine-whether-oracle-service-bus-is-in-use.md)]

[!INCLUDE [determine-whether-your-application-is-composed-of-multiple-wars](includes/determine-whether-your-application-is-composed-of-multiple-wars.md)]

[!INCLUDE [determine-whether-your-application-is-packaged-as-an-ear](includes/determine-whether-your-application-is-packaged-as-an-ear.md)]

[!INCLUDE [identify-all-outside-processes-and-daemons-running-on-the-production-servers](includes/identify-all-outside-processes-and-daemons-running-on-the-production-servers.md)]

[!INCLUDE [determine-whether-wlst-is-used](includes/determine-whether-wlst-is-used.md)]

### <a name="determine-whether-and-how-the-file-system-is-used"></a>Determinar se e como o sistema de arquivos é usado

Os sistemas de arquivos de VMs funcionam da mesma forma que os sistemas de arquivos locais em relação à persistência, inicialização e ao desligamento. Mesmo assim, é importante estar ciente das necessidades do seu sistema de arquivos e garantir que as VMs tenham o tamanho e o desempenho de armazenamento adequados.

[!INCLUDE [static-content](includes/static-content.md)]

[!INCLUDE [determine-the-network-topology](includes/determine-the-network-topology.md)]

[!INCLUDE [account-for-the-use-of-jca-adapters-and-resource-adapters](includes/account-for-the-use-of-jca-adapters-and-resource-adapters.md)]

[!INCLUDE [account-for-the-use-of-custom-security-providers-and-jaas](includes/account-for-the-use-of-custom-security-providers-and-jaas.md)]

[!INCLUDE [determine-whether-weblogic-clustering-is-used](includes/determine-whether-weblogic-clustering-is-used.md)]

[!INCLUDE [determine-whether-the-java-ee-application-client-feature-is-used](includes/determine-whether-the-java-ee-application-client-feature-is-used.md)]

## <a name="migration"></a>Migração

### <a name="select-a-weblogic-on-azure-virtual-machines-offer"></a>Selecionar uma oferta do WebLogic em Máquinas Virtuais do Azure

As ofertas a seguir estão disponíveis para o WebLogic em Máquinas Virtuais do Azure.

Durante a implantação de uma oferta, você deverá escolher o tamanho da Máquina Virtual para seus nós do WebLogic Server. É importante considerar todos os aspectos do dimensionamento (memória, processador, disco) ao escolher o tamanho da VM. Para obter mais informações, confira a [Documentação do Azure para o dimensionamento de máquinas virtuais](/azure/cloud-services/cloud-services-sizes-specs)

#### <a name="weblogic-server-single-node-with-no-admin-server"></a>WebLogic Server de um único nó sem servidor de administração

Esta oferta cria uma única VM e instala o WebLogic nela, mas não configura domínios, o que é útil para cenários em que você tem uma configuração de domínio altamente personalizada.

#### <a name="weblogic-server-single-node-with-admin-server"></a>WebLogic Server de um único nó com servidor de administração

Esta oferta provisiona uma única VM e instala o WebLogic Server nela. Ele cria um domínio e inicializa o servidor de administração.

#### <a name="weblogic-server-n-node-cluster"></a>Cluster de N nós do WebLogic Server

Esta oferta cria um cluster altamente disponível de VMs do WebLogic Server.

#### <a name="weblogic-server-n-node-dynamic-cluster"></a>Cluster dinâmico de N nós do WebLogic Server

Esta oferta cria um cluster dinâmico altamente disponível e escalonável de VMs do WebLogic Server

### <a name="provision-the-offer"></a>Provisionar a oferta

Depois de selecionar a oferta inicial, siga as instruções na [documentação das ofertas](https://docs.oracle.com/en/middleware/standalone/weblogic-server/wlazu/) para provisionar essa oferta. Lembre-se de escolher o nome de domínio que corresponda ao nome de domínio existente. Você pode até mesmo corresponder a senha de domínio com sua senha de domínio existente.

### <a name="migrate-the-domains"></a>Migrar os domínios

Depois de provisionar a oferta, você pode examinar a configuração de domínio e seguir [este guia](https://support.oracle.com/knowledge/Middleware/2336356_1.html) para obter detalhes sobre como migrar os domínios.

### <a name="connect-the-databases"></a>Conectar os bancos de dados

Após migrar os domínios, você poderá conectar os bancos de dados seguindo as instruções [na documentação da oferta](https://wls-eng.github.io/arm-oraclelinux-wls/#connecting-a-database-to-a-cluster). Essas instruções ajudarão você a considerar os segredos de banco de dados e cadeias de caracteres de acesso envolvidos.

### <a name="account-for-keystores"></a>Considerar os repositórios de chaves

Você deve considerar a migração de todos os repositórios de chaves SSL usados pelo seu aplicativo. Para obter mais informações, consulte [Configurando repositórios de chaves](https://docs.oracle.com/en/middleware/fusion-middleware/weblogic-server/12.2.1.4/secmg/identity_trust.html#GUID-7F03EB9C-9755-430B-8B86-17199E0C01DC).

### <a name="connect-the-jms-sources"></a>Conectar as fontes JMS

Depois de conectar os bancos de dados, você pode configurar o JMS seguindo as instruções em [Middleware do Fusion Administrando recursos JMS para o Oracle WebLogic Server](https://docs.oracle.com/middleware/12213/wls/JMSAD/toc.htm) na documentação do WebLogic.

[!INCLUDE [account-for-authentication-and-authorization](includes/account-for-authentication-and-authorization.md)]

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

* Se você implantou o WebLogic Server com o Gateway de Aplicativo do Azure seguindo as etapas em [Tutorial: Migrar um cluster do WebLogic Server para o Azure com o Gateway de Aplicativo do Azure como um balanceador de carga](migrate-weblogic-with-app-gateway.md), talvez você queira fazer configurações adicionais no Gateway de Aplicativo.  Para obter mais informações, confira [Visão geral da configuração do Gateway de Aplicativo ](/azure/application-gateway/configuration-overview).

* Aprimore sua topologia de rede com serviços avançados de balanceamento de carga. Para obter mais informações, consulte [Usando os serviços de balanceamento de carga no Azure](/azure/traffic-manager/traffic-manager-load-balancing-azure).

* Aproveite as identidades gerenciadas do Azure para segredos gerenciados e atribua acesso baseado em função aos recursos do Azure. Para obter mais informações, consulte [O que são identidades gerenciadas para recursos do Azure?](/azure/active-directory/managed-identities-azure-resources/overview).

* Integre a autenticação e autorização do WebLogic Java EE com o Azure Active Directory. Para obter mais informações, consulte o [guia de introdução Integrando o Azure Active Directory](/azure/active-directory/manage-apps/plan-an-application-integration).

* Use o Azure Key Vault para armazenar qualquer informação que funcione como um "segredo". Para saber mais, consulte [Conceitos básicos do Azure Key Vault](/azure/key-vault/basic-concepts).
