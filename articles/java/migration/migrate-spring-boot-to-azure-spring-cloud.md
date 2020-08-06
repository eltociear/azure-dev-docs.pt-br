---
title: Migrar aplicativos Spring Boot para o Azure Spring Cloud
description: Este guia descreve as informações das quais você deve estar ciente quando deseja migrar um aplicativo Spring Boot existente para execução no Azure Spring Cloud.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 5/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: 7bc4a5188181f3b4b6d98b5308a5027a42bbac5e
ms.sourcegitcommit: b224b276a950b1d173812f16c0577f90ca2fbff4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87810579"
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

### <a name="create-an-azure-spring-cloud-instance-and-apps"></a>Criar uma instância e aplicativos do Azure Spring Cloud

Provisione uma instância do Azure Spring Cloud na sua assinatura do Azure, se não houver uma. Em seguida, crie um aplicativo nela. Para saber mais, confira [Início Rápido: Iniciar um aplicativo existente do Azure Spring Cloud usando o portal do Azure](/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal).

[!INCLUDE [ensure-console-logging-and-configure-diagnostic-settings-azure-spring-cloud](includes/ensure-console-logging-and-configure-diagnostic-settings-azure-spring-cloud.md)]

[!INCLUDE [configure-persistent-storage-azure-spring-cloud](includes/configure-persistent-storage-azure-spring-cloud.md)]

### <a name="migrate-all-certificates-to-keyvault"></a>Migrar todos os certificados para o Key Vault

O Azure Spring Cloud não fornece acesso ao repositório de chaves JRE. Portanto, migre os certificados para o Azure Key Vault e altere o código de aplicativo para acessar os certificados no Key Vault. Para obter mais informações, confira [Introdução aos certificados do Key Vault](/azure/key-vault/certificates/certificate-scenarios) e [Biblioteca de clientes de certificados do Azure Key Vault para Java](/java/api/overview/azure/security-keyvault-certificates-readme).

### <a name="remove-application-performance-management-apm-integrations"></a>Remover as integrações de APM (gerenciamento de desempenho de aplicativos)

