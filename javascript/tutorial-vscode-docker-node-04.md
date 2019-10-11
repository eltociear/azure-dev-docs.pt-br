---
title: Implantar uma imagem de contêiner para um aplicativo Node.js usando o Visual Studio Code
description: Parte 4 do tutorial, implantar a imagem no Serviço de Aplicativo do Azure
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: kraigb
ms.openlocfilehash: 60eb5be0b3d4049c7955195f3bb6bc85dd2b2498
ms.sourcegitcommit: c04984b6367e922dbc5973af44f8cd0ca81ce157
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71686021"
---
# <a name="deploy-the-image-to-azure-app-service"></a>Implantar a imagem no Serviço de Aplicativo do Azure

[Etapa anterior: Criar a imagem do aplicativo](tutorial-vscode-docker-node-03.md)

Nesta etapa, você implanta a imagem enviada por push a um Registro para o [Serviço de Aplicativo do Azure](https://azure.microsoft.com/en-us/services/app-service/) diretamente do Visual Studio Code.

1. No Gerenciador de **DOCKER**, expanda os nós da imagem em **Registros**, clique com o botão direito do mouse em `:latest` e selecione a imagem **Implantar Imagem para o Serviço de Aplicativo do Azure**.

    ![Implantar do Gerenciador](media/deploy-containers/deploy-image-command.png)

1. Quando solicitado, forneça valores para o Serviço de Aplicativo:

    - O nome deve ser exclusivo em todo o Azure.
    - Selecione um grupo de recursos ou crie um. (Um **Grupo de Recursos** é, essencialmente, uma coleção nomeada dos recursos de um aplicativo no Azure.)
    - Selecione um Plano de Serviço de Aplicativo ou crie um. (Um **Plano de Serviço de Aplicativo** define os recursos físicos que hospedam o site. Você pode usar uma camada de plano básica ou gratuita para este tutorial.)

1. Quando a implantação for concluída, o Visual Studio Code mostrará uma notificação com a URL do site:

    ![Mensagem de implantação bem-sucedida](media/deploy-containers/deploy-successful.png)

1. Você também pode ver os resultados no painel **Saída** do Visual Studio Code na seção do **Docker**:

    ![Saída de implantação bem-sucedida](media/deploy-containers/deploy-output.png)

1. Para procurar o site implantado, você pode usar **Ctrl**+**Clique** na URL no painel **Saída**. O novo Serviço de Aplicativo também aparece no gerenciador do **AZURE** no Visual Studio Code na seção **SERVIÇO DE APLICATIVO**, em que você pode clicar com o botão direito do mouse no site e selecionar **Navegar no Site**.

> [!div class="nextstepaction"]
> [Meu site está no Azure](tutorial-vscode-docker-node-05.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=docker-extension&step=deploy-app)
