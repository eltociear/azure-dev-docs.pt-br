---
title: Criar sua primeira função no Azure usando o IntelliJ IDEA
description: Crie e publique uma função disparada por HTTP simples no Azure usando o Azure Toolkit for IntelliJ.
ms.topic: quickstart
ms.date: 03/26/2020
ms.openlocfilehash: b8d8ae4093c03e3fb0e910a0b5dd21002b0e52ab
ms.sourcegitcommit: 7da78b35a847db9929554962dfcc47860f472fb9
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85133709"
---
# <a name="quickstart-create-an-azure-functions-project-using-intellij-idea"></a>Início Rápido: Criar um projeto do Azure Functions usando o IntelliJ IDEA

Neste artigo, você usará o IntelliJ IDEA para criar uma função que responde a solicitações HTTP. Após testar o código localmente, implante-o no ambiente sem servidor do Azure Functions. A realização deste início rápido gera um pequeno custo de alguns centavos de dólar ou menos em sua conta do Azure.

## <a name="configure-your-environment"></a>Configurar seu ambiente

Antes de começar, verifique se você tem os seguintes requisitos implementados:

+ Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
+ Um [JDK (Kit de desenvolvimento Java) compatível com o Azure](https://aka.ms/azure-jdks) para Java 8
+ Um [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate Edition ou Community Edition instalado
+ [Maven 3.5.0+](https://maven.apache.org/download.cgi)
+ Versão mais recente do [Functions Core Tools](https://github.com/Azure/azure-functions-core-tools)

## <a name="installation-and-sign-in"></a>Instalação e credenciais

1. Na caixa de diálogo Configurações/Preferências do IntelliJ IDEA (Ctrl+Alt+S), selecione **Plug-ins**. Em seguida, localize o **Azure Toolkit for IntelliJ** no **Marketplace** e clique em **Instalar**. Depois de instalado, clique em **Reiniciar** para ativar o plug-in. 

   ![Plug-in do Azure Toolkit for IntelliJ no Marketplace][marketplace]

2. Para entrar sua conta do Azure, abra a barra lateral **Azure Explorer** e, em seguida, clique no ícone **Entrar no Azure** na barra na parte superior (ou no menu IDEA **Ferramentas/Azure/Entrada no Azure**).

   ![O comando de Entrada do IntelliJ Azure][I01]

3. Na janela **Entrar no Azure**, selecione **Logon do Dispositivo** e, em seguida, clique em **Entrar** ([outras opções de entrada](sign-in-instructions.md)).

   ![A janela Entrar no Azure com o logon no dispositivo selecionado][I02]

4. Clique em **Copiar e Abrir** na caixa de diálogo **Logon no Dispositivo do Azure**.

   ![A janela da caixa de diálogo Logon no Azure][I03]

5. No navegador, cole o código de dispositivo (que foi copiado quando você clicou em **Copiar e Abrir** na última etapa) e, em seguida, clique em **Avançar**.

   ![O navegador de logon do dispositivo][I04]

6. Na caixa de diálogo **Selecionar Assinaturas**, selecione as assinaturas que deseja usar e, em seguida, clique em **OK**.

   ![A caixa de diálogo Selecionar Assinaturas][I05]

## <a name="create-your-local-project"></a>Criar seu projeto local

Nesta seção, você usará o Azure Toolkit for IntelliJ para criar um projeto local do Azure Functions. Mais adiante neste artigo, você publicará o código de função no Azure. 

1. Abra a caixa de diálogo Bem-vindo do IntelliJ, selecione *Criar Projeto* para abrir um assistente de Novo projeto e selecione *Azure Functions*.

    ![Criar um projeto do Functions](media/quickstart-functions/create-functions-project.png)

1. Selecione *Gatilho Http*, clique em *Avançar* e siga o assistente para percorrer todas as configurações nas páginas a seguir, depois, confirme a localização do projeto e clique em *Concluir*. O Intellj IDEA abrirá o novo projeto.

    ![Conclusão da criação de um projeto do Functions](media/quickstart-functions/create-functions-project-finish.png)

## <a name="run-the-function-app-locally"></a>Executar o aplicativo de funções localmente

1. Navegue até `src/main/java/org/example/functions/HttpTriggerFunction.java` para ver o código gerado. Ao lado da linha *17*, você observará a presença de um botão *Executar* verde. Clique nele e selecione *Executar 'azure-function-exam...'* , você verá que seu aplicativo de funções está sendo executado localmente com alguns logs.

    ![Execução local de projeto do Functions](media/quickstart-functions/local-run-functions-project.png)

    ![Execução local de saída do Functions](media/quickstart-functions/local-run-functions-output.png)

1. Você pode experimentar a função usando o navegador para acessar o ponto de extremidade impresso, como `http://localhost:7071/api/HttpTrigger-Java?name=Azure`.

    ![Execução local de resultado do teste do Functions](media/quickstart-functions/local-run-functions-test.png)

1. O log também é impresso no IDEA. Agora, interrompa a função clicando no botão *parar*.

    ![Execução local de log de teste do Functions](media/quickstart-functions/local-run-functions-log.png)

## <a name="debug-the-function-app-locally"></a>Depurar o aplicativo de funções localmente

1. Agora, vamos tentar depurar o aplicativo de funções localmente, clicar no botão *Depurar* na barra de ferramentas (se você não a vir, clique em *Exibir -> Aparência -> Barra de Ferramentas* para habilitar a barra de ferramentas).

    ![Botão de funções de depuração local](media/quickstart-functions/local-debug-functions-button.png)

1. Clique na linha *20* do arquivo `src/main/java/org/example/functions/HttpTriggerFunction.java` para adicionar um ponto de interrupção e acesse o ponto de extremidade `http://localhost:7071/api/HttpTrigger-Java?name=Azure` novamente. Você verá que o ponto de interrupção foi atingido e poderá tentar mais recursos de depuração, como *step*, *watch* e *evaluation*. Interrompa a sessão de depuração clicando no botão parar.

    ![Interrupção de funções de depuração local](media/quickstart-functions/local-debug-functions-break.png)

## <a name="deploy-your-function-app-to-azure"></a>Implante o aplicativo de funções no Azure

1. Clique com o botão direito do mouse no seu projeto no Explorador de Projeto do IntelliJ e selecione *Azure -> Implantar no Azure Functions*

    ![Implantar funções no Azure](media/quickstart-functions/deploy-functions-to-azure.png)

1. Se você ainda não tiver nenhum aplicativo de funções, clique em *Nenhuma função disponível, clique para criar uma*.

    ![Implantar funções no Azure criar aplicativo](media/quickstart-functions/deploy-functions-create-app.png)

1. Digite o nome do aplicativo de funções e escolha o plano do Serviço de Aplicativo/assinatura/plataforma/grupo de recursos adequado. Você também pode criar um grupo de recursos/plano do Serviço de Aplicativo aqui. Em seguida, mantenha as configurações do aplicativo inalteradas, clique em *OK* e aguarde alguns minutos para que a função seja criada. Após a mensagem *Criando Aplicativo de Funções...* , a barra de progresso desaparece.

    ![Implantar funções no Azure – assistente de criação de aplicativo](media/quickstart-functions/deploy-functions-create-app-wizard.png)

1. Selecione o aplicativo de funções para o qual você deseja implantar (o novo aplicativo de funções que você acabou de criar será selecionado automaticamente). Clique em *Executar* para implantar as funções.

    ![Implantar funções para execução no Azure](media/quickstart-functions/deploy-functions-run.png)

    ![Implantar funções para registro em log no Azure](media/quickstart-functions/deploy-functions-log.png)

## <a name="manage-azure-functions-from-idea"></a>Gerenciar Azure Functions por meio do IDEA

1. Você pode gerenciar suas funções com o *Azure Explorer*. No IDEA, clique em *Aplicativo de Funções*: você verá todas as suas funções aqui.

    ![Exibir funções no Explorer](media/quickstart-functions/explorer-view-functions.png)

1. Clique para selecionar em uma de suas funções e clique com o botão direito do mouse, selecione *Mostrar Propriedades* para abrir a página de detalhes. 

    ![Mostrar propriedades de funções](media/quickstart-functions/explorer-functions-show-properties.png)

1. Clique com o botão direito do mouse em sua função *HttpTrigger-Java* e selecione *Disparar Função*, você verá que o navegador está aberto com a URL do gatilho.

    ![Implantar funções para execução no Azure](media/quickstart-functions/explorer-trigger-functions.png)

## <a name="add-more-functions-to-the-project"></a>Adicionar mais funções ao projeto

1. Clique com o botão direito do mouse no pacote *org.example.functions* e selecione *Nova -> Classe de Função do Azure*. 

    ![Adicionar funções à entrada do projeto](media/quickstart-functions/add-functions-entry.png)

1. Preencha o nome de classe *HttpTest*, selecione *HttpTrigger* no assistente para criar classe de função e clique em *OK* para criar. Dessa forma, você pode criar funções como desejar.

    ![Adicionar funções ao projeto – selecionar gatilho](media/quickstart-functions/add-functions-trigger.png)
    
    ![Adicionar funções à saída do projeto](media/quickstart-functions/add-functions-output.png)

## <a name="cleaning-up-functions"></a>Limpar funções

1. Excluir o Azure Functions no Azure Explorer
      
      ![Adicionar funções ao projeto – selecionar gatilho](media/quickstart-functions/delete-function.png)
      

## <a name="next-steps"></a>Próximas etapas

Você criou um projeto de funções Java com uma função disparada por HTTP, executou-o no computador local e implantou-o no Azure. Agora, estenda sua função por meio da...

> [!div class="nextstepaction"]
> [Adição de uma associação de saída de fila do Armazenamento do Azure](/azure/azure-functions/functions-add-output-binding-storage-queue-java)


[marketplace]:./media/create-hello-world-web-app/marketplace.png
[I01]: media/sign-in-instructions/I01.png
[I02]: media/sign-in-instructions/I02.png
[I03]: media/sign-in-instructions/I03.png
[I04]: media/sign-in-instructions/I04.png
[I05]: media/sign-in-instructions/I05.png
