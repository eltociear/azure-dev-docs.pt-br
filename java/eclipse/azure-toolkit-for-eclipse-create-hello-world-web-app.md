---
title: Criar um aplicativo Web Olá, Mundo para o Serviço de Aplicativo do Azure usando o Eclipse
description: Este tutorial mostra como usar o Kit de Ferramentas do Azure para Eclipse para criar um aplicativo Web Hello World para o Azure.
services: app-service
keywords: java, eclipse, aplicativo Web, serviço de aplicativo do azure, olá, mundo, início rápido
documentationcenter: java
author: selvasingh
manager: douge
editor: ''
ms.assetid: 20d41e88-9eab-462e-8ee3-89da71e7a33f
ms.author: brendm
ms.reviewer: asirveda
ms.date: 02/01/2018
ms.devlang: java
ms.service: app-service
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.openlocfilehash: 3d673abe4f4efd7330307999c7a945cd5cb37179
ms.sourcegitcommit: 3d42245ecbfaeda31abba5f3f813b28f715ffd7c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68691737"
---
# <a name="create-a-hello-world-web-app-for-azure-app-service-using-eclipse"></a>Criar um aplicativo Web Olá, Mundo para o Serviço de Aplicativo do Azure usando o Eclipse

Usando o plug-in [Azure Toolkit for Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) de software livre, criar e implantar um aplicativo Olá, Mundo básico para o Serviço de Aplicativo do Azure como um aplicativo Web pode ser feito em poucos minutos.

> [!NOTE]
>
> Se preferir usar o IntelliJ IDEA, confira nosso [tutorial semelhante para o IntelliJ][intellij-hello-world].
>
>[!INCLUDE [quickstarts-free-trial-note](../includes/quickstarts-free-trial-note.md)]
>
> Não se esqueça de limpar os recursos depois de concluir este tutorial. Nesse caso, executar este guia não excederá sua cota da conta gratuita.
>

[!INCLUDE [azure-toolkit-for-intellij-basic-prerequisites](../includes/azure-toolkit-for-eclipse-basic-prerequisites.md)]

## <a name="installation-and-sign-in"></a>Instalação e credenciais

