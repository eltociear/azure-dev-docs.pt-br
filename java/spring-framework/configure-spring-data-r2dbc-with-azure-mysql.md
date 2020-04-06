---
title: Como usar o Spring Data R2DBC com o Banco de Dados do Azure para MySQL
description: Saiba como usar o Spring Data R2DBC com um Banco de Dados do Azure para MySQL.
documentationcenter: java
ms.date: 03/18/2020
ms.service: mysql
ms.tgt_pltfrm: multiple
ms.topic: article
ms.author: judubois
ms.openlocfilehash: f5c9f3d0cccad7f6e1a8b5ab199f841925f416cc
ms.sourcegitcommit: a65fa8dbb168bd39e225a293d9ee73d18ece1864
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366744"
---
# <a name="how-to-use-spring-data-r2dbc-with-azure-mysql"></a>Como usar o Spring Data R2DBC com o MySQL do Azure

Este tópico demonstra a criação de um aplicativo de exemplo que usa o [Spring Data R2DBC](https://spring.io/projects/spring-data-r2dbc) para armazenar e recuperar informações em um [Banco de Dados do Azure para MySQL](https://docs.microsoft.com/azure/mysql/) usando a implementação R2DBC para MySQL de [https://github.com/mirromutth/r2dbc-mysql](https://github.com/mirromutth/r2dbc-mysql).

O [R2DBC](https://r2dbc.io/) traz APIs reativas para bancos de dados relacionais tradicionais. Ele pode ser usado com um Spring WebFlux para criar aplicativos Spring Boot totalmente reativos, usando APIs sem bloqueio e fornecendo melhor escalabilidade do que a abordagem clássica de um thread por conexão.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Se você não tiver uma, [obtenha uma avaliação gratuita](https://azure.microsoft.com/free/).
- [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart) ou [CLI do Azure](/cli/azure/install-azure-cli). É recomendável usar o Azure Cloud Shell para que o logon seja feito automaticamente e você tenha acesso a todas as ferramentas necessárias.
- [Java 8](https://www.azul.com/downloads/zulu/) (incluído no Azure Cloud Shell).
- [cURL](https://curl.haxx.se) ou utilitário HTTP semelhante para testar a funcionalidade.

## <a name="prepare-the-working-environment"></a>Preparar o ambiente de trabalho

Primeiro, configure algumas variáveis de ambiente usando os seguintes comandos:

```bash
AZ_RESOURCE_GROUP=r2dbc-workshop
AZ_DATABASE_NAME=<YOUR_DATABASE_NAME>
AZ_LOCATION=<YOUR_AZURE_REGION>
AZ_MYSQL_USERNAME=r2dbc
AZ_MYSQL_PASSWORD=<YOUR_MYSQL_PASSWORD>
AZ_LOCAL_IP_ADDRESS=<YOUR_LOCAL_IP_ADDRESS>
```

Substitua os espaços reservados pelos seguintes valores, que são usados em todo este tópico:

- `<YOUR_DATABASE_NAME>`: O nome da instância do MySQL Server. O nome deve ser exclusivo em todo o Azure.
- `<YOUR_AZURE_REGION>`: a região do Azure que você usará. Você pode usar `eastus` por padrão, mas é recomendável configurar uma região mais próxima de onde você mora. Para obter a lista completa de regiões disponíveis, digite `az account list-locations`.
- `<YOUR_MYSQL_PASSWORD>`: a senha do servidor de banco de dados MySQL. Essa senha deve ter pelo menos oito caracteres e conter caracteres das três seguintes categorias: letras maiúsculas em inglês, letras minúsculas em inglês, números (0 – 9) e caracteres não alfanuméricos (!, $, #, % e assim por diante).
- `<YOUR_LOCAL_IP_ADDRESS>`: o endereço IP do computador local, do qual você executará o aplicativo Spring Boot. Uma forma conveniente de encontrá-lo é fazer com que seu navegador acesse [http://ipv4.icanhazip.com](http://ipv4.icanhazip.com).

Em seguida, crie um grupo de recursos.

```azurecli
az group create \
    --name $AZ_RESOURCE_GROUP \
    --location $AZ_LOCATION \
    | jq
```

> [!NOTE]
> Usamos o utilitário `jq`, que é instalado por padrão no [Azure Cloud Shell](https://shell.azure.com/), a fim de exibir dados JSON e torná-los mais legíveis.
> Se você não gostar desse utilitário, poderá remover com segurança a parte `| jq` de todos os comandos que usaremos.

## <a name="create-an-azure-database-for-mysql"></a>Criar um Banco de Dados do Azure para MySQL

A primeira coisa que criaremos é uma instância gerenciada do MySQL Server.

> [!NOTE]
> 
> Veja informações mais detalhadas sobre a criação de bancos de dados MySQL em [Criar um servidor de Banco de Dados do Azure para MySQL usando o portal do Azure](/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal).

Ainda em sua instância do [Azure Shell](https://shell.azure.com/), execute o seguinte script:

```azurecli
az mysql server create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME \
    --location $AZ_LOCATION \
    --sku-name B_Gen5_1 \
    --storage-size 5120 \
    --admin-user $AZ_MYSQL_USERNAME \
    --admin-password $AZ_MYSQL_PASSWORD \
    | jq
```

Esse comando criará uma pequena instância do MySQL Server.

### <a name="configure-a-firewall-rule-for-your-mysql-server-instance"></a>Configurar uma regra de firewall para a instância do MySQL Server

As instâncias do Banco de Dados do Azure para MySQL são protegidas por padrão: elas têm um firewall que não permite nenhuma conexão de entrada. Para poder usar nosso banco de dados, precisamos adicionar uma regra de firewall que permita que nosso endereço IP local acesse o servidor de banco de dados.

Já que configuramos nosso endereço IP local no início deste artigo, você pode abrir o firewall do servidor executando o seguinte:

```azurecli
az mysql server firewall-rule create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME-database-allow-local-ip \
    --server $AZ_DATABASE_NAME \
    --start-ip-address $AZ_LOCAL_IP_ADDRESS \
    --end-ip-address $AZ_LOCAL_IP_ADDRESS \
    | jq
```

### <a name="configure-a-mysql-database"></a>Configurar um banco de dados MySQL

O servidor MySQL que criamos anteriormente está vazio: ele não tem nenhum banco de dados que possamos usar com nosso aplicativo Spring Boot. Criar um banco de dados chamado `r2dbc`:

```azurecli
az mysql db create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name r2dbc \
    --server-name $AZ_DATABASE_NAME \
    | jq
```

## <a name="create-a-reactive-spring-boot-application"></a>Criar um aplicativo Spring Boot reativo

Para criar um aplicativo Spring boot reativo, usaremos [Spring Initializr](https://start.spring.io/). O aplicativo que criaremos usará:

- Spring Boot 2.3.0 M3
- Java 8 (mas também funcionará com versões mais recentes, como Java 11)
- As seguintes dependências: Spring Reactive Web (também conhecido como "Spring WebFlux") e Spring Data R2DBC.

### <a name="generate-the-application-using-spring-initializr"></a>Gerar o aplicativo usando o Spring Initializr

Gere esse aplicativo usando a linha de comando, digitando o seguinte:

```bash
curl https://start.spring.io/starter.tgz -d dependencies=webflux,data-r2dbc -d baseDir=azure-r2dbc-workshop -d bootVersion=2.3.0.M3 -d javaVersion=8 | tar -xzvf -
```

### <a name="add-the-reactive-mysql-driver-implementation"></a>Adicionar a implementação do driver MySQL reativo

Abra o arquivo *pom.xml* do projeto gerado para adicionar o driver MySQL reativo de [https://github.com/mirromutth/r2dbc-mysql](https://github.com/mirromutth/r2dbc-mysql).

Após a dependência `spring-boot-starter-webflux`, adicione o seguinte snippet de código:

```xml
<dependency>
   <groupId>dev.miku</groupId>
   <artifactId>r2dbc-mysql</artifactId>
   <version>0.8.1.RELEASE</version>
   <scope>runtime</scope>
</dependency>
```

### <a name="configure-spring-boot-to-use-the-azure-database-for-mysql"></a>Configurar o Spring Boot para usar o Banco de Dados do Azure para MySQL

Abra o arquivo *src/main/resources/application.properties* e adicione:

```properties
logging.level.org.springframework.data.r2dbc=DEBUG

spring.r2dbc.url=r2dbc:mysql://$AZ_DATABASE_NAME.mysql.database.azure.com:3306/r2dbc
spring.r2dbc.username=r2dbc@$AZ_DATABASE_NAME
spring.r2dbc.password=$AZ_MYSQL_USERNAME
```

- Substitua as duas variáveis `$AZ_DATABASE_NAME` pelo valor que você configurou no início deste artigo.
- Substitua a variável `$AZ_MYSQL_USERNAME` pelo valor que você configurou no início deste artigo.

Agora você deve ser capaz de iniciar o seu aplicativo usando o wrapper do Maven fornecido:

```bash
./mvnw spring-boot:run
```

Aqui está uma captura de tela do aplicativo em execução pela primeira vez:

![Executar o aplicativo][R2DBC-MYSQL01]

### <a name="create-the-database-schema"></a>Criar o esquema de banco de dados

Dentro da classe `DemoApplication` principal, configure um novo Spring bean que criará o esquema de banco de dados que você usará:

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

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id SERIAL PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BOOLEAN);
```

Use o comando a seguir para interromper o aplicativo e executá-lo novamente. O aplicativo agora usará o banco de dados `r2dbc` que você criou anteriormente e criará uma tabela `todo` dentro dele.

```bash
./mvnw spring-boot:run
```

Aqui está uma captura de tela da tabela de banco de dados que está sendo criada:

   ![Criar a tabela de banco de dados][R2DBC-MYSQL02]

## <a name="code-the-application"></a>Codificar o aplicativo

Em seguida, adicione o código Java que usará R2DBC para armazenar e recuperar dados da instância do MySQL Server.

Agora, crie uma classe Java `Todo`, ao lado da classe `DemoApplication`:

```java
package com.example.demo;

import org.springframework.data.annotation.Id;

public class Todo {

    public Todo() {
    }

    public Todo(String description, String details, boolean done) {
        this.description = description;
        this.details = details;
        this.done = done;
    }

    @Id
    private Long id;

    private String description;

    private String details;

    private boolean done;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }

    public String getDetails() {
        return details;
    }

    public void setDetails(String details) {
        this.details = details;
    }

    public boolean isDone() {
        return done;
    }

    public void setDone(boolean done) {
        this.done = done;
    }
}
```

Essa classe é um modelo de domínio mapeado na tabela de `todo` que você criou anteriormente.

Para gerenciar essa classe, você precisará de um repositório. Defina uma nova interface `TodoRepository` no mesmo pacote:

```java
package com.example.demo;

import org.springframework.data.repository.reactive.ReactiveCrudRepository;

public interface TodoRepository extends ReactiveCrudRepository<Todo, Long> {
}
```

Este repositório é um repositório reativo gerenciado pelo Spring Data R2DBC.

Em seguida, conclua o aplicativo criando um controlador que possa armazenar e recuperar dados. Implemente uma classe `TodoController` no mesmo pacote e adicione o seguinte código:

```java
package com.example.demo;

import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.*;
import reactor.core.publisher.Flux;
import reactor.core.publisher.Mono;

@RestController
@RequestMapping("/")
public class TodoController {

    private final TodoRepository todoRepository;

    public TodoController(TodoRepository todoRepository) {
        this.todoRepository = todoRepository;
    }

    @PostMapping("/")
    @ResponseStatus(HttpStatus.CREATED)
    public Mono<Todo> createTodo(@RequestBody Todo todo) {
        return todoRepository.save(todo);
    }

    @GetMapping("/")
    public Flux<Todo> getTodos() {
        return todoRepository.findAll();
    }
}
```

Por fim, pare o aplicativo e inicie-o novamente:

```bash
./mvnw spring-boot:run
```

## <a name="test-the-application"></a>Testar o aplicativo

Para testar o aplicativo, você pode usar cURL.

Primeiro, crie um item "todo" no banco de dados:

```bash
curl  --header "Content-Type: application/json" \
          --request POST \
          --data '{"description":"configuration","details":"congratulations, you have set up R2DBC correctly!","done": "true"}' \
          http://127.0.0.1:8080
```

Este comando deve retornar o item criado:

```json
{"id":1,"description":"configuration","details":"congratulations, you have set up R2DBC correctly!","done":true}
```

Em seguida, recupere os dados usando uma nova solicitação de cURL:

```bash
curl http://127.0.0.1:8080
```

Este comando retornará a lista de "todos", incluindo o item que você criou:

```json
[{"id":1,"description":"configuration","details":"congratulations, you have set up R2DBC correctly!","done":true}]
```

Aqui está uma captura de tela dessas solicitações de cURL:

   ![Testar com cURL][R2DBC-MYSQL03]

Parabéns! Você criou um aplicativo Spring Boot totalmente reativo, que usa o R2DBC para armazenar e recuperar dados do Banco de Dados do Azure para MySQL.

## <a name="clean-up-resources"></a>Limpar os recursos

Para limpar todos os recursos usados durante este guia de início rápido, exclua o grupo de recursos:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Spring e o Azure, continue no Spring no Centro de Documentação do Azure.

> [!div class="nextstepaction"]
> [Spring no Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações sobre o Spring Data R2DBC, confira a [documentação de referência](https://docs.spring.io/spring-data/r2dbc/docs/1.0.x/reference/html/#reference) do Spring.

Para obter mais informações sobre como usar o Azure com Java, confira as páginas [Azure para Desenvolvedores Java](/azure/java/) e [Como trabalhar com o Java e o Azure DevOps](/azure/devops/).

<!-- IMG List -->

[R2DBC-MYSQL01]: media/configure-spring-data-r2dbc-with-azure-mysql/create-mysql-01.png
[R2DBC-MYSQL02]: media/configure-spring-data-r2dbc-with-azure-mysql/create-mysql-02.png
[R2DBC-MYSQL03]: media/configure-spring-data-r2dbc-with-azure-mysql/create-mysql-03.png
