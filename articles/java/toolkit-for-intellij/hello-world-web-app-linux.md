---
title: Implantar um aplicativo Web Olá, Mundo em um contêiner do Linux
titleSuffix: Azure Toolkit for IntelliJ
description: Executar um aplicativo Web Olá, Mundo em um contêiner do Linux e implantá-lo na nuvem usando o Kit de Ferramentas do Azure para IntelliJ.
services: app-service\web
documentationcenter: java
ms.date: 09/09/2020
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: devx-track-java
ms.openlocfilehash: df71e27850a467e03ab58edf6beaf085b195803b
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831972"
---
# <a name="deploy-java-app-to-azure-web-apps-for-containers-using-azure-toolkit-for-intellij"></a>Implantar o aplicativo Java nos Aplicativos Web para Contêineres do Azure usando o Azure Toolkit for IntelliJ

Contêineres do [Docker] são um método amplamente usado para implantar aplicativos Web. Com os contêineres do Docker, os desenvolvedores podem consolidar todos os arquivos de projeto e dependências em um único pacote para implantação em um servidor. O Azure Toolkit for IntelliJ simplifica o processo para desenvolvedores de Java adicionando recursos para implantação de contêineres no Microsoft Azure.

Este artigo demonstra as etapas que são necessárias para criar um aplicativo Web Olá, Mundo básico e publicar seu aplicativo Web em um contêiner do Linux no Azure usando o Kit de Ferramentas do Azure para IntelliJ.

[!INCLUDE [prerequisites](includes/prerequisites.md)]
* Um cliente do [Docker].

> [!NOTE]
>
> Para concluir as etapas neste tutorial, você precisa configurar o [Docker] para expor o daemon na porta 2375 sem TLS. Você pode definir essa configuração ao instalar o Docker ou por meio do menu de configurações do Docker.
>
> ![Menu de configurações do Docker][docker-settings-menu]
>

## <a name="installation-and-sign-in"></a>Instalação e credenciais

As etapas a seguir guiarão você pelo processo de entrada no Azure no ambiente de desenvolvimento do IntelliJ.

1. Se você ainda não instalou o plug-in, confira [Instalar o Azure Toolkit for IntelliJ](installation.md).

1. Para entrar em sua conta do Azure, navegue até a barra lateral esquerda do **Azure Explorer** e clique no ícone **Entrar no Azure**. Como alternativa, você pode navegar até **Ferramentas**, expandir **Azure** e clicar em **Entrar no Azure**.

   :::image type="content" source="media/sign-in-instructions/I01.png" alt-text="Entre no Azure no IntelliJ."::: 

1. Na janela **Entrar no Azure**, selecione **Logon do Dispositivo** e, em seguida, clique em **Entrar** ([outras opções de entrada](sign-in-instructions.md)).

1. Clique em **Copiar e Abrir** na caixa de diálogo **Logon no Dispositivo do Azure**.

1. No navegador, cole o código de dispositivo (que foi copiado quando você clicou em **Copiar e Abrir** na última etapa) e, em seguida, clique em **Avançar**.

1. Selecione sua conta do Azure e conclua os procedimentos de autenticação necessários para entrar.

1. Depois de entrar, feche o navegador e volte para o IDE do IntelliJ. Na caixa de diálogo **Selecionar Assinaturas**, selecione as assinaturas que deseja usar e clique em **OK**.

## <a name="creating-a-new-web-app-project"></a>Como criar um projeto de aplicativo Web

1. Clique em **Arquivo**, expanda **Novo** e clique em **Projeto**.

1. Na caixa de diálogo **Novo Projeto**, selecione **Maven** e verifique se a opção **Criar do Arquétipo** está marcada. Na lista, selecione **maven-archetype-webapp** e clique em **Avançar**.

   :::image type="content" source="media/create-hello-world-web-app/maven-archetype-webapp.png" alt-text="Selecione a opção maven-archetype-webapp."::: 

1. Expanda a lista suspensa **Coordenadas de Artefato** para exibir todos os campos de entrada e especifique as seguintes informações para seu novo aplicativo Web e clique **Avançar**:

   * **Name**: O nome do seu aplicativo Web. Isso preencherá automaticamente o campo **ArtifactId** do aplicativo Web.
   * **GroupId**: O nome do grupo de artefatos, geralmente um domínio da empresa. (por exemplo, *com.microsoft.azure*)
   * **Versão**: Manteremos a versão padrão *1.0-SNAPSHOT*.

1. Personalize as configurações de Maven ou aceite os padrões e, em seguida, clique em **Concluir**.

1. Navegue até o projeto na guia esquerda **Projeto** e abra o arquivo **src/main/webapp/WEB-INF/index.jsp**. Substitua o código pelo seguinte e escolha **salvar as alterações**:

   ```html
   <html>
    <body>
      <b><% out.println("Hello World!"); %></b>
    </body>
   </html>
   ```
   :::image type="content" source="media/create-hello-world-web-app/open-index-page.png" alt-text="Abra o arquivo index.jsp.":::

## <a name="create-an-azure-container-registry-to-use-as-a-private-docker-registry"></a>Criar um Registro de Contêiner do Azure para usar como um registro do Docker privado

As etapas a seguir orientam você no uso do portal do Azure para criar um Registro de contêiner do Azure.

> [!NOTE]
>
> Se você quiser usar a CLI do Azure, em vez do portal do Azure, siga as etapas em [Criar um registro de contêiner do Docker privado usando a CLI do Azure 2.0][Create Docker Registry using Azure CLI].
>

1. Navegue até o [Azure portal] e conecte-se.

   Depois de entrar em sua conta no portal do Azure, você pode seguir as etapas no artigo [Criar um registro de contêiner privado do Docker usando o portal do Azure], que foram parafraseadas nas etapas a seguir para fins de conveniência.

1. Clique no ícone do menu para **+ Criara um recurso** e, em seguida, clique na categoria **Contêineres** e em **Registro de Contêiner**.

1. Quando a página **Criar registro de contêiner** for exibida, especifique as seguintes informações:

   * **Assinatura**: especifica a assinatura do Azure que você deseja usar para o novo registro de contêiner.

   * **Grupo de Recursos**: Especifica o grupo de recursos para o registro de contêiner. Selecione uma das seguintes opções:
      * **Criar novo**: especifica que você deseja criar um novo grupo de recursos.
      * **Usar existente**: especifica que você selecionará em uma lista de grupos de recursos associados à sua conta do Azure.

   * **Nome do registro**: especifica o nome do novo registro de contêiner.

   * **Localização**: especifica a região em que o registro de contêiner será criado (por exemplo, "Oeste dos EUA").

   * **SKU**: especifica a camada de serviço para o registro de contêiner. Para este tutorial, selecione *Básico*. Para obter mais informações, confira [Níveis de serviço do Registro de Contêiner do Azure](/azure/container-registry/container-registry-skus).

1. Clique em **Examinar + criar** e verifique se as informações estão corretas. Para concluir, clique em **Criar**.

## <a name="deploy-your-web-app-in-a-docker-container"></a>Implante seu aplicativo Web em um contêiner do Docker

As etapas a seguir orientarão você na configuração do suporte do Docker para seu aplicativo Web e na implantação do aplicativo Web.

1. Navegue até seu projeto na guia esquerda **Projeto** e clique com o botão direito do mouse em seu projeto. Expanda **Azure** e clique em **Adicionar Suporte do Docker**.

   Isso criará automaticamente um arquivo do Docker com uma configuração padrão.

   :::image type="content" source="media/hello-world-web-app-linux/docker-support-file.png" alt-text="O arquivo de suporte do Docker.":::

1. Após ter adicionado o suporte do Docker, clique com o botão direito do mouse no seu projeto no explorador de projeto, expanda **Azure** e, em seguida, clique em **Executar no Aplicativo Web para Contêineres**.

1. Na caixa de diálogo **Executar no Aplicativo Web para Contêineres**, preencha as seguintes informações:

   * **Name**: especifica o nome amigável que é exibido no Azure Toolkit. 

   * **Registro de Contêiner**: escolha o registro de contêiner no menu suspenso que você criou na seção anterior deste artigo. Os campos para **URL do servidor**, **Nome de usuário**, e **Senha** serão preenchidos automaticamente.

   * **Imagem e marca**: especifica o nome da imagem de contêiner. Geralmente usará a sintaxe a seguir: “*registry*.azurecr.io/*appname*:latest”, em que: 
      * *registry* é o registro de contêiner da seção anterior deste artigo 
      * *appname* é o nome do seu aplicativo Web 

   * **Usar aplicativo Web existente** ou **Criar novo aplicativo Web**: especifica se você implantará o contêiner em um aplicativo Web existente ou criará um novo aplicativo Web. O **Nome do aplicativo** especificado criará a URL do aplicativo Web, por exemplo: *wingtiptoys.azurewebsites.net*.

   * **Grupo de Recursos**: especifica se você criará um novo grupo de recursos ou usará um existente. 

   * **Plano do Serviço de Aplicativo**: especifica se você criará um novo plano de serviço de aplicativo ou usará um existente. 

1. Quando terminar de definir as configurações listadas acima, clique em **Executar**. Quando seu aplicativo Web tiver sido implantado com êxito, o status será exibido na janela **Executar**.

1. Depois que seu aplicativo Web tiver sido publicado, você pode navegar até a URL especificada anteriormente para seu aplicativo Web; por exemplo: *wingtiptoys.azurewebsites.net*.

   ![Navegar até seu aplicativo Web][browsing-to-web-app]

## <a name="optional-modify-your-web-app-publish-settings"></a>Opcional: Modificar configurações de publicação do aplicativo Web

1. Depois de publicar seu aplicativo Web, as configurações serão salvas como padrão e você poderá executar seu aplicativo no Azure clicando no ícone de seta verde na barra de ferramentas. Você pode modificar essas configurações clicando no menu suspenso para seu aplicativo Web e clicando em **Editar Configurações**.

    :::image type="content" source="media/create-hello-world-web-app/edit-configuration-menu.png" alt-text="Menu Editar configuração.":::

1. Quando a caixa de diálogo **Configurações de execução/depuração** for exibida, você poderá modificar as configurações padrão e, em seguida, clicar em **OK**.

## <a name="next-steps"></a>Próximas etapas

Para obter recursos adicionais para o Docker, consulte o [site do Docker][Docker] oficial.

[!INCLUDE [additional-resources](includes/additional-resources.md)]

<!-- URL List -->

[Azure portal]: https://portal.azure.com/
[Criar um registro de contêiner privado do Docker usando o portal do Azure]: /azure/container-registry/container-registry-get-started-portal
[Azure for Java Developers]: ../index.yml
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/
[Create Docker Registry using Azure CLI]: /azure/container-registry/container-registry-get-started-azure-cli

[Docker]: https://www.docker.com/
[Configuring artifacts]: https://www.jetbrains.com/help/idea/2016.1/configuring-artifacts.html

<!-- IMG List -->

[browsing-to-web-app]:  media/hello-world-web-app-linux/browsing-to-web-app.png
[docker-settings-menu]: media/hello-world-web-app-linux/docker-settings-menu.png