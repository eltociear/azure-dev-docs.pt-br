---
title: Como usar o iniciador do Spring Boot para o Azure Active Directory
description: Descubra como configurar um aplicativo inicializador do Spring Boot com o iniciador do Azure Active Directory.
services: active-directory
documentationcenter: java
ms.date: 03/05/2020
ms.service: active-directory
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: identity
ms.custom: devx-track-java
ms.openlocfilehash: 762e40aa656ddff76f3bc8e1b38b8845e8f20cdd
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90830092"
---
# <a name="tutorial-secure-a-java-web-app-using-the-spring-boot-starter-for-azure-active-directory"></a>Tutorial: Proteger um aplicativo Web do Java usando o iniciador do Spring Boot para o Azure Active Directory

## <a name="overview"></a>Visão geral

Este artigo demonstra como criar um aplicativo Java com o **[Spring Initializr]** , que usa o Iniciador do Spring Boot para o Azure Active Directory (Azure AD).

Neste tutorial, você aprenderá como:

 * Criar um aplicativo do Java usando o Spring Initializr
 * Configurar o Azure Active Directory
 * Proteger o aplicativo com classes e anotações do Spring Boot
 * Compilar e testar seu aplicativo do Java

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos são obrigatórios para você concluir as etapas neste artigo:

* Um JDK (Java Development Kit) com suporte. Para obter mais informações sobre os JDKs disponíveis para usar durante o desenvolvimento no Azure, confira <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/) versão 3.0 ou posterior.

## <a name="create-an-app-using-spring-initializr"></a>Criar um aplicativo usando o Spring Initialzr

1. Navegue até <https://start.spring.io/>.

1. Especifique que você deseja gerar um projeto **Maven** com **Java** e insira os nomes de **Grupo** e **Artefato** para o seu aplicativo.
1. Adicione **Dependências** para o **Spring Web**, o **Azure Active Directory** e o **Spring Security**.
1. Na parte inferior da página e clique no botão **Gerar**.
   
   >[!div class="mx-imgBorder"]
   >![Especifique os nomes do Grupo e do Artefato, selecione dependências][create-spring-app-01]

1. Quando solicitado, baixe o projeto para um caminho no computador local.

## <a name="create-azure-active-directory-instance"></a>Criar uma instância do Azure Active Directory

### <a name="create-the-active-directory-instance"></a>Criar a instância do Active Directory

1. Faça logon em <https://portal.azure.com>.

1. Clique em **+Criar um recurso**, depois em **Identidade** e **Azure Active Directory**.
   
   >[!div class="mx-imgBorder"]
   >![Criar uma nova instância do Azure Active Directory][create-directory-01]

1. Insira o **Nome da organização** e seu **Nome de domínio inicial**. Copie a URL completa de seu diretório. Você usará isso para adicionar as contas de usuário mais tarde neste tutorial.
 (Por exemplo, `azuresampledirectory.onmicrosoft.com`.) 

    Copie a URL completa de seu diretório. Você usará isso para adicionar as contas de usuário mais tarde neste tutorial. (Por exemplo, azuresampledirectory.onmicrosoft.com.).

    Ao terminar, clique em **Criar**. Levará poucos minutos para criar o recurso.
   
   >[!div class="mx-imgBorder"]
   >![Especificar nomes do Azure Active Directory][create-directory-02]

1. Quando concluído, clique para acessar o novo diretório.
   
   >[!div class="mx-imgBorder"]
   >![Selecionar o nome da conta do Azure][create-directory-03]

1. Copie a **ID do Locatário**; você usará esse valor para configurar o arquivo *application.properties* posteriormente neste tutorial.
   
   >[!div class="mx-imgBorder"]
   >![Copiar sua ID de Locatário][create-directory-04]

### <a name="add-an-application-registration-for-your-spring-boot-app"></a>Adicionar um registro de aplicativo para seu aplicativo do Spring Boot

1. No menu do portal, clique em **Registros de aplicativo** e clique em **Registrar um aplicativo**.
   
   >[!div class="mx-imgBorder"]
   >![Adicionar um novo registro de aplicativo][create-app-registration-01]

1. Especifique seu aplicativo e clique em **Registrar**.
   
   >[!div class="mx-imgBorder"]
   >![Criar novo registro de aplicativo][create-app-registration-02]

1. Quando a página do seu registro de aplicativo for exibida, copie a **ID do Aplicativo** e a **ID do Locatário**; você usará esses valores para configurar seu arquivo *application.properties* posteriormente neste tutorial.
   
   >[!div class="mx-imgBorder"]
   >![Copiar as chaves de registro do aplicativo][create-app-registration-03]

