---
title: Executar o aplicativo Azure Functions localmente no Visual Studio Code
description: Parte 3 do tutorial, executar o aplicativo localmente para testá-lo.
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 2a7cb5e5c433c90d74cd3b7771ce90529f617fcb
ms.sourcegitcommit: e77f8f652128b798dbf972078a7b460ed21fb5f8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74466574"
---
# <a name="test-the-function-locally"></a>Testar a função localmente

[Etapa anterior: Criar o aplicativo Functions](tutorial-vscode-serverless-node-02.md)

Nesta etapa, você executa o projeto Azure Functions localmente para testá-lo antes de implantar no Azure.

Quando você criou o aplicativo Functions, a extensão do Azure Functions adicionou automaticamente uma configuração de inicialização do VS Code a seu projeto, que é encontrada no arquivo *.vscode/launch.json*. Essa configuração usa o mesmo runtime que é executado no Azure, assim, você pode ter certeza de que o código-fonte funciona antes da implantação na nuvem.

1. No Visual Studio Code, pressione **F5** (ou use o comando de menu **Depurar** > **Iniciar Depuração**) para iniciar o depurador e anexá-lo ao host do Azure Functions. (Esse comando usa automaticamente a configuração de depuração única que o Azure Functions criou.)

1. A saída das ferramentas Functions Core aparece no painel do **Terminal** do VS Code. Depois que o host for iniciado, use **Ctrl**+clique na URL local mostrada na saída para abrir o navegador e executar a função:

    ![Saída mostrada no painel do Terminal do VS Code ao depurar localmente](media/functions-extension/local-test-output.png)

1. O código criado pelo modelo de gatilho HTTP padrão analisa um parâmetro de consulta `name` para personalizar a resposta. No navegador, adicione `?name=<yourname>` à URL no navegador para ver a resposta produzida corretamente:

    ![Parâmetros da URL de análise da função de gatilho HTTP](media/functions-extension/local-test-browser.png)

1. Com sua função em execução localmente, você pode definir pontos de interrupção em diferentes partes do código. (Para saber mais sobre pontos de interrupção e depuração no VS Code, confira [Depuração](https://code.visualstudio.com/docs/editor/debugging).) Abra *index.js* e clique na margem à esquerda da linha 11 na janela do editor. Um pequeno ponto vermelho é exibido para indicar um ponto de interrupção. Agora, remova o argumento `?name=` da URL no navegador. Quando o navegador faz essa solicitação, o VS Code interrompe o código de função naquele ponto de interrupção:

    ![VS Code parado em um ponto de interrupção](media/functions-extension/debugging-breakpoint.png)

> [!div class="nextstepaction"]
> [Executei o aplicativo de Funções localmente](tutorial-vscode-serverless-node-04.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azurefunctions&step=run-app)
