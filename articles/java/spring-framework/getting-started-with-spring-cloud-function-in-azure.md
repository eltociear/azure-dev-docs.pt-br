---
title: Introdução ao Spring Cloud Function no Azure
description: Saiba mais sobre como usar o Spring Cloud Function no Azure.
documentationcenter: java
author: jdubois
manager: brborges
ms.author: judubois
ms.date: 07/17/2019
ms.service: azure-functions
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: 30c225ed4d8e17860fe169e326f994c70ebe7d85
ms.sourcegitcommit: 0d492c9cc9b5295285ab75da55e5ab0577576287
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370717"
---
# <a name="getting-started-with-spring-cloud-function-in-azure"></a>Introdução ao Spring Cloud Function no Azure

Este artigo descreve como usar o [Spring Cloud Function](https://spring.io/projects/spring-cloud-function) para desenvolver uma função Java e publicá-la no Azure Functions. Quando terminar, seu código de função será executado nos [Plano de Consumo](/azure/azure-functions/functions-scale#consumption-plan) no Azure e poderá ser disparado usando uma solicitação HTTP.

[!INCLUDE [quickstarts-free-trial-note](includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

Para desenvolver funções usando Java, você deve ter o seguinte instalado:

- [Java Developer Kit](https://aka.ms/azure-jdks), versão 8
- [Apache Maven](https://maven.apache.org), versão 3.0 ou posterior
- [CLI do Azure](/cli/azure)
- [Azure Functions Core Tools](/azure/azure-functions/functions-run-local#v2) versão 2.7.1158 ou posterior

> [!IMPORTANT]
> A variável de ambiente JAVA_HOME deve ser definida como o local de instalação do JDK para concluir este guia de início rápido.

## <a name="what-we-are-going-to-build"></a>O que vamos criar

Vamos criar uma função "Olá, Mundo" clássica que é executada no Azure Functions e configurada com o Spring Cloud Function.

Ela receberá um objeto JSON `User` simples, que contém um nome de usuário e retorna um objeto `Greeting`, que contém a mensagem de boas-vindas para o usuário.

O projeto que criamos aqui está disponível no [https://github.com/Azure-Samples/hello-spring-function-azure](https://github.com/Azure-Samples/hello-spring-function-azure), para que você possa usar esse repositório de exemplo diretamente se quiser ver o trabalho final que é detalhado neste guia de início rápido.

## <a name="create-a-new-maven-project"></a>Criar um novo projeto Maven

Vamos criar um projeto Maven vazio e configurá-lo com o Azure Functions e o Spring Cloud Function.

Em uma pasta vazia, crie um novo *pom.xml* e copie/cole o conteúdo do projeto de exemplo em [https://github.com/Azure-Samples/hello-spring-function-azure/blob/master/pom.xml](https://github.com/Azure-Samples/hello-spring-function-azure/blob/master/pom.xml).

> [!NOTE]
> Esse arquivo usa dependências de Maven do Spring Boot e do Spring Cloud Function e configura os plug-ins de Maven do Spring Boot e do Azure Functions.

Algumas propriedades precisam ser personalizadas para seu aplicativo:

- `<functionAppName>` é o nome de sua função do Azure
- `<functionAppRegion>` é o nome da região do Azure em que a função é implantada
- `<functionResourceGroup>` é o nome do grupo de recursos do Azure que você está usando

Você deve alterar essas propriedades diretamente próximo à parte superior do arquivo *pom.xml*:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <azure.functions.maven.plugin.version>1.5.0</azure.functions.maven.plugin.version>
    <functionAppName>my-spring-function</functionAppName>
    <functionAppRegion>westus</functionAppRegion>
    <stagingDirectory>${project.build.directory}/azure-functions/${functionAppName}</stagingDirectory>
    <functionResourceGroup>my-resource-group</functionResourceGroup>
    <start-class>com.example.HelloFunction</start-class>
    <spring.boot.wrapper.version>1.0.25.RELEASE</spring.boot.wrapper.version>
</properties>
```

## <a name="create-azure-configuration-files"></a>Criar arquivos de configuração do Azure

Crie uma pasta *src/main/azure* e adicione os seguintes arquivos de configuração do Azure Functions a ela.

*host.json*:

```json
{
  "version": "2.0",
  "functionTimeout": "00:10:00"
}
```

*local.settings.json*:

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "java",
    "AzureWebJobsDashboard": ""
  }
}
```

## <a name="create-domain-objects"></a>Criar objetos de domínio

O Azure Functions pode receber e enviar objetos no formato JSON.
Agora, vamos criar nossos objetos `User` e `Greeting`, que representam nosso modelo de domínio.
Você pode criar objetos mais complexos, com mais propriedades, se quiser personalizar este guia de início rápido e torná-lo mais interessante para você.

Crie uma pasta *src/main/java/com/example/model* e adicione os dois arquivos a seguir:

*User.java*:

```java
package com.example.model;

public class User {

    public User() {
    }

    public User(String name) {
        this.name = name;
    }

    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

*Greeting.java*:

```java
package com.example.model;

public class Greeting {

    public Greeting() {
    }

    public Greeting(String message) {
        this.message = message;
    }

    private String message;

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }
}
```

## <a name="create-the-spring-boot-application"></a>Criar o aplicativo Spring Boot

Este aplicativo gerenciará toda a lógica de negócios e terá acesso a todo o ecossistema do Spring Boot.
Sendo assim, ele trará dois benefícios principais em relação a uma função do Azure padrão:

- Ele não depende das APIs do Azure Functions, de modo que pode ser portado facilmente para outros sistemas. Por exemplo, ele pode ser reutilizado em um aplicativo Spring Boot normal.
- Ele pode usar todas as anotações `@Enable` do Spring Boot para adicionar novos recursos avançados com facilidade.

Na pasta *src/main/java/com/example*, crie o seguinte arquivo, que é um aplicativo Spring Boot normal:

*HelloFunction.java*:

```java
package com.example;

import com.example.model.Greeting;
import com.example.model.User;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;

import java.util.function.Function;

@SpringBootApplication
public class HelloFunction {

    public static void main(String[] args) throws Exception {
        SpringApplication.run(HelloFunction.class, args);
    }

    @Bean
    public Function<User, Greeting> hello() {
        return user -> new Greeting("Welcome, " + user.getName());
    }
}
```

> [!NOTE] 
> A função `hello()` é bastante específica:
> 
> - Ela retorna um `java.util.function.Function`, que é a função que será usada neste guia de início rápido. Ela contém a lógica de negócios e usa uma API Java padrão para transformar um objeto em outro.
> - Como tem a anotação `@Bean`, ela é um Spring Bean e, por padrão, seu nome é o mesmo do método, `hello`. Isso é importante quando você quer criar outras funções em seu aplicativo, pois esse nome deve corresponder ao nome do Azure Functions que criaremos na próxima seção.

## <a name="create-the-azure-function"></a>Criar a função do Azure

Para se beneficiar de toda a API do Azure Functions, agora vamos codificar uma classe específica: trata-se de uma função do Azure que delegará sua execução à função do Spring Cloud que criamos na etapa anterior.

Na pasta *src/main/java/com/example*, crie a seguinte função do Azure:

*HelloHandler.java*:

```java
package com.example;

import com.example.model.Greeting;
import com.example.model.User;
import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.AuthorizationLevel;
import com.microsoft.azure.functions.annotation.FunctionName;
import com.microsoft.azure.functions.annotation.HttpTrigger;
import org.springframework.cloud.function.adapter.azure.AzureSpringBootRequestHandler;

import java.util.Optional;

public class HelloHandler extends AzureSpringBootRequestHandler<User, Greeting> {

    @FunctionName("hello")
    public HttpResponseMessage execute(
            @HttpTrigger(name = "request", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<User>> request,
            ExecutionContext context) {

        context.getLogger().info("Greeting user name: " + request.getBody().get().getName());
        return request
                .createResponseBuilder(HttpStatus.OK)
                .body(handleRequest(request.getBody().get(), context))
                .header("Content-Type", "application/json")
                .build();
    }
}
```

Essa classe Java é uma função do Azure, com os seguintes recursos interessantes:

- Ela estende `AzureSpringBootRequestHandler`, que vincula o Azure Functions e o Spring Cloud Function. Ela fornece o método `handleRequest()` usado em seu método `execute()`.
- O nome da função, conforme definido pela anotação `@FunctionName("hello")`, é o mesmo que o Spring Bean que configuramos na etapa anterior, `hello`.
- Trata-se de uma função do Azure real, para que você possa usar a API do Azure Functions completa.

## <a name="add-unit-tests"></a>Adicionar testes de unidade

Esta etapa é opcional, mas como um bom desenvolvedor, você deve adicionar testes de unidade para validar que o aplicativo funciona corretamente.

Crie uma pasta *src/test/java/com/example* e adicione os testes de JUnit a seguir:

*HelloFunctionTest.java*:

```java
package com.example;

import com.example.model.Greeting;
import com.example.model.User;
import org.junit.Test;
import org.springframework.cloud.function.adapter.azure.AzureSpringBootRequestHandler;

import static org.assertj.core.api.Assertions.assertThat;

public class HelloFunctionTest {

    @Test
    public void test() {
        Greeting result = new HelloFunction().hello().apply(new User("foo"));
        assertThat(result.getMessage()).isEqualTo("Welcome, foo");
    }

    @Test
    public void start() throws Exception {
        AzureSpringBootRequestHandler<User, Greeting> handler = new AzureSpringBootRequestHandler<>(
                HelloFunction.class);
        Greeting result = handler.handleRequest(new User("foo"), null);
        handler.close();
        assertThat(result.getMessage()).isEqualTo("Welcome, foo");
    }
}
```

Agora, você pode testar sua função do Azure usando o Maven:

```bash
mvn clean test
```

## <a name="run-the-function-locally"></a>Executar a função localmente

Antes de implantar seu aplicativo na função do Azure, primeiro vamos testá-lo localmente.

Primeiro, você precisa empacotar o aplicativo em um arquivo Jar:

```bash
mvn package
```

Agora que o aplicativo está empacotado, você pode executá-lo usando o plug-in de Maven `azure-functions`:

```bash
mvn azure-functions:run
```

Agora, a função do Azure deve estar disponível no localhost usando a porta 7071. Você pode testar a função enviando uma solicitação POST a ela, com um objeto `User` no formato JSON. Por exemplo, usando cURL:

```bash
curl http://localhost:7071/api/hello -d "{\"name\":\"Azure\"}"
```

Agora, a função deve responder a você com um objeto `Greeting`, ainda no formato JSON:

```Output
{
  "message": "Welcome, Azure"
}
```

Esta é uma captura de tela com a solicitação cURL na parte superior da tela e a função do Azure local na parte inferior:

 ![Função do Azure em execução localmente][RFL01]

## <a name="deploy-the-function-to-azure-functions"></a>Implantar a função no Azure Functions

Agora, você publicará a função do Azure para produção. Lembre-se de que as propriedades `<functionAppName>`, `<functionAppRegion>` e `<functionResourceGroup>` que você definiu em seu *pom.xml* serão usadas para configurar a função.

> [!NOTE]
> O plug-in do Maven precisará ser autenticado com o Azure; se você tiver a CLI do Azure instalada, use `az login` antes de continuar.
> Confira [aqui](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication) para obter mais opções de autenticação.

Execute o Maven para implantar a função automaticamente:

```bash
mvn azure-functions:deploy
```

Agora, acesse o [portal do Azure](https://portal.azure.com) para encontrar o `Function App` que foi criado.

Clique na função:

- Na visão geral da função, observe a URL da função.
- Selecione a guia **Recursos da plataforma** para localizar o serviço de **Registro de streaming** e, em seguida, selecione este serviço para verificar a função em execução.

Agora, assim como fez na seção anterior, use cURL para acessar a função em execução. Substitua `your-function-name` pelo nome da função real:

```bash
curl https:/your-function-name.azurewebsites.net/api/hello -d "{\"name\":\"Azure\"}"
```

Assim como na seção anterior, a função deve responder a você com um objeto `Greeting`, ainda no formato JSON:

```Output
{
  "message": "Welcome, Azure"
}
```

Parabéns, você tem uma função Spring Cloud em execução no Azure Functions!

<!-- IMG List -->

[RFL01]: media/getting-started-with-spring-cloud-function-in-azure/RFL01.png
