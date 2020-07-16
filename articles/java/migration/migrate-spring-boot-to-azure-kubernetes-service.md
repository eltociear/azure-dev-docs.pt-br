---
title: Migrar aplicativos Spring Boot para ser executados no Serviço de Kubernetes do Azure
description: Este guia descreve as informações das quais você deve estar ciente quando deseja migrar um aplicativo Spring Boot existente para ser executado em um contêiner do Serviço de Kubernetes do Azure.
author: mriem
ms.author: manriem
ms.topic: conceptual
ms.date: 4/10/2020
ms.custom: devx-track-java
ms.openlocfilehash: 37abbaf978aabad22b8aa1200bcde2e2ba2051e3
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/14/2020
ms.locfileid: "86379750"
---
# <a name="migrate-spring-boot-applications-to-azure-kubernetes-service"></a>Migrar aplicativos Spring Boot para o Serviço de Kubernetes do Azure

Este guia descreve as informações das quais você deve estar ciente quando deseja migrar um aplicativo Spring Boot existente para ser executado no AKS (Serviço de Kubernetes do Azure).

## <a name="pre-migration"></a>Pré-migração

Antes de tudo, para garantir uma migração bem-sucedida, conclua as etapas de avaliação e de inventário descritas nas seções a seguir.

### <a name="validate-that-the-supported-java-version-works-correctly"></a>Validar se a versão Java com suporte funciona corretamente

É recomendável usar uma versão com suporte do Java ao executar um aplicativo Spring Boot no AKS. Confirme se o aplicativo é executado corretamente usando essa versão com suporte.

[!INCLUDE [note-obtain-your-current-java-version](includes/note-obtain-your-current-java-version.md)]

### <a name="determine-whether-and-how-the-file-system-is-used"></a>Determinar se e como o sistema de arquivos é usado

Qualquer uso do sistema de arquivos pelo aplicativo Spring Boot requererá reconfiguração ou, em casos raros, alterações de arquitetura. Você pode identificar alguns ou todos os cenários descritos nas seções a seguir.

[!INCLUDE [static-content](includes/static-content.md)]

[!INCLUDE [dynamic-or-internal-content-aks](includes/dynamic-or-internal-content-aks.md)]

[!INCLUDE [determine-whether-your-application-relies-on-scheduled-jobs](includes/determine-whether-your-application-relies-on-scheduled-jobs.md)]

[!INCLUDE [determine-whether-a-connection-to-on-premises-is-needed](includes/determine-whether-a-connection-to-on-premises-is-needed.md)]

[!INCLUDE [determine-whether-your-application-contains-os-specific-code](includes/determine-whether-your-application-contains-os-specific-code.md)]

[!INCLUDE [identify-all-outside-processes-and-daemons-running-on-the-production-servers](includes/identify-all-outside-processes-and-daemons-running-on-the-production-servers.md)]

[!INCLUDE [identify-spring-boot-versions](includes/identify-spring-boot-versions.md)]

Para os aplicativos que usam o Spring Boot 1.x, siga o [Guia de migração do Spring Boot 2.0](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.0-Migration-Guide) para atualizá-los para uma versão do Spring Boot com suporte.

### <a name="review-your-database-properties"></a>Examinar as propriedades de banco de dados

Se o aplicativo usar um banco de dados, examine as propriedades dele no arquivo *application.properties* para verificar se o aplicativo Spring Boot ainda pode acessar o banco de dados após a migração para o AKS. Se o banco de dados for local, você precisará migrá-lo para a nuvem ou estabelecer conectividade com o banco de dados local.

### <a name="identify-log-aggregation-solutions"></a>Identificar as soluções de agregação de log

Identifique as soluções de agregação de log em uso por meio dos aplicativos que você está migrando.

### <a name="identify-application-performance-management-apm-agents"></a>Identificar os agentes de APM (gerenciamento de desempenho de aplicativo)

Identifique os agentes de Monitoramento do desempenho de aplicativos em uso com os aplicativos (como Dynatrace e Datadog). Você precisará reconfigurar esses agentes APM para serem incluídos em uma configuração do Dockerfile ou do Jib ou para usar o agente Java em processo do Application Insights.

