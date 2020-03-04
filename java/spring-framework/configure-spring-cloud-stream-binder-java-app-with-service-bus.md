---
title: Como usar o Spring Cloud Stream Binder no Barramento de Serviço do Azure
description: Neste artigo, você verá como usar o Spring Cloud Stream Binder para enviar e receber mensagens do Barramento de Serviço do Azure.
author: seanli1988
manager: kyliel
ms.author: seal
ms.date: 08/21/2019
ms.topic: article
ms.openlocfilehash: e8f9dd93da56b17efca059e00cf1483666f4cf33
ms.sourcegitcommit: 24795630044c10a07b5dedc0f51c280f090c097e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77075260"
---
# <a name="how-to-use-spring-cloud-azure-stream-binder-for-azure-service-bus"></a>Como usar o Spring Cloud Stream Binder no Barramento de Serviço do Azure

[!INCLUDE [spring-boot-20-note.md](../includes/spring-boot-20-note.md)]

O Azure fornece uma plataforma de mensagens assíncrona chamada [Barramento de Serviço do Azure](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-overview) ("Barramento de Serviço") baseada no padrão [Advanced Message Queueing Protocol 1.0](http://www.amqp.org/) ("AMQP 1.0"). O Barramento de Serviço pode ser usado em todas as plataformas compatíveis do Azure.

Este artigo mostra como usar o Spring Cloud Stream Binder para enviar e receber mensagens de `queues` e `topics` do Barramento de Serviço.

## <a name="prerequisites"></a>Prerequisites

Os pré-requisitos a seguir são obrigatórios neste artigo:

1. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/free/).

1. Um JDK (Java Development Kit) compatível, versão 8 ou posterior. Para obter mais informações sobre os JDKs disponíveis para usar durante o desenvolvimento no Azure, confira <https://aka.ms/azure-jdks>.

