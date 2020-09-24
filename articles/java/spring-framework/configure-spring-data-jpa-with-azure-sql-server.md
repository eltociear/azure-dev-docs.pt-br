---
title: Usar o Spring Data JPA com o Banco de Dados SQL do Azure
description: Saiba como usar o Spring Data JPA com um Banco de Dados SQL do Azure.
documentationcenter: java
ms.date: 06/19/2020
ms.service: sql-database
ms.tgt_pltfrm: multiple
ms.author: judubois
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: e00c4e6c35756094c3c40e0194edff0b8ebd1b89
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831962"
---
# <a name="use-spring-data-jpa-with-azure-sql-database"></a>Usar o Spring Data JPA com o Banco de Dados SQL do Azure

Este tópico demonstra como criar um aplicativo de exemplo que usa o [Spring Data JPA](https://spring.io/projects/spring-data-jpa) para armazenar e recuperar informações no [Banco de Dados SQL do Azure](/azure/sql-database/).

[A JPA (API de Persistência Java)](https://en.wikipedia.org/wiki/Java_Persistence_API) é a API Java padrão para o mapeamento relacional de objeto.

[!INCLUDE [spring-data-prerequisites.md](includes/spring-data-prerequisites.md)]

## <a name="sample-application"></a>Aplicativo de exemplo

Neste artigo, escreveremos o código de um aplicativo de exemplo. Caso você queira adiantar o processo, esse aplicativo já está codificado e disponível em [https://github.com/Azure-Samples/quickstart-spring-data-jpa-sql-server](https://github.com/Azure-Samples/quickstart-spring-data-jpa-sql-server).

[!INCLUDE [spring-data-sql-server-setup.md](includes/spring-data-sql-server-setup.md)]

### <a name="generate-the-application-by-using-spring-initializr"></a>Gerar o aplicativo usando o Spring Initializr

Gere o aplicativo na linha de comando ao digitar:

```bash
curl https://start.spring.io/starter.tgz -d dependencies=web,data-jpa,sqlserver -d baseDir=azure-database-workshop -d bootVersion=2.3.1.RELEASE -d javaVersion=8 | tar -xzvf -
```

### <a name="configure-spring-boot-to-use-azure-sql-database"></a>Configurar o Spring Boot para usar o Banco de Dados SQL do Azure

Abra o arquivo *src/main/resources/application.properties* e adicione o conteúdo a seguir. Substitua as duas variáveis `$AZ_DATABASE_NAME` e a variável `$AZ_SQL_SERVER_PASSWORD` pelos valores que você configurou no início deste artigo.

```properties
logging.level.org.hibernate.SQL=DEBUG

spring.datasource.url=jdbc:sqlserver://$AZ_DATABASE_NAME.database.windows.net:1433;database=demo;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
spring.datasource.username=spring@$AZ_DATABASE_NAME
spring.datasource.password=$AZ_SQL_SERVER_PASSWORD

spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=create-drop
```

> [!WARNING]
> A propriedade de configuração `spring.jpa.hibernate.ddl-auto=create-drop` significa que o Spring Boot criará automaticamente um esquema de banco de dados na inicialização do aplicativo e tentará excluí-lo quando for desligado. Isso é ótimo para teste, mas não deve ser usado em produção!

Agora, você deveria conseguir iniciar o aplicativo usando o wrapper do Maven fornecido:

```bash
./mvnw spring-boot:run
```

Aqui está uma captura de tela do aplicativo em execução pela primeira vez:

[![O aplicativo em execução](media/configure-spring-data-jpa-with-azure-sql-server/create-sql-server-01.png)](media/configure-spring-data-jpa-with-azure-sql-server/create-sql-server-01.png#lightbox)

## <a name="code-the-application"></a>Codificar o aplicativo

Em seguida, adicione o código Java que usará o JPA para armazenar e recuperar dados do Banco de Dados SQL do Azure.

[!INCLUDE [spring-data-jpa-create-application.md](includes/spring-data-jpa-create-application.md)]

Aqui está uma captura de tela dessas solicitações cURL:

[![Testar com o cURL](media/configure-spring-data-jpa-with-azure-sql-server/create-sql-server-02.png)](media/configure-spring-data-jpa-with-azure-sql-server/create-sql-server-02.png#lightbox)

Parabéns! Você criou um aplicativo Spring Boot que usa o JPA para armazenar e recuperar dados do Banco de Dados SQL do Azure.

[!INCLUDE [spring-data-conclusion.md](includes/spring-data-conclusion.md)]

### <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações sobre o Spring Data JPA, confira a [documentação de referência](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#reference) do Spring.

Para obter mais informações sobre como usar o Azure com o Java, confira [Azure para desenvolvedores Java](../index.yml) e [Trabalhar com o Azure DevOps e com o Java](/azure/devops/).