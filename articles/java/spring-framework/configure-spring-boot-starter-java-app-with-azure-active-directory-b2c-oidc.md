---
title: Usar o Iniciador do Spring Boot para o Azure Active Directory B2C
description: Saiba como configurar um aplicativo inicializador do Spring Boot com o iniciador do Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: java
author: panli
manager: kevinzha
ms.author: edburns
ms.date: 06/04/2020
ms.service: active-directory-b2c
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: identity
ms.custom: devx-track-java
ms.openlocfilehash: 3835d51bbbc7f3226a1b77ba415d5e965ac8a609
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831912"
---
# <a name="tutorial-secure-a-java-web-app-using-the-spring-boot-starter-for-azure-active-directory-b2c"></a>Tutorial: Proteger um aplicativo Web do Java usando o iniciador do Spring Boot para o Azure Active Directory B2C.

## <a name="overview"></a>Visão geral

Este artigo demonstra como criar um aplicativo Java com o [Spring Initializr](https://start.spring.io/) que usa o Iniciador do Spring Boot para o Azure Active Directory (Azure AD).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um aplicativo do Java usando o Spring Initializr
> * Configurar o Azure Active Directory B2C
> * Proteger o aplicativo com classes e anotações do Spring Boot
> * Compilar e testar seu aplicativo do Java

O [Azure Active Directory](https://azure.microsoft.com/services/active-directory) é a solução de identidade empresarial em escala de nuvem da Microsoft. O [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory/external-identities/b2c/) complementa o conjunto de recursos do Azure Active Directory, permitindo que você gerencie o acesso do cliente, do consumidor e do cidadão aos seus aplicativos B2C (business-to-consumer).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se ainda não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* Um JDK (Java Development Kit) com suporte. Para obter mais informações sobre os JDKs disponíveis para usar durante o desenvolvimento no Azure, confira <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/) versão 3.0 ou posterior.

## <a name="create-an-app-using-spring-initializr"></a>Criar um aplicativo usando o Spring Initialzr

1. Navegue até <https://start.spring.io/>.

2. Preencha os valores de acordo com essas diretrizes. Observe que os rótulos e o layout podem ser diferentes da imagem mostrada aqui.

    * Em **Projeto**, selecione **Projeto Maven**.
    * Em **Linguagem de programação**, selecione **Java**.
    * Em **Spring Boot**, selecione **2.2.7**.
    * Em **Grupo**, **Artefato** e **Nome**, insira o mesmo valor, usando uma cadeia de caracteres curta descritiva. A interface do usuário pode preencher automaticamente alguns deles conforme você digita.
    * No painel **Dependências**, selecione **Adicionar Dependências**. Use a interface do usuário para adicionar dependências no **Spring Web** e na **Segurança do Spring**.

   ![Preencher os valores para gerar o projeto](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/si-n.png)

3. Selecione **Gerar Projeto** e baixe o projeto para um caminho no seu computador local. Mova o arquivo baixado para um diretório chamado após o projeto e descompacte o arquivo. O layout do arquivo deve ser semelhante ao exibido a seguir, com o valor inserido para **Grupo** no lugar de `yourProject`.

    ```
    .
    ├── HELP.md
    ├── mvnw
    ├── mvnw.cmd
    ├── pom.xml
    └── src
        ├── main
        │   ├── java
        │   │   └── yourProject
        │   │       └── yourProject
        │   │           └── YourProjectApplication.java
        │   └── resources
        │       ├── application.properties
        │       ├── static
        │       └── templates
        └── test
            └── java
                └── yourProject
                    └── yourProject
                        └── YourProjectApplicationTests.java
    ```

## <a name="create-and-initialize-an-azure-active-directory-instance"></a>Criar e inicializar uma instância do Azure Active Directory

### <a name="create-the-active-directory-instance"></a>Criar a instância do Active Directory

1. Faça logon em <https://portal.azure.com>.

2. Selecione **Criar um recurso**, **Identidade** e **Ver tudo**. Pesquise o **Azure Active Directory B2C**.

    ![Criar uma nova instância do Azure Active Directory B2C](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/az-1-n.png)

3. Selecione **Criar**.

    ![Obter o nome do locatário B2C](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/az-5-n.png)

4. Selecione **Criar um novo Locatário Azure AD B2C**.

    ![Criar um Azure Active Directory](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/az-2-n.png)

5. Para **Nome da organização** e **Nome de domínio inicial**, forneça os valores apropriados e selecione **Criar**.

    ![Escolher seu Azure Active Directory](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/az-3-n.png)

6. Após a conclusão da criação do Active Directory, navegue até o novo diretório. Ou pesquise `b2c` e selecione **Azure AD B2C**.

    ![Localize a instância do Azure Active Directory B2C](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/az-4-n.ng.png)

### <a name="add-an-application-registration-for-your-spring-boot-app"></a>Adicionar um registro de aplicativo para seu aplicativo do Spring Boot

1. No painel **Gerenciar** à esquerda, selecione **Aplicativos** e escolha **Adicionar**.

    ![Adicionar um novo registro de aplicativo](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/b2c1-n.png)

2. No campo **Nome**, insira o valor de **Grupo** acima e defina o controle **incluir o aplicativo Web/API Web** como **Sim**.

3. Defina **URL de Resposta** como `http://localhost:8080/home`.

4. Deixe os outros campos com os respectivos valores padrão.

5. Selecione **Criar**. Pode demorar um pouco antes que o aplicativo seja exibido.

    ![Adicionar URI de Redirecionamento do Aplicativo](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/b2c2-n.png)

6. Selecione **Visão geral** e **Aplicativos**.

7. Na tabela de aplicativos, selecione a linha com o nome do seu projeto.

8. No painel **Geral** selecione Chaves e escolha **Gerar Chave**.

9. Defina a **Chave do aplicativo** como `yourGroupIdkey`, substituindo `yourGroupId` pelo valor inserido acima para **Grupo**.

10. Clique em **Salvar**. Aguarde a chave aparecer na seção da chave do aplicativo e copie-a para uso posterior neste artigo.

    > [!NOTE]
    > Se você sair da seção **Chaves** e voltar, você não poderá ver o valor da chave. Nesse caso, você deverá criar outra chave e copiá-la para uso futuro.
    > Ocasionalmente, a chave gerada pode conter caracteres problemáticos para inclusão no arquivo de *application.yml*, como barra invertida ou acento grave. Nesse caso, descarte essa chave e gere outra.

    ![Criar o segredo](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/b2c3-n.png)

11. Selecione **Visão geral**.

12. Na seção **Políticas** do painel esquerdo, selecione **Fluxos dos Usuários** e escolha **Novo fluxo de usuário**.

13. Agora você sairá deste tutorial, executará outro e voltará para este quando terminar. Aqui estão algumas coisas para se ter em mente quando você acessar o outro tutorial.

    * Comece com a etapa que solicita que você selecione **Novo fluxo de usuário**.
    * Quando este tutorial se refere a `webapp1`, use o valor inserido para **Grupo**.
    * Quando você estiver selecionando declarações para retornar dos fluxos, verifique se **Nome de Exibição** está selecionado. Sem essa declaração, o aplicativo que está sendo criado neste tutorial não funcionará.
    * Quando for solicitado que você execute os fluxos dos usuários, a URL de redirecionamento especificada acima ainda não estará ativa. Você ainda pode executar os fluxos, mas o redirecionamento não será concluído com êxito. Isso é esperado.
    * Quando você chegar a "Próximas etapas", retorne a este tutorial.

    Siga as etapas listadas em [Tutorial: Crie fluxos dos usuários no Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-user-flows) para criar fluxos dos usuários para "inscrever-se e entrar", "edição de perfil" e "redefinição de senha".

    O AAD B2C dá suporte a contas locais, bem como a provedores de identidade social. Para obter um exemplo de como criar um provedor de identidade do GitHub, confira [Configurar a inscrição e a entrada com uma conta do GitHub usando o Azure Active Directory B2C](/azure/active-directory-b2c/identity-provider-github).

## <a name="configure-and-compile-your-app"></a>Configurar e compilar seu aplicativo

Agora que você criou a instância do AAD B2C e alguns fluxos dos usuários, você conectará seu aplicativo Spring à instância do AAD B2C.

1. Na linha de comando, execute cd para o diretório em que você descompactou o arquivo .zip baixado do Spring Initializr.

2. Navegue até a pasta pai no projeto e abra o arquivo de projeto Maven *pom.xml* em um editor de texto.

3. Adicione as dependências de segurança do Spring OAuth2 a *pom.xml*:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-active-directory-b2c-spring-boot-starter</artifactId>
        <version>See Below</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
        <version>See Below</version>
    </dependency>
    <dependency>
        <groupId>org.thymeleaf.extras</groupId>
        <artifactId>thymeleaf-extras-springsecurity5</artifactId>
        <version>See Below</version>
    </dependency>
    ```

    Para o `azure-active-directory-b2c-spring-boot-starter`, use a versão mais recente disponível. Talvez você possa usar [mvnrepository.com](https://mvnrepository.com/ artifact/com.microsoft.azure/azure-active-directory-spring-boot-starter) para procurar isso. No momento da redação deste artigo, a versão mais recente era `2.2.4`.

    Para o `spring-boot-starter-thymeleaf`, use a versão correspondente à versão do Spring Boot que você selecionou acima, por exemplo `2.2.7.RELASE`.

    Para `thymeleaf-extras-springsecurity5`, use a versão mais recente disponível. Talvez você possa usar [mvnrepository.com](https://mvnrepository.com/artifact/org.thymeleaf.extras/thymeleaf-extras-springsecurity5) para procurar isso. No momento da redação deste artigo, a versão mais recente era `3.0.4.RELEASE`.

4. Salve e feche o arquivo *pom.xml*.

    * Verifique se suas dependências estão corretas executando `mvn -DskipTests clean install`. Se você não vir `BUILD SUCCESS`, solucione e resolva o problema antes de continuar.

5. Navegue até a pasta *src/main/resources* no seu projeto e crie um arquivo *application.yml* em um editor de texto.

6. Especifique as configurações de registro do aplicativo usando os valores criados anteriormente. Por exemplo:

    ```yaml
    azure:
      activedirectory:
        b2c:
          tenant: ejb0518domain
          client-id: ejb0518
          client-secret: '<yourAppKey>'
          reply-url: http://localhost:8080/home
          logout-success-url: http://localhost:8080/home
          user-flows:
            sign-up-or-sign-in: B2C_1_signupsignin1
            profile-edit: B2C_1_profileediting1
            password-reset: B2C_1_passwordreset1
    ```

    Observe que o valor `client-secret` é colocado entre aspas simples. Isso é necessário porque o valor de `<yourAppKey>` certamente conterá alguns caracteres que precisam estar entre aspas simples quando presentes no YAML.

    > [!NOTE]
    > No momento da redação deste artigo, a lista completa dos valores de Integração do Spring do Active Directory B2C disponíveis para uso no *application.yml* era a seguinte:
    >
    > ```
    > azure:
    >   activedirectory:
    >     b2c:
    >       tenant:
    >       oidc-enabled:
    >       client-id:
    >       client-secret:
    >       reply-url:  # should be absolute url.
    >       logout-success-url:
    >       user-flows:
    >         sign-up-or-sign-in:
    >         profile-edit: # optional
    >         password-reset: # optional
    > ```
    >
    > O arquivo *application.yml* está disponível em [Exemplo de Spring Boot do Azure Active Directory B2C](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/spring/azure-spring-boot-samples/azure-spring-boot-sample-active-directory-b2c-oidc/src/main/resources/application.yml) no GitHub.

7. Salve e feche o arquivo *application.yml*.

8. Crie uma pasta chamada *controlador* em *src/main/java/<yourGroupId>/<yourGroupId>* , substituindo `<yourGroupId>` pelo valor que você inseriu para **Grupo**.

9. Crie um arquivo Java chamado *WebController.java* na pasta *controlador* e abra-o em um editor de texto.

10. Insira o seguinte código, alterando `yourGroupId` adequadamente e salve e feche o arquivo:

    ```java
    package yourGroupId.yourGroupId.controller;

    import org.springframework.security.oauth2.client.authentication.OAuth2AuthenticationToken;
    import org.springframework.security.oauth2.core.user.OAuth2User;
    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;
    import org.springframework.web.bind.annotation.GetMapping;

    @Controller
    public class WebController {

        private void initializeModel(Model model, OAuth2AuthenticationToken token) {
            if (token != null) {
                final OAuth2User user = token.getPrincipal();

                model.addAttribute("grant_type", user.getAuthorities());
                model.addAllAttributes(user.getAttributes());
            }
        }

        @GetMapping(value = "/")
        public String index(Model model, OAuth2AuthenticationToken token) {
            initializeModel(model, token);

            return "home";
        }

        @GetMapping(value = "/greeting")
        public String greeting(Model model, OAuth2AuthenticationToken token) {
            initializeModel(model, token);

            return "greeting";
        }

        @GetMapping(value = "/home")
        public String home(Model model, OAuth2AuthenticationToken token) {
            initializeModel(model, token);

            return "home";
        }
    }
    ```

    Como cada método no controlador chama `initializeModel()` e esse método chama `model.addAllAttributes(user.getAttributes());`, qualquer página HTML em *src/main/resources/templates* é capaz de acessar qualquer um desses atributos, como `${name}`, `${grant_type}` ou `${auth_time}`. Os valores retornados de `user.getAttributes()` são, de fato, as declarações do `id_token` para a autenticação. A lista completa de declarações disponíveis está listada em [tokens de ID da plataforma Microsoft Identity](/azure/active-directory/develop/id-tokens#payload-claims).

11. Crie uma pasta chamada *segurança* em *src/main/java/<yourGroupId>/<yourGroupId>* , substituindo `yourGroupId` pelo valor que você inseriu para **Grupo**.

12. Crie um arquivo Java chamado *WebSecurityConfiguration.java* na pasta *segurança* e abra-o em um editor de texto.

13. Insira o seguinte código, alterando `yourGroupId` adequadamente e salve e feche o arquivo:

    ```java
    package yourGroupId.yourGroupId.security;

    import com.microsoft.azure.spring.autoconfigure.b2c.AADB2COidcLoginConfigurer;
    import org.springframework.security.config.annotation.web.builders.HttpSecurity;
    import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
    import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

    @EnableWebSecurity
    public class WebSecurityConfiguration extends WebSecurityConfigurerAdapter {

        private final AADB2COidcLoginConfigurer configurer;

        public WebSecurityConfiguration(AADB2COidcLoginConfigurer configurer) {
            this.configurer = configurer;
        }

        @Override
        protected void configure(HttpSecurity http) throws Exception {
            http
                    .authorizeRequests()
                    .anyRequest()
                    .authenticated()
                    .and()
                    .apply(configurer)
            ;
        }
    }
    ```

14. Copie os arquivos *greeting.html* e *home.html* do [Exemplo de Spring Boot do Azure AD B2C](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-samples/azure-spring-boot-sample-active-directory-b2c-oidc/src/main/resources/templates) para *src/main/resources/templates* e substitua `${your-profile-edit-user-flow}` e `${your-password-reset-user-flow}` pelos nomes dos fluxos dos usuários criados anteriormente.

## <a name="build-and-test-your-app"></a>Crie e testar seu aplicativo

1. Abra um prompt de comando e altere o diretório para a pasta em que seu arquivo *pom.xml* do aplicativo está localizado.

2. Crie seu aplicativo Spring Boot com Maven e execute-o; por exemplo:

    > [!NOTE]
    > É extremamente importante que o tempo seja preciso de acordo com o relógio do sistema em que o aplicativo Spring Boot local é executado. Há muito pouca tolerância de distorção do relógio ao usar o OAuth 2.0. Até três minutos de imprecisão pode fazer com que a entrada falhe com um erro semelhante a `[invalid_id_token] An error occurred while attempting to decode the Jwt: Jwt used before 2020-05-19T18:52:10Z`. No momento da redação deste artigo, [time.gov](https://time.gov/) tinha um indicador que mostrava o valor da diferença entre o seu relógio e a hora real. O aplicativo foi executado com êxito com uma distorção de +0,019 segundos.

    ```shell
    mvn -DskipTests clean package
    mvn -DskipTests spring-boot:run
    ```

3. Após a criação e inicialização de seu aplicativo pelo Maven, abra `http://localhost:8080/` em um navegador da Web; você deverá ser redirecionado para a página de logon.

    ![Página de logon](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/lo1-n.png)

4. Selecione o link com o texto relacionado à entrada. Você deve ser redirecionado do Azure AD B2C para iniciar o processo de autenticação.

5. Depois de entrar com êxito, você deverá ver o `home page` de exemplo do navegador.

    ![Logon bem-sucedido](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/lo3-n.png)

## <a name="summary"></a>Resumo

Neste tutorial, você criou um aplicativo Web do Java usando o iniciador do Azure Active Directory B2C, configurou um novo locatário do Azure AD B2C e registrou um novo aplicativo. Em seguida, configurou o aplicativo para usar as classes e as anotações do Spring para proteger o aplicativo Web.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Spring e o Azure, continue no Spring no Centro de Documentação do Azure.

> [!div class="nextstepaction"]
> [Spring no Azure](./index.yml)