1. Clique em **Certificados e segredos** no painel de navegação esquerdo.  Em seguida, clique em **Novo segredo do cliente**.
   
   >[!div class="mx-imgBorder"]
   >![Criar chaves do registro de aplicativo][create-app-registration-03-5]

1. Adicione uma **Descrição** e selecione a duração na lista **Expira**.  Clique em **Adicionar**. O valor da chave será preenchido automaticamente.
   
   >[!div class="mx-imgBorder"]
   >![Especificar parâmetros da chave de registro do aplicativo][create-app-registration-04]

1. Copie e salve o valor do segredo do cliente para configurar seu arquivo *application.properties* posteriormente neste tutorial. (Você não conseguirá recuperar esse valor depois.)
   
   >[!div class="mx-imgBorder"]
   >![Copiar o valor da chave de registro do aplicativo][create-app-registration-04-5]

1. Clique em **Permissões de API** no painel de navegação esquerdo. 

1. Clique em **Microsoft Graph** e marque **Acessar o diretório como o usuário conectado** e **Entrar e ler o perfil do usuário**. Clique em **Conceder Permissões...** e em **Sim** quando solicitado.
   
   >[!div class="mx-imgBorder"]
   >![Adicionar permissões de acesso][create-app-registration-08]
   
1. Clique em **Conceder consentimento do administrador para o Exemplo do Azure** e em **Sim**.
   
   >[!div class="mx-imgBorder"]
   >![Conceder permissões de acesso][create-app-registration-05]

1. Na página principal do registro do aplicativo, clique em **Autenticação** e em **Adicionar uma plataforma**.  Em seguida, clique em **Aplicativos Web**.
   
   >[!div class="mx-imgBorder"]
   >![Editar URLs de Resposta][create-app-registration-09]

1. Insira 'http://localhost:8080/login/oauth2/code/azure ' como um novo **URI de Redirecionamento** e clique em **Configurar**.
   
   >[!div class="mx-imgBorder"]
   >![Adicionar nova URL de Resposta][create-app-registration-10]

1. Na página principal de registro do aplicativo, clique em **Manifesto**, defina o valor dos parâmetros `oauth2AllowIdTokenImplicitFlow` e `oauth2AllowImplicitFlow` como `true` e clique em **Salvar**.
   
   >[!div class="mx-imgBorder"]
   >![Configurar manifesto do aplicativo][create-app-registration-11]

   > [!NOTE]
   > Para obter mais informações sobre os parâmetros `oauth2AllowIdTokenImplicitFlow` e `oauth2AllowImplicitFlow` e sobre outras configurações do aplicativo, confira [Manifesto do aplicativo do Azure Active Directory][AAD app manifest].

### <a name="add-a-user-account-to-your-directory-and-add-that-account-to-a-group"></a>Adicionar uma conta de usuário ao diretório e adicionar essa conta a um grupo

1. Na página **Visão geral** do Active Directory, clique em **Usuários** e escolha **Novo usuário**.
   
   >[!div class="mx-imgBorder"]
   >![Adicionar uma nova conta de usuário][create-user-01]

1. Quando o painel **Usuário** for exibido, insira o **Nome de usuário** e o **Nome**.  Em seguida, clique em **Criar**.
   
   >[!div class="mx-imgBorder"]
   >![Inserir informações da conta de usuário][create-user-02]

   > [!NOTE]
   > Você precisa especificar a URL do diretório mostrada anteriormente neste tutorial quando inserir o nome de usuário. Por exemplo:
   >
   > `test-user@azuresampledirectory.onmicrosoft.com`

1. Na página de **Visão geral** de seu Active Directory, clique em **Grupos** e escolha o **Novo grupo** que você usará para autorização em no aplicativo.

1. Em seguida, clique em **Nenhum membro selecionado**. (Para os fins deste tutorial, criaremos um grupo chamado *usuários*.)  Pesquise o usuário criado na etapa anterior.  Clique em **Selecionar** para adicionar o usuário ao grupo.  Clique em **Criar** para criar o grupo.
   
   >[!div class="mx-imgBorder"]
   >![Selecione o usuário para o grupo][create-user-03]

1. Volte para o painel **Usuários**, selecione o usuário de teste e clique em **Redefinir senha** e copie a senha. Você usará isso quando entrar no aplicativo mais tarde neste tutorial.
   
   >[!div class="mx-imgBorder"]
   >![Mostrar a senha][create-user-04]

## <a name="configure-and-compile-your-app"></a>Configurar e compilar seu aplicativo