Elimine as integrações com ferramentas/agentes de APM. Para obter informações sobre como configurar o gerenciamento de desempenho com o Azure Monitor, confira a seção [Pós-migração](#post-migration).

### <a name="disable-metrics-clients-and-endpoints-in-your-applications"></a>Desabilitar os clientes e os pontos de extremidade de métricas nos aplicativos

Remova os clientes de métricas usados ou os pontos de extremidade de métricas expostos nos aplicativos.

### <a name="deploy-the-application"></a>Implantar o aplicativo

Implante cada um dos microsserviços migrados (não incluindo os servidores de Configuração e Registro do Spring Cloud), conforme descrito no [Guia de Início Rápido: Iniciar um aplicativo existente do Azure Spring Cloud usando o portal do Azure](/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal).

### <a name="configure-per-service-secrets-and-externalized-settings"></a>Configurar os segredos por serviço e definir as configurações externas

Você pode injetar as definições de configuração por serviço em cada serviço como variáveis de ambiente. Use as seguintes etapas no portal do Azure:

1. Navegue até a instância do Azure Spring Cloud e selecione **Aplicativos**.
1. Escolha o serviço a ser configurado.
1. Selecione **Configuração**.
1. Insira as variáveis para a configuração.
1. Clique em **Salvar**.

![Definições de Configuração de Aplicativos do Spring Cloud](media/migrate-spring-cloud-to-azure-spring-cloud/spring-cloud-app-configuration-settings.png)

### <a name="migrate-and-enable-the-identity-provider"></a>Migrar e habilitar o provedor de identidade

Se qualquer um dos aplicativos Spring Cloud requerer autenticação ou autorização, verifique se eles estão configurados para acessar o provedor de identidade:

* Se o provedor de identidade for o Azure Active Directory, nenhuma alteração será necessária.
* Se o provedor de identidade for uma floresta local do Active Directory, considere implementar uma solução de identidade híbrida com o Azure Active Directory. Para obter mais informações, confira a [Documentação sobre identidade híbrida](/azure/active-directory/hybrid/).
* Se o provedor de identidade for outra solução local, como o PingFederate, confira o tópico [Instalação personalizada do Azure AD Connect](/azure/active-directory/hybrid/how-to-connect-install-custom) para configurar a federação com o Azure Active Directory. Como alternativa, considere o Spring Security para o uso do provedor de identidade por meio do [OAuth2/OpenID Connect](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#oauth2) ou do [SAML](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-saml2).

### <a name="expose-the-application"></a>Expor o aplicativo

Por padrão, os aplicativos implantados no Azure Spring Cloud não são visíveis externamente. Você pode expor seu aplicativo tornando-o público com o seguinte comando:

```azurecli
az spring-cloud app update -n <application name> --is-public true
```

Ignore esta etapa se estiver usando um Spring Cloud Gateway ou pretender usá-lo (saiba mais sobre isso na seção a seguir).

## <a name="post-migration"></a>Após a migração

Agora que você concluiu a migração, confirme se o aplicativo funciona conforme o esperado. Em seguida, você pode tornar seu aplicativo mais nativo de nuvem usando as recomendações a seguir.

* Considere a possibilidade de habilitar seu aplicativo para que ele funcione com o Registro do Spring Cloud. Isso permitirá que o aplicativo seja descoberto dinamicamente por outros microsserviços e clientes implantados. Para saber mais, confira [Tutorial: Preparar um aplicativo Spring Java para implantação](/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment). Em seguida, modifique todos os clientes do aplicativo para que eles usem o balanceador de carga do Cliente Spring. Isso permite que o cliente obtenha endereços de todas as instâncias do aplicativo em execução e localize uma instância que funcionará se outra instância for corrompida ou ficar sem resposta. Para obter mais informações, confira [Dicas do Spring: Balanceador de carga do Spring Cloud](https://spring.io/blog/2020/03/25/spring-tips-spring-cloud-loadbalancer) no blog do Spring.

* Em vez de tornar seu aplicativo público, considere a possibilidade de adicionar uma instância do [Spring Cloud Gateway](https://cloud.spring.io/spring-cloud-gateway/reference/html/). O Spring Cloud Gateway fornece um só ponto de extremidade para todos os aplicativos/microsserviços implantados na sua instância do Azure Spring Cloud. Se um Spring Cloud Gateway já estiver implantado, verifique se ele está configurado para rotear o tráfego para o aplicativo recém-implantado.

* Considere a possibilidade de adicionar um Config Server do Spring Cloud para gerenciar de maneira centralizada a configuração e fazer o controle de versão dela para todos os microsserviços do Spring Cloud. Primeiro, crie um repositório Git para hospedar a configuração e configure a instância do Azure Spring Cloud para usá-la. Para saber mais, confira [Tutorial: Configurar uma instância do Servidor de Configuração do Spring Cloud para seu serviço](/azure/spring-cloud/spring-cloud-tutorial-config-server). Em seguida, migre sua configuração usando as seguintes etapas:

  1. Crie um diretório no repositório Git de configuração com o mesmo nome do aplicativo que você definiu na instância do Azure Spring Cloud.

  1. Nesse diretório, crie um arquivo *bootstrap.yml* com o seguinte conteúdo:

     ```yml
     spring:
       application:
         name: <Your the application name used in the previous step>
     ```

  1. Crie um arquivo *application.yml* dentro do diretório acima e mova as configurações do aplicativo para ele. Se as configurações estiverem anteriormente em um arquivo *.properties*, elas precisarão ser convertidas em YAML.

  1. Faça commit dessas alterações e efetue push delas para o repositório Git.

* Considere adicionar um pipeline de implantação para implantações automáticas e consistentes. As instruções estão disponíveis [Para o Azure Pipelines](/azure/spring-cloud/spring-cloud-howto-cicd), [Para o GitHub Actions](/azure/spring-cloud/spring-cloud-howto-github-actions) e [Para o Jenkins](/azure/jenkins/tutorial-jenkins-deploy-cli-spring-cloud-service).

* Considere usar implantações de preparo para testar alterações de código em produção antes que elas sejam disponibilizadas para alguns ou todos os usuários finais. Para obter mais informações, confira [Configurar um ambiente de preparo no Azure Spring Cloud](/azure/spring-cloud/spring-cloud-howto-staging-environment).

* Considere adicionar associações de serviço para conectar o aplicativo a bancos de dados do Azure com suporte. Essas associações de serviço eliminariam a necessidade de fornecer informações de conexão, incluindo credenciais, para os aplicativos Spring Cloud.

* Considere a possibilidade de usar o rastreamento distribuído e o Azure App Insights para monitorar o desempenho e as interações dos seus aplicativos. Para obter mais informações, confira [Usar o rastreamento distribuído com o Azure Spring Cloud](/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing).

* Considere adicionar as regras de alerta e os grupos de ações do Azure Monitor para detectar e abordar as condições anormais rapidamente. Para saber mais, confira [Tutorial: Monitorar recursos do Spring Cloud usando alertas e grupos de ações](/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups).

* Considere replicar a implantação do Azure Spring Cloud em outra região para obter latência mais baixa, maior confiabilidade e tolerância a falhas. Use o [Gerenciador de Tráfego do Azure](/azure/traffic-manager) para balancear a carga entre implantações ou o [Azure Front Door](/azure/frontdoor) para adicionar o descarregamento de SSL e o firewall do aplicativo Web com proteção contra DDoS.

* Se a replicação geográfica não for necessária, considere adicionar um [Gateway de Aplicativo do Azure](/azure/application-gateway) para adicionar o descarregamento de SSL e o Firewall do aplicativo Web com proteção contra DDoS.
