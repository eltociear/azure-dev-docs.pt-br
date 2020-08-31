---
title: Migrar aplicativos Spring Cloud para o Azure Spring Cloud
description: Este guia descreve as informações das quais você deve estar ciente quando deseja migrar um aplicativo Spring Cloud existente para ser executado no Azure Spring Cloud.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 2/12/2020
ms.custom: devx-track-java
ms.openlocfilehash: e07bc23a7316352017e1ea99dd87253f0639a559
ms.sourcegitcommit: 95fdc444c424f4a7d7d53437837e9532a0b897e9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88662977"
---
# <a name="migrate-spring-cloud-applications-to-azure-spring-cloud"></a>Migrar aplicativos Spring Cloud para o Azure Spring Cloud

Este guia descreve as informações das quais você deve estar ciente quando deseja migrar um aplicativo Spring Cloud existente para ser executado no Azure Spring Cloud.

## <a name="pre-migration"></a>Pré-migração

Antes de tudo, para garantir uma migração bem-sucedida, conclua as etapas de avaliação e de inventário descritas nas seções a seguir.

Se você não puder atender a nenhum desses requisitos de pré-migração, confira os seguintes guias de migração complementares:

* Migrar aplicativos JAR executáveis para contêineres no Serviço de Kubernetes do Azure (diretrizes planejadas)
* Migrar aplicativos JAR executáveis para Máquinas Virtuais do Azure (diretrizes planejadas)

### <a name="inspect-application-components"></a>Inspecionar os componentes de aplicativo

[!INCLUDE [determine-whether-and-how-the-file-system-is-used-azure-spring-cloud](includes/determine-whether-and-how-the-file-system-is-used-azure-spring-cloud.md)]

#### <a name="determine-whether-any-of-the-services-contain-os-specific-code"></a>Determinar se algum dos serviços contém o código específico do SO

[!INCLUDE [determine-whether-your-application-contains-os-specific-code](includes/determine-whether-your-application-contains-os-specific-code-no-title.md)]

[!INCLUDE [switch-to-a-supported-platform-azure-spring-cloud](includes/switch-to-a-supported-platform-azure-spring-cloud.md)]

[!INCLUDE [identify-spring-boot-versions](includes/identify-spring-boot-versions.md)]