1. Arraste o botão a seguir para seu workspace do Eclipse em execução para instalar o plug-in do Azure Toolkit for Eclipse ([outras opções de instalação](azure-toolkit-for-eclipse-installation.md)).

    [![Arraste para seu workspace do Eclipse* em execução. *Requer o Cliente do Eclipse Marketplace](https://marketplace.eclipse.org/sites/all/themes/solstice/public/images/marketplace/btn-install.png)](http://marketplace.eclipse.org/marketplace-client-intro?mpc_install=1919278 "Arraste para seu workspace do Eclipse* em execução. *Requer o Cliente do Eclipse Marketplace")

1. Para entrar em sua conta do Azure, clique em **Ferramentas**, então em **Azure** e em **Entrar**.
   ![Menu do Eclipse para Entrada no Azure][I01]

1. Na janela **Entrar no Azure**, selecione **Logon do Dispositivo** e, em seguida, clique em **Entrar** ([outras opções de entrada](azure-toolkit-for-eclipse-sign-in-instructions.md)).

   ![A janela Entrar no Azure com o logon no dispositivo selecionado][I02]

1. Clique em **Copiar e Abrir** na caixa de diálogo **Logon no Dispositivo do Azure**.

   ![A janela da caixa de diálogo Logon no Azure][I03]

1. No navegador, cole o código de dispositivo (que foi copiado quando você clicou em **Copiar e Abrir** na última etapa) e, em seguida, clique em **Avançar**.

   ![O navegador de logon do dispositivo][I04]

1. Por fim, na caixa de diálogo **Selecionar Assinaturas**, selecione as assinaturas que deseja usar e clique em **OK**.

   ![A caixa de diálogo Selecionar Assinaturas][I05]

## <a name="creating-web-app-project"></a>Como criar um projeto de aplicativo Web

1. Clique em **Arquivo**, **Novo**, em seguida, clique em **Projeto Web Dinâmico**. (Se você não vir o **Projeto Web Dinâmico** listado como um projeto disponível depois de clicar em **Arquivo** e em **Novo**, faça o seguinte: clique em **Arquivo**, clique em **Novo**, clique em **Projeto...** , expanda **Web**, clique em **Projeto Web Dinâmico** e clique em **Avançar**.)

   ![Criando um novo projeto Web dinâmico][file-new-dynamic-web-project]

2. Para o objetivo deste tutorial, nomeie o projeto **MyWebApp**. Sua tela será semelhante à seguinte:
   
   ![Novas propriedades do Projeto Web Dinâmico][dynamic-web-project-properties]

3. Clique em **Concluir**.

4. No modo de exibição do Gerenciador de Projeto do Eclipse, expanda **MyWebApp**. Clique com o botão direito do mouse em **WebContent**, clique em **Novo** e, em seguida, clique em **Arquivo JSP**.

   ![Criar um novo arquivo JSP][create-new-jsp-file]

5. Na caixa de diálogo **Novo Arquivo JSP**, nomeie o arquivo **index.jsp**, mantenha a pasta pai como **MyWebApp/WebContent** e clique em **Avançar**.

   ![Caixa de diálogo Novo Arquivo JSP][new-jsp-file-dialog]

6. Na caixa de diálogo **Selecionar Modelo JSP**, para a finalidade deste tutorial, escolha **Novo Arquivo JSP (html)** e clique em **Concluir**.

   ![Selecionar um modelo JSP][select-jsp-template]

7. Quando o arquivo index.jsp for aberto no Eclipse, adicione o texto para exibir dinamicamente **Hello World!** dentro do elemento existente `<body>`. Seu conteúdo do `<body>` atualizado deve ser parecido com o exemplo a seguir:
   
   ```jsp
   <body><b><% out.println("Hello World!"); %></b></body>
   ```

8. Salve o index.jsp.

## <a name="deploying-web-app-to-azure"></a>Como implantar o aplicativo Web no Azure

1. Na exibição Gerenciador de Projetos do Eclipse, clique no projeto com o botão direito, escolha **Azure**, em seguida, escolha **Publicar como Aplicativo Web**.
   
   ![Publicar como Aplicativo Web do Azure][publish-as-azure-web-app]

1. Quando a caixa de diálogo **Implantar Aplicativo Web**for exibida, você poderá escolher uma das seguintes opções:

   * Selecione um aplicativo Web existente se houver.

      ![Selecionar o serviço de aplicativo][select-app-service]

   * Clique em **Criar Novo Aplicativo Web**.

      ![Criar Serviço de Aplicativo][create-app-service]

      Especifique as informações necessárias para seu aplicativo Web na caixa de diálogo **Criar Serviço de Aplicativo** e clique em **Criar**.

      Aqui você pode configurar o ambiente de tempo de execução, as configurações do aplicativo, o grupo de recursos e o plano de serviço.

      ![Criar a caixa de diálogo Serviço de Aplicativo][create-app-service-dialog]

1. Selecione seu aplicativo Web e clique em **Implantar**.

   ![Implantar serviço de aplicativo][deploy-app-service]

1. O kit de ferramentas exibirá um status **Publicado** na guia do **Log de Atividades do Azure** quando o aplicativo Web for implantado com êxito, que é um hiperlink para a URL do aplicativo Web implantado.

   ![Status de publicação][publish-status]

1. Você pode navegar até seu aplicativo Web usando o link fornecido na mensagem de status.

   ![Procurar seu aplicativo Web][browse-web-app]

[!INCLUDE [azure-toolkit-for-eclipse-show-azure-explorer](../includes/azure-toolkit-for-eclipse-show-azure-explorer.md)]

## <a name="cleaning-up-resources"></a>Limpando recursos

1. Depois de publicar o aplicativo Web no Azure, você poderá gerenciá-lo clicando com o botão direito do mouse no Azure Explorer e selecionando uma das opções no menu de contexto. Por exemplo, você pode **Excluir** seu aplicativo Web aqui para limpar o recurso para este tutorial.

   ![Gerenciar o serviço de aplicativo][manage-app-service]

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [azure-toolkit-for-eclipse-additional-resources](../includes/azure-toolkit-for-eclipse-additional-resources.md)]

Para obter mais informações sobre como criar aplicativos Web do Azure, confira a [Visão geral de Aplicativos Web].

<!-- URL List -->

[Azure Toolkit for Eclipse]: azure-toolkit-for-eclipse.md
[Azure Toolkit for IntelliJ]: ../intellij/azure-toolkit-for-intellij.md
[intellij-hello-world]: ../intellij/azure-toolkit-for-intellij-create-hello-world-web-app.md
[Visão geral de Aplicativos Web]: /azure/app-service/app-service-web-overview
[Apache Tomcat]: http://tomcat.apache.org/
[Jetty]: http://www.eclipse.org/jetty/
[Legacy Version]: azure-toolkit-for-eclipse-create-hello-world-web-app-legacy-version.md

<!-- IMG List -->
[I01]: media/azure-toolkit-for-eclipse-sign-in-instructions/I01.png
[I02]: media/azure-toolkit-for-eclipse-sign-in-instructions/I02.png
[I03]: media/azure-toolkit-for-eclipse-sign-in-instructions/I03.png
[I04]: media/azure-toolkit-for-eclipse-sign-in-instructions/I04.png
[I05]: media/azure-toolkit-for-eclipse-sign-in-instructions/I05.png

[browse-web-app]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app/browse-web-app.png
[file-new-dynamic-web-project]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app/file-new-dynamic-web-project.png
[dynamic-web-project-properties]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app/dynamic-web-project-properties.png
[create-new-jsp-file]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app/create-new-jsp-file.png
[new-jsp-file-dialog]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app/new-jsp-file-dialog.png
[select-jsp-template]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app/select-jsp-template.png
[publish-as-azure-web-app]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app/publish-as-azure-web-app.png
[deploy-web-app-dialog]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app/deploy-web-app-dialog.png
[select-app-service]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app/select-app-service.png
[create-app-service-dialog]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app/create-app-service-dialog.png
[publish-status]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app/publish-status.png
[create-app-service]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app/create-app-service.png
[deploy-app-service]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app/deploy-app-service.png
[manage-app-service]: ./media/azure-toolkit-for-eclipse-create-hello-world-web-app/manage-app-service.png
