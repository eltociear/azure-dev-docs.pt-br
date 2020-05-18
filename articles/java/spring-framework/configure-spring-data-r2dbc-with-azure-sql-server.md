---
title: Usar o Spring Data R2DBC com o Banco de Dados SQL do Azure
description: Saiba como usar o Spring Data R2DBC com um Banco de Dados SQL do Azure.
documentationcenter: java
ms.date: 04/28/2020
ms.service: sql-database
ms.tgt_pltfrm: multiple
ms.author: judubois
ms.topic: article
ms.openlocfilehash: 057f261de707adac2ab3ef9ac52834a9848362b6
ms.sourcegitcommit: a631b36ec1277ee9397a860c597ffdd5495d88e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83369993"
---
# <a name="use-spring-data-r2dbc-with-azure-sql-database"></a>Usar o Spring Data R2DBC com o Banco de Dados SQL do Azure

Este tópico demonstra a criação de um aplicativo de exemplo que usa o [Spring Data R2DBC](https://spring.io/projects/spring-data-r2dbc) para armazenar e recuperar informações no [Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/) usando a implementação do R2DBC para Microsoft SQL Server do [repositório do GitHub r2dbc-mssql](https://github.com/r2dbc/r2dbc-mssql).

O [R2DBC](https://r2dbc.io/) traz APIs reativas para bancos de dados relacionais tradicionais. Você pode usá-lo com o Spring WebFlux para criar aplicativos Spring Boot totalmente reativos que usam APIs sem bloqueio. Ele fornece melhor escalabilidade do que a abordagem clássica "um thread por conexão".

[!INCLUDE [spring-data-prerequisites.md](includes/spring-data-prerequisites.md)]

## <a name="prepare-the-working-environment"></a>Preparar o ambiente de trabalho

Primeiro, configure algumas variáveis de ambiente usando os seguintes comandos:

```bash
AZ_RESOURCE_GROUP=r2dbc-workshop
AZ_DATABASE_NAME=<YOUR_DATABASE_NAME>
AZ_LOCATION=<YOUR_AZURE_REGION>
AZ_SQL_SERVER_USERNAME=spring
AZ_SQL_SERVER_PASSWORD=<YOUR_AZURE_SQL_PASSWORD>
AZ_LOCAL_IP_ADDRESS=<YOUR_LOCAL_IP_ADDRESS>
```

Substitua os espaços reservados pelos seguintes valores, que são usados em todo este artigo:

- `<YOUR_DATABASE_NAME>`: O nome do servidor do Banco de Dados SQL do Azure. O nome deve ser exclusivo em todo o Azure.
- `<YOUR_AZURE_REGION>`: A região do Azure que você usará. Você pode usar `eastus` por padrão, mas é recomendável configurar uma região mais próxima de onde você mora. Você pode ter a lista completa de regiões disponíveis ao digitar `az account list-locations`.
- `<AZ_SQL_SERVER_PASSWORD>`: A senha do servidor do Banco de Dados SQL do Azure. Essa senha deveria ter um mínimo de oito caracteres. Os caracteres deveriam ser de três das seguintes categorias: Letras maiúsculas, letras minúsculas, números (0-9) e caracteres não alfanuméricos (!, $, #, % e assim por diante).
- `<YOUR_LOCAL_IP_ADDRESS>`: O endereço IP do computador local, do qual você executará o aplicativo Spring Boot. Uma forma conveniente de encontrá-lo é apontar o navegador para [whatismyip.akamai.com](http://whatismyip.akamai.com/).

Em seguida, crie um grupo de recursos:

```azurecli
az group create \
    --name $AZ_RESOURCE_GROUP \
    --location $AZ_LOCATION \
    | jq
```

> [!NOTE]
> Usamos o utilitário `jq`, que é instalado por padrão no [Azure Cloud Shell](https://shell.azure.com/) para exibir dados JSON e torná-los mais legíveis.
> Se você não gostar desse utilitário, poderá remover com segurança a parte `| jq` de todos os comandos que usaremos.

## <a name="create-an-azure-sql-database-instance"></a>Criar uma instância do Banco de Dados SQL do Azure

A primeira coisa que criaremos é um servidor gerenciado do Banco de Dados SQL do Azure.

> [!NOTE]
> Você pode ler informações mais detalhadas sobre como criar servidores do Banco de Dados SQL do Azure no [Início Rápido: Criar um banco de dados individual do Banco de Dados SQL do Azure](/azure/sql-database/sql-database-single-database-get-started).

No [Azure Cloud Shell](https://shell.azure.com/), execute o seguinte script:

```azurecli
az sql server create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME \
    --location $AZ_LOCATION \
    --admin-user $AZ_SQL_SERVER_USERNAME \
    --admin-password $AZ_SQL_SERVER_PASSWORD \
    | jq
```

Esse comando cria um servidor do Banco de Dados SQL do Azure.

### <a name="configure-a-firewall-rule-for-your-azure-sql-database-server"></a>Configurar uma regra de firewall para o servidor do Banco de Dados SQL do Azure

As instâncias do Banco de Dados SQL do Azure são protegidas por padrão. Elas têm um firewall que não permite nenhuma conexão de entrada. Para usar o banco de dados, você precisa adicionar uma regra de firewall que permitirá que o endereço IP local acesse o servidor de banco de dados.

Como você configurou nosso endereço IP local no início deste artigo, você pode abrir o firewall do servidor ao executar:

```azurecli
az sql server firewall-rule create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME-database-allow-local-ip \
    --server $AZ_DATABASE_NAME \
    --start-ip-address $AZ_LOCAL_IP_ADDRESS \
    --end-ip-address $AZ_LOCAL_IP_ADDRESS \
    | jq
```

### <a name="configure-a-azure-sql-database"></a>Configurar um banco de dados SQL do Azure

O servidor do Banco de Dados SQL do Azure que você criou anteriormente está vazio. Ele não tem nenhum banco de dados que você possa usar com o aplicativo Spring Boot. Criar um banco de dados chamado `demo`:

```azurecli
az sql db create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name demo \
    --server $AZ_DATABASE_NAME \
    | jq
```

[!INCLUDE [spring-data-create-reactive.md](includes/spring-data-create-reactive.md)]

### <a name="generate-the-application-by-using-spring-initializr"></a>Gerar o aplicativo usando o Spring Initializr

Gere o aplicativo na linha de comando ao digitar:

```bash
curl https://start.spring.io/starter.tgz -d dependencies=webflux,data-r2dbc -d baseDir=azure-database-workshop -d bootVersion=2.3.0.RC1 -d javaVersion=8 | tar -xzvf -
```

### <a name="add-the-reactive-azure-sql-database-driver-implementation"></a>Adicionar a implementação do driver reativo do Banco de Dados SQL do Azure

Abra o arquivo *pom.xml* do projeto gerado para adicionar o driver reativo do Banco de Dados SQL do Azure do [repositório do GitHub r2dbc-mssql](https://github.com/r2dbc/r2dbc-mssql).

Após a dependência `spring-boot-starter-webflux`, adicione o seguinte snippet de código:

```xml
<dependency>
    <groupId>io.r2dbc</groupId>
    <artifactId>r2dbc-mssql</artifactId>
    <scope>runtime</scope>
</dependency>
```

### <a name="configure-spring-boot-to-use-azure-sql-database"></a>Configurar o Spring Boot para usar o Banco de Dados SQL do Azure

Abra o arquivo *src/main/resources/application.properties* e adicione:

```properties
logging.level.org.springframework.data.r2dbc=DEBUG

spring.r2dbc.url=r2dbc:pool:mssql://$AZ_DATABASE_NAME.database.windows.net:1433/demo
spring.r2dbc.username=spring@$AZ_DATABASE_NAME
spring.r2dbc.password=$AZ_SQL_SERVER_PASSWORD
```

- Substitua as duas variáveis `$AZ_DATABASE_NAME` pelo valor que você configurou no início deste artigo.
- Substitua a variável `$AZ_SQL_SERVER_PASSWORD` pelo valor que você configurou no início deste artigo.

> [!NOTE]
> Para obter um melhor desempenho, a propriedade `spring.r2dbc.url` está configurada para usar um pool de conexões usando [r2dbc-pool](https://github.com/r2dbc/r2dbc-pool).

Agora, você deveria conseguir iniciar o aplicativo usando o wrapper do Maven fornecido:

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

Pare o aplicativo em execução e inicie-o novamente. O aplicativo agora usará o banco de dados `demo` que você criou anteriormente e criará uma tabela `todo` dentro dele.

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
