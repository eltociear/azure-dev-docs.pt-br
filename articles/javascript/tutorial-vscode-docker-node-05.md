---
title: Implantar uma imagem de contêiner para um aplicativo Node.js usando o Visual Studio Code
description: Parte 5 do tutorial, implantar a imagem no Serviço de Aplicativo do Azure
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 487110258ed3302e781cfa24a5ae9f518ebb3bda
ms.sourcegitcommit: 553da4e9aa988e5bb823364244ea81961cee5bc7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85791294"
---
# <a name="deploy-the-image-to-azure-app-service"></a>Implantar a imagem no Serviço de Aplicativo do Azure

[Etapa anterior: Criar a imagem do aplicativo](tutorial-vscode-docker-node-04.md)

Nesta etapa, você implanta a imagem enviada por push a um Registro para o [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/) diretamente do Visual Studio Code.

## <a name="enable-admin-access-on-the-registry"></a>Habilitar o Acesso de administrador no registro

Para implantar a imagem em um aplicativo Web, você precisa habilitar o acesso de "Administrador" no registro no portal do Azure.

1. No gerenciador do **Docker**, clique com o botão direito do mouse no nome do registro e selecione "Abrir no portal". 

    ![Comando Abrir no portal do VS Code](media/deploy-containers/open-in-portal.png)

    Isso abrirá o registro no portal do Azure.

1. Clique em "Chaves de acesso" na barra lateral e, em seguida, alterne a configuração "Usuário administrador" para "Habilitado".  
    
    ![Habilitar a configuração de usuário administrador no portal do Azure](media/deploy-containers/access-keys.png)

## <a name="deploy-image"></a>Implantar a imagem

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
> [Meu site está no Azure](tutorial-vscode-docker-node-06.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=docker-extension&step=deploy-app)
