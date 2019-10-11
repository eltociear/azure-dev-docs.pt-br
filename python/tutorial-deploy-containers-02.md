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
ms.openlocfilehash: a099e25134615fc05508cde3c0f128a3be813402
ms.sourcegitcommit: bed07b313eeab51281d1a6d4eba67a75524b2f57
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72172270"
---
# <a name="tutorial-deploy-a-container-image-to-azure-app-service"></a>Tutorial: Implantar uma imagem de contêiner no Serviço de Aplicativo do Azure

[Etapa anterior: pré-requisitos](tutorial-deploy-containers-01.md)

Com uma imagem de contêiner em um registro, você pode usar a extensão do Docker no VS Code para configurar facilmente um Serviço de Aplicativo do Azure que executa o contêiner.

1. No gerenciador do **Docker**, expanda **Registros**, expanda o nó de seu registro (como o **Azure**) e, em seguida, expanda o nó do nome da imagem até que você veja a imagem com a marca `:latest`.

    ![Localizando uma imagem no gerenciador do Docker](media/deploy-containers/deploy-find-image.png)

1. Clique com o botão direito do mouse na imagem e selecione **Implantar Imagem no Serviço de Aplicativo do Azure**.

    ![Selecionando o comando de menu Implantar](media/deploy-containers/deploy-menu.png)

1. Siga os prompts para selecionar uma assinatura do Azure, selecione ou especifique um grupo de recursos, especifique uma região, configure um Plano do Serviço de Aplicativo (B1 é o menos caro) e especifique um nome para o site. A animação a seguir ilustra o processo.

    ![Animação de Criar e Implantar](media/deploy-containers/deploy-to-app-service.gif)

    Um **Grupo de recursos** é uma coleção nomeada dos diferentes recursos que compõem um aplicativo. Ao atribuir todos os recursos do aplicativo a um único grupo, você pode gerenciar facilmente esses recursos como uma unidade. (Para saber mais, confira [Visão geral do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) na documentação do Azure.)

    Um **Plano do Serviço de Aplicativo** define os recursos físicos (uma máquina virtual subjacente) que hospedam o contêiner em execução. Para este tutorial, B1 é o plano menos caro com suporte para contêineres do Docker. (Para saber mais, confira [Visão geral do plano do Serviço de Aplicativo](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview) na documentação do Azure.)

    O nome do Serviço de Aplicativo deve ser único em todo o Azure, de modo que normalmente você usaria um nome de empresa ou um nome pessoal. Para sites de produção, normalmente você configura o Serviço de Aplicativo com um nome de domínio registrado separadamente.

1. A criação do serviço de aplicativo leva alguns minutos e você vê o andamento no painel de Saída do VS Code.

1. Após a conclusão, você também **precisa** adicionar uma configuração chamada `WEBSITES_PORT` (observe que é usado "WEBSITES", no plural) ao Serviço de Aplicativo para especificar a porta em que o contêiner está escutando. (Se você estiver usando uma imagem do tutorial [Criar um contêiner de Python no VS Code](https://code.visualstudio.com/docs/python/tutorial-create-container), por exemplo, a porta será 5000 para Flask e 8000 para Django). Para definir `WEBSITES_PORT`, vá para o explorador do **Azure: Serviço de Aplicativo**, expanda o nó do novo Serviço de Aplicativo (atualize se necessário), clique com o botão direito do mouse em **Configurações do Aplicativo** e selecione **Adicionar Nova Configuração**. Nos prompts, insira `WEBSITES_PORT` como a chave e o número da porta para o valor.

    ![Comando do menu de contexto em um Serviço de Aplicativo para Adicionar Nova Configuração](media/deploy-containers/add-app-service-setting.png)

1. O Serviço de Aplicativo é reiniciado automaticamente quando você altera as configurações. Você também pode clicar com o botão direito do mouse no Serviço de Aplicativo e selecionar **Reiniciar** a qualquer momento.

1. Após o serviço ser reiniciado, navegue até o site em `http://<name>.azurewebsites.net`. Você pode usar **Ctrl**+ clique (ou **Cmd** + clique no macOS) na URL no painel de Saída ou clicar com o botão direito do mouse no Serviço de Aplicativo no explorador **Azure: Serviço de Aplicativo** e selecione **Procurar Site**.

> [!div class="nextstepaction"]
> [Implantei a imagem](tutorial-deploy-containers-03.md)

[Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-containers&step=02-deploy-container)
