---
title: Como usar o iniciador do Spring Boot para o JMS do Barramento de Serviço do Azure
description: Neste artigo, você verá como usar o iniciador de JMS do Spring para enviar e receber mensagens do Barramento de Serviço do Azure.
author: seanli1988
manager: kyliel
ms.author: Sean.Li
ms.date: 08/21/2019
ms.devlang: java
ms.service: azure-java
ms.topic: article
ms.openlocfilehash: f41486c7063a6b0fa26ca4055d5f10e625676e8a
ms.sourcegitcommit: f519a1ee8017850b2fa37049af3bac1ea5ca5516
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69892370"
---
# <a name="how-to-use-the-spring-boot-starter-for-azure-service-bus-jms"></a>Como usar o iniciador do Spring Boot para o JMS do Barramento de Serviço do Azure

[!INCLUDE [spring-boot-20-note.md](../includes/spring-boot-20-note.md)]

O Azure fornece uma plataforma de mensagens assíncrona chamada [Barramento de Serviço do Azure](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-overview) ("Barramento de Serviço") baseada no padrão [Advanced Message Queueing Protocol 1.0](http://www.amqp.org/) ("AMQP 1.0"). O Barramento de Serviço pode ser usado em todas as plataformas compatíveis do Azure.

O iniciador do Spring Boot para o JMS do Barramento de Serviço do Azure oferece integração do Spring com o Barramento de Serviço.

Neste artigo, você verá como usar o iniciador do Spring Boot para JMS do Barramento de Serviço do Azure para enviar e receber mensagens de `queues` e `topics` do Barramento de Serviço.

> [!NOTE]
> No momento, este artigo usa a versão `SNAPSHOT` do iniciador.

## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos a seguir são obrigatórios neste artigo:

1. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.comfree/).

1. Um JDK (Java Development Kit) compatível, versão 8 ou posterior. Para obter mais informações sobre os JDKs disponíveis para usar durante o desenvolvimento no Azure, confira <https://aka.ms/azure-jdks>.

