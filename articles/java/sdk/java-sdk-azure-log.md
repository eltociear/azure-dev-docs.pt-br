---
title: Configurar o registro em log com o SDK do Azure para Java
description: Saiba como configurar estruturas de log para o SDK do Azure para bibliotecas de clientes Java
keywords: Azure, Java, SDK, registro em log
author: bmitchell287
ms.author: brendm
ms.date: 03/25/2020
ms.topic: article
ms.service: multiple
ms.custom: devx-track-java
ms.openlocfilehash: 5bb7f711eae230a08893d2f94c242a06af809f88
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87400614"
---
# <a name="configure-logging-with-the-azure-sdk-for-java"></a>Configurar o registro em log com o SDK do Azure para Java

Este artigo fornece exemplos de configurações de registro em log para o [SDK do Azure](https://azure.microsoft.com/downloads/) para Java. Para obter mais detalhes sobre as opções de configuração, como definir os níveis de log ou o log personalizado por classe, veja a documentação da estrutura de registro em log escolhida.

O SDK do Azure para bibliotecas de clientes Java usa o SLF4J ([Simple Logging Facade for Java](https://www.slf4j.org/)). O SLF4J permite que você use sua estrutura de registro em log preferida, que é chamada no momento da implantação do aplicativo.

> [!NOTE]
> Este artigo se aplica às versões mais recentes das bibliotecas de clientes do SDK do Azure. Para ver se uma biblioteca é compatível, veja a lista de [versões mais recentes do SDK do Azure](https://azure.github.io/azure-sdk/releases/latest/java.html). Se o aplicativo estiver usando uma versão mais antiga das bibliotecas de clientes do SDK do Azure, veja as instruções específicas na documentação do serviço aplicável.

## <a name="declare-a-logging-framework"></a>Declarar uma estrutura de registro em log

Antes de implementar esses agentes, você deve declarar a estrutura relevante como uma dependência em seu projeto. Para obter mais informações, confira o [Manual do usuário do SLF4J](https://www.slf4j.org/manual.html#projectDep).

As seções a seguir fornecem exemplos de configuração para estruturas de registros comuns.

## <a name="use-log4j"></a>Usar o Log4j

Os exemplos a seguir mostram as configurações para a estrutura de registros Log4j. Para obter mais informações, confira [a documentação do Log4j](https://logging.apache.org/log4j/1.2/).

**Habilitar o Log4j adicionando uma dependência Maven**

Adicione o seguinte ao arquivo *pom.xml* do seu projeto:

```xml
<!-- https://mvnrepository.com/artifact/org.slf4j/slf4j-log4j12 -->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-log4j12</artifactId>
    <version>[1.0,)</version> <!-- Version number 1.0 and above -->
</dependency>
```

**Habilitar o Log4j usando um arquivo de propriedades**

Crie um arquivo *log4j.properties* no diretório *./src/main/resource* do seu projeto e adicione o seguinte conteúdo:

```properties
log4j.rootLogger=INFO, A1
log4j.appender.A1=org.apache.log4j.ConsoleAppender
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%m%n
log4j.logger.com.azure.core=ERROR
```

**Habilitar o Log4j usando um arquivo XML**

Crie um arquivo *log4j.xml* no diretório *./src/main/resource* do seu projeto e adicione o seguinte conteúdo:

```xml
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
<log4j:configuration debug="true" xmlns:log4j='http://jakarta.apache.org/log4j/'>

    <appender name="console" class="org.apache.log4j.ConsoleAppender">
        <param name="Target" value="System.out"/>
        <layout class="org.apache.log4j.PatternLayout">
            <param name="ConversionPattern" value="%m%n" />
        </layout>
    </appender>
    <logger name="com.azure.core">
        <level value="ERROR" />
        <appender-ref ref="console" />
    </logger>

    <root>
        <level value="info" />
        <appender-ref ref="console" />
    </root>

</log4j:configuration>
```

## <a name="use-log4j-2"></a>Usar o Log4j 2

Os exemplos a seguir mostram as configurações para a estrutura de registros do Log4j 2. Para obter mais informações, confira [a documentação do Log4j 2](https://logging.apache.org/log4j/2.x/manual/configuration.html).

**Habilitar o Log4j 2 adicionando uma dependência Maven**

Adicione o seguinte ao arquivo *pom.xml* do seu projeto:

```
<!-- https://mvnrepository.com/artifact/org.apache.logging.log4j/log4j-slf4j-impl -->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j-impl</artifactId>
    <version>[2.0,)</version> <!-- Version number 2.0 and above -->
</dependency>
```

**Habilitar o Log4j 2 usando um arquivo de propriedades**

Crie um arquivo *log4j2.properties* no diretório *./src/main/resource* do seu projeto e adicione o seguinte conteúdo:

```properties
appender.console.type = Console
appender.console.name = STDOUT
appender.console.layout.type = PatternLayout
appender.console.layout.pattern = %msg%n
logger.app.name=com.azure.core
logger.app.level=ERROR

rootLogger.level = info
rootLogger.appenderRefs = stdout
rootLogger.appenderRef.stdout.ref = STDOUT
```

**Habilitar o Log4j 2 usando um arquivo XML**

Crie um arquivo *log4j2.xml* no diretório *./src/main/resource* do seu projeto e adicione o seguinte conteúdo:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="INFO">
    <Appenders>
        <Console name="console" target="SYSTEM_OUT">
            <PatternLayout
                pattern="%msg%n" />
        </Console>
    </Appenders>
    <Loggers>
        <Logger name="com.azure.core" level="error" additivity="true">
            <appender-ref ref="console" />
        </Logger>
        <Root level="info" additivity="false">
            <appender-ref ref="console" />
        </Root>
     </Loggers>
</Configuration>
```

## <a name="use-logback"></a>Usar o Logback

Os exemplos a seguir mostram as configurações básicas para a estrutura de registros Logback. Para obter mais informações, confira [a documentação do Logback](https://logback.qos.ch/manual/configuration.html).

**Habilitar o Logback adicionando uma dependência Maven**

Adicione o seguinte ao arquivo *pom.xml* do seu projeto:

```
<!-- https://mvnrepository.com/artifact/ch.qos.logback/logback-classic -->
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>[0.2.5,)</version> <!-- Version number 0.2.5 and above -->
</dependency>
```

**Habilitar o Logback usando um arquivo XML**

Crie um arquivo *logback.xml* no diretório *./src/main/resources* do seu projeto e adicione o seguinte conteúdo:

```xml
<configuration>
  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern>%message%n</pattern>
    </encoder>
  </appender>

  <logger name="com.azure.core" level="ERROR" />

  <root level="INFO">
    <appender-ref ref="STDOUT" />
  </root>
</configuration>
```

## <a name="use-logback-in-a-spring-boot-application"></a>Usar o Logback em um aplicativo Spring Boot

Os exemplos a seguir mostram algumas configurações para usar o Logback com o Spring. Normalmente, você adicionará configurações de registro em log a um arquivo *logback.xml* no diretório *./src/main/resources* do seu projeto. O Spring examina esse arquivo em busca de várias configurações, incluindo registro em log. Para obter mais informações, confira [a documentação do Logback](https://logback.qos.ch/manual/configuration.html).

Você pode configurar seu aplicativo para ler as configurações do Logback de qualquer arquivo. Para vincular seu arquivo *logback.xml* ao seu aplicativo Spring, crie um arquivo *application.properties* no diretório *./src/main/resources* do seu projeto e adicione o seguinte conteúdo:

```properties
logging.config=classpath:logback.xml
```

Para criar uma configuração de Logback para registro em log no console, adicione o seguinte ao arquivo *logback.xml*:

```xml 
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <appender name="Console"
    class="ch.qos.logback.core.ConsoleAppender">
    <layout class="ch.qos.logback.classic.PatternLayout">
      <Pattern>
        %black(%d{ISO8601}) %highlight(%-5level) [%blue(%t)] %blue(%logger{100}): %msg%n%throwable
      </Pattern>
    </layout>
  </appender>

  <root level="INFO">
    <appender-ref ref="Console" />
  </root>
</configuration>
```

Para configurar o registro em log para um arquivo que é substituído a cada hora e arquivado no formato gzip, adicione o seguinte ao seu arquivo *logback.xml*:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <property name="LOGS" value="./logs" />
  <appender name="RollingFile" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <file>${LOGS}/spring-boot-logger.log</file>
    <encoder
      class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
      <Pattern>%d %p %C{1.} [%t] %m%n</Pattern>
    </encoder>

    <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
      <!-- rollover hourly and gzip logs -->
      <fileNamePattern>${LOGS}/archived/spring-boot-logger-%d{yyyy-MM-dd-HH}.log.gz</fileNamePattern>
    </rollingPolicy>
  </appender>

  <!-- LOG everything at INFO level -->
  <root level="info">
    <appender-ref ref="RollingFile" />
  </root>
</configuration>
```

## <a name="configure-fallback-logging-for-temporary-debugging"></a>Configurar o log de fallback para depuração temporária

Em cenários em que não é possível reimplantar o aplicativo com um agente do SLF4J, você pode usar o agente de fallback interno às bibliotecas de clientes do Azure para Java, no Azure Core 1.3.0 ou posterior. Para habilitar esse agente, primeiro você deverá confirmar que não há nenhum agente do SLF4J (pois isso terá precedência) e, em seguida, definir a variável de ambiente `AZURE_LOG_LEVEL`. Depois de definir a variável de ambiente, você precisará reiniciar o aplicativo para iniciar a geração de logs.

A tabela a seguir mostra os valores permitidos para essa variável de ambiente.

|Nível de log   |Valores de variáveis de ambiente permitidos   |
|----------|-----------|
|VERBOSE   |"verbose", "debug"     |
|INFORMATIONAL|"info", "information", "informational"  |
|WARNING     |"warn", "warning"       |
|ERROR    |"err", "error"  |

## <a name="next-steps"></a>Próximas etapas

- [Habilitar log de diagnósticos para aplicativos no Serviço de Aplicativo do Azure](/azure/app-service/troubleshoot-diagnostic-logs) 
- [Examinar o log de segurança do Azure e as opções de auditoria](/azure/security/fundamentals/log-audit)
- [Saiba como trabalhar com os logs da plataforma Azure](/azure/azure-monitor/platform/platform-logs-overview)
