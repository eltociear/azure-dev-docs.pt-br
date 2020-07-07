---
author: judubois
ms.date: 05/06/2020
ms.author: judubois
ms.openlocfilehash: fa66c4e9db481e31853c8e67816a14b6ee753fd2
ms.sourcegitcommit: 81577378a4c570ced1e9c6765f4a9eee8453c889
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84507475"
---
Dentro da classe principal `DemoApplication`, configure um novo bean do Spring que criará um esquema de banco de dados, usando o seguinte código:

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

Esse bean do Spring usa um arquivo chamado *schema.sql*; portanto, crie esse arquivo na pasta *src/main/resources* e adicione o seguinte texto:
