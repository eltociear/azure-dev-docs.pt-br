---
title: Implantar um aplicativo Web Olá, Mundo em um contêiner do Linux
titleSuffix: Azure Toolkit for IntelliJ
description: Executar um aplicativo Web Olá, Mundo em um contêiner do Linux e implantá-lo na nuvem usando o Kit de Ferramentas do Azure para IntelliJ.
services: app-service\web
documentationcenter: java
ms.date: 12/20/2018
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: 7b6d393f44034794494f4e77a6365bf0d7bf6c1d
ms.sourcegitcommit: 8cd0ddf1651c3b64bb72dedc2890108c2cfe3bcb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87334421"
---
# <a name="deploy-java-app-to-azure-web-apps-for-containers-using-azure-toolkit-for-intellij"></a>Implantar o aplicativo Java nos Aplicativos Web para Contêineres do Azure usando o Azure Toolkit for IntelliJ

Contêineres do [Docker] são um método amplamente usado para implantar aplicativos Web. Com os contêineres do Docker, os desenvolvedores podem consolidar todos os arquivos de projeto e dependências em um único pacote para implantação em um servidor. O Kit de Ferramentas do Azure para IntelliJ simplifica o processo para desenvolvedores de Java adicionando recursos para implantação de contêineres no Microsoft Azure.

Este artigo demonstra as etapas que são necessárias para criar um aplicativo Web Olá, Mundo básico e publicar seu aplicativo Web em um contêiner do Linux no Azure usando o Kit de Ferramentas do Azure para IntelliJ.

[!INCLUDE [prerequisites](includes/prerequisites.md)]
* Um cliente do [Docker].

> [!NOTE]
>
> Para concluir as etapas neste tutorial, você precisa configurar o [Docker] para expor o daemon na porta 2375 sem TLS. Você pode definir essa configuração ao instalar o Docker ou por meio do menu de configurações do Docker.
>
> ![Menu de configurações do Docker][docker-settings-menu]
>

## <a name="create-a-new-web-app-project"></a>Criar um novo projeto do aplicativo Web

1. Inicie o IntelliJ e entre em sua conta do Azure usando as etapas no artigo [Instruções de entrada para o Kit de Ferramentas do Azure para IntelliJ](sign-in-instructions.md).

1. Clique menu **Arquivo**, clique em **Novo** e em **Projeto**.
   
   ![Criar um novo projeto][file-new-project]

1. Na caixa de diálogo **Novo Projeto**, selecione **Maven**, em seguida, **maven-archetype-webapp** e, em seguida, clique em **Avançar**.
   
   ![Escolha o aplicativo Web do modelo Maven][maven-archetype-webapp]
   
1. Especifique o **GroupId** e **ArtifactId** para seu aplicativo Web e depois clique em **Avançar**.
   
   ![Especifique GroupId e ArtifactId][groupid-and-artifactid]

1. Personalize as configurações de Maven ou aceite os padrões e, em seguida, clique em **Avançar**.
   
   ![Especifique as configurações de Maven][maven-options]

1. Especifique um nome de projeto e local e, em seguida, clique em **Concluir**.
   
   ![Especifique o nome do projeto][project-name]

## <a name="create-an-azure-container-registry-to-use-as-a-private-docker-registry"></a>Criar um Registro de Contêiner do Azure para usar como um registro do Docker privado

As etapas a seguir orientam você no uso do portal do Azure para criar um Registro de contêiner do Azure.

> [!NOTE]
>
> Se você quiser usar a CLI do Azure, em vez do portal do Azure, siga as etapas em [Criar um registro de contêiner do Docker privado usando a CLI do Azure 2.0][Create Docker Registry using Azure CLI].
>

1. Navegue até o [Azure portal] e conecte-se.

   Depois de entrar em sua conta no portal do Azure, você pode seguir as etapas no artigo [Criar um registro de contêiner privado do Docker usando o portal do Azure], que foram parafraseadas nas etapas a seguir para fins de conveniência.

1. Clique no ícone do menu para **+ Criara um recurso** e, em seguida, clique em **Contêineres** e em **Registro de Contêiner**.
   
   ![Criar um novo Registro de Contêiner do Azure][create-container-registry-01]

1. Quando a página **Criar registro de contêiner** for exibida, insira seu **Nome do registro** e **Grupo de recursos**, escolha **Habilitar** para o **Usuário administrador** e clique em **Criar**.

   ![Definir configurações do registro de contêiner do Azure][create-container-registry-02]

## <a name="deploy-your-web-app-in-a-docker-container"></a>Implante seu aplicativo Web em um contêiner do Docker

1. Clique com o botão direito do mouse no seu projeto no explorador de projeto, escolha **Azure** e, em seguida, clique em **Adicionar suporte do Docker**.

   Isso criará automaticamente um arquivo do Docker com uma configuração padrão.

   ![Adicionar suporte ao Docker][add-docker-support]

