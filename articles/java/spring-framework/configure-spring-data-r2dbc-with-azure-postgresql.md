---
title: Usar o Spring Data R2DBC com o Banco de Dados do Azure para PostgreSQL
description: Saiba como usar o Spring Data R2DBC com um banco de dados do Banco de Dados do Azure para PostgreSQL.
documentationcenter: java
ms.date: 03/18/2020
ms.service: postgresql
ms.tgt_pltfrm: multiple
ms.author: judubois
ms.topic: article
ms.openlocfilehash: bd3208cc68b863afe2ce39e83d62d358a4baea96
ms.sourcegitcommit: 0d492c9cc9b5295285ab75da55e5ab0577576287
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370727"
---
# <a name="use-spring-data-r2dbc-with-azure-database-for-postgresql"></a>Usar o Spring Data R2DBC com o Banco de Dados do Azure para PostgreSQL

Este tópico demonstra como criar um aplicativo de exemplo que usa o [Spring Data R2DBC](https://spring.io/projects/spring-data-r2dbc) para armazenar e recuperar informações em um banco de dados do [Banco de Dados do Azure para PostgreSQL](/azure/postgresql/). O exemplo usará a implementação R2DBC para PostgreSQL no repositório [r2dbc-postgresql](https://github.com/pgjdbc/r2dbc-postgresql) no GitHub.

O [R2DBC](https://r2dbc.io/) traz APIs reativas para bancos de dados relacionais tradicionais. Você pode usá-lo com o Spring WebFlux para criar aplicativos Spring Boot totalmente reativos que usam APIs sem bloqueio. Ele fornece melhor escalabilidade do que a abordagem clássica "um thread por conexão".

[!INCLUDE [spring-data-prerequisites.md](includes/spring-data-prerequisites.md)]

[!INCLUDE [spring-data-postgresql-setup.md](includes/spring-data-postgresql-setup.md)]

[!INCLUDE [spring-data-create-reactive.md](includes/spring-data-create-reactive.md)]

### <a name="generate-the-application-by-using-spring-initializr"></a>Gerar o aplicativo usando o Spring Initializr

Gere o aplicativo na linha de comando usando o seguinte comando:

```bash
curl https://start.spring.io/starter.tgz -d dependencies=webflux,data-r2dbc -d baseDir=azure-database-workshop -d bootVersion=2.3.0.RELEASE -d javaVersion=8 | tar -xzvf -
```

### <a name="add-the-reactive-postgresql-driver-implementation"></a>Adicionar a implementação do driver PostgreSQL reativo

Abra o arquivo *pom.xml* do projeto gerado e adicione o driver PostgreSQL reativo do [repositório no GitHub r2dbc-postgresql](https://github.com/pgjdbc/r2dbc-postgresql). Após a dependência `spring-boot-starter-webflux`, adicione o seguinte texto:

```xml
<dependency>
    <groupId>io.r2dbc</groupId>
    <artifactId>r2dbc-postgresql</artifactId>
    <scope>runtime</scope>
</dependency>
```

### <a name="configure-spring-boot-to-use-azure-database-for-postgresql"></a>Configurar o Spring Boot para usar o Banco de Dados do Azure para PostgreSQL

Abra o arquivo *src/main/resources/application.properties* e adicione o seguinte texto:

```properties
logging.level.org.springframework.data.r2dbc=DEBUG

spring.r2dbc.url=r2dbc:pool:postgres://$AZ_DATABASE_NAME.postgres.database.azure.com:5432/demo
spring.r2dbc.username=spring@$AZ_DATABASE_NAME
spring.r2dbc.password=$AZ_POSTGRESQL_PASSWORD
spring.r2dbc.properties.sslMode=REQUIRE
```

> [!WARNING]
> Por razões de segurança, o Banco de Dados do Azure para PostgreSQL requer o uso de conexões SSL. É por isso que você precisa adicionar a propriedade de configuração `spring.r2dbc.properties.sslMode=REQUIRE`. Caso contrário, o driver PostgreSQL do R2DBC tentará se conectar usando uma conexão não segura, o que falhará.

Substitua as duas variáveis `$AZ_DATABASE_NAME` e a variável `$AZ_POSTGRESQL_PASSWORD` pelos valores que você configurou no início deste artigo.

> [!NOTE]
> Para obter um melhor desempenho, a propriedade `spring.r2dbc.url` está configurada para usar um pool de conexões usando [r2dbc-pool](https://github.com/r2dbc/r2dbc-pool).

Agora, você deverá conseguir iniciar seu aplicativo usando o wrapper do Maven fornecido da seguinte maneira:

```bash
./mvnw spring-boot:run
```

Aqui está uma captura de tela do aplicativo em execução pela primeira vez:

[![O aplicativo em execução](media/configure-spring-data-r2dbc-with-azure-postgresql/create-postgresql-01.png)](media/configure-spring-data-r2dbc-with-azure-postgresql/create-postgresql-01.png#lightbox)

### <a name="create-the-database-schema"></a>Criar o esquema de banco de dados

[!INCLUDE [spring-data-r2dbc-create-schema.md](includes/spring-data-r2dbc-create-schema.md)]

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id SERIAL PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BOOLEAN);
```

Pare o aplicativo em execução e inicie-o novamente usando o comando a seguir. O aplicativo agora usará o banco de dados `demo` que você criou anteriormente e criará uma tabela `todo` dentro dele.

```bash
./mvnw spring-boot:run
```

Aqui está uma captura de tela da tabela do banco de dados enquanto está sendo criada:

[![Criação da tabela do banco de dados](media/configure-spring-data-r2dbc-with-azure-postgresql/create-postgresql-02.png)](media/configure-spring-data-r2dbc-with-azure-postgresql/create-postgresql-02.png#lightbox)

## <a name="code-the-application"></a>Codificar o aplicativo

Em seguida, adicione o código Java que usará o R2DBC para armazenar e recuperar dados do servidor PostgreSQL.

[!INCLUDE [spring-data-r2dbc-create-application.md](includes/spring-data-r2dbc-create-application.md)]

Aqui está uma captura de tela dessas solicitações cURL:

[![Testar com o cURL](media/configure-spring-data-r2dbc-with-azure-postgresql/create-postgresql-03.png)](media/configure-spring-data-r2dbc-with-azure-postgresql/create-postgresql-03.png#lightbox)

Parabéns! Você criou um aplicativo Spring Boot totalmente reativo que usa o R2DBC para armazenar e recuperar dados do Banco de Dados do Azure para PostgreSQL.

[!INCLUDE [spring-data-conclusion.md](includes/spring-data-conclusion.md)]

### <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações sobre o Spring Data R2DBC, confira a [documentação de referência](https://docs.spring.io/spring-data/r2dbc/docs/current/reference/html/#reference) do Spring.

Para obter mais informações sobre como usar o Azure com o Java, confira [Azure para desenvolvedores Java](/azure/developer/java/) e [Trabalhar com o Azure DevOps e com o Java](/azure/devops/).
