---
title: Criar o aplicativo Azure Functions usando o Visual Studio Code
description: Parte 2 do tutorial, criar o aplicativo Azure Functions
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 5b2e46cde8740020cc2ad7a1b50ac9b4687d17d3
ms.sourcegitcommit: e77f8f652128b798dbf972078a7b460ed21fb5f8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74467118"
---
# <a name="create-the-local-functions-app"></a>Criar o aplicativo Functions local

[Etapa anterior: Introdução e pré-requisitos](tutorial-vscode-serverless-node-01.md)

Nesta etapa, você cria um aplicativo Azure Functions local contendo uma função que usa um gatilho HTTP. Um aplicativo Azure Functions pode conter muitas Funções com diferentes gatilhos. O gatilho HTTP processa especificamente o tráfego HTTP de entrada.

1. Em um terminal ou prompt de comando, execute o Visual Studio Code de dentro de uma pasta adequada para o projeto:

    ```bash
    # Create and navigate to a project folder

    # Run VS Code in that folder
    code .
    ```

1. No VS Code, selecione o logotipo do Azure para abrir o gerenciador do **Azure Functions**, então selecione o comando **Criar Projeto**:

    ![Criar um aplicativo de Funções local no VS Code](media/functions-extension/create-function-app-project.png)

1. Nos dois primeiros prompts, selecione a pasta atual e, em seguida, selecione **JavaScript** para o idioma.

1. No prompt **Selecione um modelo para a primeira função de seu projeto**, selecione **Gatilho HTTP**:

    ![Selecionar o gatilho para a Função](media/functions-extension/create-function-choose-template.png)

1. No prompt **Forneça um nome de função**, insira **HttpExample**. (Evite usar o nome padrão "HttpTrigger", pois ele é igual ao gatilho, o que pode ser confuso.)

    ![Como inserir um nome de função](media/functions-extension/create-function-name.png)

1. No prompt **Nível de Autorização**, selecione **Anônimo**:

    ![Como inserir um nome de função](media/functions-extension/create-function-anonymous-auth.png)

1. Após alguns instantes, o VS Code conclui a criação do projeto. Você tem uma pasta nomeada para a função, *HttpExample*, na qual há três arquivos:

    | Nome de arquivo | DESCRIÇÃO |
    | --- | --- |
    | *index.js* |  O código-fonte que responde à solicitação HTTP. |
    | *functions.json* | A [configuração de associação](/azure/azure-functions/functions-triggers-bindings) para o gatilho HTTP. |
    | *sample.dat* | Um arquivo de dados de espaço reservado para demonstrar que você pode ter outros arquivos na pasta. Você pode excluir esse arquivo, se desejar, pois ele não é usado neste tutorial. |

    ![Resultado da criação de um aplicativo de funções](media/functions-extension/create-function-app-results.png)

> [!div class="nextstepaction"]
> [Criei o aplicativo Functions](tutorial-vscode-serverless-node-03.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azurefunctions&step=create-app)
