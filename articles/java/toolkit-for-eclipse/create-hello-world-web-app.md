---
title: Criar um aplicativo Web Olá, Mundo para o Serviço de Aplicativo do Azure usando o Eclipse
description: Este tutorial mostra como usar o Kit de Ferramentas do Azure para Eclipse para criar um aplicativo Web Hello World para o Azure.
services: app-service
keywords: java, eclipse, aplicativo Web, serviço de aplicativo do azure, olá, mundo, início rápido
documentationcenter: java
author: selvasingh
ms.assetid: 20d41e88-9eab-462e-8ee3-89da71e7a33f
ms.reviewer: asirveda
ms.date: 08/25/2020
ms.service: app-service
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: web
ms.custom: devx-track-java
ms.openlocfilehash: 227e1a98bb14474b444a8d2cfb288b62ed5cf8ed
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831623"
---
# <a name="create-a-hello-world-web-app-for-azure-app-service-using-eclipse"></a>Criar um aplicativo Web Olá, Mundo para o Serviço de Aplicativo do Azure usando o Eclipse

Este artigo demonstra as etapas que são necessárias para criar um aplicativo Web Olá, Mundo básico e publicar seu aplicativo Web em um Serviço de Aplicativo do Azure usando o [Azure Toolkit for Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse).

> [!NOTE]
>
> Se preferir usar o IntelliJ IDEA, confira nosso [tutorial semelhante para o IntelliJ][intellij-hello-world].
>
>[!INCLUDE [quickstarts-free-trial-note](includes/quickstarts-free-trial-note.md)]
>
> Não se esqueça de limpar os recursos depois de concluir este tutorial. Nesse caso, executar este guia não excederá sua cota da conta gratuita.
>

[!INCLUDE [basic-prerequisites](includes/basic-prerequisites.md)]

## <a name="installation-and-sign-in"></a>Instalação e credenciais

As etapas a seguir guiarão você pelo processo de entrada no Azure no ambiente de desenvolvimento do Eclipse.

1. Se você ainda não instalou o plug-in, confira [Instalar o Azure Toolkit for Eclipse](installation.md).

1. Para entrar em sua conta do Azure, clique em **Ferramentas**, em **Azure** e então em **Entrar**.

   :::image type="content" source="media/sign-in-instructions/eclipse-azure-signin.png" alt-text="Entre no Azure no IDE do Eclipse.":::

1. Na janela **Entrar no Azure**, selecione **Logon do Dispositivo** e, em seguida, clique em **Entrar** ([outras opções de entrada](sign-in-instructions.md)).

1. Clique em **Copiar e Abrir** na caixa de diálogo **Logon no Dispositivo do Azure**.

1. No navegador, cole o código de dispositivo (que foi copiado quando você clicou em **Copiar e Abrir** na última etapa) e, em seguida, clique em **Avançar**.

1. Selecione sua conta do Azure e conclua os procedimentos de autenticação necessários para entrar.

1. Depois de entrar, feche o navegador e volte para o IDE do Eclipse. Na caixa de diálogo **Selecionar Assinaturas**, selecione as assinaturas que deseja usar e clique em **OK**.

### <a name="install-required-software-optional"></a>Instalar software necessário *(opcional)*

Para garantir que os componentes necessários funcionem com projetos de aplicativos Web, siga estas etapas:

1. Clique no menu **Ajuda** e em **Instalar Novo Software**.

1. Na caixa de diálogo **Software Disponível**, clique em **Gerenciar** e verifique se a versão mais recente do Eclipse está selecionada (por exemplo, *2020-06*).

1. Clique em **Aplicar e Fechar**. Expanda o menu suspenso *Trabalhar com:* para mostrar os sites sugeridos. Selecione o site de versão mais recente do Eclipse para consultar o software disponível.

1. Role a lista para baixo e selecione o item **Web, XML, Java EE e OSGi Enterprise Development**. Clique em **Avançar**.

1. Na janela Detalhes da Instalação, clique em **Avançar**.

1. Na caixa de diálogo Analisar Licença, analise os termos dos contratos de licença. Se você aceitar os termos dos contratos de licença, clique em **Aceito os termos dos contratos de licença** e clique em **Concluir**. 

   > [!NOTE]
   > Você pode verificar o progresso da instalação no canto inferior direito do workspace do Eclipse.

1. Se solicitado, reinicie o Eclipse para concluir a instalação e clique em **Reiniciar Agora**.

## <a name="creating-a-web-app-project"></a>Como criar um projeto de aplicativo Web

