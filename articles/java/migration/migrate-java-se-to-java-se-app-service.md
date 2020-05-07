---
title: Migrar aplicativos Java SE para Java SE no Serviço de Aplicativo do Azure
description: Este guia descreve as informações das quais você deve estar ciente quando desejar migrar um aplicativo Spring Boot existente ou outro aplicativo Web independente para o Serviço de Aplicativo do Azure usando Java SE.
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 9576a3831afb00b10b9bec3531c2dbc1dc60f29f
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "81672182"
---
# <a name="migrate-executable-jar-web-applications-to-java-se-on-azure-app-service"></a>Migrar aplicativos Web JAR executáveis para Java SE no Serviço de Aplicativo do Azure

Este guia descreve as informações das quais você deve estar ciente quando desejar migrar um aplicativo Spring Boot existente ou outro aplicativo Web com servidor incorporado para o Serviço de Aplicativo do Azure usando Java SE.

## <a name="before-you-start"></a>Antes de começar

Se você não puder atender a nenhum dos requisitos de pré-migração, consulte os seguintes guias de migração complementares:

* Migrar aplicativos JAR executáveis para contêineres no Serviço de Kubernetes do Azure (diretrizes planejadas)
* Migrar aplicativos JAR executáveis para Máquinas Virtuais do Azure (diretrizes planejadas)

## <a name="pre-migration"></a>Pré-migração

### <a name="switch-to-a-supported-platform"></a>Alternar para uma plataforma compatível

O Serviço de Aplicativo oferece versões específicas do Java SE. Para garantir a compatibilidade, migre seu aplicativo para uma das versões compatíveis do ambiente atual antes de continuar com qualquer uma das etapas restantes. É necessário testar completamente a configuração resultante. Use a versão estável mais recente da sua distribuição do Linux nesses testes.

[!INCLUDE [note-obtain-your-current-java-version](includes/note-obtain-your-current-java-version.md)]

### <a name="inventory-external-resources"></a>Recursos externos de inventário

Identifique recursos externos, como fontes de dados, agentes de mensagens JMS e URLs de outros serviços. Em aplicativos Spring Boot, a configuração desses recursos normalmente pode ser encontrada em *src/main/directory*, em um arquivo normalmente chamado *application.properties* ou *application.yml*. Além disso, verifique as variáveis de ambiente da implantação de produção para ver as definições de configuração pertinentes.

#### <a name="databases"></a>Bancos de dados

Para qualquer banco de dados SQL, identifique a cadeia de conexão.

Para um aplicativo Spring Boot, as cadeias de conexão normalmente aparecem em arquivos de configuração.

Este é um exemplo de um arquivo *application.properties*:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/mysql_db
spring.datasource.username=dbuser
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```

Este é um exemplo de um arquivo *application.yaml*:

```yaml
spring:
  data:
    mongodb:
      uri: mongodb://mongouser:deepsecret@mongoserver.contoso.com:27017
```

Para obter mais informações, consulte [Repositórios JPA](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.repositories) e [Repositórios JDBC](https://docs.spring.io/spring-data/jdbc/docs/current/reference/html/#jdbc.repositories) na documentação do Spring.

#### <a name="jms-message-brokers"></a>Agentes de mensagem JMS

Identifique qual agente está sendo usado. Para fazer isso, examine o manifesto da compilação (normalmente, *pom.xml* ou *build.gradle*) para ver as dependências relevantes.

Por exemplo, um aplicativo Spring Boot que usa ActiveMQ normalmente conteria essa dependência em *pom.xml*:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-activemq</artifactId>
</dependency>
```

Os aplicativos Spring boot que usam agentes proprietários normalmente contêm dependências diretamente nas bibliotecas de driver JMS dos agentes. Este é um exemplo de um arquivo *build.gradle*:

```json
    dependencies {
      ...
      compile("com.ibm.mq:com.ibm.mq.allclient:9.0.4.0")
      ...
    }
```

Depois de identificar os agentes que estão sendo usados, localize as configurações correspondentes. Elas normalmente estão nos arquivos *application.properties* e *application.yml* para o Spring Boot.

Este é um exemplo de um arquivo *application.properties*:

```properties
spring.activemq.brokerurl=broker:(tcp://localhost:61616,network:static:tcp://remotehost:61616)?persistent=false&useJmx=true
spring.activemq.user=admin
spring.activemq.password=tryandguess
```

Este é um exemplo de um arquivo *application.yaml*:

```yaml
ibm:
  mq:
    queueManager: qm1
    channel: dev.ORDERS
    connName: localhost(14)
    user: admin
    password: big$ecr3t
```

#### <a name="all-other-external-resources"></a>Todos os outros recursos externos

Não é possível documentar todas as dependências externas possíveis neste guia. É responsabilidade da sua equipe verificar se todas as dependências externas de seu aplicativo podem ser atendidas após uma migração do Serviço de Aplicativo.

### <a name="inventory-secrets"></a>Segredos de inventário

#### <a name="passwords-and-secure-strings"></a>Senhas e cadeias de caracteres seguras

