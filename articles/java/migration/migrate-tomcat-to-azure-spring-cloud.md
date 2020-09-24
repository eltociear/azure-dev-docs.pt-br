---
title: Migrar aplicativos do Tomcat para o Azure Spring Cloud
description: Este guia descreve as informações que você deve ter em mente quando quiser migrar um aplicativo do Tomcat existente para o Azure Spring Cloud
author: yevster
ms.author: yebronsh
ms.topic: conceptual
ms.date: 6/16/2020
ms.openlocfilehash: a718c7b8b481b99f5a4b1303acac7147c88d1550
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831662"
---
# <a name="migrate-a-tomcat-application-to-azure-spring-cloud"></a>Migrar um aplicativo do Tomcat para o Azure Spring Cloud

Este guia descreve as informações que você deve ter em mente quando quiser migrar um aplicativo do Tomcat existente para executar no Azure Spring Cloud.

## <a name="pre-migration"></a>Pré-migração

Antes de tudo, para garantir uma migração bem-sucedida, conclua as etapas de avaliação e de inventário descritas nas seções a seguir.

### <a name="switch-to-a-supported-platform"></a>Alternar para uma plataforma compatível

O Azure Spring Cloud oferece versões específicas do Java SE. Para garantir a compatibilidade, migre seu aplicativo para uma das versões compatíveis do ambiente atual antes de continuar com qualquer uma das etapas restantes. É necessário testar completamente a configuração resultante. Use a versão estável mais recente da sua distribuição do Linux nesses testes.

[!INCLUDE [note-obtain-your-current-java-version](includes/note-obtain-your-current-java-version.md)]

[!INCLUDE [determine-whether-and-how-the-file-system-is-used](includes/determine-whether-and-how-the-file-system-is-used.md)]

### <a name="identify-the-application-builddependency-system"></a>Identificar o Sistema de Compilação/Dependência do Aplicativo

Identifique quais ferramentas são usadas para compilar/empacotar o aplicativo, incluindo o download de todas as dependências.

[!INCLUDE [inventory-external-resources](includes/inventory-external-resources.md)]

### <a name="inventory-secrets"></a>Segredos de inventário

#### <a name="passwords-and-secure-strings"></a>Senhas e cadeias de caracteres seguras

Verifique todas as propriedades e os arquivos de configuração nos servidores de produção para quaisquer senhas e cadeias de caracteres secretas. É necessário verificar *server.xml* e *context.xml* em *$CATALINA_BASE/conf*. Você também pode encontrar arquivos de configuração que contenham senhas ou credenciais dentro do aplicativo em *META-INF/context.xml*.

[!INCLUDE [inventory-certificates](includes/inventory-certificates.md)]

### <a name="determine-whether-your-application-contains-os-specific-code"></a>Determinar se o aplicativo contém código específico do sistema operacional

[!INCLUDE [determine-whether-your-application-contains-os-specific-code-no-title](includes/determine-whether-your-application-contains-os-specific-code-no-title.md)]

[!INCLUDE [identify-all-outside-processes-and-daemons-running-on-the-production-servers](includes/identify-all-outside-processes-and-daemons-running-on-the-production-servers.md)]

### <a name="determine-whether-tomcat-is-connected-to-a-web-server"></a>Determinar se o Tomcat está conectado a um servidor Web

O Tomcat pode estar conectado a um servidor Web estático, como o Apache, por meio de um conector do Tomcat, como `mod_jk`. Inspecione o arquivo `workers.properties` no diretório `conf` para determinar se tal conexão existe.

### <a name="special-cases"></a>Casos especiais

Determinados cenários de produção podem exigir alterações adicionais ou impor limitações adicionais. Embora esses cenários sejam raros, é importante verificar se eles não se aplicam ao aplicativo ou estão resolvidos corretamente.

#### <a name="determine-if-the-application-uses-filters"></a>Determinar se o aplicativo usa filtros

Inspecione o arquivo *web.xml* do aplicativo em busca de algum [filtro configurado](https://tomcat.apache.org/tomcat-9.0-doc/config/filter.html#Expires_Filter/Basic_configuration_sample).

[!INCLUDE [determine-whether-your-application-relies-on-scheduled-jobs-azure-spring-cloud](includes/determine-whether-your-application-relies-on-scheduled-jobs-azure-spring-cloud.md)]

#### <a name="determine-whether-non-http-connectors-are-used"></a>Determinar se conectores não HTTP são ou não usados

O Azure Spring Cloud é compatível somente com conexões HTTP em apenas uma porta HTTP não personalizável. Se o aplicativo exige portas ou protocolos adicionais, não use o Azure Spring Cloud.

Para identificar os conectores HTTP usados pelo seu aplicativo, procure elementos `<Connector>` dentro do arquivo *server.xml* em sua configuração do Tomcat.

#### <a name="determine-whether-ssl-session-tracking-is-used"></a>Determinar se o acompanhamento de sessão SSL é usado

No Azure Spring Cloud, a sessão SSL será encerrada antes de alcançar o código do aplicativo, de modo que você não pode usar o [acompanhamento de sessão SSL](https://tomcat.apache.org/tomcat-9.0-doc/servletapi/javax/servlet/SessionTrackingMode.html#SSL). Em vez disso, você precisará mudar para a [Sessão do Spring](https://docs.spring.io/spring-session/docs/current/reference/html5/index.html).

#### <a name="determine-whether-tomcat-realms-are-used"></a>Determinar se os realms do Tomcat são usados

No Azure Spring Cloud, a Segurança do Spring deve ser usada no lugar dos realms do Tomcat. Inspecione o arquivo *server.xml* para inventariar qualquer [realm configurado](https://tomcat.apache.org/tomcat-9.0-doc/realm-howto.html#Configuring_a_Realm).

#### <a name="determine-whether-servlet-filters-are-used"></a>Determinar se os filtros de servlet são usados

Inspecione o arquivo *web.xml* no aplicativo em busca de algum elemento `<filter>`. Para obter uma lista de filtros disponíveis, confira a [documentação de filtros do Tomcat](https://tomcat.apache.org/tomcat-9.0-doc/config/filter.html).

## <a name="migration"></a>Migração

### <a name="remove-connection-to-web-server-if-present"></a>Remover a conexão com o servidor Web, se houver

Se o Tomcat estiver conectado a um servidor Web estático, normalmente ao Apache via `mod_jk`, desabilite essa conexão para que o Tomcat seja executado como um servidor autônomo, criando redirecionamentos da Web do servidor padrão, conforme necessário. Considere a migração do conteúdo da Web estático para o Armazenamento do Microsoft Azure com a CDN (Rede de Distribuição de Conteúdo) do Azure. Para obter mais informações, confira [Hospedagem de site estático no Armazenamento do Microsoft Azure](/azure/storage/blobs/storage-blob-static-website) e [Início rápido: Integre uma conta de Armazenamento do Microsoft Azure à CDN do Azure](/azure/cdn/cdn-create-a-storage-account-with-cdn).

### <a name="update-to-tomcat-9"></a>Atualizar para o Tomcat 9

Se o aplicativo atual estiver sendo executado em uma versão do Tomcat anterior a 9, migre para o Tomcat 9 e verifique se o aplicativo está totalmente funcional. Para obter mais informações, confira o [Guia de Migração do Tomcat 9](http://tomcat.apache.org/migration-9.html).

### <a name="switch-to-maven-or-gradle"></a>Mudar para Maven ou Gradle

O Spring Boot e o Spring Cloud exigem Maven ou Gradle para o gerenciamento de dependência e compilação. Se o aplicativo usa outro sistema de gerenciamento de dependência ou de compilação, mude para a versão atual do [Maven](https://maven.apache.org/download.cgi) antes de continuar. Embora o Gradle também seja compatível, usaremos o Maven em todas as etapas deste guia. Se você decidir usar o Gradle, adapte as instruções conforme necessário.

Crie um [arquivo POM](https://maven.apache.org/pom.html) para o aplicativo e verifique se o aplicativo é compilado e executado com funcionalidade completa antes de continuar.

### <a name="migrate-to-spring-boot"></a>Migrar para o Spring Boot

A tabela a seguir mostra um resumo das migrações necessárias e das alterações de código para migrar um aplicativo do Tomcat para o Spring Boot e, posteriormente, para o Azure Spring Cloud. Se algum elemento da coluna Herdada for usado no aplicativo, ele deverá ser substituído pelo elemento correspondente da coluna Mínima ou, de maneira ideal, da coluna Recomendada.

|Herdada | Onde verificar |Migração mínima |Migração recomendada|
|---|---|---|---|
|[JDBC via DataSource](https://docs.oracle.com/javase/tutorial/jdbc/basics/connecting.html)|*server.xml*|[Fonte de Dados do Spring Data](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-sql) com o [Modelo do JDBC](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-using-jdbc-template)|Considere o Spring Data e o JPA, se apropriado.|
|Servlets |*web.xml*|Habilitar a [Inicialização de Contexto do Servlet](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/html/spring-boot-features.html#boot-features-embedded-container-context-initializer) e anotar com `@WebServlet`|Reescrever como [Controladores Spring MVC (com `@RestController`](https://spring.io/guides/gs/rest-service/#_create_a_resource_controller))
|Filtros |*web.xml*| Habilitar a [Inicialização de Contexto do Servlet](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/html/spring-boot-features.html#boot-features-embedded-container-context-initializer) e [anotar com `@WebFilter`](https://docs.oracle.com/javaee/7/api/javax/servlet/annotation/WebFilter.html) |Igual ao mínimo|
|JSPs (Páginas do Servidor Java)|Conteúdo dos arquivos *web.xml* e WAR|[Exibições da JSP para Spring MVC](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-view-jsp)|Hospedar a camada de exibição separadamente|
|JMS (Serviço de Mensagens Java)|*server.xml*|Instanciar uma fábrica de conexões como um [Spring Bean](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-spring-beans-and-dependency-injection)|Usar [Spring JMS](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/integration.html#jms-using)
|Conteúdo estático (imagens, arquivos JavaScript e assim por diante) dentro do arquivo WAR|diretório de conteúdo estático (geralmente */static*, */public* ou */resources*)|Mover o conteúdo para */src/main/resources*|Confira [recomendações de conteúdo estático na pré-migração](#read-only-static-content).
|Conteúdo estático (imagens, arquivos JavaScript etc.) fora do arquivo WAR|Um caminho no sistema de arquivos local|Mover conteúdo para *src/main/resources*. Pesquisar o código-fonte para caminhos embutidos em código e substituir por [ClassPathResource](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/io/ClassPathResource.html) |Confira [recomendações de conteúdo estático na pré-migração](#read-only-static-content).

1. Se o aplicativo depende de bibliotecas injetadas por meio de recursos JNDI (como drivers JDBC), adicione essas bibliotecas como dependências no arquivo POM. Remova as bibliotecas do servidor Tomcat (normalmente do diretório *tomcat/lib*) e verifique se o aplicativo é executado com a funcionalidade completa antes de continuar.

1. Adicione o POM pai do Spring Boot ao arquivo POM. Para obter mais informações, confira [Criação do POM](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#getting-started-first-application-pom) na documentação do Spring Boot.

1. Adicione o iniciador do Tomcat no Spring Boot como uma dependência do arquivo POM:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    ```

    Embora esse tenha sido anteriormente um aplicativo do Tomcat, não adicione `war` como empacotamento de destino.

1. Substitua as fontes de dados do Tomcat por fontes de dados do Spring. [Configure as Fontes de Dados do Spring](https://docs.spring.io/spring-boot/docs/current/reference/html/howto.html#howto-configure-a-datasource) para todos os bancos de dados usados pelo aplicativo. Se algum código executa consultas SQL diretas, modifique-o para [usar o JdbcTemplate](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-using-jdbc-template). Confira a [documentação do Spring Framework](https://docs.spring.io/spring/docs/current/spring-framework-reference/data-access.html#jdbc) e a [documentação do Spring Data](https://docs.spring.io/spring-data/jdbc/docs/current/reference/html/#reference) para obter recursos adicionais de acesso a dados, como o gerenciamento de transações e as ferramentas CRUD.

1. Embora seja possível ter implementações de servlet dentro de um [contêiner de servlet inserido](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/html/spring-boot-features.html#boot-features-embedded-container), isso não é recomendável. Em vez disso, substitua as [implementações de servlet](https://docs.oracle.com/javaee/7/api/javax/servlet/http/HttpServletRequest.html) pelos [controladores REST](https://spring.io/guides/gs/rest-service/#_create_a_resource_controller) do Spring. Se o aplicativo usa uma estrutura que não seja o Spring MVC, substitua pelo Spring MVC. Confira a [referência do controlador anotado do Spring MVC](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/web.html#mvc-controller) para obter mais informações.

1. Recrie todas as outras dependências de JNDI com os [Spring Beans](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-spring-beans-and-dependency-injection). Favorece o uso de mecanismos idiomáticos do Spring, como o [JMS do Spring](https://spring.io/guides/gs/messaging-jms/) para mensagens.

1. Substitua os Realms do Tomcat pelo [Spring Security](https://docs.spring.io/spring-security/site/docs/current/reference/html5/#servlet-filters-review). Considere o uso do Azure Active Directory para o gerenciamento de autorização por meio do [Iniciador do Spring Boot para Active Directory](../spring-framework/spring-boot-starters-for-azure.md#azure-active-directory).

1. Recrie os filtros de servlet configurados no *web.xml* com [Spring Beans](https://docs.spring.io/spring-boot/docs/current/reference/html/howto.html#howto-add-a-servlet-filter-or-listener-as-spring-bean) ou [verificação de classpath](https://docs.spring.io/spring-boot/docs/current/reference/html/howto.html#howto-add-a-servlet-filter-or-listener-using-scanning).

1. Se o aplicativo contiver ou fizer referência a conteúdo estático, como imagens ou arquivos JavaScript, esses arquivos deverão ser movidos para *src/main/resources* no código-fonte do projeto. Depois de mover os arquivos, atualize o código-fonte para remover qualquer referência ao sistema de arquivos local. Use a classe `ClassPathResource` do Spring para acessar esses arquivos.

Teste o aplicativo executando `mvn spring-boot:run`. Verifique se o aplicativo resultante é executado com funcionalidade completa antes de continuar.

[!INCLUDE [migrate-steps-spring-boot-azure-spring-cloud](includes/migrate-steps-spring-boot-azure-spring-cloud.md)]

## <a name="post-migration"></a>Após a migração

[!INCLUDE [post-migration-spring-boot-azure-spring-cloud](includes/post-migration-spring-boot-azure-spring-cloud.md)]