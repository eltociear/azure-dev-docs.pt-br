---
title: Usar o Spring Data R2DBC com o Banco de Dados SQL do Azure
description: Saiba como usar o Spring Data R2DBC com um Banco de Dados SQL do Azure.
documentationcenter: java
ms.date: 04/28/2020
ms.service: sql-database
ms.tgt_pltfrm: multiple
ms.author: judubois
ms.topic: article
ms.openlocfilehash: 64316322d509d588f94185452d5c21fdee6e9ef1
ms.sourcegitcommit: e9accb9d82b5c633dffffd148974911398f2d096
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/06/2020
ms.locfileid: "86018654"
---
# <a name="use-spring-data-r2dbc-with-azure-sql-database"></a>Usar o Spring Data R2DBC com o Banco de Dados SQL do Azure

Este tópico demonstra a criação de um aplicativo de exemplo que usa o [Spring Data R2DBC](https://spring.io/projects/spring-data-r2dbc) para armazenar e recuperar informações no [Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/) usando a implementação do R2DBC para Microsoft SQL Server do [repositório do GitHub r2dbc-mssql](https://github.com/r2dbc/r2dbc-mssql).

O [R2DBC](https://r2dbc.io/) traz APIs reativas para bancos de dados relacionais tradicionais. Você pode usá-lo com o Spring WebFlux para criar aplicativos Spring Boot totalmente reativos que usam APIs sem bloqueio. Ele fornece melhor escalabilidade do que a abordagem clássica "um thread por conexão".

[!INCLUDE [spring-data-prerequisites.md](includes/spring-data-prerequisites.md)]

## <a name="sample-application"></a>Aplicativo de exemplo

Neste artigo, escreveremos o código de um aplicativo de exemplo. Caso você queira adiantar o processo, esse aplicativo já está codificado e disponível em [https://github.com/Azure-Samples/quickstart-spring-data-r2dbc-sql-server](https://github.com/Azure-Samples/quickstart-spring-data-r2dbc-sql-server).

[!INCLUDE [spring-data-sql-server-setup.md](includes/spring-data-sql-server-setup.md)]

[!INCLUDE [spring-data-create-reactive.md](includes/spring-data-create-reactive.md)]

### <a name="generate-the-application-by-using-spring-initializr"></a>Gerar o aplicativo usando o Spring Initializr

Gere o aplicativo na linha de comando executando o seguinte comando:

```bash
curl https://start.spring.io/starter.tgz -d dependencies=webflux,data-r2dbc -d baseDir=azure-database-workshop -d bootVersion=2.3.1.RELEASE -d javaVersion=8 | tar -xzvf -
```

### <a name="add-the-reactive-azure-sql-database-driver-implementation"></a>Adicionar a implementação do driver reativo do Banco de Dados SQL do Azure

Abra o arquivo *pom.xml* do projeto gerado para adicionar o driver reativo do Banco de Dados SQL do Azure do [repositório do GitHub r2dbc-mssql](https://github.com/r2dbc/r2dbc-mssql).

Após a dependência `spring-boot-starter-webflux`, adicione o seguinte texto:

```xml
<dependency>
    <groupId>io.r2dbc</groupId>
    <artifactId>r2dbc-mssql</artifactId>
    <scope>runtime</scope>
</dependency>
```

### <a name="configure-spring-boot-to-use-azure-sql-database"></a>Configurar o Spring Boot para usar o Banco de Dados SQL do Azure

Abra o arquivo *src/main/resources/application.properties* e adicione o seguinte texto:

```properties
logging.level.org.springframework.data.r2dbc=DEBUG

spring.r2dbc.url=r2dbc:pool:mssql://$AZ_DATABASE_NAME.database.windows.net:1433/demo
spring.r2dbc.username=spring@$AZ_DATABASE_NAME
spring.r2dbc.password=$AZ_SQL_SERVER_PASSWORD
```

Substitua as duas variáveis `$AZ_DATABASE_NAME` e a variável `$AZ_SQL_SERVER_PASSWORD` pelos valores que você configurou no início deste artigo.

> [!NOTE]
> Para obter um melhor desempenho, a propriedade `spring.r2dbc.url` está configurada para usar um pool de conexões usando [r2dbc-pool](https://github.com/r2dbc/r2dbc-pool).

Agora, você deverá conseguir iniciar seu aplicativo usando o wrapper do Maven fornecido da seguinte maneira:

```bash
./mvnw spring-boot:run
```

Aqui está uma captura de tela do aplicativo em execução pela primeira vez:

[![O aplicativo em execução](media/configure-spring-data-r2dbc-with-azure-azure-sql/create-azure-sql-01.png)](media/configure-spring-data-r2dbc-with-azure-azure-sql/create-azure-sql-01.png#lightbox)

### <a name="create-the-database-schema"></a>Criar o esquema de banco de dados

[!INCLUDE [spring-data-r2dbc-create-schema.md](includes/spring-data-r2dbc-create-schema.md)]

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id INT IDENTITY PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BIT);
```

Pare o aplicativo em execução e inicie-o novamente usando o comando a seguir. O aplicativo agora usará o banco de dados `demo` que você criou anteriormente e criará uma tabela `todo` dentro dele.

```bash
./mvnw spring-boot:run
```

Aqui está uma captura de tela da tabela do banco de dados enquanto está sendo criada:

[![Criação da tabela do banco de dados](media/configure-spring-data-r2dbc-with-azure-azure-sql/create-azure-sql-02.png)](media/configure-spring-data-r2dbc-with-azure-azure-sql/create-azure-sql-02.png#lightbox)

## <a name="code-the-application"></a>Codificar o aplicativo

Em seguida, adicione o código Java que usará o R2DBC para armazenar e recuperar dados do servidor do Banco de Dados SQL do Azure.

[!INCLUDE [spring-data-r2dbc-create-application.md](includes/spring-data-r2dbc-create-application.md)]

Aqui está uma captura de tela dessas solicitações cURL:

[![Testar com o cURL](media/configure-spring-data-r2dbc-with-azure-azure-sql/create-azure-sql-03.png)](media/configure-spring-data-r2dbc-with-azure-azure-sql/create-azure-sql-03.png#lightbox)

Parabéns! Você criou um aplicativo Spring Boot totalmente reativo que usa o R2DBC para armazenar e recuperar dados do Banco de Dados SQL do Azure.

[!INCLUDE [spring-data-conclusion.md](includes/spring-data-conclusion.md)]

### <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações sobre o Spring Data R2DBC, confira a [documentação de referência](https://docs.spring.io/spring-data/r2dbc/docs/current/reference/html/#reference) do Spring.

Para obter mais informações sobre como usar o Azure com o Java, confira [Azure para desenvolvedores Java](/azure/developer/java/) e [Trabalhar com o Azure DevOps e com o Java](/azure/devops/).
