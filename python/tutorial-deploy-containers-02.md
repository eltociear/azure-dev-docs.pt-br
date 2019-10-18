---
title: 'Tutorial: Implantar uma imagem de contêiner no Serviço de Aplicativo do Azure com o Visual Studio Code'
description: Etapa 2 do tutorial, implantando a imagem real do Docker no Serviço de Aplicativo do Azure de um registro de contêiner.
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: kraigb
ms.custom: seo-python-october2019
ms.openlocfilehash: bb1797ef86d9a98661412ef555b4eb78a9282ad9
ms.sourcegitcommit: 6012460ad8d6ff112226b8f9ea6da397ef77712d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72278860"
---
# <a name="tutorial-deploy-a-container-image-to-azure-app-service"></a>Tutorial: Implantar uma imagem de contêiner no Serviço de Aplicativo do Azure

[Etapa anterior: pré-requisitos](tutorial-deploy-containers-01.md)

Com uma imagem de contêiner em um registro, você pode usar a extensão do Docker no VS Code para configurar facilmente um Serviço de Aplicativo do Azure que executa o contêiner.

1. No gerenciador do **Docker**, expanda **Registros**, expanda o nó de seu registro (como o **Azure**) e, em seguida, expanda o nó do nome da imagem até que você veja a imagem com a marca `:latest`.

    ![Localizar uma imagem no gerenciador do Docker](media/deploy-containers/find-image-to-deploy-in-docker-explorer.png)

1. Clique com o botão direito do mouse na imagem e selecione **Implantar Imagem no Serviço de Aplicativo do Azure**.

    ![Selecionar o item de menu Implantar Imagem no Serviço de Aplicativo do Azure](media/deploy-containers/deploy-image-to-azure-app-service-with-docker-explorer.png)

1. Siga os prompts para selecionar uma assinatura do Azure, selecione ou especifique um grupo de recursos, especifique uma região, configure um Plano do Serviço de Aplicativo (B1 é o menos caro) e especifique um nome para o site. A animação a seguir ilustra o processo.

    ![Criar e implantar a imagem no Serviço de Aplicativo do Azure](media/deploy-containers/deploy-image-to-azure-app-service.gif)

    Um **Grupo de recursos** é uma coleção nomeada dos diferentes recursos que compõem um aplicativo. Ao atribuir todos os recursos do aplicativo a um único grupo, você pode gerenciar facilmente esses recursos como uma unidade. (Para saber mais, confira [Visão geral do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) na documentação do Azure.)

    Um **Plano do Serviço de Aplicativo** define os recursos físicos (uma máquina virtual subjacente) que hospedam o contêiner em execução. Para este tutorial, B1 é o plano menos caro com suporte para contêineres do Docker. (Para saber mais, confira [Visão geral do plano do Serviço de Aplicativo](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview) na documentação do Azure.)

    O nome do Serviço de Aplicativo deve ser único em todo o Azure, de modo que normalmente você usaria um nome de empresa ou um nome pessoal. Para sites de produção, normalmente você configura o Serviço de Aplicativo com um nome de domínio registrado separadamente.

1. A criação do serviço de aplicativo leva alguns minutos e você vê o andamento no painel de Saída do VS Code.

1. Após a conclusão, você também **precisa** adicionar uma configuração chamada `WEBSITES_PORT` (observe que é usado "WEBSITES", no plural) ao Serviço de Aplicativo para especificar a porta em que o contêiner está escutando. (Se você estiver usando uma imagem do tutorial [Criar um contêiner de Python no VS Code](https://code.visualstudio.com/docs/python/tutorial-create-container), por exemplo, a porta será 5000 para Flask e 8000 para Django). Para definir `WEBSITES_PORT`, vá para o explorador do **Azure: Serviço de Aplicativo**, expanda o nó do novo Serviço de Aplicativo (atualize se necessário), clique com o botão direito do mouse em **Configurações do Aplicativo** e selecione **Adicionar Nova Configuração**. Nos prompts, insira `WEBSITES_PORT` como a chave e o número da porta para o valor.

    ![Adicionar Nova Configuração a um Serviço de Aplicativo que especifica uma porta](media/deploy-containers/add-new-setting-in-app-service-settings-explorer.png)

1. O Serviço de Aplicativo é reiniciado automaticamente quando você altera as configurações. Você também pode clicar com o botão direito do mouse no Serviço de Aplicativo e selecionar **Reiniciar** a qualquer momento.

1. Após o serviço ser reiniciado, navegue até o site em `http://<name>.azurewebsites.net`. Você pode usar **Ctrl**+ clique (ou **Cmd** + clique no macOS) na URL no painel de Saída ou clicar com o botão direito do mouse no Serviço de Aplicativo no explorador **Azure: Serviço de Aplicativo** e selecione **Procurar Site**.

> [!div class="nextstepaction"]
> [Implantei a imagem](tutorial-deploy-containers-03.md)

[Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-containers&step=02-deploy-container)
