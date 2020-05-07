---
title: Introdução ao Logz.io para aplicativos Java em execução no Azure
description: Este tutorial mostra como integrar e configurar o Logz.io para aplicativos Java em execução no Azure.
author: jdubois
manager: bborges
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: judubois
ms.openlocfilehash: d7f90939701bfbcdcd895b8baf3cabcae4d5efa9
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "81670752"
---
# <a name="tutorial-getting-started-with-monitoring-and-logging-using-logzio-for-java-apps-running-on-azure"></a>Tutorial: Introdução ao monitoramento e registro em log usando o Logz.io para aplicativos Java em execução no Azure

Este tutorial mostra como configurar um aplicativo Java clássico para enviar logs para o serviço [Logz.io](https://logz.io/) para ingestão e análise. O Logz.io fornece uma solução de monitoramento completa, baseada em Elasticsearch/Logstash/Kibana (ELK) e Grafana.

O tutorial pressupõe que você esteja usando Log4J ou Logback. Essas bibliotecas são as duas mais amplamente usadas para registro em Java, portanto, o tutorial deve funcionar para a maioria dos aplicativos em execução no Azure. Caso esteja usando a pilha Elástica para monitorar seu aplicativo Java, este tutorial mostrará como reconfigurar o destino do ponto de extremidade do Logz.io.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Enviar logs de um aplicativo Java existente para o Logz.io.
> * Enviar logs de diagnóstico e métricas dos serviços do Azure para o Logz.io.

## <a name="prerequisites"></a>Pré-requisitos

* [Java Developer Kit](https://aka.ms/azure-jdks), versão 8 ou superior
* Uma conta do Logz.io do [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/logz.logzio-elk-as-a-service-pro)
* Um aplicativo Java existente que usa Log4J ou Logback

## <a name="send-java-application-logs-to-logzio"></a>Enviar logs de aplicativos Java para o Logz.io

Primeiro, você aprenderá a configurar seu aplicativo Java com um token que fornece acesso à sua conta do Logz.io.

### <a name="get-your-logzio-access-token"></a>Obter seu token de acesso do Logz.io

Para obter seu token, faça logon na conta do Logz.io, selecione o ícone de engrenagem no canto direito e, em seguida, selecione **Configurações > Geral**. Copie o token de acesso exibido nas configurações de sua conta para que você possa usá-lo mais tarde.

### <a name="install-and-configure-the-logzio-library-for-log4j-or-logback"></a>Instalar e configurar a biblioteca do Logz.io para Log4J ou Logback

A biblioteca Java do Logz.io está disponível no Maven Central para que seja possível adicioná-la como uma dependência à configuração de seu aplicativo. Verifique o número da versão no Maven Central e use a versão mais recente nas definições de configuração a seguir.

Caso esteja usando o Maven, adicione a seguinte dependência ao seu arquivo `pom.xml`:

**Log4J:**

```xml
<dependency>
    <groupId>io.logz.log4j2</groupId>
    <artifactId>logzio-log4j2-appender</artifactId>
    <version>1.0.11</version>
</dependency>
```

**Logback:**

```xml
<dependency>
    <groupId>io.logz.logback</groupId>
    <artifactId>logzio-logback-appender</artifactId>
    <version>1.0.22</version>
</dependency>
```

Caso esteja usando o Gradle, adicione a seguinte dependência ao seu script de compilação:

**Log4J:**

```
implementation 'io.logz.log4j:logzio-log4j-appender:1.0.11'
```

**Logback:**

```
implementation 'io.logz.logback:logzio-logback-appender:1.0.22'
```

Em seguida, atualize seu arquivo de configuração Log4J ou Logback:

**Log4J:**

```xml
<Appenders>
    <LogzioAppender name="Logzio">
        <logzioToken><your-logz-io-token></logzioToken>
        <logzioType>java-application</logzioType>
        <logzioUrl>https://<your-logz-io-listener-host>:8071</logzioUrl>
    </LogzioAppender>
</Appenders>

<Loggers>
    <Root level="info">
        <AppenderRef ref="Logzio"/>
    </Root>
</Loggers>
```

**Logback:**

```xml
<configuration>
    <!-- Use shutdownHook so that we can close gracefully and finish the log drain -->
    <shutdownHook class="ch.qos.logback.core.hook.DelayingShutdownHook"/>
    <appender name="LogzioLogbackAppender" class="io.logz.logback.LogzioLogbackAppender">
        <token><your-logz-io-token></token>
        <logzioUrl>https://<your-logz-io-listener-host>:8071</logzioUrl>
        <logzioType>java-application</logzioType>
        <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
            <level>INFO</level>
        </filter>
    </appender>

    <root level="debug">
        <appender-ref ref="LogzioLogbackAppender"/>
    </root>
</configuration>
```

Substitua o espaço reservado `<your-logz-io-token>` pelo seu token de acesso e o espaço reservado `<your-logz-io-listener-host>` pelo host do ouvinte da sua região (por exemplo, listener.logz.io). Para obter mais informações sobre como localizar a região da sua conta, consulte [Região da conta](https://docs.logz.io/user-guide/accounts/account-region.html).

O elemento `logzioType` refere-se a um campo lógico em Elasticsearch usado para separar documentos diferentes uns dos outros. É essencial configurar esse parâmetro corretamente para obter o máximo do Logz.io.

Um "Tipo" Logz.io é seu formato de log (por exemplo: Apache, NGinx e MySQL) e não sua fonte (por exemplo: server1, server2 e server3). Para este tutorial, chamamos o tipo `java-application` porque estamos configurando aplicativos Java e esperamos que todos tenham o mesmo formato.

Para o uso avançado, você pode agrupar seus aplicativos Java em diferentes tipos, todos com um formato de log específico (configurável com Log4J e Logback). Por exemplo, você poderia ter um tipo "spring-boot-monolith" e um tipo "spring-boot-microservice".

### <a name="test-your-configuration-and-log-analysis-on-logzio"></a>Teste sua configuração e a análise de log no Logz.io

Após a configuração da biblioteca do Logz.io, seu aplicativo agora deve enviar logs diretamente para ele. Para testar se tudo está funcionando corretamente, vá para o console do Logz.io, selecione a guia **Parte final ativa** e, sem seguida, selecione **executar**. Você verá uma mensagem parecida com a seguinte, informando que a conexão está funcionando:

```output
Requesting Live Tail access...
Access granted. Opening connection...
Connected. Tailing...
````

Em seguida, inicie seu aplicativo ou use-o para produzir alguns logs. Os logs devem aparecer diretamente na tela. Por exemplo, aqui estão as primeiras mensagens de inicialização de um aplicativo Spring Boot:

```output
2019-09-19 12:54:40.685Z Starting JavaApp on javaapp-default-9-5cfcb8797f-dfp46 with PID 1 (/workspace/BOOT-INF/classes started by cnb in /workspace)
2019-09-19 12:54:40.686Z The following profiles are active: prod
2019-09-19 12:54:42.052Z Bootstrapping Spring Data repositories in DEFAULT mode.
2019-09-19 12:54:42.169Z Finished Spring Data repository scanning in 103ms. Found 6 repository interfaces.
2019-09-19 12:54:43.426Z Bean 'spring.task.execution-org.springframework.boot.autoconfigure.task.TaskExecutionProperties' of type [org.springframework.boot.autoconfigure.task.TaskExecutionProperties] is not eligible for getting processed by all BeanPostProcessors (for example: not eligible for auto-proxying)
```

Agora que os logs foram processados pelo Logz.io, é possível se beneficiar de todos os serviços da plataforma.

## <a name="send-azure-services-data-to-logzio"></a>Enviar dados de serviços do Azure para o Logz.io

Em seguida, você aprenderá como enviar logs e métricas de seus recursos do Azure para o Logz.io.

### <a name="deploy-the-template"></a>Implantar o modelo

A primeira etapa é implantar o modelo de integração Logz.io-Azure. A integração é baseada em um modelo pronto de implantação do Azure que configura todos os blocos de construção necessários do pipeline. O modelo cria um namespace do Hub de Eventos, um Hub de Eventos, dois blobs de armazenamento e todas as permissões e conexões corretas e necessárias. Os recursos configurados pela implantação automatizada podem coletar dados para uma única região do Azure e enviar esses dados para o Logz.io.

Localize o botão **Implantar no Azure**, exibido na [primeira etapa do Leiame do repositório](https://github.com/logzio/logzio-azure-serverless).

Ao selecionar **Implantar no Azure**, a página **Implantação Personalizada**, no portal do Azure, aparecerá com uma lista de campos preenchidos previamente.

É possível deixar a maioria dos campos no estado em que se encontram, mas não se esqueça de inserir as seguintes configurações:

* **Grupo de recursos**: Selecione um grupo existente ou crie um.
* **Host de Logs/Métricas do Logzio**: Insira a URL do ouvinte Logz.io. Se não tiver certeza qual é essa URL, verifique a URL de logon. Caso seja app.logz.io, use listener.logz.io (a configuração padrão). Caso seja app-eu.logz.io, use listener-eu.logz.io.
* **Token de Logs/Métricas do Logzio**: Insira o token da conta do Logz.io para a qual você deseja enviar os logs ou métricas do Azure. É possível encontrar esse token na página de conta na interface do usuário do Logz.io.

Concorde com os termos na parte inferior da página e selecione **Comprar**. Em seguida, o Azure implantará o modelo, o que pode levar um ou dois minutos. Eventualmente, você verá a mensagem "Implantação bem-sucedida" na parte superior do Portal.

É possível visitar o grupo de recursos definido para examinar os recursos implantados.

Para saber como configurar o logzio-azure-serverless para fazer backup de dados no Armazenamento de Blobs do Azure, consulte [Enviar logs de atividade do Azure](https://docs.logz.io/shipping/log-sources/azure-activity-logs.html).

### <a name="stream-azure-logs-and-metrics-to-logzio"></a>Transmita as métricas e os logs do Azure para o Logz.io

Agora que você implantou o modelo de integração, precisará configurar o Azure para transmitir os dados de diagnóstico para o Hub de eventos que você acabou de implantar. Quando os dados entrarem no Hub de Eventos, o aplicativo de funções encaminhará esses dados ao Logz.io.

1. Na barra de pesquisa, digite "Diagnóstico" e, em seguida, selecione **Configurações de diagnóstico**.

2. Escolha um recurso na lista de recursos e, em seguida, selecione **Adicionar configuração de diagnóstico** para abrir o painel **Configurações de diagnóstico** desse recurso.

    ![Painel de configurações de diagnóstico](media/java-get-started-with-logzio/diagnostics-settings.png)

3. Escolha um **Nome** para suas configurações de diagnóstico.

4. Selecione **Transmitir para um hub de eventos** e, em seguida, selecione **Configurar** para abrir o painel **Selecionar Hub de Eventos**.

5. Escolha seu Hub de Eventos:

    * **Selecione o namespace do hub de eventos**: Escolha o namespace que começa com **Logzio** (`LogzioNS6nvkqdcci10p`, por exemplo).
    * **Selecione o nome do hub de eventos**: Para os logs, escolha **insights-operational-logs** e para as métricas escolha **insights-operational-metrics**.
    * **Selecione o nome da política do hub de eventos**: Escolha **LogzioSharedAccessKey**.

6. Selecione **OK** para retornar ao painel de **Configurações de diagnóstico**.

7. Na seção de Log, selecione os dados que deseja transmitir e, em seguida, selecione **Salvar**.

Os dados selecionados agora serão transmitidos para o Hub de Eventos.

### <a name="visualize-your-data"></a>Visualize seus dados

Em seguida, aguarde algum tempo para que seus dados saiam do sistema para o Logz.io e depois abra o Kibana. Você deve ver os dados (com o tipo _eventhub_) preenchendo seus painéis. Para obter mais informações sobre como criar painéis, consulte [Como Criar o Painel Perfeito do Kibana](https://logz.io/blog/perfect-kibana-dashboard/).

A partir daí é possível consultar dados específicos na guia **Localizar** ou criar objetos Kibana para visualizar os dados na guia **Visualizar**.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando tiver terminado de usar os recursos do Azure criados neste tutorial, será possível excluí-los usando o seguinte comando:

```azurecli-interactive
az group delete --name <resource group>
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a configurar seu aplicativo Java e os serviços do Azure para enviar logs e métricas para o Logz.io.

Em seguida, saiba mais sobre como usar o Hub de Eventos para monitorar seu aplicativo:

> [!div class="nextstepaction"]
> [Transmitir os dados de monitoramento do Azure para um Hub de Eventos para consumo por meio de uma ferramenta externa](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs)
