---
title: Usar o Iniciador do Spring Boot para o Azure Active Directory B2C
description: Descubra como configurar um aplicativo inicializador do Spring Boot com o iniciador do Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: java
author: panli
manager: kevinzha
ms.author: panli
ms.date: 02/06/2020
ms.service: active-directory-b2c
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: identity
ms.openlocfilehash: 71bc7e2e7677ce3f53c70bd68e5e73765070bd06
ms.sourcegitcommit: 3c69d7c3e5c5a00a01ee18e63b0659830c7d4ec0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82104857"
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

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos são obrigatórios para você concluir as etapas neste artigo:

* Um JDK (Java Development Kit) com suporte. Para obter mais informações sobre os JDKs disponíveis para usar durante o desenvolvimento no Azure, confira <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/) versão 3.0 ou posterior.

## <a name="create-an-app-using-spring-initializr"></a>Criar um aplicativo usando o Spring Initialzr

1. Navegue até <https://start.spring.io/>.

2. Especifique que você deseja gerar um projeto **Maven** com **Java**, insira os nomes de **Grupo** e **Artefato** para o seu aplicativo e, em seguida, selecione o módulo **Web** e **Segurança** do Spring Initializr.

   ![Especificar os nomes do grupo e do artefato](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/si-n.png)


3. Clique em `Generate Project` e baixe o projeto para um caminho no computador local quando solicitado.

## <a name="create-azure-active-directory-instance"></a>Criar uma instância do Azure Active Directory

### <a name="create-the-active-directory-instance"></a>Criar a instância do Active Directory

1. Faça logon em <https://portal.azure.com>.

2. Clique em **+Criar um recurso**, em **Identidade** e, em seguida, em **Ver tudo**.  Pesquise o **Azure Active Directory B2C**.

   ![Criar uma nova instância do Azure Active Directory B2C](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/az-1-n.png)

3. Clique em **Criar**.

   ![Obter o nome do locatário B2C](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/az-5-n.png)

4. Selecione **Criar um novo Locatário Azure AD B2C**.

   ![Criar um Azure Active Directory](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/az-2-n.png)

5. Insira o nome da sua organização e o nome de domínio inicial e salve o nome de domínio para consultar posteriormente.  Clique em **Criar**.

   ![Escolher seu Azure Active Directory](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/az-3-n.png)

6. Após a conclusão da criação do Active Directory, navegue até o novo diretório.  Ou pesquise `b2c` e clique no serviço `Azure AD B2C`.

   ![Localize a instância do Azure Active Directory B2C](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/az-4-n.ng.png)

### <a name="add-an-application-registration-for-your-spring-boot-app"></a>Adicionar um registro de aplicativo para seu aplicativo do Spring Boot

1. Selecione **Azure AD B2C** no menu do portal, clique em **Aplicativos** e, em seguida, clique em **Adicionar**.

   ![Adicionar um novo registro de aplicativo](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/b2c1-n.png)

2. Especifique o **Nome** do aplicativo, adicione `http://localhost:8080/home` para o **URI de Redirecionamento**. Clique em **Save** (Salvar).  Em seguida, registre a **ID do Aplicativo** como seu `${your-client-id}`.  

   ![Adicionar URI de Redirecionamento do Aplicativo](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/b2c2-n.png)

3. Selecione **Certificados e segredos** do seu aplicativo, clique em **Gerar chave** para gerar `${your-client-secret}` e, em seguida, **Salvar**.

   ![Criar fluxo de usuário](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/b2c3-n.png)

4. Selecione **Fluxos dos usuários** à esquerda e então **Clique em** **Novo fluxo de usuário**.

5. Escolher **Inscrever-se ou entrar**, **Edição de perfil** e **Redefinição de senha** para criar fluxos dos usuários, respectivamente. Especifique o **Nome** e os **Atributos e declarações do usuário** de seu fluxo de usuário e clique em **Criar**.

   ![Configurar fluxo de usuário](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/b2c4-n.png)

## <a name="configure-and-compile-your-app"></a>Configurar e compilar seu aplicativo

1. Extraia os arquivos do arquivo de projeto criado e baixado antes neste tutorial para um diretório.

2. Navegue até a pasta-mãe no projeto e abra o `pom.xml`arquivo de projeto Maven em um editor de texto.

3. Adicione as dependências de segurança do Spring OAuth2 a `pom.xml`:

   ```xml
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-active-directory-b2c-spring-boot-starter</artifactId>
       <version>2.1.6.M2</version>
   </dependency>
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-thymeleaf</artifactId>
   </dependency>
   <dependency>
       <groupId>org.thymeleaf.extras</groupId>
       <artifactId>thymeleaf-extras-springsecurity5</artifactId>
   </dependency>
   ```

