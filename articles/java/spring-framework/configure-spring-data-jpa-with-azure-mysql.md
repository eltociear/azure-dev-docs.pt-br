---
title: Usar o Spring Data JPA com o Banco de Dados do Azure para MySQL
description: Saiba como usar o Spring Data JPA com o Banco de Dados do Azure para MySQL.
documentationcenter: java
ms.date: 06/16/2020
ms.service: mysql
ms.tgt_pltfrm: multiple
ms.author: judubois
ms.topic: article
ms.openlocfilehash: af4239b1757051a01be5625ff815aa189d42bbbc
ms.sourcegitcommit: 7da78b35a847db9929554962dfcc47860f472fb9
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85133669"
---
# <a name="use-spring-data-jpa-with-azure-database-for-mysql"></a>Usar o Spring Data JPA com o Banco de Dados do Azure para MySQL

Este tópico demonstra como criar um aplicativo de exemplo que usa o [Spring Data JPA](https://spring.io/projects/spring-data-jpa) para armazenar e recuperar informações no [Banco de Dados do Azure para MySQL](https://docs.microsoft.com/azure/mysql/).

[A JPA (API de Persistência Java)](https://en.wikipedia.org/wiki/Java_Persistence_API) é a API Java padrão para o mapeamento relacional de objeto.

[!INCLUDE [spring-data-prerequisites.md](includes/spring-data-prerequisites.md)]

[!INCLUDE [spring-data-mysql-setup.md](includes/spring-data-mysql-setup.md)]

### <a name="generate-the-application-by-using-spring-initializr"></a>Gerar o aplicativo usando o Spring Initializr

Gere o aplicativo na linha de comando ao digitar:

```bash
curl https://start.spring.io/starter.tgz -d dependencies=web,data-jpa,mysql -d baseDir=azure-database-workshop -d bootVersion=2.3.0.RELEASE -d javaVersion=8 | tar -xzvf -
```

### <a name="configure-spring-boot-to-use-azure-database-for-mysql"></a>Configurar o Spring Boot para usar o Banco de Dados do Azure para MySQL

Abra o arquivo *src/main/resources/application.properties* e adicione o conteúdo a seguir. Substitua as duas variáveis `$AZ_DATABASE_NAME` e a variável `$AZ_MYSQL_PASSWORD` pelos valores que você configurou no início deste artigo.

```properties
logging.level.org.hibernate.SQL=DEBUG

spring.datasource.url=jdbc:mysql://$AZ_DATABASE_NAME.mysql.database.azure.com:3306/demo?serverTimezone=UTC
spring.datasource.username=spring@$AZ_DATABASE_NAME
spring.datasource.password=$AZ_MYSQL_PASSWORD

spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=create-drop
```

> [!WARNING]
> A propriedade de configuração `spring.jpa.hibernate.ddl-auto=create-drop` significa que o Spring Boot criará automaticamente um esquema de banco de dados na inicialização do aplicativo e tentará excluí-lo quando for desligado. Isso é ótimo para teste, mas não deve ser usado em produção!

> [!NOTE]
> Acrescentamos `?serverTimezone=UTC` à propriedade de configuração `spring.datasource.url` para instruir o driver JDBC a usar o formato de data UTC (Tempo Universal Coordenado) ao conectar-se ao banco de dados. Caso contrário, nosso servidor Java não usaria o mesmo formato de data que o banco de dados, o que resultaria em um erro.

Agora, você deveria conseguir iniciar o aplicativo usando o wrapper do Maven fornecido:

```bash
./mvnw spring-boot:run
```

Aqui está uma captura de tela do aplicativo em execução pela primeira vez:

[![O aplicativo em execução](media/configure-spring-data-jpa-with-azure-mysql/create-mysql-01.png)](media/configure-spring-data-jpa-with-azure-mysql/create-mysql-01.png#lightbox)

## <a name="code-the-application"></a>Codificar o aplicativo

Em seguida, adicione o código Java que usará o JPA para armazenar e recuperar dados do servidor MySQL.

[!INCLUDE [spring-data-jpa-create-application.md](includes/spring-data-jpa-create-application.md)]

Aqui está uma captura de tela dessas solicitações cURL:

[![Testar com o cURL](media/configure-spring-data-jpa-with-azure-mysql/create-mysql-02.png)](media/configure-spring-data-jpa-with-azure-mysql/create-mysql-02.png#lightbox)

Parabéns! Você criou um aplicativo Spring Boot que usa o JPA para armazenar e recuperar dados do Banco de Dados do Azure para MySQL.

[!INCLUDE [spring-data-conclusion.md](includes/spring-data-conclusion.md)]

### <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações sobre o Spring Data JPA, confira a [documentação de referência](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#reference) do Spring.

Para obter mais informações sobre como usar o Azure com o Java, confira [Azure para desenvolvedores Java](/azure/developer/java/) e [Trabalhar com o Azure DevOps e com o Java](/azure/devops/).