Para os aplicativos que usam o Spring Boot 1.x, siga o [Guia de migração do Spring Boot 2.0](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.0-Migration-Guide) para atualizá-los para uma versão do Spring Boot com suporte. Para obter as versões com suporte, confira [Preparar um aplicativo Spring Java para a implantação](/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment#spring-boot-and-spring-cloud-versions).

#### <a name="identify-spring-cloud-versions"></a>Identificar as versões do Spring Cloud

Examine as dependências de cada aplicativo que você está migrando para determinar a versão dos componentes do Spring Cloud usados.

##### <a name="maven"></a>Maven

Em projetos Maven, a versão do Spring Cloud é normalmente definida na propriedade `spring-cloud.version`:

```xml
  <properties>
    <java.version>1.8</java.version>
    <spring-cloud.version>Hoxton.SR3</spring-cloud.version>
  </properties>
```

##### <a name="gradle"></a>Gradle

Em projetos Gradle, a versão do Spring Cloud é normalmente definida no bloco “propriedades extras”:

```gradle
ext {
  set('springCloudVersion', "Hoxton.SR3")
}
```

Você precisará atualizar todos os aplicativos para usar as versões com suporte do Spring Cloud. Para obter uma lista das versões com suporte, confira [Preparar um aplicativo Spring Java para a implantação](/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment#spring-boot-and-spring-cloud-versions).

[!INCLUDE [identify-logs-metrics-apm-azure-spring-cloud.md](includes/identify-logs-metrics-apm-azure-spring-cloud.md)]

#### <a name="identify-zipkin-dependencies"></a>Identificar as dependências Zipkin

Determine se o aplicativo tem dependências explícitas no Zipkin. Procure dependências no grupo `io.zipkin.java` nas dependências Maven ou Gradle.

### <a name="inventory-external-resources"></a>Recursos externos de inventário

Identifique recursos externos, como fontes de dados, agentes de mensagens JMS e URLs de outros serviços. Em aplicativos Spring Cloud, normalmente é possível encontrar a configuração desses recursos em uma das seguintes localizações:

* Na pasta *src/main/directory*, em um arquivo geralmente chamado de *application.properties* ou de *application.yml*.
* No repositório do Spring Cloud Config identificado na etapa anterior.

[!INCLUDE [inventory-databases-spring-boot](includes/inventory-databases-spring-boot.md)]

[!INCLUDE [identify-jms-brokers-in-spring](includes/identify-jms-brokers-in-spring.md)]

Depois de identificar os agentes em uso, localize as configurações correspondentes. Em aplicativos Spring Cloud, normalmente é possível encontrá-las nos arquivos *application.properties* ou *application.yml* no diretório de aplicativo ou no repositório do servidor do Spring Cloud Config.

[!INCLUDE [jms-broker-settings-examples-in-spring](includes/jms-broker-settings-examples-in-spring.md)]

[!INCLUDE [identify-external-caches-azure-spring-cloud](includes/identify-external-caches-azure-spring-cloud.md)]

#### <a name="identity-providers"></a>Provedores de identidade

Identifique todos os provedores de identidade e todos os aplicativos Spring Cloud que requerem autenticação e/ou autorização. Para obter informações sobre como os provedores de identidade podem ser configurados, confira o seguinte:

* Para obter a configuração do OAuth2, confira o [Guia de início rápido do Spring Cloud Security](https://cloud.spring.io/spring-cloud-security/2.1.x/multi/multi__quickstart.html#_quickstart).
* Para obter a configuração do Spring Security com o Auth0, confira a [Documentação do Spring Security com o Auth0](https://auth0.com/docs/quickstart/backend/java-spring-security5/01-authorization).
* Para obter a configuração do Spring Security com o PingFederate, confira as [Instruções do PingFederate com o Auth0](https://auth0.com/authenticate/java-spring-security/ping-federate/).

#### <a name="resources-configured-through-vmware-tanzu-application-service-tas-formerly-pivotal-cloud-foundry"></a>Recursos configurados por meio do VMware TAS (Tanzu Application Service) (antigo Pivotal Cloud Foundry)

Para os aplicativos gerenciados com o TAS, os recursos externos, incluindo aqueles descritos anteriormente, são geralmente configurados por meio de associações de serviço do TAS. Para examinar a configuração desses recursos, use a [CLI do TAS (Cloud Foundry)](https://docs.cloudfoundry.org/cf-cli/) para exibir a variável `VCAP_SERVICES` do aplicativo.

```bash
# Log into TAS, if needed (enter credentials when prompted)
cf login -a <API endpoint>

# Set the organization and space containing the application, if not already selected during login.
cf target org <organization name>
cf target space <space name>

# Display variables for the application
cf env <Application Name>
```

Examine a variável `VCAP_SERVICES` para obter as definições de configuração de serviços externos associados ao aplicativo. Para obter mais informações, confira a [documentação do TAS (Cloud Foundry)](https://docs.cloudfoundry.org/devguide/deploy-apps/environment-variable.html#VCAP-SERVICES).

#### <a name="all-other-external-resources"></a>Todos os outros recursos externos

É inviável que este guia documente todas as dependências externas possíveis. Após a migração, é sua responsabilidade verificar se você pode satisfazer todas as dependência externas do aplicativo.

[!INCLUDE [inventory-configuration-sources-and-secrets-spring-cloud](includes/inventory-configuration-sources-and-secrets-spring-cloud.md)]

[!INCLUDE [inspect-the-deployment-architecture-spring-cloud](includes/inspect-the-deployment-architecture-spring-cloud.md)]

## <a name="migration"></a>Migração

### <a name="remove-explicit-configuration-server-settings"></a>Remover as configurações explícitas do servidor de configuração

Nos serviços que você está migrando, localize as atribuições explícitas das configurações do Eureka e remova-as. Essas configurações normalmente aparecem nos arquivos *application.properties* ou *application.yml*:

**application.yml**

```yaml
eureka:
  client:
    serviceUrl:
      defaultZone: http://myusername:mysecretpassword@localhost:8761/eureka/
```

Se uma configuração como essa aparecer na configuração de aplicativo, remova-a. O Azure Spring Cloud injetará automaticamente as informações de conexão do servidor de configuração.

### <a name="create-an-azure-spring-cloud-instance-and-apps"></a>Criar uma instância e aplicativos do Azure Spring Cloud

Provisione uma instância do Azure Spring Cloud na assinatura do Azure. Em seguida, provisione um aplicativo para cada serviço que você está migrando. Não inclua o registro e os servidores de configuração do Spring Cloud. Inclua o serviço Spring Cloud Gateway. Para obter instruções, confira [Início Rápido: Iniciar um aplicativo existente do Azure Spring Cloud usando o portal do Azure](/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal).

### <a name="prepare-the-spring-cloud-config-server"></a>Preparar o servidor do Spring Cloud Config

Configure o servidor de configuração na instância do Azure Spring Cloud. Para saber mais, confira [Tutorial: Configurar uma instância do Servidor de Configuração do Spring Cloud para seu serviço](/azure/spring-cloud/spring-cloud-tutorial-config-server).

> [!NOTE]
> Se o repositório Spring Cloud Config atual estiver no local ou no sistema de arquivos local, você precisará replicar os arquivos de configuração e migrá-los para um repositório privado baseado em nuvem, como o GitHub, o Azure Repos ou o BitBucket.

[!INCLUDE [ensure-console-logging-and-configure-diagnostic-settings-azure-spring-cloud](includes/ensure-console-logging-and-configure-diagnostic-settings-azure-spring-cloud.md)]

[!INCLUDE [configure-persistent-storage-azure-spring-cloud](includes/configure-persistent-storage-azure-spring-cloud.md)]

### <a name="migrate-spring-cloud-vault-secrets-to-azure-keyvault"></a>Migrar segredos do Spring Cloud Vault para o Azure Key Vault

Você pode injetar segredos diretamente em aplicativos por meio do Spring usando o iniciador do Spring Boot para o Azure Key Vault. Para obter mais informações, confira [Como usar o iniciador do Spring Boot para o Azure Key Vault](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-azure-key-vault).

> [!NOTE]
> A migração pode requerer que você renomeie alguns segredos. Atualize o código de aplicativo de acordo.

### <a name="migrate-all-certificates-to-keyvault"></a>Migrar todos os certificados para o Key Vault

O Azure Spring Cloud não fornece acesso ao repositório de chaves JRE. Portanto, migre os certificados para o Azure Key Vault e altere o código de aplicativo para acessar os certificados no Key Vault. Para obter mais informações, confira [Introdução aos certificados do Key Vault](/azure/key-vault/certificates/certificate-scenarios) e [Biblioteca de clientes de certificados do Azure Key Vault para Java](/java/api/overview/azure/security-keyvault-certificates-readme).

### <a name="remove-application-performance-management-apm-integrations"></a>Remover as integrações de APM (gerenciamento de desempenho de aplicativos)

Elimine as integrações com ferramentas/agentes de APM. Para obter informações sobre como configurar o gerenciamento de desempenho com o Azure Monitor, confira a seção [Pós-migração](#post-migration).

### <a name="replace-explicit-zipkin-dependencies-with-spring-cloud-starters"></a>Substituir as dependências Zipkin explícitas pelos iniciadores do Spring Cloud

Se qualquer um dos aplicativos migrados tiver dependências Zipkin explícitas, remova-as e substitua-as pelos iniciadores do Spring Cloud, conforme descrito na seção [Dependência de rastreamento distribuído](/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment#distributed-tracing-dependency) de [Preparar um aplicativo Spring Java para a implantação no Azure Spring Cloud](/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment). Para obter informações sobre o rastreamento distribuído com o Application Insights do Azure, confira a seção [Pós-migração](#post-migration).

### <a name="disable-metrics-clients-and-endpoints-in-your-applications"></a>Desabilitar os clientes e os pontos de extremidade de métricas nos aplicativos

Remova os clientes de métricas usados ou os pontos de extremidade de métricas expostos nos aplicativos.

### <a name="deploy-the-services"></a>Implantar os serviços

Implante cada um dos microsserviços migrados (não incluindo os servidores do Spring Cloud Config e Registry), conforme descrito no [Início Rápido: Iniciar um aplicativo existente do Azure Spring Cloud usando o portal do Azure](/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal).

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
* Se o provedor de identidade for uma floresta local do Active Directory, considere implementar uma solução de identidade híbrida com o Azure Active Directory. Para obter diretrizes, confira a [Documentação de identidade híbrida](/azure/active-directory/hybrid/).
* Se o provedor de identidade for outra solução local, como o PingFederate, confira o tópico [Instalação personalizada do Azure AD Connect](/azure/active-directory/hybrid/how-to-connect-install-custom) para configurar a federação com o Azure Active Directory. Como alternativa, considere o Spring Security para o uso do provedor de identidade por meio do [OAuth2/OpenID Connect](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#oauth2) ou do [SAML](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-saml2).

### <a name="update-client-applications"></a>Atualizar aplicativos cliente

Atualize a configuração de todos os aplicativos cliente para usar os pontos de extremidade publicados do Azure Spring Cloud para aplicativos migrados.

## <a name="post-migration"></a>Após a migração

* Considere adicionar um pipeline de implantação para implantações automáticas e consistentes. As instruções estão disponíveis [Para o Azure Pipelines](/azure/spring-cloud/spring-cloud-howto-cicd), [Para o GitHub Actions](/azure/spring-cloud/spring-cloud-howto-github-actions) e [Para o Jenkins](/azure/jenkins/tutorial-jenkins-deploy-cli-spring-cloud-service).

* Considere usar implantações de preparo para testar alterações de código em produção antes que elas sejam disponibilizadas para alguns ou todos os usuários finais. Para obter mais informações, confira [Configurar um ambiente de preparo no Azure Spring Cloud](/azure/spring-cloud/spring-cloud-howto-staging-environment).

* Considere adicionar associações de serviço para conectar o aplicativo a bancos de dados do Azure com suporte. Essas associações de serviço eliminariam a necessidade de fornecer informações de conexão, incluindo credenciais, para os aplicativos Spring Cloud.

* Considere [usar o rastreamento distribuído e o Application Insights do Azure](/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing) para monitorar o desempenho e as interações dos aplicativos.

* Considere adicionar as regras de alerta e os grupos de ações do Azure Monitor para detectar e abordar as condições anormais rapidamente. Para saber mais, confira [Tutorial: Monitorar recursos do Spring Cloud usando alertas e grupos de ações](/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups).

* Considere replicar a implantação do Azure Spring Cloud em outra região para obter latência mais baixa, maior confiabilidade e tolerância a falhas. Use o [Gerenciador de Tráfego do Azure](/azure/traffic-manager) para balancear a carga entre implantações ou o [Azure Front Door](/azure/frontdoor) para adicionar o descarregamento de SSL e o firewall do aplicativo Web com proteção contra DDoS.

* Se a replicação geográfica não for necessária, considere adicionar um [Gateway de Aplicativo do Azure](/azure/application-gateway) para adicionar o descarregamento de SSL e o Firewall do aplicativo Web com proteção contra DDoS.

* Se os aplicativos usarem componentes herdados do Spring Cloud Netflix, considere substituí-los por alternativas atuais:

   | Herdada                        | Current                                                |
   |-------------------------------|--------------------------------------------------------|
   | Spring Cloud Eureka           | Spring Cloud Service Registry                          |
   | Spring Cloud Netflix Zuul     | Spring Cloud Gateway                                   |
   | Spring Cloud Netflix Archaius | Servidor de Configuração do Spring Cloud                             |
   | Spring Cloud Netflix Ribbon   | Spring Cloud Load Balancer (balanceador de carga do lado do cliente) |
   | Spring Cloud Hystrix          | Spring Cloud Circuit Breaker + Resilience4j            |
   | Spring Cloud Netflix Turbine  | Micrometer + Prometheus                                |