Verifique todas as propriedades e os arquivos de configuração, assim como as variáveis de ambiente, nas implantações de produção em busca de senhas e cadeias de caracteres secretas. Em um aplicativo Spring Boot, essas cadeias de caracteres provavelmente serão encontradas em *application.properties* ou *application.yml*.

[!INCLUDE [inventory-certificates](includes/inventory-certificates.md)]

[!INCLUDE [inventory-persistence-usage](includes/inventory-persistence-usage.md)]

### <a name="special-cases"></a>Casos especiais

Determinados cenários de produção podem exigir alterações adicionais ou impor limitações adicionais. Embora esses cenários possam ser infrequentes, é importante garantir que eles não se apliquem ao seu aplicativo ou que sejam resolvidos corretamente.

#### <a name="determine-whether-application-relies-on-scheduled-jobs"></a>Determinar se o aplicativo depende de trabalhos agendados

Trabalhos agendados, como tarefas do Agendador do Quartz ou trabalhos cron, não podem ser usados com o Serviço de Aplicativo. O Serviço de Aplicativo não impedirá que você implante um aplicativo que contenha tarefas agendadas internamente. No entanto, se o aplicativo for escalado horizontalmente, um mesmo trabalho agendado poderá ser executado mais de uma vez por período agendado. Essa situação pode levar a consequências indesejadas.

Faça um inventário de todos os trabalhos agendados, dentro ou fora do processo de aplicativos.

#### <a name="determine-whether-your-application-contains-os-specific-code"></a>Determinar se o aplicativo contém código específico do sistema operacional