1. Clique em **Arquivo**, expanda **Novo** e clique em **...Projeto**. Na janela da caixa de diálogo Novo Projeto, expanda **Web**, selecione **Projeto Web Dinâmico** e clique em **Avançar**.

   > [!TIP]
   > Se você não vir **Web** listada como um projeto disponível, confira [esta seção](#install-required-software-optional) para verificar se você tem o software Eclipse necessário.

1. Para o objetivo deste tutorial, nomeie o projeto **MyWebApp**. Sua tela será semelhante à seguinte:
   
   ![Novas propriedades do Projeto Web Dinâmico][dynamic-web-project-properties]

1. Clique em **Concluir**.

1. No painel Gerenciador de Pacotes à esquerda, expanda **MyWebApp**. Clique com o botão direito do mouse em **WebContent**, focalize **Novo** e clique em **Outro...** .

1. Expanda **Web** para localizar a opção **Arquivo JSP**. Clique em **Avançar**.

1. Na caixa de diálogo **Novo Arquivo JSP**, nomeie o arquivo **index.jsp**, mantenha a pasta pai como **MyWebApp/WebContent** e clique em **Avançar**.

   ![Caixa de diálogo Novo Arquivo JSP][new-jsp-file-dialog]

1. Na caixa de diálogo **Selecionar Modelo JSP**, para a finalidade deste tutorial, escolha **Novo Arquivo JSP (html 5)** e clique em **Concluir**.

1. Quando o arquivo index.jsp for aberto no Eclipse, adicione o texto para exibir dinamicamente **Hello World!** dentro do elemento existente `<body>`. Seu conteúdo do `<body>` atualizado deve ser parecido com o exemplo a seguir:
   
   ```jsp
   <body>
   <b><% out.println("Hello World!"); %></b>
   </body>
   ```
1. Salve o index.jsp.

## <a name="deploying-the-web-app-to-azure"></a>Como implantar o aplicativo Web no Azure

1. No painel do Gerenciador de Pacotes à esquerda, clique com o botão direito do mouse em seu projeto, escolha **Azure** e **Publicar como Aplicativo Web do Azure**.
   
   ![Publicar como Aplicativo Web do Azure][publish-as-azure-web-app]

1. Quando a caixa de diálogo **Implantar Aplicativo Web**for exibida, você poderá escolher uma das seguintes opções:

   * Selecione um aplicativo Web existente se houver.

   * Se você não tiver um aplicativo Web, clique em **Criar**.

      Aqui você pode configurar o ambiente de runtime, o grupo de recursos do plano de serviço de aplicativo e as configurações do aplicativo. Crie recursos, se necessário.

      Especifique as informações necessárias para seu aplicativo Web na caixa de diálogo **Criar Serviço de Aplicativo** e clique em **Criar**.

1. Selecione seu aplicativo Web e clique em **Implantar**.

1. O kit de ferramentas exibirá um status **Publicado** na guia do **Log de Atividades do Azure** quando o aplicativo Web for implantado com êxito, que é um hiperlink para a URL do aplicativo Web implantado.

1. Você pode navegar até seu aplicativo Web usando o link fornecido na mensagem de status.

   ![Procurar seu aplicativo Web][browse-web-app]

## <a name="cleaning-up-resources"></a>Limpando recursos

1. Depois de publicar o aplicativo Web no Azure, você poderá gerenciá-lo clicando com o botão direito do mouse no Azure Explorer e selecionando uma das opções no menu de contexto. Por exemplo, você pode **Excluir** seu aplicativo Web aqui para limpar o recurso para este tutorial.

   ![Gerenciar o serviço de aplicativo][manage-app-service]

[!INCLUDE [show-azure-explorer](includes/show-azure-explorer.md)]

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [additional-resources](includes/additional-resources.md)]

Para obter mais informações sobre como criar aplicativos Web do Azure, confira a [Visão geral de Aplicativos Web].

<!-- URL List -->

[Azure Toolkit for Eclipse]: /azure/developer/java/tookit-for-eclipse
[Azure Toolkit for IntelliJ]: ../toolkit-for-intellij
[intellij-hello-world]: ../toolkit-for-intellij/create-hello-world-web-app.md
[Visão geral de Aplicativos Web]: /azure/app-service/app-service-web-overview
[Apache Tomcat]: http://tomcat.apache.org/
[Jetty]: http://www.eclipse.org/jetty/
[Legacy Version]: create-hello-world-web-app-legacy-version.md

<!-- IMG List -->

[browse-web-app]: media/create-hello-world-web-app/browse-web-app.png
[dynamic-web-project-properties]: media/create-hello-world-web-app/dynamic-web-project-properties.png
[new-jsp-file-dialog]: media/create-hello-world-web-app/new-jsp-file-dialog.png
[publish-as-azure-web-app]: media/create-hello-world-web-app/publish-as-azure-web-app.png
[publish-status]: media/create-hello-world-web-app/publish-status.png
[manage-app-service]: media/create-hello-world-web-app/manage-app-service.png