1. Extraia os arquivos do arquivo de projeto criado e baixado antes neste tutorial para um diretório.

1. Navegue até a pasta-mãe no projeto e abra o `pom.xml`arquivo de projeto Maven em um editor de texto.

1. Adicione as dependências de segurança do Spring OAuth2 a `pom.xml`:

   ```xml
   <dependency>
      <groupId>org.springframework.security</groupId>
      <artifactId>spring-security-oauth2-client</artifactId>
   </dependency>
   <dependency>
      <groupId>org.springframework.security</groupId>
      <artifactId>spring-security-oauth2-jose</artifactId>
   </dependency>
   ```

1. Salve e feche o arquivo *pom.xml*.

1. Navegue até a pasta *src/main/resources* no seu projeto e abra o arquivo *application.properties* em um editor de texto.

1. Especifique as configurações de registro do aplicativo usando os valores criados anteriormente. Por exemplo:

   ```yaml
   # Specifies your Active Directory ID:
   azure.activedirectory.tenant-id=22222222-2222-2222-2222-222222222222

   # Specifies your App Registration's Application ID:
   spring.security.oauth2.client.registration.azure.client-id=11111111-1111-1111-1111-1111111111111111

   # Specifies your App Registration's secret key:
   spring.security.oauth2.client.registration.azure.client-secret=AbCdEfGhIjKlMnOpQrStUvWxYz==

   # Specifies the list of Active Directory groups to use for authorization:
   azure.activedirectory.user-group.allowed-groups=Users
   ```

   Em que:

   | Parâmetro | Descrição |
   |---|---|
   | `azure.activedirectory.tenant-id` | Contém a **ID de Diretório** anterior do seu Active Directory. |
   | `spring.security.oauth2.client.registration.azure.client-id` | Contém a **ID de Aplicativo** do registro de aplicativo que você preencheu anteriormente. |
   | `spring.security.oauth2.client.registration.azure.client-secret` | Contém o **Valor** da chave do registro de aplicativo que você preencheu anteriormente. |
   | `azure.activedirectory.user-group.allowed-groups` | Contém uma lista dos grupos do Active Directory a usar para autorização. |

   > [!NOTE]
   > Para ver a lista completa dos valores disponíveis no seu arquivo *application.properties*, confira o [Exemplo de Spring Boot do Azure Active Directory][AAD Spring Boot Sample] no GitHub.

1. Salve e feche o arquivo *application.properties*.

1. Crie uma pasta chamada *controller* na pasta de origem do Java para o seu aplicativo. Por exemplo: *src/main/java/com/wingtiptoys/security/controller*.

1. Crie um arquivo Java chamado *HelloController.java* na pasta *controller* e abra-o em um editor de texto.

1. Insira o código a seguir, depois salve e feche o arquivo:

   ```java
   package com.wingtiptoys.security;

   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RestController;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.security.access.prepost.PreAuthorize;
   import org.springframework.security.oauth2.client.OAuth2AuthorizedClient;
   import org.springframework.security.oauth2.client.authentication.OAuth2AuthenticationToken;
   import org.springframework.ui.Model;

   @RestController
   public class HelloController {
      @Autowired
      @PreAuthorize("hasRole('Users')")
      @RequestMapping("/")
      public String helloWorld() {
         return "Hello World!";
      }
   }
   ```

   > [!NOTE]
   > O nome do grupo que você especificar para o método `@PreAuthorize("hasRole('')")` deve conter um dos grupos que você especificou no campo `azure.activedirectory.user-group.allowed-groups` de seu arquivo *application.properties*.
   >
   > Também é possível especificar configurações de autorização diferentes para mapeamentos de solicitação diferentes; por exemplo:
   >
   > ``` java
   > public class HelloController {
   >    @Autowired
   >    @PreAuthorize("hasRole('Users')")
   >    @RequestMapping("/")
   >    public String helloWorld() {
   >       return "Hello Users!";
   >    }
   >    @PreAuthorize("hasRole('Group1')")
   >    @RequestMapping("/Group1")
   >    public String groupOne() {
   >       return "Hello Group 1 Users!";
   >    }
   >    @PreAuthorize("hasRole('Group2')")
   >    @RequestMapping("/Group2")
   >    public String groupTwo() {
   >       return "Hello Group 2 Users!";
   >    }
   > }
   > ```

1. Crie uma pasta chamada *security* na pasta de origem do Java para o seu aplicativo. Por exemplo: *src/main/java/com/wingtiptoys/security/security*.

1. Crie um novo arquivo Java chamado *WebSecurityConfig.java* na pasta *security* e abra-o em um editor de texto.

