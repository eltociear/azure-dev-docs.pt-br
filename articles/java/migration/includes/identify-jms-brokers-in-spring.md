---
author: yevster
ms.author: yebronsh
ms.date: 2/12/2020
ms.openlocfilehash: d17b69ea5299c24406a63fc6239c350b6cbcbdd4
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990138"
---
#### <a name="jms-message-brokers"></a>Agentes de mensagem JMS

Identifique os agentes em uso examinando o manifesto de build (normalmente, um arquivo *pom.xml* ou um *build.gradle*) para as dependências relevantes.

Por exemplo, um aplicativo Spring Boot que usa o ActiveMQ normalmente conteria essa dependência no arquivo *pom.xml*:

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