Se seu aplicativo contiver qualquer código que esteja acomodando o sistema operacional no qual seu aplicativo estiver sendo executado, o aplicativo precisará ser refatorado para NÃO depender do sistema operacional subjacente. Por exemplo, qualquer uso de `/` ou `\` em caminhos de sistema de arquivos podem precisar ser substituídos por [`File.Separator`](https://docs.oracle.com/javase/8/docs/api/java/io/File.html#separator) ou [`Path.get`](https://docs.oracle.com/javase/8/docs/api/java/nio/file/Paths.html#get-java.lang.String-java.lang.String...-).

#### <a name="identify-all-outside-processesdaemons-running-on-the-production-servers"></a>Identificar todos os processos/daemons externos em execução nos servidores de produção

Os processos em execução fora do Servidor de Aplicativos, como o monitoramento de daemons, precisarão ser migrados para outro lugar ou eliminados.

#### <a name="identify-handling-of-non-http-requests-or-multiple-ports"></a>Identificar o tratamento de solicitações não HTTP ou várias portas

O Serviço de Aplicativo dá suporte a apenas um único ponto de extremidade HTTP em uma única porta. Se seu aplicativo escutar várias portas ou aceitar solicitações usando protocolos diferentes de HTTP, não use o Serviço de Aplicativo do Azure.

## <a name="migration"></a>Migração

### <a name="parameterize-the-configuration"></a>Parametrizar a configuração

Verifique se todas as coordenadas de recursos externos (como cadeias de conexão de banco de dados) e outras configurações personalizáveis podem ser lidas nas variáveis de ambiente. Se você estiver migrando um aplicativo Spring Boot, todas as definições de configuração já deverão ser externalizáveis. Para obter mais informações, consulte [Configuração externalizada](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-external-config) na documentação do Spring Boot.

Este é um exemplo que faz referência a uma variável de ambiente `SERVICEBUS_CONNECTION_STRING` de um arquivo *application.properties*:

```properties
spring.jms.servicebus.connection-string=${SERVICEBUS_CONNECTION_STRING}
spring.jms.servicebus.topic-client-id=contoso1
spring.jms.servicebus.idle-timeout=10000
```

### <a name="provision-an-app-service-plan"></a>Provisionar um plano do Serviço de Aplicativo

Na [lista de planos de serviço disponíveis](https://azure.microsoft.com/pricing/details/app-service/linux/), selecione o plano cujas especificações correspondem às do hardware de produção atual ou as superam.

> [!NOTE]
> Se você planeja executar implantações de preparo/canário ou usar [slots de implantação](/azure/app-service/deploy-staging-slots), o plano do Serviço de Aplicativo precisará incluir essa capacidade adicional. É recomendável usar planos Premium ou superiores para aplicativos Java.

[Crie o plano do Serviço de Aplicativo](/azure/app-service/app-service-plan-manage#create-an-app-service-plan).

### <a name="create-and-deploy-web-apps"></a>Criar e implantar aplicativos Web

Você precisará criar um aplicativo Web em seu Plano do Serviço de Aplicativo (escolhendo "Java SE" como a pilha de tempo de execução) para cada arquivo JAR executável que pretende executar.

#### <a name="maven-applications"></a>Aplicativos Maven

Se o seu aplicativo for criado com base em um arquivo POM do Maven, [use o plug-in do Aplicativo Web para Maven](/azure/developer/java/spring-framework/deploy-spring-boot-java-app-with-maven-plugin#configure-maven-plugin-for-azure-app-service) para criar o aplicativo Web e implantar seu aplicativo.

#### <a name="non-maven-applications"></a>Aplicativos não Maven

Se não for possível usar o plug-in do Maven, você precisará provisionar o aplicativo Web por meio de outros mecanismos, como:

* [Azure portal](https://portal.azure.com/#create/Microsoft.WebSite)
* [CLI do Azure](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)
* [PowerShell do Azure](/powershell/module/az.websites/new-azwebapp)

Depois que o aplicativo Web tiver sido criado, use um dos [mecanismos de implantação disponíveis](/azure/app-service/deploy-ftp) para implantar o aplicativo. Se possível, seu aplicativo deverá ser carregado em */home/site/wwwroot/app.jar*. Caso não deseje renomear o JAR como app.jar, você pode carregar um script de shell com o comando para executar seu JAR. Em seguida, cole o caminho completo desse script na caixa de texto [Arquivo de inicialização](/azure/app-service/containers/app-service-linux-faq#built-in-images), na seção Configuração do portal. O script de inicialização não é executado no diretório no qual ele é colocado. Portanto, sempre use caminhos absolutos para fazer referência a arquivos em seu script de inicialização (por exemplo: `java -jar /home/myapp/myapp.jar`).

### <a name="migrate-jvm-runtime-options"></a>Migrar opções de runtime da JVM

Se o aplicativo exigir opções específicas de runtime, [use o mecanismo mais apropriado para especificá-las](/azure/app-service/containers/configure-language-java#set-java-runtime-options).

[!INCLUDE [configure-custom-domain-and-ssl](includes/configure-custom-domain-and-ssl.md)]

[!INCLUDE [import-backend-certificates](includes/import-backend-certificates.md)]

### <a name="migrate-external-resource-coordinates-and-other-settings"></a>Migrar coordenadas de recursos externos e outras configurações

Siga [estas etapas para migrar cadeias de conexão e outras configurações](/azure/app-service/containers/configure-language-java#spring-boot-1).

> [!NOTE]
> Para todas as configurações do aplicativo Spring Boot parametrizadas com variáveis na seção [Parametrizar a configuração](#parameterize-the-configuration), essas variáveis de ambiente devem ser definidas na configuração de aplicativo. Todas as configurações de aplicativo do Spring Boot não explicitamente parametrizadas com variáveis de ambiente ainda podem ser substituídas por elas por meio da Configuração de aplicativo. Por exemplo: 

  ```properties
  spring.jms.servicebus.connection-string=${CUSTOMCONNSTR_SERVICE_BUS}
  spring.jms.servicebus.topic-client-id=contoso1
  spring.jms.servicebus.idle-timeout=1800000
  ```

![Configuração de aplicativo do Serviço de Aplicativo](media/migrate-java-se-to-java-se-app-service/app-service-parameterized-spring-boot-app-settings.png)

[!INCLUDE [migrate-scheduled-jobs](includes/migrate-scheduled-jobs.md)]

### <a name="restart-and-smoke-test"></a>Reinicialização e smoke test

Por fim, você precisará reiniciar seu aplicativo Web para aplicar todas as alterações de configuração. Após a conclusão da reinicialização, verifique se o aplicativo está sendo executado corretamente.

## <a name="post-migration"></a>Após a migração

Agora que você migrou seu aplicativo para o Serviço de Aplicativo do Azure, você deve verificar se ele funciona conforme o esperado. Depois de fazer isso, temos algumas recomendações para você que podem tornar seu aplicativo mais nativo da nuvem.

### <a name="recommendations"></a>Recomendações

* Se você optou por usar o diretório */home* para armazenamento de arquivos, considere [substituí-lo pelo Armazenamento do Azure](/azure/app-service/containers/how-to-serve-content-from-azure-storage).

* Se você tiver uma configuração no diretório */home* que contenha cadeias de conexão, chaves SSL e outras informações secretas, considere usar [Azure Key Vault](/azure/app-service/app-service-key-vault-references) e/ou [injeção de parâmetro com configurações de aplicativo](/azure/app-service/configure-common#configure-app-settings) sempre que possível.

* Considere [usar slots de implantação](/azure/app-service/deploy-staging-slots) para implantações confiáveis sem nenhum tempo de inatividade.

* Crie e implemente uma estratégia de DevOps. Para manter a confiabilidade, aumentando simultaneamente a velocidade de desenvolvimento, considere [automatizar implantações e testar com Azure Pipelines](/azure/devops/pipelines/ecosystems/java-webapp). Se estiver usando slots de implantação, você poderá [automatizar a implantação em um slot](/azure/devops/pipelines/targets/webapp?view=azure-devops&tabs=yaml#deploy-to-a-slot) e a troca de slots subsequente.

* Criar a implementar uma estratégia de continuidade dos negócios e recuperação de desastre. Para aplicativos críticos, considere a possibilidade de [usar uma arquitetura de implantação em várias regiões](/azure/architecture/reference-architectures/app-service-web-app/multi-region).
