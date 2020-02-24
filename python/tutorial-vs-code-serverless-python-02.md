---
title: 'Etapa 2: Criar uma função do Python para o Azure Functions com o VS Code'
description: 'Tutorial, etapa 2: demonstração do uso da extensão do Azure Functions para VS Code.'
ms.topic: conceptual
ms.date: 09/02/2019
ms.custom: seo-python-october2019
ms.openlocfilehash: 3d33213d00c2b20d3a712c5cf9ca6888d25ebb8a
ms.sourcegitcommit: 44d1abfb836f90b8731d7ea5d5a5af09245b2b89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2020
ms.locfileid: "77422165"
---
# <a name="2-create-a-python-function-for-azure-functions"></a>2: Criar uma função do Python para o Azure Functions

[Etapa anterior: pré-requisitos](tutorial-vs-code-serverless-python-01.md)

Neste artigo, você criará uma função do Python para o Azure Functions com o Visual Studio Code. O código do Azure Functions é gerenciado em um _projeto_ do Functions, que deve ser criado primeiro, antes da criação do código.

1. No gerenciador do **Azure: Functions** (aberto usando o ícone do Azure, do lado esquerdo), selecione o ícone de comando **Novo projeto** ou abra a Paleta de Comandos (F1) e selecione **Azure Functions: Criar Novo Projeto**.

    ![Criar um projeto no gerenciador do Azure Functions](media/tutorial-vs-code-serverless-python/create-a-new-project-in-azure-functions-explorer.png)

1. Nos prompts seguintes:

    | Prompt | Valor | Descrição |
    | --- | --- | --- |
    | Especifique uma pasta para o projeto | Pasta aberta atual | A pasta na qual o projeto será criado. Você pode optar por criar o projeto em uma subpasta. |
    | Selecione uma linguagem de programação para o seu projeto de aplicativo de função. | **Python** | A linguagem empegada na função, que determina o modelo usado para o código. |
    | Selecione um modelo para a primeira função do projeto | **Gatilho HTTP** | Uma função que usa um gatilho HTTP é executada sempre que há uma solicitação HTTP feita para o ponto de extremidade da função. (Há uma série de outros gatilhos para o Azure Functions. Para saber mais, confira [O que posso fazer com o Functions?](/azure/azure-functions/functions-overview#what-can-i-do-with-functions).) |
    | Forneça um nome de função | HttpExample | O nome é usado para uma subpasta que contém o código da função, juntamente com os dados de configuração, além de definir também o nome do ponto de extremidade HTTP. Use "HttpExample" em vez de aceitar o padrão "HTTPTrigger" oferecido, para distinguir a função em si do gatilho. |
    | Nível de autorização | **Anônimo** | A autorização anônima torna a função publicamente acessível a qualquer pessoa. |
    | Selecione como você gostaria de abrir seu projeto | **Abrir na janela atual** | Abre o projeto na janela atual do Visual Studio Code. |

    > [!NOTE]
    > Caso tenha o Python 3.6 e o 3.7 instalados, o Visual Studio Code usará o Python 3.6 por padrão para o projeto do Azure Functions. Para usar o Python 3.7 no momento, primeiro crie e ative um ambiente do Python 3.7 e depois use o comando `func init` de um terminal. Em seguida, reinicie o Visual Studio Code dessa pasta usando o comando `code .`.

1. Após um breve período, será exibida uma mensagem indicando que o novo projeto foi criado. No **Gerenciador**, há a subpasta criada para a função e o Visual Studio Code abre o arquivo *\_\_init\_\_.py* que contém o código padrão da função:

    ![Resultado da criação de um projeto do Python no Azure Functions](media/tutorial-vs-code-serverless-python/display-results-of-new-python-project-in-azure-functions.png)

    > [!NOTE]
    > Se o Visual Studio Code informar que você não tem um interpretador do Python selecionado ao abrir o *\_\_init\_\_.py*, abra a Paleta de Comandos (**F1**), selecione o comando **Python: selecione interpretador** e, em seguida, selecione o ambiente virtual na pasta local `.env` (que foi criada como parte do projeto). O ambiente deve ser baseado especificamente no Python 3.6x, conforme observado no artigo anterior, em [Pré-requisitos](tutorial-vs-code-serverless-python-01.md#prerequisites).
    >
    > ![Selecionar o ambiente virtual criado com o projeto do Python](media/tutorial-vs-code-serverless-python/select-virtual-environment-created-with-the-python-project.png)

> [!TIP]
> Sempre que você quiser criar outra função no mesmo projeto, use o comando **Criar função**, no gerenciador do **Azure: Functions** ou abra a Paleta de Comandos(**F1**) e selecione o comando **Azure Functions: Criar Função**. Ambos os comandos solicitam a você um nome de função (que é o nome do ponto de extremidade) e, em seguida, uma subpasta é criada com os arquivos padrão.
>
> ![Criar funções usando Nova Função no gerenciador do Azure Functions](media/tutorial-vs-code-serverless-python/create-new-functions-in-azure-functions-explorer.png)

> [!div class="nextstepaction"]
> [Implantei as funções – prossiga para a etapa 3 >>>](tutorial-vs-code-serverless-python-03.md)

[Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=vscode-functions-python&step=02-create-function)
