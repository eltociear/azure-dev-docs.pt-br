---
title: Usar o Spring Data R2DBC com o Banco de Dados do Azure para MySQL
description: Saiba como usar o Spring Data R2DBC com um Banco de Dados do Azure para MySQL.
documentationcenter: java
ms.date: 03/18/2020
ms.service: mysql
ms.tgt_pltfrm: multiple
ms.author: judubois
ms.topic: article
ms.openlocfilehash: d4da0f60df8d4c6e1da89ad24c893387cdda6be9
ms.sourcegitcommit: a631b36ec1277ee9397a860c597ffdd5495d88e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83369905"
---
# <a name="use-spring-data-r2dbc-with-azure-database-for-mysql"></a>Usar o Spring Data R2DBC com o Banco de Dados do Azure para MySQL

Este tópico demonstra a criação de um aplicativo de exemplo que usa o [Spring Data R2DBC](https://spring.io/projects/spring-data-r2dbc) para armazenar e recuperar informações no [Banco de Dados do Azure para MySQL](https://docs.microsoft.com/azure/mysql/) usando a implementação do R2DBC para MySQL do [repositório do GitHub r2dbc-mysql](https://github.com/mirromutth/r2dbc-mysql).

O [R2DBC](https://r2dbc.io/) traz APIs reativas para bancos de dados relacionais tradicionais. Você pode usá-lo com o Spring WebFlux para criar aplicativos Spring Boot totalmente reativos que usam APIs sem bloqueio. Ele fornece melhor escalabilidade do que a abordagem clássica "um thread por conexão".

[!INCLUDE [spring-data-prerequisites.md](includes/spring-data-prerequisites.md)]

[!INCLUDE [spring-data-mysql-setup.md](includes/spring-data-mysql-setup.md)]

[!INCLUDE [spring-data-create-reactive.md](includes/spring-data-create-reactive.md)]

### <a name="generate-the-application-by-using-spring-initializr"></a>Gerar o aplicativo usando o Spring Initializr

Gere o aplicativo na linha de comando ao digitar:

```bash
curl https://start.spring.io/starter.tgz -d dependencies=webflux,data-r2dbc -d baseDir=azure-database-workshop -d bootVersion=2.3.0.RC1 -d javaVersion=8 | tar -xzvf -
```

### <a name="add-the-reactive-mysql-driver-implementation"></a>Adicionar a implementação do driver MySQL reativo

Abra o arquivo *pom.xml* do projeto gerado para adicionar o driver MySQL reativo do [repositório no GitHub r2dbc-mysql](https://github.com/mirromutth/r2dbc-mysql).

Após a dependência `spring-boot-starter-webflux`, adicione o seguinte snippet de código:

```xml
<dependency>
   <groupId>dev.miku</groupId>
   <artifactId>r2dbc-mysql</artifactId>
   <version>0.8.1.RELEASE</version>
   <scope>runtime</scope>
</dependency>
```

### <a name="configure-spring-boot-to-use-azure-database-for-mysql"></a>Configurar o Spring Boot para usar o Banco de Dados do Azure para MySQL

Abra o arquivo *src/main/resources/application.properties* e adicione:

```properties
logging.level.org.springframework.data.r2dbc=DEBUG

spring.r2dbc.url=r2dbc:pool:mysql://$AZ_DATABASE_NAME.mysql.database.azure.com:3306/demo
spring.r2dbc.username=spring@$AZ_DATABASE_NAME
spring.r2dbc.password=$AZ_MYSQL_PASSWORD
```

- Substitua as duas variáveis `$AZ_DATABASE_NAME` pelo valor que você configurou no início deste artigo.
- Substitua a variável `$AZ_MYSQL_PASSWORD` pelo valor que você configurou no início deste artigo.

> [!NOTE]
> Para obter um melhor desempenho, a propriedade `spring.r2dbc.url` está configurada para usar um pool de conexões usando [r2dbc-pool](https://github.com/r2dbc/r2dbc-pool).

Agora, você deveria conseguir iniciar o aplicativo usando o wrapper do Maven fornecido:

```bash
./mvnw spring-boot:run
```

Aqui está uma captura de tela do aplicativo em execução pela primeira vez:

[![O aplicativo em execução](media/configure-spring-data-r2dbc-with-azure-mysql/create-mysql-01.png)](media/configure-spring-data-r2dbc-with-azure-mysql/create-mysql-01.png#lightbox)

### <a name="create-the-database-schema"></a>Criar o esquema de banco de dados

[!INCLUDE [spring-data-r2dbc-create-schema.md](includes/spring-data-r2dbc-create-schema.md)]

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id SERIAL PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BOOLEAN);
```

Pare o aplicativo em execução e inicie-o novamente. O aplicativo agora usará o banco de dados `demo` que você criou anteriormente e criará uma tabela `todo` dentro dele.

```bash
./mvnw spring-boot:run
```

Aqui está uma captura de tela da tabela do banco de dados enquanto está sendo criada:

[![Criação da tabela do banco de dados](media/configure-spring-data-r2dbc-with-azure-mysql/create-mysql-02.png)](media/configure-spring-data-r2dbc-with-azure-mysql/create-mysql-02.png#lightbox)

## <a name="code-the-application"></a>Codificar o aplicativo

Em seguida, adicione o código Java que usará o R2DBC para armazenar e recuperar dados do servidor MySQL.

[!INCLUDE [spring-data-r2dbc-create-application.md](includes/spring-data-r2dbc-create-application.md)]

Aqui está uma captura de tela dessas solicitações cURL:

[![Testar com o cURL](media/configure-spring-data-r2dbc-with-azure-mysql/create-mysql-03.png)](media/configure-spring-data-r2dbc-with-azure-mysql/create-mysql-03.png#lightbox)

Parabéns! Você criou um aplicativo Spring Boot totalmente reativo que usa o R2DBC para armazenar e recuperar dados do Banco de Dados do Azure para MySQL.

[!INCLUDE [spring-data-conclusion.md](includes/spring-data-conclusion.md)]

### <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações sobre o Spring Data R2DBC, confira a [documentação de referência](https://docs.spring.io/spring-data/r2dbc/docs/current/reference/html/#reference) do Spring.

Para obter mais informações sobre como usar o Azure com o Java, confira [Azure para desenvolvedores Java](/azure/developer/java/) e [Trabalhar com o Azure DevOps e com o Java](/azure/devops/).
