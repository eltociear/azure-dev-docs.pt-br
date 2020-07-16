---
title: Configure o aplicativo inicializador para o Spring Boot Starter – Azure Monitor
description: Configure um aplicativo Spring Boot criado com Spring Initializr para usar o Application Insights Spring Boot Starter.
services: Application-Insights
documentationcenter: java
author: dhaval24
ms.author: dhdoshi
ms.date: 11/29/2019
ms.service: azure-monitor
ms.tgt_pltfrm: application-insights
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 687ecf87894a22e9300bf8ff4681e790db5a0d48
ms.sourcegitcommit: 44016b81a15b1625c464e6a7b2bfb55938df20b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/14/2020
ms.locfileid: "86379369"
---
# <a name="configure-a-spring-boot-initializer-app-to-use-application-insights"></a>Configure um aplicativo Inicializador do Spring Boot para usar o Application Insights

Este artigo descreve como criar um aplicativo Spring boot usando **[Spring Initializr]** . Ele usa o Spring Boot Starter do Azure Application Insights para fazer o monitoramento de ponta a ponta de aplicativos Java na nuvem.

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos são obrigatórios para você concluir as etapas neste artigo:

* Uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [benefício de assinante do MSDN] ou inscrever-se para obter uma [conta gratuita do Azure].
* Um JDK (Java Development Kit) com suporte. Para obter mais informações sobre os JDKs disponíveis para usar durante o desenvolvimento no Azure, confira <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/) versão 3.0 ou posterior.
* As APIs do Flux Web e do Netty **atualmente não têm suporte** com o iniciador do Spring Boot do Application Insights.

## <a name="create-a-custom-application-using-spring-initializr"></a>Criar um aplicativo personalizado usando Spring Initializr

Crie um aplicativo com o procedimento a seguir.

