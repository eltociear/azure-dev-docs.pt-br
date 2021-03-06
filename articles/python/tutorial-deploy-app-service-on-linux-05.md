---
title: 'Etapa 5: Implantar um aplicativo Web do Python no Serviço de Aplicativo do Azure no Linux usando o VS Code'
description: Etapa 5 do tutorial, implantando o código do aplicativo Web
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: e7c600314f1535589ca15daaa3bbbd9ffdc69b9d
ms.sourcegitcommit: 815cf2acff71e849735f7afce54723f03ffa5df3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88501451"
---
# <a name="5-deploy-your-python-web-app-to-azure-app-service-on-linux"></a>5: Implantar o aplicativo Web Python no Serviço de Aplicativo do Azure no Linux

[Etapa anterior: configurar um arquivo de inicialização personalizado](tutorial-deploy-app-service-on-linux-04.md)

Use este procedimento para implantar o aplicativo Python em um Serviço de Aplicativo do Azure.

1. No Visual Studio Code, abra o explorador **Azure: Serviço de Aplicativo** e selecione a seta para cima azul:

   ![Implantar seu aplicativo Web no Serviço de Aplicativo no gerenciador do Serviço de Aplicativo](media/deploy-azure/deploy-web-app-to-app-service-in-app-service-explorer.png)

    Como alternativa, você pode clicar com o botão direito do mouse no nome do Serviço de Aplicativo e selecionar o comando **Implantar no Aplicativo Web**.

1. Nos prompts seguintes, forneça os seguintes detalhes:

    - Para "Selecionar a pasta a implantar", selecione a pasta do aplicativo atual.
    - Para "Selecionar Aplicativo Web", escolha o Serviço de Aplicativo criado na etapa anterior.

1. Enquanto o processo de implantação estiver em andamento, você poderá exibir o progresso na janela **Saída** do VS Code.

    ![Progresso da implantação na janela de saída do VS Code](media/deploy-azure/view-deployment-progress-in-visual-studio-code-output.png)

1. Quando a implantação for concluída após alguns minutos (dependendo de quantas dependências precisam ser instaladas), a mensagem abaixo será exibida. Selecione o botão **Procurar Site** para exibir o site em execução.

    ![Implantação concluída com o botão Procurar no site](media/deploy-azure/web-app-deployment-complete-with-browse-website-button.png)

    ![Aplicativo executado com êxito no Serviço de Aplicativo](media/deploy-azure/web-app-running-successfully-on-app-service.png)

1. Para verificar se os arquivos estão implantados, expanda o Serviço de Aplicativo no explorador **Azure: Serviço de Aplicativo** e, em seguida, expanda **Arquivos**:

    ![Verificando os arquivos de implantação no explorador do Serviço de Aplicativo](media/deploy-azure/expand-files-node-to-check-deployment-of-web-app-files.png)

    O Serviço de Aplicativo cria um ambiente virtual com suas dependências na pasta *antenv*. Se expandir esse nó, você poderá verificar que os pacotes nomeados em *requirements.txt* estão instalados em *antenv/lib/python3.7/site-packages*.

> [!div class="nextstepaction"]
> [Implantei meu aplicativo – prossiga para a etapa 6 >>>](tutorial-deploy-app-service-on-linux-06.md)

[Está com problemas? Fale conosco.](https://aka.ms/FlaskVSCQuickstartHelp)
