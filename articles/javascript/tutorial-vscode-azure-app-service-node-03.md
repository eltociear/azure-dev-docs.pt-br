---
title: Implantar aplicativos no Serviço de Aplicativo do Azure por meio do Visual Studio Code
description: Parte 3 do tutorial, implantar o site
ms.topic: conceptual
ms.date: 03/04/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: 5d812f9aa6efb308cafcb5d3e3ccb1ce852e93f1
ms.sourcegitcommit: 815cf2acff71e849735f7afce54723f03ffa5df3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88501501"
---
# <a name="deploy-the-app-to-azure"></a>Implantar o aplicativo no Azure

[Etapa anterior: Criar o aplicativo](tutorial-vscode-azure-app-service-node-02.md)

Nesta etapa, você implantará seu aplicativo Node.js no Azure usando a implantação do Git por meio do VS Code e da extensão do Serviço de Aplicativo do Azure. Para atingir essa meta, primeiro, inicialize um repositório Git local e crie um aplicativo Web no Azure. Depois, configure o VS Code para usar a implantação do Git.

1. No terminal, verifique se você está na pasta *expressApp1* e inicie o Visual Studio Code com o seguinte comando:

    ```bash
    code .
    ```

1. No VS Code, selecione o ícone do controle do código-fonte para abrir o **Source Control Explorer** e escolha **+** para inicializar um repositório Git local:

    ![Inicializar repositório git](media/deploy-azure/git-init.png)

1. Nos prompts, escolha *expressApp1* como pasta do workspace.

1. Depois que o repositório for inicializado, insira a mensagem "Confirmação inicial" e selecione a marca de seleção para criar a confirmação inicial de seus arquivos de origem.

    ![Concluir uma confirmação inicial para o repositório](media/deploy-azure/initial-commit.png)

1. Na paleta de comandos (**Ctrl**+**Shift**+**P**), digite "criar Web" e selecione **Serviço de Aplicativo do Azure: Criar Aplicativo Web...Avançado**. Use o comando avançado para ter controle completo sobre a implantação, incluindo o grupo de recursos, o Plano do Serviço de Aplicativo e o sistema operacional em vez de usar padrões do Linux.

1. Responda aos prompts da seguinte maneira:

    - Selecionar sua conta de **Assinatura**.
    - Em **Inserir um nome globalmente exclusivo**, insira um nome que seja exclusivo em todo o Azure. Use apenas caracteres alfanuméricos ("A-Z", "a-z" e "0-9") e hifens ("-")
    - Selecione **Criar grupo de recursos** e forneça um nome como `AppServiceTutorial-rg`.
    - Selecione um sistema operacional (Windows ou Linux)
    - Somente Linux: selecione uma versão do Node.js. (Para Windows, você define a versão usando uma configuração de aplicativo mais tarde).
    - Selecione **Criar um plano do Serviço de Aplicativo**, forneça um nome como `AppServiceTutorial-plan` e selecione o tipo de preço **F1 Gratuito**.
    - Selecione **Ignorar por enquanto** para o recurso do Application Insights.
    - Selecione uma localização próxima de você.

1. Após um curto período, o VS Code notificará que a criação foi concluída. Feche a notificação com o botão **X**:

    ![Notificação sobre a conclusão da criação do aplicativo Web](media/deploy-azure/creation-complete.png)

1. Com o aplicativo Web implementado, instrua o VS Code a implantar seu código por meio do repositório Git local. Selecione o ícone do Azure para abrir o gerenciador do **Serviço de Aplicativo do Azure**, expanda seu nó de assinatura, clique com o botão direito do mouse no nome do aplicativo Web recém-criado e selecione **Configurar a Fonte da Implantação**.

    ![Configurar um comando de origem de implantação em um aplicativo Web](media/deploy-azure/configure-deployment-source.png)

1. Quando solicitado, selecione **LocalGit**.

1. Se estiver implantando no Serviço de Aplicativo no Windows, você precisará criar duas configurações antes da implantação:

    1. No VS Code, expanda o nó do novo serviço de aplicativo, clique com o botão direito do mouse em **Configurações do Aplicativo** e selecione **Adicionar Nova Configuração**:

        ![Comando Adicionar configuração de aplicativo](media/deploy-azure/add-setting.png)

    1. Insira `WEBSITE_NODE_DEFAULT_VERSION` para a chave de configuração e `10.15.2` para o valor da configuração. Essa configuração define a versão do Node.js.
    1. Repita o processo para criar uma chave para `SCM_DO_BUILD_DURING_DEPLOYMENT` com o valor `1`. Essa configuração força o servidor a executar `npm install` após a implantação.
    1. Expanda o nó **Configurações de Aplicativo** para verificar se as configurações foram implementadas.

1. Selecione o ícone de seta azul para cima para implantar seu código no Azure:

    ![Ícone Implantar no Aplicativo Web](media/deploy-azure/deploy.png)

1. Nos prompts, selecione a pasta *expressApp1* e, em seguida, selecione a conta de **assinatura** novamente e o nome do aplicativo Web criado anteriormente.

1. Ao implantar no Linux, selecione **Sim** quando solicitado a atualizar sua configuração para executar `npm install` no servidor de destino.

    ![Prompt para atualizar a configuração no servidor Linux de destino](media/deploy-azure/server-build.png)

1. Para Linux e Windows, selecione **Sim** quando solicitado com **Sempre implantar o workspace "nodejs-docs-hello-world" em (nome do aplicativo)"** . Selecionar **Sim** informa o VS Code para direcionar automaticamente o mesmo aplicativo Web do Serviço de Aplicativo com implantações subsequentes.

1. Quando a implantação for concluída, selecione **Procurar no Site** no prompt para exibir seu aplicativo Web implantado recentemente. O navegador deve exibir “Olá, Mundo!”

1. (Opcional): você pode fazer alterações em seus arquivos de código e usar o botão de implantação novamente para atualizar o aplicativo Web.

> [!div class="nextstepaction"]
> [Meu site está no Azure](tutorial-vscode-azure-app-service-node-04.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azureappservice&step=deploy-app)
