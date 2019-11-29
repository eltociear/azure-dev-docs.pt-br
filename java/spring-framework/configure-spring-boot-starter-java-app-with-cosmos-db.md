---
title: Como usar o Inicializador do Spring Boot com a API SQL do Azure Cosmos DB
description: Saiba como configurar um aplicativo criado com o Inicializador do Spring Boot com a API SQL do Azure Cosmos DB.
services: cosmos-db
documentationcenter: java
author: KarlErickson
manager: barbkess
ms.author: karler
ms.date: 10/02/2019
ms.devlang: java
ms.service: cosmos-db
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: data-services
ms.openlocfilehash: 192550b74a35eb24620c58e86e6a55e86e5e90ab
ms.sourcegitcommit: 54d34557bb83f52a215bf9020263cb9f9782b41d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74118189"
---
# <a name="how-to-use-the-spring-boot-starter-with-the-azure-cosmos-db-sql-api"></a>Como usar o Inicializador do Spring Boot com a API SQL do Azure Cosmos DB

O Azure Cosmos DB é um serviço de banco de dados distribuído globalmente que permite aos desenvolvedores trabalhar com os dados usando várias APIs padrão, como as APIs SQL, MongoDB, Graph e de Tabela. O Inicializador do Spring Boot da Microsoft permite aos desenvolvedores usar aplicativos Spring Boot que se integram facilmente ao Azure Cosmos DB por meio da API SQL.

Este artigo demonstra como criar um Azure Cosmos DB usando o portal do Azure e, em seguida, usar o **[Spring Initializr]** para criar um aplicativo Spring Boot personalizado e adicionar o [Iniciador Spring Boot do Cosmos DB para Azure] ao aplicativo personalizado para armazenar e recuperar dados do Azure Cosmos DB usando o Spring Data e a API SQL do Cosmos DB.

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos são obrigatórios para que você siga as etapas neste artigo:

* Uma assinatura do Azure; se ainda não tiver uma assinatura do Azure, você poderá ativar o [benefício de assinante do MSDN] ou inscrever-se para uma [conta gratuita do Azure].
* Um JDK (Java Development Kit) com suporte. Para obter mais informações sobre os JDKs disponíveis para usar durante o desenvolvimento no Azure, confira <https://aka.ms/azure-jdks>.

## <a name="create-an-azure-cosmos-db-by-using-the-azure-portal"></a>Criar um Azure Cosmos DB usando o portal do Azure

1. Navegue até o portal do Azure em <https://portal.azure.com/> e clique em **Criar um recurso**.

1. Clique em **Bancos de Dados** e, em seguida, clique em **Azure Cosmos DB**.

    ![Portal do Azure][AZ02]

1. Na página **Azure Cosmos DB**, insira as seguintes informações:

    * Escolha a **Assinatura** você deseja usar para seu banco de dados.
    * Especifique se deseja criar um novo **Grupo de recursos** para seu banco de dados ou escolher um grupo de recursos existente.
    * Insira um **Nome da Conta** exclusivo, que será usado como o URI do banco de dados. Por exemplo: *wingtiptoysdata*.
    * Escolha **Core (SQL)** para a API.
    * Especifique o **Local** para seu banco de dados.

    Depois de especificar essas opções, clique em **Revisar + criar**, verifique suas especificações e clique em **Criar**.

    ![Portal do Azure][AZ03]

1. Quando seu banco de dados for criado, ele será listado no seu **Painel** do Azure, bem como nas páginas **Todos os Recursos** e **Azure Cosmos DB**. Você pode clicar no banco de dados em qualquer um desses locais para abrir a página de propriedades do seu cache.

1. Quando a página de propriedades do banco de dados for exibida, clique em **Chaves** e copie o URI e as chaves de acesso do banco de dados. Você usará esses valores no aplicativo Spring Boot.

    ![Portal do Azure][AZ05]

## <a name="create-a-simple-spring-boot-application-with-the-spring-initializr"></a>Criar um aplicativo Spring Boot simples com o Spring Initializr

