---
author: yevster
ms.author: yebronsh
ms.date: 2/12/2020
ms.openlocfilehash: e9d6993c96fc90c065cda5f8b3177147c35d0242
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990258"
---
Aqui está um exemplo do ActiveMQ em um arquivo *application.properties*:

```properties
spring.activemq.brokerurl=broker:(tcp://localhost:61616,network:static:tcp://remotehost:61616)?persistent=false&useJmx=true
spring.activemq.user=admin
spring.activemq.password=tryandguess
```

Para obter mais informações sobre o ActiveMQ, confira a [Documentação de mensagens do Spring Boot](https://docs.spring.io/spring-boot/docs/2.0.x/reference/html/boot-features-messaging.html).

Aqui está um exemplo do IBM MQ em um arquivo *application.yaml*:

```yaml
ibm:
  mq:
    queueManager: qm1
    channel: dev.ORDERS
    connName: localhost(14)
    user: admin
    password: big$ecr3t
```

Para obter mais informações sobre a configuração do IBM MQ, confira a [Documentação dos componentes do Spring IBM MQ](https://github.com/ibm-messaging/mq-jms-spring#ibm-mq-jms-spring-components).
