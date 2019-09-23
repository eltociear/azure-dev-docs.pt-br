---
title: Criar uma função do Python para o Azure Functions com o Visual Studio Code
description: 'Tutorial, etapa 2: demonstração do uso da extensão do Azure Functions para VS Code.'
services: functions
author: kraigb
manager: barbkess
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: kraigb
ms.openlocfilehash: 4427137000e7fca7706b782a73a45db03782872f
ms.sourcegitcommit: 74e28a479c87a3a53592646420b78e69852dd86a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/16/2019
ms.locfileid: "71019554"
---
# <a name="create-the-python-function"></a>Criar a função do Python

[Etapa anterior: pré-requisitos](tutorial-vs-code-serverless-python-01.md)

1. O código do Azure Functions é gerenciado em um _projeto_ do Functions, que deve ser criado primeiro, antes da criação do código. No gerenciador do **Azure: Functions** (aberto usando o ícone do Azure, do lado esquerdo), selecione o ícone de comando **Novo projeto** ou abra a Paleta de Comandos (F1) e selecione **Azure Functions: Criar Novo Projeto**.

    ![Criar botão do projeto no gerenciador do Functions](media/tutorial-vs-code-serverless-python/project-create-new.png)

1. Nos prompts seguintes:

    | Prompt | Valor | DESCRIÇÃO |
    | --- | --- | --- |
    | Especifique uma pasta para o projeto | Pasta aberta atual | A pasta na qual o projeto será criado. Você pode optar por criar o projeto em uma subpasta. |
    | Selecione uma linguagem de programação para o seu projeto de aplicativo de função. | **Python** | A linguagem empegada na função, que determina o modelo usado para o código. |
    | Selecione um modelo para a primeira função do projeto | **Gatilho HTTP** | Uma função que usa um gatilho HTTP é executada sempre que há uma solicitação HTTP feita para o ponto de extremidade da função. (Há uma série de outros gatilhos para o Azure Functions. Para saber mais, confira [O que posso fazer com o Functions?](/azure/azure-functions/functions-overview.md#what-can-i-do-with-functions).) |
    | Forneça um nome de função | HttpExample | O nome é usado para uma subpasta que contém o código da função, juntamente com os dados de configuração, além de definir também o nome do ponto de extremidade HTTP. Use "HttpExample" em vez de aceitar o padrão "HTTPTrigger" oferecido, para distinguir a função em si do gatilho. |
    | Nível de autorização | **Anônimo** | A autorização anônima torna a função publicamente acessível a qualquer pessoa. |
    | Selecione como você gostaria de abrir seu projeto | **Abrir na janela atual** | Abre o projeto na janela atual do Visual Studio Code. |

1. Após um breve período, será exibida uma mensagem indicando que o novo projeto foi criado. No **Gerenciador**, há a subpasta criada para a função e o Visual Studio Code abre o arquivo *\_\_init\_\_.py* que contém o código padrão da função:

    [![Resultado da criação do projeto de funções do Python](media/tutorial-vs-code-serverless-python/project-create-results.png)](media/tutorial-vs-code-serverless-python/project-create-results.png)

    > [!NOTE]
    > Se o Visual Studio Code informar que você não tem um interpretador do Python selecionado ao abrir o *\_\_init\_\_.py*, abra a Paleta de Comandos (**F1**), selecione o comando **Python: selecione interpretador** e, em seguida, selecione o ambiente virtual na pasta local `.env` (que foi criada como parte do projeto). O ambiente deve ser baseado especificamente no Python 3.6x, conforme observado no artigo anterior, em [Pré-requisitos](tutorial-vs-code-serverless-python-01.md#prerequisites).
    >
    > ![Seleção do ambiente virtual criado com o projeto](media/tutorial-vs-code-serverless-python/select-venv-interpreter.png)

> [!TIP]
> Sempre que você quiser criar outra função no mesmo projeto, use o comando **Criar função**, no gerenciador do **Azure: Functions** ou abra a Paleta de Comandos(**F1**) e selecione o comando **Azure Functions: Criar Função**. Ambos os comandos solicitam a você um nome de função (que é o nome do ponto de extremidade) e, em seguida, uma subpasta é criada com os arquivos padrão.
>
> ![Comando Nova Função no gerenciador do Azure: Functions](media/tutorial-vs-code-serverless-python/function-create-new.png)

> [!div class="nextstepaction"]
> [Criei a função](tutorial-vs-code-serverless-python-03.md)

[Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=vscode-functions-python&step=02-create-function)
