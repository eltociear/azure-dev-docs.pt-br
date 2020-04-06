---
title: Configurar o registro em log com o SDK do Azure para Java
description: Saiba como configurar estruturas de log para o SDK do Azure para bibliotecas de clientes Java
keywords: Azure, Java, SDK, registro em log
author: bmitchell287
ms.author: brendm
ms.date: 03/25/2020
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 9f7ad8d772b7de1335ebc3ba77cdea8aa1e8b683
ms.sourcegitcommit: 31f6d047f244f1e447faed6d503afcbc529bd28c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80319893"
---
# <a name="configure-logging-with-the-azure-sdk-for-java"></a>Configurar o registro em log com o SDK do Azure para Java

Este artigo fornece exemplos de configurações de registro em log para o [SDK do Azure](https://azure.microsoft.com/downloads/) para Java. Para obter mais detalhes sobre as opções de configuração, como definir os níveis de log ou o log personalizado por classe, veja a documentação da estrutura de registro em log escolhida.

O SDK do Azure para bibliotecas de clientes Java usa o SLF4J ([Simple Logging Facade for Java](https://www.slf4j.org/)). O SLF4J permite que você use sua estrutura de registro em log preferida, que é chamada no momento da implantação do aplicativo.

> [!NOTE]
> Este artigo se aplica às versões mais recentes das bibliotecas de clientes do SDK do Azure. Para ver se uma biblioteca é compatível, veja a lista de [versões mais recentes do SDK do Azure](https://azure.github.io/azure-sdk/releases/latest/java.html). Se o aplicativo estiver usando uma versão mais antiga das bibliotecas de clientes do SDK do Azure, veja as instruções específicas na documentação do serviço aplicável.

## <a name="declare-a-logging-framework"></a>Declarar uma estrutura de registro em log

Antes de implementar esses agentes, você deve declarar a estrutura relevante como uma dependência em seu projeto. Para obter mais informações, veja o [Manual do usuário do SLF4J](http://www.slf4j.org/manual.html#projectDep).

## <a name="configure-log4j-or-log4j-2"></a>Configurar Log4j ou Log4j 2

Você pode configurar o registro em log do Log4j e do Log4j 2 em um arquivo de propriedades ou em um arquivo XML. Para obter informações detalhadas sobre o registro em log do Log4j e do Log4j 2, veja o [manual do Log4j 2 do Apache](https://logging.apache.org/log4j/2.x/manual/configuration.html).

### <a name="use-a-properties-file"></a>Usar um arquivo de propriedades

No diretório *./src/main/resource* do seu projeto, crie um arquivo chamado *log4j.properties* ou *log4j2.properties* (o segundo é para Logj4 2). Use estes exemplos para começar.

Exemplo de Log4j:

```properties
log4j.rootLogger=INFO, A1
log4j.appender.A1=org.apache.log4j.ConsoleAppender
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%m%n
log4j.logger.com.azure.core=ERROR
```

Exemplo de Log4j2:

```properties
appender.console.type = Console
appender.console.name = LogToConsole
appender.console.layout.type = PatternLayout
appender.console.layout.pattern = %msg%n
logger.app.name=com.azure.core
logger.app.level=ERROR
```

### <a name="use-an-xml-file"></a>Usar um arquivo XML

Como alternativa, você pode usar um arquivo XML para configurar Log4j e Log4j2. No diretório *./src/main/resource* do seu projeto, crie um arquivo chamado *log4j.xml* ou *log4j2.xml* (o segundo é para Logj4 2). Use estes exemplos para começar.

Exemplo de Log4j:

```xml
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
<log4j:configuration debug="true" xmlns:log4j='http://jakarta.apache.org/log4j/'>

  <appender name="console" class="org.apache.log4j.ConsoleAppender">
    <param name="Target" value="System.out"/>
    <layout class="org.apache.log4j.PatternLayout">
    <param name="ConversionPattern" value="%m%n" />
    </layout>
  </appender>
  <logger name="com.azure.core" additivity="true">
    <level value="ERROR" />
    <appender-ref ref="console" />
  </logger>

  <root>
    <priority value ="info"></priority>
    <appender-ref ref="console"></appender>
  </root>

</log4j:configuration>
```

Exemplo de Log4j2:

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

## <a name="configure-logback"></a>Configurar o Logback

O [Logback](https://logback.qos.ch/manual/introduction.html) é uma das estruturas de log populares e uma implementação nativa de SLF4J. Para configurar o Logback, crie um arquivo XML chamado *logback.xml* no diretório *./src/main/resources* do projeto. Você pode encontrar mais informações sobre as opções de configuração no [site do projeto de Logback](https://logback.qos.ch/manual/configuration.html).

Aqui está um exemplo de configuração de Logback:

```xml
<?xml version="1.0" encoding="UTF-8"?>
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

Veja uma configuração de Logback simples para fazer logon no console:

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

Aqui está uma configuração para fazer logon em um arquivo que é substituído após cada hora e arquivado no formato de arquivo GZIP:

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

### <a name="configure-logback-for-a-spring-boot-application"></a>Configurar o Logback para um aplicativo Spring Boot

O Spring procura as configurações do seu projeto, incluindo o log, no arquivo *application.properties*, que está no diretório *./src/main/resources*. No arquivo *application.properties*, adicione a seguinte linha para vincular *logback.xml* ao aplicativo Spring Boot:

```properties
logging.config=classpath:logback.xml
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
