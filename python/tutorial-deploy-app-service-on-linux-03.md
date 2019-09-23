---
title: Criar o Serviço de Aplicativo no Visual Studio Code
description: Etapa 3 do tutorial, criando o Serviço de Aplicativo na extensão do VS Code.
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: kraigb
ms.openlocfilehash: 15e4e3b4ec5ab79110b1785335ce064cd90fd602
ms.sourcegitcommit: 74e28a479c87a3a53592646420b78e69852dd86a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/16/2019
ms.locfileid: "71019804"
---
# <a name="create-the-app-service"></a>Criar o Serviço de Aplicativo

[Etapa anterior: preparar seu aplicativo](tutorial-deploy-app-service-on-linux-01.md)

Nesta etapa, você cria a instância do Serviço de Aplicativo do Azure na qual implanta seu aplicativo. Você pode realizar essa etapa antes de implantar seu código, para que possa configurar um arquivo de inicialização personalizado, se necessário, na próxima etapa.

1. Na área **Azure: Serviço de Aplicativo**, no explorador, selecione o comando **+** para criar o Serviço de Aplicativo ou abra a Paleta de Comandos (**F1**) e selecione **Serviço de Aplicativo do Azure: Criar Aplicativo Web**. (Na terminologia do Serviço de Aplicativo, um "aplicativo Web" é um **host** para o código do aplicativo Web, não o próprio código do aplicativo.)

    ![Botão Criar Serviço de Aplicativo no explorador do Serviço de Aplicativo](media/deploy-azure/app-service-create-new.png)

1. Nos prompts seguintes:

    - Insira um nome para seu aplicativo, que deve ser globalmente exclusivo no Serviço de Aplicativo. Normalmente, você usa seu nome ou o nome da empresa seguido pelo nome do aplicativo.
    - Selecione **Python 3.7** como o tempo de execução.

1. Quando aparecer uma mensagem indicando que o novo Serviço de Aplicativo foi criado, selecione **Exibir Saída** para alternar para a janela **Saída** do VS Code. A saída mostra os nomes do grupo de recursos do Azure e do Plano do Serviço de Aplicativo que foram criados, juntamente com a URL do Serviço de Aplicativo.

    ![Mensagem que aparece após a criação do Serviço de Aplicativo](media/deploy-azure/app-service-created.png)

1. Para confirmar se o Serviço de Aplicativo está sendo executado corretamente, expanda sua assinatura no explorador **Azure: Serviço de Aplicativo**, clique com o botão direito do mouse no nome do Serviço de Aplicativo e selecione **Procurar Site**:

    ![Comando Procurar site em um Serviço de Aplicativo no explorador do Serviço de Aplicativo](media/deploy-azure/browse-website-command.png)

1. Como você ainda não implantou seu próprio código no Serviço de Aplicativo (isso será feito na próxima etapa), apenas um aplicativo padrão é exibido:

    ![Aplicativo Python padrão no Serviço de Aplicativo no Linux](media/deploy-azure/default-python-app.png)

## <a name="optional-upload-an-environment-variable-definitions-file"></a>(Opcional) Carregar um arquivo de definições de variáveis de ambiente

Se tiver um arquivo de definições de variáveis de ambiente, você poderá usá-lo para configurar o ambiente do Serviço de Aplicativo também. (Para saber mais sobre esses arquivos, que normalmente têm a extensão *.env*, consulte [Visual Studio Code – Ambientes de Python](https://code.visualstudio.com/docs/python/environments#environment-variable-definitions-file).)

1. Na área **Azure: Serviço de Aplicativo**, no explorador, expanda o nó do Serviço de Aplicativo desejado e, em seguida, clique com o botão direito do mouse no nó **Configurações do Aplicativo** e selecione **Carregar Configurações Locais**.

1. O VS Code solicita o local de seu arquivo *.env* e o carrega no Serviço de Aplicativo.

1. Quando o upload for concluído, você poderá expandir o nó **Configurações do Aplicativo** para ver os valores individuais. Você também pode exibi-los no portal do Azure navegando até o Serviço de Aplicativo e selecionando **Configuração**.

1. Se criar configurações diretamente no portal do Azure, você poderá salvá-las em um arquivo de definições clicando com o botão direito do mouse no nó **Configurações do Aplicativo** e selecionando **Baixar Configurações Remotas**. Esse processo garante que você tenha essas configurações em seu repositório, e não apenas no portal.

> [!div class="nextstepaction"]
> [Criei o Serviço de Aplicativo](tutorial-deploy-app-service-on-linux-04.md)

[Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-python&step=03-create-app-service)