### <a name="identify-zipkin-dependencies"></a>Identificar as dependências Zipkin

Determine se o aplicativo tem dependências explícitas no Zipkin. Procure dependências no grupo `io.zipkin.java` nas dependências Maven ou Gradle.

### <a name="inventory-external-resources"></a>Recursos externos de inventário

Identifique recursos externos, como fontes de dados, agentes de mensagens JMS e URLs de outros serviços. Em aplicativos Spring Boot, normalmente é possível encontrar a configuração desses recursos na pasta *src/main/directory*, em um arquivo geralmente chamado de *application.properties* ou de *application.yml*. Além disso, verifique as variáveis de ambiente da implantação de produção para ver as definições de configuração pertinentes.

[!INCLUDE [inventory-databases-spring-boot](includes/inventory-databases-spring-boot.md)]

[!INCLUDE [identify-jms-brokers-in-spring](includes/identify-jms-brokers-in-spring.md)]

Depois de identificar os agentes em uso, localize as configurações correspondentes. Em aplicativos Spring Boot, normalmente é possível encontrá-las nos arquivos *application.properties* e *application.yml* no diretório de aplicativo.

[!INCLUDE [jms-broker-settings-examples-in-spring](includes/jms-broker-settings-examples-in-spring.md)]

[!INCLUDE [identify-external-caches-azure-spring-cloud](includes/identify-external-caches-azure-spring-cloud.md)]

[!INCLUDE [inventory-configuration-sources-and-secrets-spring-boot](includes/inventory-configuration-sources-and-secrets-spring-boot.md)]

[!INCLUDE [inspect-the-deployment-architecture-spring-boot](includes/inspect-the-deployment-architecture-spring-boot.md)]

#### <a name="identity-providers"></a>Provedores de identidade

Identifique todos os provedores de identidade e todos os aplicativos Spring Boot que requerem autenticação e/ou autorização. Para obter informações sobre como os provedores de identidade podem ser configurados, confira o seguinte:

* Para obter a configuração do Spring Security com o OAuth ou com o OAuth2, confira [Spring Security](https://spring.io/projects/spring-security).
* Para obter a configuração do Spring Security com o Auth0, confira a [Documentação do Spring Security com o Auth0](https://auth0.com/docs/quickstart/backend/java-spring-security5/01-authorization).
* Para obter a configuração do Spring Security com o PingFederate, confira as [Instruções do PingFederate com o Auth0](https://auth0.com/authenticate/java-spring-security/ping-federate/).

#### <a name="resources-configured-through-pivotal-cloud-foundry-pcf"></a>Recursos configurados por meio do PCF (Pivotal Cloud Foundry)

Para os aplicativos gerenciados com o PCF, os recursos externos, incluindo os descritos anteriormente, são geralmente configurados por meio de associações de serviço do PCF. Para examinar a configuração desses recursos, use a [CLI do Cloud Foundry](https://docs.cloudfoundry.org/cf-cli/) para exibir a variável `VCAP_SERVICES` do aplicativo.

```bash
# Log into PCF, if needed (enter credentials when prompted)
cf login -a <API endpoint>

# Set the organization and space containing the application, if not already selected during login.
cf target org <Organization Name>
cf target space <Space Name>

# Display variables for the application
cf env <Application Name>
```

Examine a variável `VCAP_SERVICES` para obter as definições de configuração de serviços externos associados ao aplicativo. Para obter mais informações, confira [Documentação do PCF](https://docs.cloudfoundry.org/devguide/deploy-apps/environment-variable.html#VCAP-SERVICES).

### <a name="in-place-testing"></a>Teste in-loco

Antes de criar imagens de contêiner, migre o aplicativo para o JDK e a versão do Spring Boot que você pretende usar no AKS. Teste seu aplicativo cuidadosamente para garantir a compatibilidade e o desempenho.

## <a name="migration"></a>Migração

[!INCLUDE [provision-azure-container-registry-and-azure-kubernetes-service](includes/provision-azure-container-registry-and-azure-kubernetes-service.md)]

### <a name="create-a-docker-image-for-spring-boot"></a>Criar uma imagem do Docker para o Spring Boot

Para criar um Dockerfile, você precisará dos seguintes pré-requisitos:

* Um JDK com suporte.
* Suas opções de runtime do JVM.
* Uma maneira de passar variáveis de ambiente (se aplicável).

Em seguida, você pode realizar as etapas descritas nas seções a seguir, quando aplicáveis. Você pode usar o [Repositório de guias de início rápido de contêineres do Spring Boot](https://github.com/Azure/spring-boot-container-quickstart) como um ponto de partida para o Dockerfile e o aplicativo Spring Boot.

#### <a name="configure-keyvault-flexvolume"></a>Configurar FlexVolume do KeyVault

Crie um Azure KeyVault e preencha todos os segredos necessários. Para saber mais, confira [Início Rápido: Definir e recuperar um segredo do Azure Key Vault usando a CLI do Azure](/azure/key-vault/quick-create-cli). Em seguida, configure um [FlexVolume do KeyVault](https://github.com/Azure/kubernetes-keyvault-flexvol/blob/master/README.md) para tornar esses segredos acessíveis para pods.

Você também precisará atualizar o script de inicialização usado para inicializar o aplicativo Spring Boot. Esse script deve importar os certificados para o repositório de chaves usado pelo Spring Boot antes de iniciar o aplicativo.

### <a name="build-and-push-the-docker-image-to-azure-container-registry"></a>Compilar e efetuar push da imagem do Docker para o Registro de Contêiner do Azure

Depois de criar o Dockerfile, você precisará compilar a imagem do Docker e publicá-la no seu registro de contêiner do Azure.

Se você tiver usado nosso [Repositório GitHub Guia de início rápido de contêineres Spring Boot](https://github.com/Azure/spring-boot-container-quickstart), o processo de criação e de envio por push da imagem para o Registro de Contêiner do Azure será equivalente à invocação dos três comandos a seguir.

Nesses exemplos, a variável de ambiente `MY_ACR` contém o nome do registro de contêiner do Azure e a variável `MY_APP_NAME` contém o nome do aplicativo Web que você deseja usar em seu registro de contêiner do Azure.

Crie o arquivo de implantação:

```bash
mvn package
```

Fazer logon no em seu registro de contêiner do Azure:

```azurecli
az acr login -n ${MY_ACR}
```

Compilar e efetuar push da imagem:

```azurecli
az acr build -t ${MY_ACR}.azurecr.io/${MY_APP_NAME} .
```

Como alternativa, você pode usar a CLI do Docker para compilar e testar a imagem localmente, conforme mostrado nos comandos a seguir. Essa abordagem pode simplificar o teste e refinar a imagem antes da implantação inicial no ACR. No entanto, ela requer que você instale a CLI do Docker e verifique se o daemon do Docker está em execução.

Compilar a imagem:

```bash
docker build -t ${MY_ACR}.azurecr.io/${MY_APP_NAME}
```

Executar a imagem localmente:

```bash
docker run -it -p 8080:8080 ${MY_ACR}.azurecr.io/${MY_APP_NAME}
```

Agora você pode acessar o aplicativo em `http://localhost:8080`.

Fazer logon no em seu registro de contêiner do Azure:

```azurecli
az acr login -n ${MY_ACR}
```

Efetuar push da imagem ao registro de contêiner do Azure:

```bash
docker push ${MY_ACR}.azurecr.io/${MY_APP_NAME}
```

Para obter informações mais detalhadas sobre como compilar e armazenar imagens de contêiner no Azure, confira o módulo do Learn [Compilar e armazenar imagens de contêiner com o Registro de Contêiner do Azure](/learn/modules/build-and-store-container-images/).

Se usou nosso [Repositório do GitHub de guias de início rápido de contêineres do Spring Boot](https://github.com/Azure/spring-boot-container-quickstart), você também pode incluir um repositório de chaves personalizado que será adicionado à JVM após a inicialização. Essa adição ocorrerá se você colocar o arquivo do repositório de chaves em */opt/spring-boot/mycert.crt*. Você pode fazer isso adicionando o arquivo diretamente ao Dockerfile ou usando um FlexVolume do Key Vault, conforme mencionado anteriormente.

Se usou nosso [Repositório do GitHub de guias de início rápido de contêineres do Spring Boot](https://github.com/Azure/spring-boot-container-quickstart), você também pode habilitar o Application Insights configurando a variável de ambiente `APPLICATIONINSIGHTS_CONNECTION_STRING` no arquivo de implantação do Kubernetes (o valor da variável de ambiente deveria parecer `InstrumentationKey=00000000-0000-0000-0000-000000000000`). Para obter mais informações, confira [Monitoramento de aplicativos sem código Java com o Application Insights do Azure Monitor](/azure/azure-monitor/app/java-in-process-agent).

Se você não precisar de nenhuma personalização da imagem do Docker, poderá explorar o uso do [Plug-in Jib do Maven](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin) ou implantá-la no AKS. Para obter mais informações, confira [Implantar um aplicativo Spring Boot no Serviço de Kubernetes do Azure](/azure/developer/java/spring-framework/deploy-spring-boot-java-app-on-kubernetes).

[!INCLUDE [provision-a-public-ip-address](includes/provision-a-public-ip-address.md)]

### <a name="deploy-to-aks"></a>Implantar no AKS

Criar e aplicar seus arquivos YAML do Kubernetes. Para saber mais, confira [Início Rápido: Implantar um cluster do Serviço de Kubernetes do Azure usando a CLI do Azure](/azure/aks/kubernetes-walkthrough#run-the-application). Se você estiver criando um balanceador de carga externo (seja para seu aplicativo ou para um controlador de entrada), será necessário fornecer o endereço IP provisionado na seção anterior como o `LoadBalancerIP`.

Inclua parâmetros externos como variáveis de ambiente. Para saber mais, confira [Definir variáveis de ambiente para um contêiner](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/).

Inclua as configurações de memória e CPU ao criar seu YAML de implantação para que os contêineres sejam dimensionados corretamente.

### <a name="ensure-console-logging-and-configure-diagnostic-settings"></a>Verificar o registro em log de console e definir as configurações de diagnóstico

Configure o registro em log para que todos os aplicativos registrem no console e não em arquivos.

Após a implantação de um aplicativo no Serviço de Kubernetes do Azure, você pode exibir os logs usando `kubectl`.

#### <a name="logstashelk-stack"></a>LogStash/ELK Stack

Se você usar o LogStash/ELK Stack para a agregação de log, defina a configuração de diagnóstico para transmitir a saída de console para um [hub de eventos do Azure](/azure/event-hubs/). Em seguida, use o [Plug-in do LogStash para Hub de Eventos](https://github.com/logstash-plugins/logstash-input-azure_event_hubs) a fim de ingerir eventos registrados no LogStash.

#### <a name="splunk"></a>Splunk

Se você usar o Splunk para a agregação de log, defina a configuração de diagnóstico para transmitir a saída de console para o [Armazenamento de Blobs do Azure](/azure/storage/blobs/). Em seguida, use o [Complemento do Splunk para os serviços em nuvem da Microsoft](https://splunkbase.splunk.com/app/3757/) para ingerir eventos registrados no Splunk.

### <a name="migrate-and-enable-the-identity-provider"></a>Migrar e habilitar o provedor de identidade

Se qualquer um dos aplicativos Spring Boot requerer autenticação ou autorização, verifique se eles estão configurados para acessar o provedor de identidade:

* Se o provedor de identidade for o Azure Active Directory, nenhuma alteração será necessária.
* Se o provedor de identidade for uma floresta local do Active Directory, considere implementar uma solução de identidade híbrida com o Azure Active Directory. Para obter diretrizes, confira a [Documentação de identidade híbrida](/azure/active-directory/hybrid/).
* Se o provedor de identidade for outra solução local, como o PingFederate, confira o tópico [Instalação personalizada do Azure AD Connect](/azure/active-directory/hybrid/how-to-connect-install-custom) para configurar a federação com o Azure Active Directory. Como alternativa, considere o Spring Security para o uso do provedor de identidade por meio do [OAuth2/OpenID Connect](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#oauth2) ou do [SAML](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-saml2).

### <a name="configure-persistent-storage"></a>Configurar um armazenamento persistente

Se seu aplicativo exigir armazenamento não volátil, configure um ou mais [Volumes Persistentes](/azure/aks/azure-disks-dynamic-pv).

[!INCLUDE [migrate-scheduled-jobs-aks](includes/migrate-scheduled-jobs-aks.md)]

## <a name="post-migration"></a>Após a migração

Agora que você migrou seu aplicativo para o AKS, você deve verificar se ele funciona conforme o esperado. Depois de fazer isso, temos algumas recomendações para você que podem tornar seu aplicativo mais nativo de nuvem.

### <a name="recommendations"></a>Recomendações

* Considere adicionar um nome DNS ao endereço IP alocado ao controlador de entrada ou ao balanceador de carga do aplicativo. Para obter mais informações, confira [Criar um controlador de entrada com um endereço IP público estático no AKS](/azure/aks/ingress-static-ip).

* Considere adicionar [gráficos HELM](https://helm.sh/docs/topics/charts/) ao aplicativo. Um gráfico do Helm permite que você parametrize a implantação do aplicativo para uso e personalização por um conjunto mais diversificado de clientes.

* Crie e implemente uma estratégia de DevOps. Para manter a confiabilidade, aumentando simultaneamente a velocidade de desenvolvimento, considere automatizar implantações e testar com Azure Pipelines. Para obter mais informações, confira [Criar e implantar no AKS](/azure/devops/pipelines/ecosystems/kubernetes/aks-template).

* Habilite o [Monitoramento do Azure para o cluster](/azure/azure-monitor/insights/container-insights-enable-existing-clusters) a fim de permitir a coleta de logs de contêiner, acompanhar o uso e assim por diante.

* Considere a possibilidade de expor métricas específicas do aplicativo por meio do Prometheus. O Prometheus é uma estrutura de métricas open-source amplamente adotada na comunidade do Kubernetes. Você pode configurar o [recorte de métricas do Prometheus no Azure Monitor](/azure/azure-monitor/insights/container-insights-prometheus-integration), em vez de hospedar seu próprio servidor do Prometheus para habilitar a agregação de métricas de seus aplicativos e resposta automatizada para condições anormais ou escalonamento dessas condições.

* Criar a implementar uma estratégia de continuidade dos negócios e recuperação de desastre. Para aplicativos críticos, considere a possibilidade de [usar uma arquitetura de implantação em várias regiões](/azure/aks/operator-best-practices-multi-region).

* Examine a [política de suporte a versão do Kubernetes](/azure/aks/supported-kubernetes-versions#kubernetes-version-support-policy). É sua responsabilidade continuar [atualizando seu cluster do AKS](/azure/aks/upgrade-cluster) para garantir que você esteja sempre executando uma versão compatível.

* Faça com que todos os membros da equipe responsáveis pela administração de clusters e pelo desenvolvimento de aplicativos [examinem as práticas recomendadas do AKS](/azure/aks/best-practices).

* Verifique se o arquivo de implantação especifica como as atualizações sem interrupção são feitas. Para obter mais informações, confira [Implantação de atualização sem interrupção](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#rolling-update-deployment) na documentação do Kubernetes.

* Configure o dimensionamento automático para lidar com as cargas em horário de pico. Para obter mais informações, confira [Dimensionar automaticamente um cluster para atender às demandas de aplicativo no AKS](/azure/aks/cluster-autoscaler).

* Considere a possibilidade de [monitorar o tamanho do cache de código](https://docs.oracle.com/javase/8/embedded/develop-apps-platforms/codecache.htm) e adicione os parâmetros `-XX:InitialCodeCacheSize` e `-XX:ReservedCodeCacheSize` à variável `JAVA_OPTS` no Dockerfile para otimizar ainda mais o desempenho.