1. [Maven](http://maven.apache.org/) do Apache, versão 3.2 ou posterior.

1. Se você já tiver configurado a fila ou o tópico do Barramento de Serviço, verifique se o namespace dele cumpre os seguintes requisitos:

    1. Permitir acesso de todas as redes
    1. Ser Premium (ou superior)
    1. Ter uma política de acesso com acesso de leitura/gravação para a fila e o tópico

1. Se você não tiver configurado a fila ou o tópico do Barramento de Serviço, use o portal do Azure para [criar uma fila do Barramento de Serviço](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quickstart-portal) ou [criar um tópico do Barramento de Serviço](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal). Verifique se o namespace cumpre os requisitos especificados na etapa anterior. Além disso, anote a cadeia de conexão no namespace, porque ela será necessária no aplicativo de teste deste tutorial.

1. Se você não tiver um aplicativo do Spring Boot, [crie um projeto do **Maven** com o Spring Initializr](https://start.spring.io/). Lembre-se de selecionar o **Projeto do Maven** e, em **Dependências**, adicione a dependência **Web**.

## <a name="use-the-azure-service-bus-jms-starter"></a>Usar o iniciador de JMS do Barramento de Serviço do Azure

1. Localize o arquivo *pom.xml* no diretório pai do aplicativo, por exemplo:

    `C:\SpringBoot\servicebus\pom.xml`

    -ou-

    `/users/example/home/servicebus/pom.xml`

1. Abra o arquivo *pom.xml* em um editor de texto.

1. Adicione o iniciador de JMS do Barramento de Serviço do Azure do Spring Boot à lista de `<dependencies>`:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus-jms-spring-boot-starter</artifactId>
        <version>2.1.7-SNAPSHOT</version>
    </dependency>
    ```

    ![Adicione a seção de dependências ao arquivo pom.xml.](./media/configure-spring-boot-starter-java-app-with-azure-service-bus/add-dependency-section.png)

1. Adicione a configuração [repositórios do Maven](https://maven.apache.org/settings.html#Repositories) para usar a versão INSTANTÂNEO:

    ```xml
    <repositories>
        <repository>
            <id>nexus-snapshots</id>
            <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
            <snapshots>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            </snapshots>
        </repository>
    </repositories>
    ```

    ![Adicione a seção de repositórios ao arquivo pom.xml.](./media/configure-spring-boot-starter-java-app-with-azure-service-bus/add-repository-section.png)

1. Salve e feche o arquivo *pom.xml*.

## <a name="configure-the-app-for-your-service-bus"></a>Configurar o aplicativo para o barramento de serviço 

Nesta seção, você verá como configurar o aplicativo para usar uma fila ou um tópico do Barramento de Serviço.

### <a name="use-a-service-bus-queue"></a>Usar uma fila do Barramento de Serviço

1. Localize *application.properties* no diretório *recursos* do aplicativo, por exemplo:

    `C:\SpringBoot\servicebus\application.properties`

    -ou-

    `/users/example/home/servicebus/application.properties`

1. Abra o arquivo *application.properties* em um editor de texto.

1. Acrescente o código a seguir ao fim do arquivo *application.properties*. Substitua os valores de exemplo pelos valores corretos do barramento de serviço:

    ```yml
    spring.jms.servicebus.connection-string=<ServiceBusNamespaceConnectionString>
    spring.jms.servicebus.idle-timeout=<IdleTimeout>
    ```

    **Descrições de campo**

    | Campo                                     | DESCRIÇÃO                                                                                     |
    |-------------------------------------------|-------------------------------------------------------------------------------------------------|
    | `spring.jms.servicebus.connection-string` | Especifique a cadeia de conexão obtida no namespace do Barramento de Serviço no portal do Azure. |
    | `spring.jms.servicebus.idle-timeout`      | Especifique o tempo limite de ociosidade em milissegundos. O valor recomendado para este tutorial é de 1800000.   |

3. Salve e feche o arquivo *application.properties*.

### <a name="use-service-bus-topic"></a>Usar um tópico do Barramento de Serviço

1. Localize *application.properties* no diretório *recursos* do aplicativo, por exemplo:

    `C:\SpringBoot\servicebus\application.properties`

    -ou-

    `/users/example/home/servicebus/application.properties`

1. Abra o arquivo *application.properties* em um editor de texto.

1. Acrescente o código a seguir ao fim do arquivo *application.properties*. Substitua os valores de exemplo pelos valores corretos do barramento de serviço:

    ```yml
    spring.jms.servicebus.connection-string=<ServiceBusNamespaceConnectionString>
    spring.jms.servicebus.topic-client-id=<ServiceBusTopicClientId>
    spring.jms.servicebus.idle-timeout=<IdleTimeout>
    ```

    **Descrições de campo**

    | Campo                                     | DESCRIÇÃO                                                                                       |
    |-------------------------------------------|---------------------------------------------------------------------------------------------------|
    | `spring.jms.servicebus.connection-string` | Especifique a cadeia de conexão obtida no namespace do Barramento de Serviço no portal do Azure.   |
    | `spring.jms.servicebus.topic-client-id`   | Especifique a ID do cliente do JMS se você estiver usando um tópico do Barramento de Serviço do Azure com uma assinatura durável. |
    | `spring.jms.servicebus.idle-timeout`      | Especifique o tempo limite de ociosidade em milissegundos. O valor recomendado para este tutorial é de 1800000.     |

1. Salve e feche o arquivo *application.properties*.

## <a name="implement-basic-service-bus-functionality"></a>Implementar recursos básicos do Barramento de Serviço

Nesta seção, você criará as classes Java necessárias para enviar mensagens para a fila ou tópico do Barramento de Serviço ou receber mensagens da assinatura de fila ou tópico correspondente.

### <a name="modify-the-main-application-class"></a>Modificar a classe principal do aplicativo

1. Localize o arquivo Java do aplicativo principal no diretório do pacote do seu aplicativo. Por exemplo:

    `C:\SpringBoot\servicebus\src\main\java\com\wingtiptoys\servicebus\ServiceBusJmsStarterApplication.java`

    -ou-

    `/users/example/home/servicebus/src/main/java/com/wingtiptoys/servicebus/ServiceBusJmsStarterApplication.java`

1. Abra o principal arquivo Java em um editor de texto.

1. Adicione o seguinte código ao arquivo:

   ```java
    package com.wingtiptoys.servicebus;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;

    @SpringBootApplication
    public class ServiceBusJmsStarterApplication {

        public static void main(String[] args) {
            SpringApplication.run(ServiceBusJmsStarterApplication.class, args);
        }
    }
    ```

1. Salve e feche o arquivo.

### <a name="define-a-test-java-class"></a>Definir uma classe Java de teste

1. Com um editor de texto, crie um arquivo Java chamado *User.java* no diretório do pacote do aplicativo.

1. Defina uma classe de usuário genérica que armazene e recupere o nome do usuário:

    ```java
    package com.wingtiptoys.servicebus;

    import java.io.Serializable;

    // Define a generic User class.
    public class User implements Serializable {

        private static final long serialVersionUID = -295422703255886286L;

        private String name;

        public User() {
        }

        public User(String name) {
            setName(name);
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

    }
    ```

    `Serializable` será implementado para usar o método `send` em `JmsTemplate` na estrutura do Spring. Caso contrário, um bean personalizado `MessageConverter` deverá ser definido para serializar o conteúdo para JSON em formato de texto. Para saber mais sobre o `MessageConverter`, confira o [projeto de iniciador de JMS do Spring](https://spring.io/guides/gs/messaging-jms/).

1. Salve e feche o arquivo *User.java*.

### <a name="create-a-new-class-for-the-message-send-controller"></a>Criar uma nova classe para o controlador de mensagens enviadas

1. Com um editor de texto, crie um arquivo Java chamado *SendController.java* no diretório do pacote do aplicativo

1. Adicione o seguinte código ao novo arquivo:

    ```java
    package com.wingtiptoys.servicebus;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.jms.core.JmsTemplate;
    import org.springframework.web.bind.annotation.PostMapping;
    import org.springframework.web.bind.annotation.RequestParam;
    import org.springframework.web.bind.annotation.RestController;

    @RestController
    public class SendController {

        private static final String DESTINATION_NAME = "<DestinationName>";

        private static final Logger logger = LoggerFactory.getLogger(SendController.class);

        @Autowired
        private JmsTemplate jmsTemplate;

        @PostMapping("/messages")
        public String postMessage(@RequestParam String message) {
            logger.info("Sending message");
            jmsTemplate.convertAndSend(DESTINATION_NAME, new User(message));
            return message;
        }
    }
    ```

    > [!NOTE]
    > Substitua `<DestinationName>` pelo nome da fila ou do tópico configurado no namespace do Barramento de Serviço.

1. Salve e feche o *SendController.java*.

### <a name="create-a-class-for-the-message-receive-controller"></a>Criar uma classe para o controlador de mensagens recebidas

#### <a name="receive-messages-from-a-service-bus-queue"></a>Receber mensagens de uma fila do Barramento de Serviço

1. Com um editor de texto, crie um arquivo Java chamado *QueueReceiveController.java* no diretório do pacote do aplicativo

1. Adicione o seguinte código ao novo arquivo:

    ```java
    package com.wingtiptoys.servicebus;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.jms.annotation.JmsListener;
    import org.springframework.stereotype.Component;

    @Component
    public class QueueReceiveController {

        private static final String QUEUE_NAME = "<ServiceBusQueueName>";

        private final Logger logger = LoggerFactory.getLogger(QueueReceiveController.class);

        @JmsListener(destination = QUEUE_NAME, containerFactory = "jmsListenerContainerFactory")
        public void receiveMessage(User user) {
            logger.info("Received message: {}", user.getName());
        }
    }
    ```

    > [!NOTE]
    > Substitua `<ServiceBusQueueName>` pelo nome da fila no namespace do Barramento de Serviço.

1. Salve e feche o arquivo *QueueReceiveController.java*.

#### <a name="receive-messages-from-a-service-bus-subscription"></a>Receber mensagens de uma assinatura do Barramento de Serviço

1. Com um editor de texto, crie um arquivo Java chamado *TopicReceiveController.java* no diretório do pacote do aplicativo. 

1. Adicione o seguinte código ao novo arquivo. Substitua o espaço reservado `<ServiceBusTopicName>` pelo nome do tópico configurado no namespace do Barramento de Serviço. Substitua o espaço reservado `<ServiceBusSubscriptionName>` pelo nome da assinatura do tópico do Barramento de Serviço.

    ```java
    package com.wingtiptoys.servicebus;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.jms.annotation.JmsListener;
    import org.springframework.stereotype.Component;

    @Component
    public class TopicReceiveController {

        private static final String TOPIC_NAME = "<ServiceBusTopicName>";

        private static final String SUBSCRIPTION_NAME = "<ServiceBusSubscriptionName>";

        private final Logger logger = LoggerFactory.getLogger(TopicReceiveController.class);

        @JmsListener(destination = TOPIC_NAME, containerFactory = "topicJmsListenerContainerFactory",
                subscription = SUBSCRIPTION_NAME)
        public void receiveMessage(User user) {
            logger.info("Received message: {}", user.getName());
        }
    }
    ```

1. Salve e feche o arquivo *TopicReceiveController.java*.

## <a name="build-and-test-your-application"></a>Criar e testar o aplicativo

1. Abra um prompt de comando e altere o diretório para o local do *pom.xml*. Por exemplo:

    `cd C:\SpringBoot\servicebus`

    -ou-

    `cd cd /users/example/home/servicebus`

1. Crie um aplicativo do Spring Boot com o Maven e execute-o:

    ```shell
    mvn clean spring-boot:run
    ```

3. Quando o aplicativo estiver em execução, use *curl* para testá-lo:

    ```shell
    curl -X POST localhost:8080/messages?message=hello
    ```

    Você deverá ver "Enviando mensagem" e "olá" publicados nos logs do aplicativo:

    ```shell
    [nio-8080-exec-1] com.wingtiptoys.servicebus.SendController : Sending message
    [enerContainer-1] com.wingtiptoys.servicebus.ReceiveController : Received message: hello
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, use o [portal do Azure](http://ms.portal.azure.com/) para excluir os recursos criados neste artigo para evitar cobranças inesperadas.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como usar a API do JMS com o Barramento de Serviço e o AMQP 1.0](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp)