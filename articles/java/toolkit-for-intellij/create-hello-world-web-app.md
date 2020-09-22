---
title: Criar um aplicativo Web Olá, Mundo para o Serviço de Aplicativo do Azure usando o IntelliJ
description: Este tutorial mostra como usar o Kit de Ferramentas do Azure para IntelliJ para criar um aplicativo Web Hello World para o Azure.
services: app-service
keywords: java, intellij, aplicativo Web, serviço de aplicativo do azure, olá, mundo, início rápido
documentationcenter: java
author: selvasingh
ms.assetid: 75ce7b36-e3ae-491d-8305-4b42ce37db4e
ms.reviewer: asirveda
ms.date: 09/09/2020
ms.service: app-service
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.custom: devx-track-java
ms.openlocfilehash: 3a096ac0cb533fda11f2b2f3a652dfae628298d4
ms.sourcegitcommit: bfaeacc2fb68f861a9403585d744e51a8f99829c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90682139"
---
# <a name="create-a-hello-world-web-app-for-azure-app-service-using-intellij"></a>Criar um aplicativo Web Olá, Mundo para o Serviço de Aplicativo do Azure usando o IntelliJ

Este artigo demonstra as etapas que são necessárias para criar um aplicativo Web Olá, Mundo básico e publicar seu aplicativo Web em um Serviço de Aplicativo do Azure usando o [Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053).

> [!NOTE]
>
> Se você preferir usar o Eclipse, confira nosso [tutorial semelhante para o Eclipse][eclipse-hello-world].
>
>[!INCLUDE [quickstarts-free-trial-note](includes/quickstarts-free-trial-note.md)]
>
> Não se esqueça de limpar os recursos depois de concluir este tutorial. Nesse caso, executar este guia não excederá sua cota da conta gratuita.
>

[!INCLUDE [basic-prerequisites](includes/basic-prerequisites.md)]

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

## <a name="deploying-web-app-to-azure"></a>Como implantar o aplicativo Web no Azure

1. Na exibição do Explorador de Projeto, clique com o botão direito do mouse em seu projeto, expanda **Azure** e, em seguida, clique em **Implantar em Aplicativos Web do Azure**.

1. Na caixa de diálogo Implantar no Azure, você pode implantar o aplicativo em um webapp do Tomcat existente ou criar um.

   a. Clique em **Nenhum webapp disponível, clique para criar um** para criar um webapp. Caso contrário, escolha **Criar WebApp** na lista suspensa WebApp se houver webapps em sua assinatura.

      :::image type="content" source="media/create-hello-world-web-app/deploy-to-azure-webapps.png" alt-text="Janela da caixa de diálogo Implantar para o Azure.":::

   Na caixa de diálogo pop-up **Criar WebApp**, especifique as informações a seguir e clique em **OK**: 

      * **Name**: A cadeia de caracteres do nome de domínio do WebApp.
      * **Assinatura**: especifica a assinatura do Azure que você deseja usar para o novo WebApp.
      * **Plataforma**: Selecione *Linux*.
      * **Contêiner da Web**: Selecione *TOMCAT 9.0-jre8* ou conforme adequado.
      * **Grupo de Recursos**: Especifica o grupo de recursos para seu WebApp. Você pode selecionar um grupo de recursos associado à sua conta do Azure ou criar um.
      * **Plano do Serviço de Aplicativo**: Especifica o plano do serviço de aplicativo para seu WebApp. Você pode selecionar um plano associado à sua conta do Azure ou criar um.

   b. Para implantar em um webapp existente, escolha o aplicativo Web no menu suspenso WebApp e clique em **Executar**.

1. O kit de ferramentas exibirá uma mensagem de status quando tiver implantado com êxito seu aplicativo Web, junto com a URL do aplicativo Web implantado, se bem-sucedido.

1. Você pode navegar até seu aplicativo Web usando o link fornecido na mensagem de status.

   :::image type="content" source="media/create-hello-world-web-app/browse-web-app.png" alt-text="Procure seu aplicativo Web.":::

## <a name="managing-deploy-configurations"></a>Como gerenciar configurações de implantação

> [!TIP]
> Depois de publicar seu aplicativo Web, você pode executar a implantação clicando no ícone de seta verde na barra de ferramentas.

1. Antes de executar a implantação do WebApp, você pode modificar as configurações padrão clicando no menu suspenso para seu aplicativo Web e selecionando **Editar Configurações**.

   :::image type="content" source="media/create-hello-world-web-app/edit-configuration-menu.png" alt-text="Menu Editar configuração.":::

1. Na caixa de diálogo **Executar/Depurar Configurações**, você pode modificar qualquer uma das configurações padrão. Clique em **OK** para salvar as configurações.

## <a name="cleaning-up-resources"></a>Limpando recursos

1. Para excluir seu aplicativo Web, navegue até a barra lateral esquerda do **Azure Explorer** e localize o item **Aplicativos Web**. 

   > [!NOTE]
   > Se o item de menu de aplicativos Web não se expandir, atualize manualmente a lista clicando no ícone **Atualizar** na barra de ferramentas do Azure Explorer ou clicando com o botão direito do mouse no item de menu aplicativos Web e selecionando **Atualizar**.

1. Clique com o botão direito do mouse no aplicativo Web que você gostaria de excluir e clique **Excluir**.

1. Para excluir o plano do serviço de aplicativo ou o grupo de recursos, acesse o [portal do Azure](https://portal.azure.com) e exclua manualmente os recursos em sua assinatura.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [additional-resources](includes/additional-resources.md)]

Para obter mais informações sobre como criar aplicativos Web do Azure, confira a [Visão geral de Aplicativos Web].

<!-- URL List -->

[Azure Toolkit for IntelliJ]: azure-toolkit-for-intellij.md
[Azure Toolkit for Eclipse]: ../toolkit-for-eclipse/azure-toolkit-for-eclipse.md
[eclipse-hello-world]: ../toolkit-for-eclipse/create-hello-world-web-app.md
[Visão geral de Aplicativos Web]: /azure/app-service/app-service-web-overview
[Apache Tomcat]: http://tomcat.apache.org/
[Jetty]: http://www.eclipse.org/jetty/
[Legacy Version]: create-hello-world-web-app-legacy-version.md
[intelliJ-sign-in-instructions]: sign-in-instructions.md

<!-- IMG List -->
[marketplace]:media/create-hello-world-web-app/marketplace.png
[file-new-project]: media/create-hello-world-web-app/file-new-project.png
[maven-archetype-webapp]: media/create-hello-world-web-app/maven-archetype-webapp.png
[groupid-and-artifactid]: media/create-hello-world-web-app/groupid-and-artifactid.png
[maven-options]: media/create-hello-world-web-app/maven-options.png
[project-name]: media/create-hello-world-web-app/project-name.png
[open-index-page]: media/create-hello-world-web-app/open-index-page.png
[edit-index-page]: media/create-hello-world-web-app/edit-index-page.png
[deploy-to-azure-menu]: media/create-hello-world-web-app/run-on-web-app-menu.png
[deploy-to-azure-dialog]: media/create-hello-world-web-app/run-on-web-app-dialog.png
[deploy-to-existing-webapp]: media/create-hello-world-web-app/deploy-to-existing-webapp.png
[create-new-web-app-dialog]: media/create-hello-world-web-app/create-new-web-app-dialog.png
[successfully-deployed]: media/create-hello-world-web-app/successfully-deployed.png
[browse-web-app]: media/create-hello-world-web-app/browse-web-app.png
[edit-configuration-menu]: media/create-hello-world-web-app/edit-configuration-menu.png
[edit-configuration-dialog]: media/create-hello-world-web-app/edit-configuration-dialog.png
[clean-resources]: media/create-hello-world-web-app/clean-resource.png