1. Insira o código a seguir, depois salve e feche o arquivo:

    ```java
    package com.wingtiptoys.security;

    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.security.config.annotation.method.configuration.EnableGlobalMethodSecurity;
    import org.springframework.security.config.annotation.web.builders.HttpSecurity;
    import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
    import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
    import org.springframework.security.oauth2.client.oidc.userinfo.OidcUserRequest;
    import org.springframework.security.oauth2.client.userinfo.OAuth2UserService;
    import org.springframework.security.oauth2.core.oidc.user.OidcUser;

    @EnableWebSecurity
    @EnableGlobalMethodSecurity(prePostEnabled = true)
    public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
        @Autowired
        private OAuth2UserService<OidcUserRequest, OidcUser> oidcUserService;

        @Override
        protected void configure(HttpSecurity http) throws Exception {
            http
                .authorizeRequests()
                .anyRequest().authenticated()
                .and()
                .oauth2Login()
                .userInfoEndpoint()
                .oidcUserService(oidcUserService);
        }
    }
    ```

## <a name="build-and-test-your-app"></a>Crie e testar seu aplicativo

1. Abra um prompt de comando e altere o diretório para a pasta em que seu arquivo *pom.xml* do aplicativo está localizado.

1. Crie seu aplicativo Spring Boot com Maven e execute-o; por exemplo:

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```
   
   >[!div class="mx-imgBorder"]
   >![Crie seu aplicativo][build-application]

1. Após a criação e a inicialização de seu aplicativo pelo Maven, abra http:<span></span>//localhost:8080 em um navegador da Web; você deverá receber uma solicitação de nome de usuário e senha.
   
   >[!div class="mx-imgBorder"]
   ![Fazer logon em seu aplicativo][application-login]

   > [!NOTE]
   > Você pode ser solicitado a alterar sua senha se for a primeira conexão para uma nova conta de usuário.

   >[!div class="mx-imgBorder"]
   >![Alterando sua senha][update-password]

1. Após o logon bem-sucedido, você deverá ver o texto "Olá, Mundo" de exemplo no controlador.

   >[!div class="mx-imgBorder"]
   >![Logon bem-sucedido][hello-world]

   > [!NOTE]
   > Contas de usuário sem autorização receberão uma mensagem **HTTP 403 Não autorizado**.

## <a name="summary"></a>Resumo

Neste tutorial, você criou um novo aplicativo Web do Java usando o iniciador do Azure Active Directory, configurou um novo locatário do Azure AD e registrou um novo aplicativo, em seguida, configurou o aplicativo para usar as classes e as anotações do Spring para proteger o aplicativo Web.

## <a name="see-also"></a>Confira também
* Para obter informações sobre novas opções de interface do usuário, confira [Novo guia de treinamento de registro de aplicativo do portal do Azure](/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Spring e o Azure, continue no Spring no Centro de Documentação do Azure.

   >[!div class="nextstepaction"]
   >[Spring no Azure](./index.yml)

<!-- URL List -->

[Azure Active Directory Documentation]: /azure/active-directory/
[AAD app manifest]: /azure/active-directory/develop/active-directory-application-manifest
[Get started with Azure AD]: /azure/active-directory/get-started-azure-ad
[Azure for Java Developers]: ../index.yml
[free Azure account]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: /azure/devops/
[MSDN subscriber benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/
[AAD Spring Boot Sample]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-samples/azure-spring-boot-sample-active-directory-backend

<!-- IMG List -->

[create-spring-app-01]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-spring-app-01.png

[create-directory-01]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-directory-01.png
[create-directory-02]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-directory-02.png
[create-directory-03]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-directory-03.png
[create-directory-04]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-directory-04.png

[create-app-registration-01]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-01.png
[create-app-registration-02]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-02.png
[create-app-registration-03]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-03.png
[create-app-registration-03-5]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-03-5.png
[create-app-registration-04]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-04.png
[create-app-registration-04-5]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-04-5.png
[create-app-registration-05]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-05.png
[create-app-registration-08]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-08.png
[create-app-registration-09]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-09.png
[create-app-registration-10]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-10.png
[create-app-registration-11]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-app-registration-11.png

[create-user-01]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-user-01.png
[create-user-02]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-user-02.png
[create-user-03]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-user-03.png
[create-user-04]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/create-user-04.png

[application-login]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/application-login.png
[build-application]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/build-application.png
[hello-world]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/hello-world.png
[update-password]: media/configure-spring-boot-starter-java-app-with-azure-active-directory/update-password.png