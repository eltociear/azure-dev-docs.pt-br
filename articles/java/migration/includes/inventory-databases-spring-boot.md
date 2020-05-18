---
author: yevster
ms.author: yebronsh
ms.date: 2/12/2020
ms.openlocfilehash: 6bf4c54c8f428e3ffce79a7ba9ac49aae028e2fd
ms.sourcegitcommit: 226ebca0d0e3b918928f58a3a7127be49e4aca87
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82990228"
---
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

Confira a documentação do Spring Data para obter mais cenários de configuração possíveis:

* [Repositórios JPA](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.repositories)
* [Repositórios JDBC](https://docs.spring.io/spring-data/jdbc/docs/current/reference/html/#jdbc.repositories)
* [Repositórios Cassandra](https://docs.spring.io/spring-data/cassandra/docs/current/reference/html/#cassandra.repositories)
* [Repositórios MongoDB](https://docs.spring.io/spring-data/mongodb/docs/current/reference/html/#mongodb.repositories)