1. Após ter adicionado o suporte do Docker, clique com o botão direito do mouse no seu projeto no explorador de projeto, escolha **Azure** e, em seguida, clique em **Executar no Aplicativo Web para Contêineres**.

   ![Executar no Aplicativo Web para Contêineres][run-on-web-app-for-containers]

1. Quando a caixa de diálogo **Executar no Aplicativo Web para Contêineres** for exibida, preencha as informações necessárias:

   * **Name**: especifica o nome amigável que é exibido no Azure Toolkit. 

   * **Registro de Contêiner**: escolha o registro de contêiner no menu suspenso que você criou na seção anterior deste artigo. Os campos para **URL do servidor**, **Nome de usuário**, e **Senha** serão preenchidos automaticamente.

   * **Imagem e marca**: especifica o nome da imagem de contêiner. Geralmente usará a sintaxe a seguir: “*registry*.azurecr.io/*appname*:latest”, em que: 
      * *registry* é o registro de contêiner da seção anterior deste artigo 
      * *appname* é o nome do seu aplicativo Web 

   * **Usar aplicativo Web existente** ou **Criar novo aplicativo Web**: especifica se você implantará o contêiner em um aplicativo Web existente ou criará um novo aplicativo Web. O **Nome do aplicativo** especificado criará a URL do aplicativo Web, por exemplo: *wingtiptoys.azurewebsites.net*.

   * **Grupo de Recursos**: especifica se você criará um novo grupo de recursos ou usará um existente. 

   * **Plano do Serviço de Aplicativo**: especifica se você criará um novo plano de serviço de aplicativo ou usará um existente. 

   ![Executar no Aplicativo Web para Contêineres][run-on-web-app-linux]

1. Quando terminar de definir as configurações listadas acima, clique em **Executar**. Quando seu aplicativo Web tiver sido implantado com êxito, o status será exibido na janela **Executar**.

   ![Aplicativo Web implantado com êxito][successfully-deployed]

1. Depois que seu aplicativo Web tiver sido publicado, você pode navegar até a URL especificada anteriormente para seu aplicativo Web; por exemplo: *wingtiptoys.azurewebsites.net*.

   ![Navegar até seu aplicativo Web][browsing-to-web-app]

## <a name="optional-modify-your-web-app-publish-settings"></a>Opcional: Modificar configurações de publicação do aplicativo Web

1. Depois de publicar seu aplicativo Web, as configurações serão salvas como padrão e você poderá executar seu aplicativo no Azure clicando no ícone de seta verde na barra de ferramentas. Você pode modificar essas configurações clicando no menu suspenso para seu aplicativo Web e clicando em **Editar Configurações**.

   ![Menu Editar configuração][edit-configuration-menu]

1. Quando a caixa de diálogo **Configurações de execução/depuração** for exibida, você poderá modificar as configurações padrão e, em seguida, clicar em **OK**.

   ![Caixa de diálogo Editar configuração][edit-configuration-dialog]

## <a name="next-steps"></a>Próximas etapas

Para obter recursos adicionais para o Docker, consulte o [site do Docker][Docker] oficial.

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->

[Azure portal]: https://portal.azure.com/
[Criar um registro de contêiner privado do Docker usando o portal do Azure]: /azure/container-registry/container-registry-get-started-portal
[Azure for Java Developers]: /azure/developer/java
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/
[Create Docker Registry using Azure CLI]: /azure/container-registry/container-registry-get-started-azure-cli

[Docker]: https://www.docker.com/
[Configuring artifacts]: https://www.jetbrains.com/help/idea/2016.1/configuring-artifacts.html

<!-- IMG List -->

[add-docker-support]: media/hello-world-web-app-linux/add-docker-support.png
[browsing-to-web-app]:  media/hello-world-web-app-linux/browsing-to-web-app.png
[create-container-registry-01]: media/hello-world-web-app-linux/create-container-registry-01.png
[create-container-registry-02]: media/hello-world-web-app-linux/create-container-registry-02.png
[docker-settings-menu]: media/hello-world-web-app-linux/docker-settings-menu.png
[edit-configuration-dialog]: media/hello-world-web-app-linux/edit-configuration-dialog.png
[edit-configuration-menu]: media/hello-world-web-app-linux/edit-configuration-menu.png
[file-new-project]: media/hello-world-web-app-linux/file-new-project.png
[groupid-and-artifactid]: media/hello-world-web-app-linux/groupid-and-artifactid.png
[maven-archetype-webapp]: media/hello-world-web-app-linux/maven-archetype-webapp.png
[maven-options]: media/hello-world-web-app-linux/maven-options.png
[project-name]: media/hello-world-web-app-linux/project-name.png
[run-on-web-app-for-containers]: media/hello-world-web-app-linux/run-on-web-app-for-containers.png
[run-on-web-app-linux]: media/hello-world-web-app-linux/run-on-web-app-linux.png
[successfully-deployed]: media/hello-world-web-app-linux/successfully-deployed.png
