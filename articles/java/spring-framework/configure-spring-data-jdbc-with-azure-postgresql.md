---
title: Usar o Spring Data JDBC com o Banco de Dados do Azure para PostgreSQL
description: Saiba como usar o Spring Data JDBC com o Banco de Dados do Azure para PostgreSQL.
documentationcenter: java
ms.date: 05/18/2020
ms.service: postgresql
ms.tgt_pltfrm: multiple
ms.author: judubois
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: c700d78fa8cb7568e207fd020714cda25abb2bc5
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831812"
---
# <a name="use-spring-data-jdbc-with-azure-database-for-postgresql"></a>Usar o Spring Data JDBC com o Banco de Dados do Azure para PostgreSQL

Este tópico demonstra como criar um aplicativo de exemplo que usa o [Spring Data JDBC](https://spring.io/projects/spring-data-jdbc) para armazenar e recuperar informações em um banco de dados do [Banco de Dados do Azure para PostgreSQL](/azure/postgresql/).

O [JDBC](https://en.wikipedia.org/wiki/Java_Database_Connectivity) é a API Java padrão para se conectar a bancos de dados relacionais tradicionais.

[!INCLUDE [spring-data-prerequisites.md](includes/spring-data-prerequisites.md)]

## <a name="sample-application"></a>Aplicativo de exemplo

Neste artigo, escreveremos o código de um aplicativo de exemplo. Caso você queira adiantar o processo, esse aplicativo já está codificado e disponível em [https://github.com/Azure-Samples/quickstart-spring-data-jdbc-postgresql](https://github.com/Azure-Samples/quickstart-spring-data-jdbc-postgresql).

[!INCLUDE [spring-data-postgresql-setup.md](includes/spring-data-postgresql-setup.md)]

### <a name="generate-the-application-by-using-spring-initializr"></a>Gerar o aplicativo usando o Spring Initializr

Gere o aplicativo na linha de comando usando o seguinte comando:

```bash
curl https://start.spring.io/starter.tgz -d dependencies=web,data-jdbc,postgresql -d baseDir=azure-database-workshop -d bootVersion=2.3.1.RELEASE -d javaVersion=8 | tar -xzvf -
```

### <a name="configure-spring-boot-to-use-azure-database-for-postgresql"></a>Configurar o Spring Boot para usar o Banco de Dados do Azure para PostgreSQL

Abra o arquivo *src/main/resources/application.properties* e adicione o seguinte texto:

```properties
logging.level.org.springframework.jdbc.core=DEBUG

spring.datasource.url=jdbc:postgresql://$AZ_DATABASE_NAME.postgres.database.azure.com:5432/demo
spring.datasource.username=spring@$AZ_DATABASE_NAME
spring.datasource.password=$AZ_POSTGRESQL_PASSWORD

spring.datasource.initialization-mode=always
```

Substitua as duas variáveis `$AZ_DATABASE_NAME` e a variável `$AZ_POSTGRESQL_PASSWORD` pelos valores que você configurou no início deste artigo.

> [!WARNING]
> A propriedade de configuração `spring.datasource.initialization-mode=always` significa que o Spring Boot vai gerar um esquema de banco de dados automaticamente, usando o arquivo *schema.sql* que criaremos mais tarde, toda vez que o servidor for iniciado. Isso é ótimo para testes, mas lembre-se de que isso excluirá os dados a cada reinicialização. Portanto, você não deve usá-la em produção.

Agora, você deverá conseguir iniciar seu aplicativo usando o wrapper do Maven fornecido da seguinte maneira:

```bash
./mvnw spring-boot:run
```

Aqui está uma captura de tela do aplicativo em execução pela primeira vez:

[![O aplicativo em execução](media/configure-spring-data-jdbc-with-azure-postgresql/create-postgresql-01.png)](media/configure-spring-data-jdbc-with-azure-postgresql/create-postgresql-01.png#lightbox)

### <a name="create-the-database-schema"></a>Criar o esquema de banco de dados

O Spring Boot executará o arquivo *src/main/resources/schema.sql* automaticamente para criar um esquema de banco de dados. Crie esse arquivo e adicione o seguinte conteúdo:

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id SERIAL PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BOOLEAN);
```

Pare o aplicativo em execução e inicie-o novamente usando o comando a seguir. O aplicativo agora usará o banco de dados `demo` que você criou anteriormente e criará uma tabela `todo` dentro dele.

```bash
./mvnw spring-boot:run
```

## <a name="code-the-application"></a>Codificar o aplicativo

Em seguida, adicione o código Java que usará o JDBC para armazenar e recuperar dados do servidor PostgreSQL.

[!INCLUDE [spring-data-jdbc-create-application.md](includes/spring-data-jdbc-create-application.md)]

Aqui está uma captura de tela dessas solicitações cURL:

[![Testar com o cURL](media/configure-spring-data-jdbc-with-azure-postgresql/create-postgresql-02.png)](media/configure-spring-data-jdbc-with-azure-postgresql/create-postgresql-02.png#lightbox)

Parabéns! Você criou um aplicativo Spring Boot que usa o JDBC para armazenar e recuperar dados do Banco de Dados do Azure para PostgreSQL.

[!INCLUDE [spring-data-conclusion.md](includes/spring-data-conclusion.md)]

### <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações sobre o Spring Data JDBC, confira a [documentação de referência](https://docs.spring.io/spring-data/jdbc/docs/current/reference/html/#reference) do Spring.

Para obter mais informações sobre como usar o Azure com o Java, confira [Azure para desenvolvedores Java](../index.yml) e [Trabalhar com o Azure DevOps e com o Java](/azure/devops/).