Use as etapas a seguir para criar um novo projeto de aplicativo Spring boot com o Suporte do Azure. Como alternativa, é possível usar a amostra [azure-cosmosdb-spring-boot-sample](https://github.com/microsoft/azure-spring-boot/tree/master/azure-spring-boot-samples/azure-cosmosdb-spring-boot-sample) no repositório [azure-spring-boot](https://github.com/microsoft/azure-spring-boot). Em seguida, é possível pular diretamente para [Criar e testar o aplicativo](#build-and-test-your-app).

1. Navegue até <https://start.spring.io/>.

1. Especifique que você deseja gerar um **Projeto Maven** com **Java**, especifique a versão do **Spring Boot**, insira os nomes de **Grupo** e de **Artefato** do aplicativo, adicione o **Suporte do Azure** nas dependências e, em seguida, clique no botão para **Gerar o Projeto**.

    ![Opções básicas do Initializr Basic][SI01]

    > [!NOTE]
    >
    > O Spring Initializr usa os nomes de **Grupo** e **Artefato** para criar o nome do pacote; por exemplo: *com.example.wintiptoysdata*.

1. Quando solicitado, baixe o projeto em um caminho no computador local e extraia os arquivos.

Seu aplicativo Spring Boot simples agora está pronto para edição.

## <a name="configure-your-spring-boot-application-to-use-the-azure-spring-boot-starter"></a>Configurar o aplicativo Spring Boot para usar o Iniciador Spring Boot do Azure

1. Localize o arquivo *pom.xml* no diretório do seu aplicativo; por exemplo:

    `C:\SpringBoot\wingtiptoysdata\pom.xml`

    -ou-

    `/users/example/home/wingtiptoysdata/pom.xml`

1. Abra o arquivo *pom.xml* em um editor de texto e adicione o seguinte conteúdo ao elemento `<dependencies>`:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
    </dependency>

    <dependency>
        <groupId>io.projectreactor.netty</groupId>
        <artifactId>reactor-netty</artifactId>
        <version>0.8.3.RELEASE</version>
    </dependency>
    ```

1. Verifique se o elemento *properties* indica as versões necessárias do Java e do Azure:

    ```xml
    <properties>
       <java.version>1.8</java.version>
       <azure.version>2.2.0.M1</azure.version>
    </properties>
    ```

1. Salve e feche o arquivo *pom.xml*.

## <a name="configure-your-spring-boot-application-to-use-your-azure-cosmos-db"></a>Configure o aplicativo Spring Boot para usar o Azure Cosmos DB

1. Localize o arquivo *application.properties* no diretório *recursos* do seu aplicativo; por exemplo:

    `C:\SpringBoot\wingtiptoysdata\src\main\resources\application.properties`

    -ou-

    `/users/example/home/wingtiptoysdata/src/main/resources/application.properties`

1. Abra o arquivo *application.properties* em um editor de texto e adicione as seguintes linhas ao arquivo, então substitua os valores de exemplo pelas propriedades adequadas para seu banco de dados:

    ```text
    # Specify the DNS URI of your Azure Cosmos DB.
    azure.cosmosdb.uri=https://wingtiptoys.documents.azure.com:443/

    # Specify the access key for your database.
    azure.cosmosdb.key=57686f6120447564652c20426f6220526f636b73==

    # Specify the name of your database.
    azure.cosmosdb.database=wingtiptoysdata
    ```

1. Salve e feche o arquivo *application.properties*.

## <a name="add-sample-code-to-implement-basic-database-functionality"></a>Adicione o código de exemplo para implementar a funcionalidade básica de banco de dados

Nesta seção, você criará duas classes Java para armazenamento de dados de usuário e, em seguida, modificará sua classe de aplicativo principal para criar uma instância da classe *Usuário* e salvá-la no banco de dados.

### <a name="define-a-base-class-for-storing-user-data"></a>Definir uma classe base para armazenar os dados do usuário

1. Criar um novo arquivo denominado *User.java* no mesmo diretório que o arquivo Java do seu aplicativo principal.

1. Abra o arquivo *User.java* em um editor de texto e adicione as seguintes linhas ao arquivo para definir uma classe de usuário genérica que armazena e recupera valores no seu banco de dados:

    ```java
    package com.example.wingtiptoysdata;

    import com.microsoft.azure.spring.data.cosmosdb.core.mapping.Document;
    import com.microsoft.azure.spring.data.cosmosdb.core.mapping.PartitionKey;
    import org.springframework.data.annotation.Id;

    @Document(collection = "mycollection")
    public class User {

        @Id
        private String id;
        private String firstName;

        @PartitionKey
        private String lastName;
        private String address;

        public User(String id, String firstName, String lastName, String address) {
            this.id = id;
            this.firstName = firstName;
            this.lastName = lastName;
            this.address = address;
        }

        public User() {
        }

        public String getId() {
            return id;
        }

        public void setId(String id) {
            this.id = id;
        }

        public String getFirstName() {
            return firstName;
        }

        public void setFirstName(String firstName) {
            this.firstName = firstName;
        }

        public String getLastName() {
            return lastName;
        }

        public void setLastName(String lastName) {
            this.lastName = lastName;
        }

        public String getAddress() {
            return address;
        }

        public void setAddress(String address) {
            this.address = address;
        }

        @Override
        public String toString() {
            return String.format("%s %s, %s", firstName, lastName, address);
        }
    }
    ```

1. Salve e feche o arquivo *User.java*.

### <a name="define-a-data-repository-interface"></a>Defina uma interface de repositório de dados

1. Criar um novo arquivo denominado *UserRepository.java* no mesmo diretório que o arquivo Java do seu aplicativo principal.

1. Abra o arquivo *UserRepository.java* em um editor de texto e adicione as seguintes linhas ao arquivo para definir uma interface de repositório do usuário que estende a interface `ReactiveCosmosRepository`:

    ```java
    package com.example.wingtiptoysdata;

    import com.microsoft.azure.spring.data.cosmosdb.repository.ReactiveCosmosRepository;
    import org.springframework.stereotype.Repository;
    import reactor.core.publisher.Flux;

    @Repository
    public interface UserRepository extends ReactiveCosmosRepository<User, String> {
        Flux<User> findByFirstName(String firstName);
    }
    ```

    A interface `ReactiveCosmosRepository` substitui a interface `DocumentDbRepository` da versão anterior do inicializador. A nova interface fornece APIs síncronas e reativas para operações básicas de salvar, excluir e localizar.

1. Salve e feche o arquivo *UserRepository.java*.

### <a name="modify-the-main-application-class"></a>Modificar a classe principal do aplicativo

1. Localize o arquivo Java principal do aplicativo no diretório do pacote do aplicativo, por exemplo:

    `C:\SpringBoot\wingtiptoysdata\src\main\java\com\example\wingtiptoysdata\WingtiptoysdataApplication.java`

    -ou-

    `/users/example/home/wingtiptoysdata/src/main/java/com/example/wingtiptoysdata/WingtiptoysdataApplication.java`

1. Abra o arquivo Java do aplicativo principal em um editor de texto e adicione as seguintes linhas ao arquivo:

    ```java
    package com.example.wingtiptoysdata;

    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.boot.CommandLineRunner;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.util.Assert;
    import reactor.core.publisher.Flux;
    import reactor.core.publisher.Mono;

    import javax.annotation.PostConstruct;
    import javax.annotation.PreDestroy;
    import java.util.Optional;

    @SpringBootApplication
    public class WingtiptoysdataApplication implements CommandLineRunner {

        private static final Logger LOGGER = LoggerFactory.getLogger(WingtiptoysdataApplication.class);

        @Autowired
        private UserRepository repository;

        public static void main(String[] args) {
            SpringApplication.run(WingtiptoysdataApplication.class, args);
        }

        public void run(String... var1) throws Exception {
            final User testUser = new User("1", "Tasha", "Calderon", "4567 Main St Buffalo, NY 98052");

            LOGGER.info("Saving user: {}", testUser);

            // Save the User class to Azure CosmosDB database.
            final Mono<User> saveUserMono = repository.save(testUser);

            final Flux<User> firstNameUserFlux = repository.findByFirstName("testFirstName");

            //  Nothing happens until we subscribe to these Monos.
            //  findById will not return the user as user is not present.
            final Mono<User> findByIdMono = repository.findById(testUser.getId());
            final User findByIdUser = findByIdMono.block();
            Assert.isNull(findByIdUser, "User must be null");

            final User savedUser = saveUserMono.block();
            Assert.state(savedUser != null, "Saved user must not be null");
            Assert.state(savedUser.getFirstName().equals(testUser.getFirstName()), "Saved user first name doesn't match");

            LOGGER.info("Saved user");

            firstNameUserFlux.collectList().block();

            final Optional<User> optionalUserResult = repository.findById(testUser.getId()).blockOptional();
            Assert.isTrue(optionalUserResult.isPresent(), "Cannot find user.");

            final User result = optionalUserResult.get();
            Assert.state(result.getFirstName().equals(testUser.getFirstName()), "query result firstName doesn't match!");
            Assert.state(result.getLastName().equals(testUser.getLastName()), "query result lastName doesn't match!");

            LOGGER.info("Found user by findById : {}", result);
        }

        @PostConstruct
        public void setup() {
            LOGGER.info("Clear the database");
            this.repository.deleteAll().block();
        }

        @PreDestroy
        public void cleanup() {
            LOGGER.info("Cleaning up users");
            this.repository.deleteAll().block();
        }
    }
    ```

1. Salve e feche o arquivo Java do aplicativo principal.

## <a name="build-and-test-your-app"></a>Crie e testar seu aplicativo

1. Abra um prompt de comando e navegue para a pasta em que seu arquivo *pom.xml* está localizado, por exemplo:

    `cd C:\SpringBoot\wingtiptoysdata`

    -ou-

    `cd /users/example/home/wingtiptoysdata`

1. Use o comando a seguir para criar e executar o aplicativo:

    ```console
    mvnw clean test
    ```

    Esse comando executa o aplicativo automaticamente como parte da fase de teste. Também é possível usar:

    ```console
    mvnw clean spring-boot:run
    ```

    Após alguma saída gerada pelo build e pelo teste, a janela do console exibirá uma mensagem semelhante à seguinte:

    ```console
      .   ____          _            __ _ _
     /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
    ( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
     \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
      '  |____| .__|_| |_|_| |_\__, | / / / /
     =========|_|==============|___/=/_/_/_/
     :: Spring Boot ::            (v2.2.0.RC1)
    >
    > 2019-10-04 15:19:06.817  INFO 30013 --- [           main] c.e.w.WingtiptoysdataApplicationTests    : Starting WingtiptoysdataApplicationTests on devmachine03 with PID 30013 (started by <user> in /d/source/repos/wingtiptoysdata)
    > 2019-10-04 15:19:06.818  INFO 30013 --- [           main] c.e.w.WingtiptoysdataApplicationTests    : No active profile set, falling back to default profiles: default
    > 2019-10-04 15:19:08.329  INFO 30013 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Bootstrapping Spring Data repositories in DEFAULT mode.
    > 2019-10-04 15:19:09.720  INFO 30013 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Finished Spring Data repository scanning in 1369ms. Found 1 repository interfaces.
    > 2019-10-04 15:19:09.734  INFO 30013 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Bootstrapping Spring Data repositories in DEFAULT mode.
    > 2019-10-04 15:19:09.748  INFO 30013 --- [           main] .s.d.r.c.RepositoryConfigurationDelegate : Finished Spring Data repository scanning in 13ms. Found 0 repository interfaces.

    ... (omitting Cosmos DB connection output) ...

    > 2019-10-04 15:19:46.584  INFO 30013 --- [           main] c.e.w.WingtiptoysdataApplicationTests    : Started WingtiptoysdataApplicationTests in 40.702 seconds (JVM running for 44.647)
    > 2019-10-04 15:19:46.587  INFO 30013 --- [           main] c.e.w.WingtiptoysdataApplication         : Saving user: Tasha Calderon, 4567 Main St Buffalo, NY 98052
    > 2019-10-04 15:19:47.122  INFO 30013 --- [           main] c.e.w.WingtiptoysdataApplication         : Saved user
    > 2019-10-04 15:19:47.289  INFO 30013 --- [           main] c.e.w.WingtiptoysdataApplication         : Found user by findById : Tasha Calderon, 4567 Main St Buffalo, NY 98052
    > [INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 44.003 s - in com.example.wingtiptoysdata.WingtiptoysdataApplicationTests
    > 2019-10-04 15:19:48.124  INFO 30013 --- [extShutdownHook] c.a.d.c.internal.RxDocumentClientImpl    : Shutting down ...
    > 2019-10-04 15:19:48.194  INFO 30013 --- [extShutdownHook] c.a.d.c.internal.RxDocumentClientImpl    : Shutting down ...
    > 2019-10-04 15:19:48.200  INFO 30013 --- [extShutdownHook] c.e.w.WingtiptoysdataApplication         : Cleaning up users
    > [INFO]
    > [INFO] Results:
    > [INFO]
    > [INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0
    > [INFO]
    > [INFO]
    > [INFO] --- maven-jar-plugin:3.1.2:jar (default-jar) @ wingtiptoysdata ---
    > [INFO] Building jar: /d/source/repos/wingtiptoysdata/target/wingtiptoysdata-0.0.1-SNAPSHOT.jar
    > [INFO]
    > [INFO] --- spring-boot-maven-plugin:2.2.0.RC1:repackage (repackage) @ wingtiptoysdata ---
    > [INFO] Replacing main artifact with repackaged archive
    > [INFO] ------------------------------------------------------------------------
    > [INFO] BUILD SUCCESS
    > [INFO] ------------------------------------------------------------------------
    > [INFO] Total time:  02:18 min
    > [INFO] Finished at: 2019-10-04T15:20:05-07:00
    > [INFO] ------------------------------------------------------------------------
    ```

    ![Saída bem-sucedida do aplicativo][JV02]

    As mensagens `Saved user` e `Found user` indicam que os dados foram salvos com êxito no Cosmos DB e recuperados novamente.

## <a name="clean-up-resources"></a>Limpar recursos

Se você não pretende usar este aplicativo, é preciso excluir o grupo de recursos que contém o Cosmos DB criado anteriormente. Você pode obter isso no Portal do Azure.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Spring e o Azure, continue no Spring no Centro de Documentação do Azure.

> [!div class="nextstepaction"]
> [Spring no Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações sobre como usar o Azure Cosmos DB e Java, consulte os seguintes artigos:

* [Documentação do Azure Cosmos DB].

* [Banco de dados do Azure Cosmos DB: criar um banco de dados de documentos usando o Java e o portal do Microsoft Azure][Build a SQL API app with Java]

* [Spring Data para a API do SQL do Azure Cosmos DB]

Para obter mais informações sobre como usar aplicativos Spring Boot no Azure, confira os seguintes artigos:

* [Iniciador Spring Boot do Cosmos DB para Azure]

* [Implantar um aplicativo Spring Boot no Serviço de Aplicativo do Azure](deploy-spring-boot-java-app-from-container-registry-using-maven-plugin.md)

* [Executando um Aplicativo Spring Boot em um Cluster Kubernetes no Serviço de Contêiner do Azure](deploy-spring-boot-java-app-on-kubernetes.md)

Para obter mais informações sobre como usar o Azure com Java, confira [Azure para Desenvolvedores Java] e [Como trabalhar com o Java e o Azure DevOps].

O **[Spring Framework]** é uma solução de software livre que ajuda os desenvolvedores Java criar aplicativos de nível empresarial. Um dos projetos mais populares que é criado com base nessa plataforma é o [Spring Boot], que fornece uma abordagem simplificada para a criação de aplicativos Java autônomos. Para ajudar os desenvolvedores a começarem a usar o Spring Boot, vários exemplos de pacotes do Spring Boot estão disponíveis em <https://github.com/spring-guides/>. Além de escolher na lista de projetos básicos do Spring Boot, o  **[Spring Initializr]** ajuda os desenvolvedores a começarem a criar aplicativos personalizados do Spring Boot.

<!-- URL List -->

[Documentação do Azure Cosmos DB]: /azure/cosmos-db/
[Azure para desenvolvedores Java]: /azure/java/
[Build a SQL API app with Java]: /azure/cosmos-db/create-sql-api-java 
[Spring Data para a API do SQL do Azure Cosmos DB]: https://azure.microsoft.com/blog/spring-data-azure-cosmos-db-nosql-data-access-on-azure/
[Iniciador Spring Boot do Cosmos DB para Azure]: https://github.com/microsoft/azure-spring-boot/tree/master/azure-spring-boot-starters/azure-cosmosdb-spring-boot-starter
[conta gratuita do Azure]: https://azure.microsoft.com/pricing/free-trial/
[Como trabalhar com o Java e o Azure DevOps]: https://azure.microsoft.com/services/devops/java/
[benefício de assinante do MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[AZ01]: ./media/configure-spring-boot-starter-java-app-with-cosmos-db/AZ01.png
[AZ02]: ./media/configure-spring-boot-starter-java-app-with-cosmos-db/AZ02.png
[AZ03]: ./media/configure-spring-boot-starter-java-app-with-cosmos-db/AZ03.png
[AZ04]: ./media/configure-spring-boot-starter-java-app-with-cosmos-db/AZ04.png
[AZ05]: ./media/configure-spring-boot-starter-java-app-with-cosmos-db/AZ05.png

[SI01]: ./media/configure-spring-boot-starter-java-app-with-cosmos-db/SI01.png

[JV02]: ./media/configure-spring-boot-starter-java-app-with-cosmos-db/JV02.png
