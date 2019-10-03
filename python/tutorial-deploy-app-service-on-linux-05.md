---
title: Implantar um aplicativo Web do Python no Serviço de Aplicativo do Azure no Linux usando o VS Code
description: Etapa 5 do tutorial, implantando o código do aplicativo Web
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: kraigb
ms.openlocfilehash: e9b85928bd2b1308ab57747d7b0c32c085274cde
ms.sourcegitcommit: 74e28a479c87a3a53592646420b78e69852dd86a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/16/2019
ms.locfileid: "71019674"
---
# <a name="deploy-your-app"></a>Implantar seu aplicativo

[Etapa anterior: configurar um arquivo de inicialização personalizado](tutorial-deploy-app-service-on-linux-04.md)

1. No Visual Studio Code, abra o explorador **Azure: Serviço de Aplicativo** e selecione a seta para cima azul:

   ![Comando Implantar no aplicativo Web](media/deploy-azure/deploy-to-web-app-command.png)

    Como alternativa, você pode clicar com o botão direito do mouse no nome do Serviço de Aplicativo e selecionar o comando **Implantar no Aplicativo Web**.

1. Nos prompts seguintes, forneça os seguintes detalhes:

    - Para "Selecionar a pasta a implantar", selecione a pasta do aplicativo atual.
    - Para "Selecionar Aplicativo Web", escolha o Serviço de Aplicativo criado na etapa anterior.

1. Enquanto o processo de implantação estiver em andamento, você poderá exibir o progresso na janela **Saída** do VS Code.

    ![Progresso da implantação na janela de saída do VS Code](media/deploy-azure/deployment-progress.png)

1. Quando a implantação for concluída após alguns minutos (dependendo de quantas dependências precisam ser instaladas), a mensagem abaixo será exibida. Selecione o botão **Procurar Site** para exibir o site em execução.

    ![Mensagem de conclusão da implantação](media/deploy-azure/deployment-complete.png)

    ![Aplicativo executado com êxito no Serviço de Aplicativo](media/deploy-azure/running-app.png)

1. Para verificar se os arquivos estão implantados, expanda o Serviço de Aplicativo no explorador **Azure: Serviço de Aplicativo** e, em seguida, expanda **Arquivos**:

    ![Verificando os arquivos de implantação no explorador do Serviço de Aplicativo](media/deploy-azure/expand-files-node.png)

    O Serviço de Aplicativo cria um ambiente virtual com suas dependências na pasta *antenv*. Se expandir esse nó, você poderá verificar que os pacotes nomeados em *requirements.txt* estão instalados em *antenv/lib/python3.7/site-packages*.

> [!div class="nextstepaction"]
> [Implantei meu aplicativo](tutorial-deploy-app-service-on-linux-06.md)

[Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-python&step=05-deploy-app)