4. Salve e feche o arquivo *pom.xml*.

5. Navegue até a pasta *src/main/resources* no seu projeto e abra o arquivo *application.yml* em um editor de texto.

6. Especifique as configurações de registro do aplicativo usando os valores criados anteriormente. Por exemplo:

   ```yaml
   azure:
     activedirectory:
       b2c:
         tenant: ${your-tenant-name}
         client-id: ${your-client-id}
         client-secret: ${your-client-secret}
         reply-url: ${your-redirect-uri-from-aad} # should be absolute url.
         logout-success-url: ${you-logout-success-url}
         user-flows:
           sign-up-or-sign-in: ${your-sign-up-or-in-user-flow}
           profile-edit: ${your-profile-edit-user-flow}     # optional
           password-reset: ${your-password-reset-user-flow} # optional
   ```
   Em que:

   | Parâmetro | Descrição |
   |---|---|
   | `azure.activedirectory.b2c.tenant` | Contém `${your-tenant-name` do AD B2C de antes. |
   | `azure.activedirectory.b2c.client-id` | Contém o `${your-client-id}` de seu aplicativo que você preencheu anteriormente. |
   | `azure.activedirectory.b2c.client-secret` | Contém o `${your-client-secret}` de seu aplicativo que você preencheu anteriormente. |
   | `azure.activedirectory.b2c.reply-url` | Contém um dos **URIs de Redirecionamento** do seu aplicativo que você preencheu anteriormente. |
   | `azure.activedirectory.b2c.logout-success-url` | Especifique a URL ao fazer logoff do aplicativo com êxito. |
   | `azure.activedirectory.b2c.user-flows` | Contém o nome dos fluxos dos usuários que você preencheu anteriormente.

   > [!NOTE]
   > 
   > Para obter uma lista completa de valores disponíveis em seu arquivo *application.yml*, confira o [Exemplo de Spring Boot do Azure Active Directory B2C][Exemplo de Spring Boot do AAD B2C] no GitHub.
   >

7. Salve e feche o arquivo *application.yml*.

8. Crie uma pasta chamada *controlador* na pasta de origem do Java para seu aplicativo.

9. Crie um arquivo Java chamado *WebController.java* na pasta *controlador* e abra-o em um editor de texto.

10. Insira o código a seguir, depois salve e feche o arquivo:

    ```java
    package com.example.demo.controller;
    
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

11. Crie uma pasta chamada *segurança* na pasta de origem do Java para seu aplicativo.

12. Crie um arquivo Java chamado *WebSecurityConfiguration.java* na pasta *segurança* e abra-o em um editor de texto.

13. Insira o código a seguir, depois salve e feche o arquivo:

    ```java
    package com.example.demo.security;
    
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
14. Copie o `greeting.html` e o `home.html` do [Exemplo de Spring Boot do Azure AD B2C](https://github.com/Microsoft/azure-spring-boot/tree/master/azure-spring-boot-samples/azure-active-directory-b2c-oidc-spring-boot-sample/src/main/resources/templates) e substitua `${your-profile-edit-user-flow}` e `${your-password-reset-user-flow}` pelo nome do fluxo de usuário, respectivamente, concluídos anteriormente.

## <a name="build-and-test-your-app"></a>Crie e testar seu aplicativo

1. Abra um prompt de comando e altere o diretório para a pasta em que seu arquivo *pom.xml* do aplicativo está localizado.

2. Crie seu aplicativo Spring Boot com Maven e execute-o; por exemplo:

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```

3. Após a criação e inicialização de seu aplicativo pelo Maven, abra `http://localhost:8080/` em um navegador da Web; você deverá ser redirecionado para a página de logon.

   ![Página de logon](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/lo1-n.png)

4. Clique no link com o nome do fluxo de usuário `${your-sign-up-or-in}`; você deve ser redirecionado do Azure AD B2C para iniciar o processo de autenticação.

   ![Logon do Azure AD B2C](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/lo2-n.png)

4. Depois de entrar com êxito, você deverá ver o `home page` de exemplo do navegador.

   ![Logon bem-sucedido](media/configure-spring-boot-starter-java-app-with-azure-active-directory-b2c-oidc/lo3-n.png)

## <a name="summary"></a>Resumo

Neste tutorial, você criou um novo aplicativo Web do Java usando o iniciador do Azure Active Directory B2C, configurou um novo locatário do Azure AD B2C e registrou um novo aplicativo, em seguida, configurou o aplicativo para usar as classes e as anotações do Spring para proteger o aplicativo Web.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Spring e o Azure, continue no Spring no Centro de Documentação do Azure.

> [!div class="nextstepaction"]
> [Spring no Azure](/azure/developer/java/spring-framework)