1. Navegue até [https://start.spring.io/](https://start.spring.io/).

1. Especifique que você deseja gerar um projeto **Maven** com **Java**, insira os nomes de **Grupo** e **Artefato** para seu aplicativo e, em seguida, selecione a dependência da Web na seção de dependências.

   ![Opções básicas do Initializr Basic][SI01]

   > [!NOTE]
   >
   > O Spring Initializr usará o **Grupo** e os nomes do **Artefato** para criar o nome do pacote, por exemplo: *com.vged.appinsights*.
   >

1. Clique no botão **Gerar**.

1. Quando solicitado, baixe o projeto para um caminho no computador local.

1. Depois de ter extraído os arquivos no sistema local, seu aplicativo Spring Boot personalizado estará pronto para edição.

## <a name="create-an-application-insights-resource-on-azure"></a>Criar um recurso do Application Insights no Azure

Crie um recurso do Application Insights usando o procedimento a seguir.

1. Navegue até o Azure em <https://portal.azure.com/> e clique em **Criar + um novo recurso**.

1. Clique em **Ferramentas de TI e de Gerenciamento** e, em seguida, clique em **Application Insights**.

1. Na página **Novo Recurso do Application Insights**, insira as seguintes informações:

* Especifique sua **Assinatura** e o seu **Grupo de recursos**.
* Insira o **Nome** do seu recurso do Application Insights.
* Selecione a **Região**.

   Após especificar essas opções, clique em **Revisar e criar**.

   ![Azure][AZ03]

* Verifique as especificações e clique em **Criar**.

Após a criação de seu recurso, ele estará listado no **Painel** do Azure, bem como nas páginas **Todos os Recursos**. Clique em seu recurso em qualquer um desses locais para abrir a página de visão geral do recurso Application Insights.

Na página de visão geral, copie a **chave de instrumentação**.

   ![Azure][AZ04]

## <a name="configure-your-downloaded-spring-boot-application-to-use-application-insights"></a>Configurar seu aplicativo baixado do Spring Boot para usar o Application Insights

Configure o aplicativo usando o procedimento a seguir.

1. Localize o arquivo *POM.xml* no diretório raiz do seu aplicativo e adicione a seguinte dependência em sua seção de dependências.

```XML
 <dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>applicationinsights-spring-boot-starter</artifactId>
    <version>1.1.1</version>
</dependency>
```

1. Localize o arquivo *application.properties* no diretório *recursos* do seu aplicativo, ou crie o arquivo se ele ainda não existe.

1. Abra o arquivo *application.properties* em um editor de texto e adicione as seguintes linhas ao arquivo. Depois, substitua os valores de exemplo pelas propriedades adequadas com as credenciais certas:

   ```yaml
   # Specify the instrumentation key of your Application Insights resource.
   azure.application-insights.instrumentation-key=[your ikey from the resource]
   # Specify the name of your springboot application. This can be any logical name you would like to give to your app.
   spring.application.name=[your app name]
   ```

   Para conferir outras maneiras de ajustar o Application Insights, consulte o [Leiame do Application Insights Springboot Starter](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/azure-application-insights-spring-boot-starter/README.md).

   > [!NOTE]
   > 
   > Use chaves de instrumentação diferentes (como recursos diferentes) do Application Insights para perfis diferentes, como PROD, DEV etc. Consulte as [Propriedades específicas de perfil do Spring Boot] para saber mais. 

1. Salve e feche o arquivo *application.properties*.

1. Crie uma pasta chamada *controlador* sob a pasta de origem para o seu pacote; por exemplo:

   `D:\Microsoft\demo\src\main\java\com\example\demo\controller`

   -ou-

   `/users/example/home/demo/src/main/java/com/example/demo/controller`

1. Crie um novo arquivo chamado *TestController.java* na pasta *controlador*. Abra o arquivo em um editor de texto e adicione o seguinte código a ele:

   ```java
    package com.example.demo;

    import com.microsoft.applicationinsights.TelemetryClient;
    import java.io.IOException;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RestController;
    import com.microsoft.applicationinsights.telemetry.Duration;

    @RestController
    @RequestMapping("/sample")
    public class TestController {

        @Autowired
        TelemetryClient telemetryClient;

        @GetMapping("/hello")
        public String hello() {

            //track a custom event  
            telemetryClient.trackEvent("Sending a custom event...");

            //trace a custom trace
            telemetryClient.trackTrace("Sending a custom trace....");

            //track a custom metric
            telemetryClient.trackMetric("custom metric", 1.0);

            //track a custom dependency
            telemetryClient.trackDependency("SQL", "Insert", new Duration(0, 0, 1, 1, 1), true);

            return "hello";
        }
    }
   ```

   Substitua `com.example.demo` pelo nome do pacote de seu projeto.

1. Salve e feche o arquivo *TestController.java*.

1. Compile o aplicativo Spring Boot com Maven e execute-o. Por exemplo: 

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```

1. Teste o aplicativo Web navegando até `http://localhost:8080/sample/hello` com um navegador da Web ou use a sintaxe semelhante ao seguinte exemplo se você tiver o **curl** disponível:

   ```shell
   curl http://localhost:8080/sample/hello
   ```

   Você deve ver a mensagem "olá!" no controlador de exemplo exibido. O Application Insights coletará automaticamente essa solicitação e a enviará como um item de telemetria com seu evento, métrica, dependência e rastreamento personalizados associados, conforme especificado na lógica do controlador. 

   Após alguns segundos, você deverá ver os dados no Azure. 

   ![Azure][AZ05]

Clique no bloco **Mapa do Aplicativo** para exibir os componentes de alto nível e a interação entre eles. Isso é recomendado para obter uma visão geral de alto nível de todo o aplicativo. Cada Microsserviço do Spring Boot é reconhecido pelo nome do aplicativo spring. Lembre-se de defini-lo.

   ![Azure][AZ08] 

## <a name="configure-springboot-application-to-send-log4j-logs-to-application-insights"></a>Configurar aplicativo Springboot para enviar logs log4j ao Application Insights

Configure o aplicativo para enviar logs usando o procedimento a seguir.

1. Modifique o arquivo POM.xml do projeto e adicione/modifique a seção de dependências com o seguinte. 

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
        <exclusions>
            <exclusion>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-logging</artifactId>
            </exclusion>
        </exclusions>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-log4j2</artifactId>
    </dependency>

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-spring-boot-starter</artifactId>
        <version>1.1.1</version>
    </dependency>

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-logging-log4j2</artifactId>
        <version>2.1.1</version>
    </dependency>
</dependencies>
```

2. Salve e feche o arquivo *POM.xml*.

3. Na pasta \src\main\resources, crie um novo arquivo *log4j2.xml* e configure-o. Por exemplo: 

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Configuration packages="com.microsoft.applicationinsights.log4j.v2">
  <Properties>
    <Property name="LOG_PATTERN">
      %d{yyyy-MM-dd HH:mm:ss.SSS} %5p ${hostName} --- [%15.15t] %-40.40c{1.} : %m%n%ex
    </Property>
  </Properties>
  <Appenders>
    <Console name="Console" target="SYSTEM_OUT">
      <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
    </Console>
    <ApplicationInsightsAppender name="aiAppender">
    </ApplicationInsightsAppender>
  </Appenders>
  <Loggers>
    <Root level="trace">
      <AppenderRef ref="Console"  />
      <AppenderRef ref="aiAppender"  />
    </Root>
  </Loggers>
</Configuration>
```

4. Compile e execute novamente o aplicativo Spring Boot, como mostrado acima.

Em alguns segundos, você verá todos os logs de spring disponíveis no Azure. É possível verificar as mensagens de log detalhadas e fazer a análise no Portal de Análise.

![Azure][AZ07]

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Spring e o Azure, continue no Spring no Centro de Documentação do Azure.

> [!div class="nextstepaction"]
> [Spring no Azure](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações sobre como usar aplicativos Spring Boot no Azure, confira os seguintes artigos:

* [Implantar um aplicativo Spring Boot no Serviço de Aplicativo do Azure](deploy-spring-boot-java-app-from-container-registry-using-maven-plugin.md)

* [Executando um Aplicativo Spring Boot em um Cluster Kubernetes no Serviço de Contêiner do Azure](deploy-spring-boot-java-app-on-kubernetes.md)

O Application Insights oferece suporte à coleta automática de dependências externas e a correlação com as solicitações de entrada. Atualmente, damos suporte à coleta automática da Oracle, MsSQL, MySQL e Redis. Para obter mais detalhes sobre como habilitar a coleta automática, confira o artigo [como usar o agente Java do Application Insights](/azure/application-insights/app-insights-java-agent).

Para saber mais sobre o Azure Application Insights e seus recursos de monitoramento, confira a home page do **[Application Insights]** .

Para saber mais sobre detalhes de configuração adicionais do Inicializador do Spring Boot do Application Insights, consulte este [link](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/azure-application-insights-spring-boot-starter/README.md).

Para solicitações de recursos e possíveis bugs, registre os problemas em nosso repositório do [GitHub](https://github.com/Microsoft/ApplicationInsights-Java/issues).

Para obter mais informações sobre como usar o Azure com Java, confira [Azure para Desenvolvedores Java] e [Como trabalhar com o Java e o Azure DevOps].

O **[Spring Framework]** é uma solução de software livre que ajuda os desenvolvedores Java criar aplicativos de nível empresarial. Um dos projetos mais populares criado com base nessa plataforma é o [Spring Boot], que fornece uma abordagem simplificada para a criação de aplicativos Java autônomos. Para ajudar os desenvolvedores a começarem a usar o Spring Boot, vários exemplos de pacotes do Spring Boot estão disponíveis em [https://github.com/spring-guides/](https://github.com/spring-guides/). Além de escolher na lista de projetos básicos do Spring Boot, o **[Spring Initializr]** ajuda os desenvolvedores a começarem a criar aplicativos personalizados do Spring Boot.

<!-- URL List -->

[Azure para desenvolvedores Java]: /azure/developer/java/
[conta gratuita do Azure]: https://azure.microsoft.com/pricing/free-trial/
[Como trabalhar com o Java e o Azure DevOps]: /azure/devops/
[benefício de assinante do MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Propriedades específicas de perfil do Spring Boot]: https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html#boot-features-external-config-profile-specific-properties
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/
[Application Insights]: /azure/application-insights/

<!-- IMG List -->

[AZ01]: media/configure-spring-boot-starter-java-app-with-azure-application-insights/Create_resource.png
[AZ02]: media/configure-spring-boot-starter-java-app-with-azure-application-insights/Create_resource_2.png
[AZ03]: media/configure-spring-boot-starter-java-app-with-azure-application-insights/Create_resource_3.png
[AZ04]: media/configure-spring-boot-starter-java-app-with-azure-application-insights/Get_IKey.png
[AZ05]: media/configure-spring-boot-starter-java-app-with-azure-application-insights/OverviewBladeResults.png
[AZ06]: media/configure-spring-boot-starter-java-app-with-azure-application-insights/Search_and_traces.png
[AZ07]: media/configure-spring-boot-starter-java-app-with-azure-application-insights/traces_details.png
[AZ08]: media/configure-spring-boot-starter-java-app-with-azure-application-insights/AppMap.png

[SI01]: media/configure-spring-boot-starter-java-app-with-azure-application-insights/spring_start.PNG
[SI02]: media/configure-spring-boot-starter-java-app-with-azure-application-insights/After_extract.png

[RE01]: media/configure-spring-boot-starter-java-app-with-azure-application-insights/applicationproperties_loc.png
[RE02]: media/configure-spring-boot-starter-java-app-with-azure-application-insights/applicationinsightsproperties.png