1. [Maven](http://maven.apache.org/) do Apache, versão 3.2 ou posterior.

1. Se você já tiver configurado a fila ou o tópico do Barramento de Serviço, verifique se o namespace dele cumpre os seguintes requisitos:

    1. Permitir acesso de todas as redes
    1. É Standard (ou superior)
    1. Ter uma política de acesso com acesso de leitura/gravação para a fila e o tópico

1. Se você não tiver configurado a fila ou o tópico do Barramento de Serviço, use o portal do Azure para [criar uma fila do Barramento de Serviço](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quickstart-portal) ou [criar um tópico do Barramento de Serviço](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal). Verifique se o namespace cumpre os requisitos especificados na etapa anterior. Além disso, anote a cadeia de conexão no namespace, porque ela será necessária no aplicativo de teste deste tutorial.

1. Se você não tiver um aplicativo do Spring Boot, [crie um projeto do **Maven** com o Spring Initializr](https://start.spring.io/). Lembre-se de selecionar o **Projeto do Maven** e, em **Dependências**, adicione a dependência **Web**.

## <a name="use-the-spring-cloud-stream-binder-starter"></a>Usar o iniciador do Spring Cloud Stream Binder

1. Localize o arquivo *pom.xml* no diretório pai do aplicativo, por exemplo:

    `C:\SpringBoot\servicebus\pom.xml`

    -ou-

    `/users/example/home/servicebus/pom.xml`

1. Abra o arquivo *pom.xml* em um editor de texto.

1. Adicione o seguinte bloco de código no elemento **&lt;dependências>** , de acordo com o que você está usando um tópico ou fila do Barramento de Serviço:

    **Fila do Barramento de Serviço**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-servicebus-queue-stream-binder</artifactId>
        <version>1.1.0.RC5</version>
    </dependency>
    ```

    ![Edite o arquivo pom.xml na fila do Barramento de Serviço.](./media/configure-spring-cloud-stream-binder-java-app-with-service-bus/add-stream-binder-starter-pom-file-dependency-for-service-bus-queue.png)

    **Tópico do Barramento de Serviço**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-servicebus-topic-stream-binder</artifactId>
        <version>1.1.0.RC5</version>
    </dependency>
    ```

    ![Edite o arquivo pom.xml no tópico do Barramento de Serviço.](./media/configure-spring-cloud-stream-binder-java-app-with-service-bus/add-stream-binder-starter-pom-file-dependency-for-service-bus-topic.png)

1. Salve e feche o arquivo *pom.xml*.

## <a name="configure-the-app-for-your-service-bus"></a>Configurar o aplicativo para o barramento de serviço

É possível configurar o aplicativo com base na cadeia de conexão ou como um arquivo de credenciais. Este tutorial usa uma cadeia de conexão. Para saber mais sobre o uso de arquivos de credenciais, confira [Spring Cloud Azure Stream Binder para exemplo de código de fila do Barramento de Serviço](https://github.com/microsoft/spring-cloud-azure/tree/release/1.1.0.RC4/spring-cloud-azure-samples/servicebus-queue-binder-sample#credential-file-based-usage
) e [Spring Cloud Azure Stream Binder para exemplo de código de tópico do Barramento de Serviço](https://github.com/microsoft/spring-cloud-azure/tree/release/1.1.0.RC4/spring-cloud-azure-samples/servicebus-topic-binder-sample#credential-file-based-usage).

1. Localize o arquivo *application.properties* no diretório *recursos* do seu aplicativo; por exemplo:

   `C:\SpringBoot\servicebus\src\main\resources\application.properties`

   -ou-

   `/users/example/home/servicebus/src/main/resources/application.properties`

1. Abra o arquivo *application.properties* em um editor de texto.

1. Acrescente o código correto ao fim do arquivo *application.properties*, de acordo com o que você está usando no Barramento de Serviço: uma fila ou um tópico. Use a [tabela de descrições de Campo](#fd) para substituir os valores de exemplo pelas propriedades corretas do barramento de serviço.

    **Fila do Barramento de Serviço**

    ```yaml
    spring.cloud.azure.servicebus.connection-string=<ServiceBusNamespaceConnectionString>
    spring.cloud.stream.bindings.input.destination=examplequeue
    spring.cloud.stream.bindings.output.destination=examplequeue
    spring.cloud.stream.servicebus.queue.bindings.input.consumer.checkpoint-mode=MANUAL
    ```

    **Tópico do Barramento de Serviço**

    ```yaml
    spring.cloud.azure.servicebus.connection-string=<ServiceBusNamespaceConnectionString>
    spring.cloud.stream.bindings.input.destination=exampletopic
    spring.cloud.stream.bindings.input.group=examplesubscription
    spring.cloud.stream.bindings.output.destination=exampletopic
    spring.cloud.stream.servicebus.topic.bindings.input.consumer.checkpoint-mode=MANUAL
    ```

    **<a name="fd">Descrições de campo</a>**

    |                                        Campo                                   |                                                                                   Descrição                                                                                    |
    |--------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    |               `spring.cloud.azure.servicebus.connection-string`                |                                        Especifique a cadeia de conexão obtida no namespace do Barramento de Serviço no portal do Azure.                                   |
    |               `spring.cloud.stream.bindings.input.destination`                 |                            Especifique a fila ou o tópico do Barramento de Serviço usado neste tutorial.                         |
    |                  `spring.cloud.stream.bindings.input.group`                    |                                            Se você tiver usado um tópico do Barramento de Serviço, especifique a assinatura dele.                                |
    |               `spring.cloud.stream.bindings.output.destination`                |                               Especifique o mesmo valor usado para o destino de entrada.                        |
    | `spring.cloud.stream.servicebus.queue.bindings.input.consumer.checkpoint-mode` |                                                       Especifique `MANUAL`.                                                   |
    | `spring.cloud.stream.servicebus.topic.bindings.input.consumer.checkpoint-mode` |                                                       Especifique `MANUAL`.                                                   |

1. Salve e feche o arquivo *application.properties*.

## <a name="implement-basic-service-bus-functionality"></a>Implementar recursos básicos do Barramento de Serviço

Nesta seção, você criará as classes Java necessárias para enviar mensagens para o barramento de serviço.

### <a name="modify-the-main-application-class"></a>Modificar a classe principal do aplicativo

1. Localize o arquivo Java do aplicativo principal no diretório do pacote do seu aplicativo. Por exemplo:

    `C:\SpringBoot\servicebus\src\main\java\com\example\ServiceBusBinderApplication.java`

   -ou-

   `/users/example/home/servicebus/src/main/java/com/example/ServiceBusBinderApplication.java`

1. Abra o principal arquivo Java em um editor de texto.

1. Adicione o seguinte código ao arquivo:

    ```java
    package com.example;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;

    @SpringBootApplication
    public class ServiceBusBinderApplication {

        public static void main(String[] args) {
            SpringApplication.run(ServiceBusBinderApplication.class, args);
        }
    }
    ```

1. Salve e feche o arquivo.

### <a name="create-a-new-class-for-the-source-connector"></a>Criar uma nova classe para o conector de origem

1. Com um editor de texto, crie um arquivo Java chamado *StreamBinderSource.java* no diretório do pacote do aplicativo.

1. Adicione o seguinte código ao novo arquivo:

    ```java
    package com.example;

    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.cloud.stream.annotation.EnableBinding;
    import org.springframework.cloud.stream.messaging.Source;
    import org.springframework.messaging.support.GenericMessage;
    import org.springframework.web.bind.annotation.PostMapping;
    import org.springframework.web.bind.annotation.RequestParam;
    import org.springframework.web.bind.annotation.RestController;

    @EnableBinding(Source.class)
    @RestController
    public class StreamBinderSource {

        @Autowired
        private Source source;

        @PostMapping("/messages")
        public String postMessage(@RequestParam String message) {
            this.source.output().send(new GenericMessage<>(message));
            return message;
        }
    }
    ```

1. Salve e feche o arquivo *StreamBinderSources.java*.

### <a name="create-a-new-class-for-the-sink-connector"></a>Criar uma nova classe para o conector do coletor

1. Com um editor de texto, crie um arquivo Java chamado *StreamBinderSink.java* no diretório do pacote do aplicativo.

1. Adicione as seguintes linhas de código ao novo arquivo:

    ```java
    package com.example;

    import com.microsoft.azure.spring.integration.core.AzureHeaders;
    import com.microsoft.azure.spring.integration.core.api.Checkpointer;
    import org.springframework.cloud.stream.annotation.EnableBinding;
    import org.springframework.cloud.stream.annotation.StreamListener;
    import org.springframework.cloud.stream.messaging.Sink;
    import org.springframework.messaging.handler.annotation.Header;

    @EnableBinding(Sink.class)
    public class StreamBinderSink {

        @StreamListener(Sink.INPUT)
        public void handleMessage(String message, @Header(AzureHeaders.CHECKPOINTER) Checkpointer checkpointer) {
            System.out.println(String.format("New message received: '%s'", message));
            checkpointer.success().handle((r, ex) -> {
                if (ex == null) {
                    System.out.println(String.format("Message '%s' successfully checkpointed", message));
                }
                return null;
            });
        }
    }
    ```

1. Salve e feche o arquivo *StreamBinderSink.java*.

## <a name="build-and-test-your-application"></a>Criar e testar o aplicativo

1. Abra um prompt de comando.

1. Altere o diretório para o local do arquivo *pom.xml*; por exemplo:

    `cd C:\SpringBoot\servicebus`

    -ou-

    `cd /users/example/home/servicebus`

2. Crie um aplicativo do Spring Boot com o Maven e execute-o:

    ```shell
    mvn clean spring-boot:run
    ```

3. Quando o aplicativo estiver em execução, use *curl* para testá-lo:

    ```shell
    curl -X POST localhost:8080/messages?message=hello
    ```

    Você deverá ver "olá" publicado no log do aplicativo:

    ```shell
    New message received: 'hello'
    Message 'hello' successfully checkpointed
    ```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, use o [portal do Azure](https://portal.azure.com/) para excluir os recursos criados neste artigo para evitar cobranças inesperadas.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Spring no Azure](/java/azure/spring-framework)