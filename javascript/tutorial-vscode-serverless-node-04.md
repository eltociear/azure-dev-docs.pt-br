---
title: Implantar o aplicativo Azure Functions usando o Visual Studio Code
description: Parte 4 do tutorial, implantar o aplicativo Functions na nuvem.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 8cb80b25e224d6f4552ec165779cda35f990ad6a
ms.sourcegitcommit: 44d1abfb836f90b8731d7ea5d5a5af09245b2b89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2020
ms.locfileid: "77422529"
---
# <a name="deploy-the-functions-app"></a>Implantar o aplicativo do Functions

[Etapa anterior: Testar a função localmente](tutorial-vscode-serverless-node-03.md)

1. No VS Code, selecione o logotipo do Azure para abrir o **Gerenciador do Azure** e, em **Functions**, selecione a seta azul para cima para implantar seu aplicativo:

    ![Comando Implantar no Azure Functions](media/functions-extension/deploy-app.png)

    Como alternativa, você pode implantar abrindo a **Paleta de Comandos** (**F1**), inserindo 'implantar para o aplicativo de funções' e executando o comando **Azure Functions: Implantar no Aplicativo de Funções**.

1. No prompt **Selecione o Aplicativo de Funções no Azure**, escolha **Criar novo aplicativo de Funções no Azure**.

1. No próximo prompt, insira um nome globalmente exclusivo para seu Aplicativo de Funções e pressione **Enter**. Os caracteres válidos para um nome de aplicativo de funções são "a-z", "0-9" e "-".

1. Escolha a versão/o runtime do Node.js

    ![Painel de saída do VS Code mostrando a versão/o runtime do Node.js](media/functions-extension/nodejs-runtime-version.png)

1. No próximo prompt, selecione uma [região](https://azure.microsoft.com/regions/) do Azure perto de você.

1. O painel **Saída** do VS Code para o **Azure Functions** mostra o progresso:

    ![Painel de saída do VS Code mostrando o progresso da implantação](media/functions-extension/deploy-progress.png)

1. Depois que a implantação for concluída, vá para o gerenciador do **Azure Functions**, expanda o nó da sua assinatura do Azure, expanda o nó para seu aplicativo Functions e, em seguida, expanda **Functions (somente leitura)** . Clique com o botão direito do mouse no nome da função e selecione **Copiar URL da Função**:

    ![Comando Copiar URL da função](media/functions-extension/copy-function-url-command.png)

1. Cole a URL em um navegador e acrescente um argumento `?name=<yourname>`. O navegador então deve mostrar a função em execução na nuvem:

    ![Função em execução na nuvem](media/functions-extension/remote-test-browser.png)

1. Se desejar, faça algumas alterações ao código de função em *index.js* ou adicione mais funções com outros gatilhos. Depois de testar localmente, reimplante o código como nas etapas anteriores para testar essas alterações na nuvem.

    > [!TIP]
    > Durante a implantação, todo o aplicativo Functions é implantado para que as alterações a todas as Funções individuais sejam implantadas de uma só vez.

> [!div class="nextstepaction"]
> [Implantei o aplicativo de funções](tutorial-vscode-serverless-node-05.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azurefunctions&step=deploy-app)
