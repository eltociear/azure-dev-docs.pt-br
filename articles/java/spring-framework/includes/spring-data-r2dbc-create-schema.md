---
author: judubois
ms.date: 05/06/2020
ms.author: judubois
ms.openlocfilehash: 1f73c46d5bc259c94e58a48a8a6bdb883a4454fd
ms.sourcegitcommit: a631b36ec1277ee9397a860c597ffdd5495d88e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83369850"
---
Dentro da classe principal `DemoApplication`, configure um novo Spring bean que criará um esquema de banco de dados:

```java
    @Bean
    public ConnectionFactoryInitializer initializer(ConnectionFactory connectionFactory) {
        ConnectionFactoryInitializer initializer = new ConnectionFactoryInitializer();
        initializer.setConnectionFactory(connectionFactory);
        ResourceDatabasePopulator populator = new ResourceDatabasePopulator(new ClassPathResource("schema.sql"));
        initializer.setDatabasePopulator(populator);
        return initializer;
    }
```

Esse Spring bean usa um arquivo chamado *schema.sql*, portanto, crie esse arquivo na pasta *src/main/resources*:
