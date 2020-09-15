---
title: 'Tutorial: Criar e implantar o Azure Functions sem servidor no Python com o VS Code'
description: Etapa 1 do tutorial, trabalhar com o Azure Functions, introdução e pré-requisitos.
ms.topic: conceptual
ms.date: 05/19/2020
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: 740a64785c57694be34f37ef6aa6571b0b3304b7
ms.sourcegitcommit: 9e282fc2ec967bee181c3034e7e70b28ae308905
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89473601"
---
# <a name="tutorial-create-and-deploy-serverless-azure-functions-in-python-with-visual-studio-code"></a>Tutorial: Criar e implantar o Azure Functions sem servidor em Python com o Visual Studio Code

Neste artigo, você usa o Visual Studio Code e a extensão Azure Functions para criar um ponto de extremidade HTTP sem servidor com o Python e para adicionar uma conexão (ou "associação") ao armazenamento.

O Azure Functions executa seu código em um ambiente sem servidor, sem precisar provisionar uma máquina virtual nem publicar um aplicativo Web. A extensão do Azure Functions para Visual Studio Code simplifica muito o processo de usar funções ao tratar automaticamente de muitas questões de configuração.

Se você encontrar problemas com alguma das etapas deste tutorial, adoraríamos ouvir os detalhes. Use o botão **Encontrei um problema** no final de cada artigo para enviar comentários.

Para assistir a um vídeo de demonstração, confira <a href="https://www.youtube.com/watch?v=9bMsdBYy-D0&feature=youtu.be&ocid=AID3006292" target="_blank">Criar Azure Functions com o VS Code</a> (youtube.com) do PyCon 2020 virtual. Você também pode ter interesse na sessão mais longa, <a href="https://www.youtube.com/watch?v=PV7iy6FPjAY&feature=youtu.be&t=13&ocid=AID3006292" target="_blank">Fácil processamento de dados com o Azure Functions</a> (youtube.com). 

## <a name="prerequisites"></a>Pré-requisitos

- Uma [assinatura do Azure](#azure-subscription).
- [Azure Functions Core Tools](#azure-functions-core-tools).
- [Visual Studio Code com a extensão Azure Functions](#visual-studio-code-python-and-the-azure-functions-extension).

### <a name="azure-subscription"></a>Assinatura do Azure

Se não tiver uma assinatura do Azure, [inscreva-se agora](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-functions-extension&mktingSource=vscode-tutorial-functions-extension) para obter uma conta gratuita por 30 dias, com US$ 200 em créditos do Azure, para experimentar qualquer combinação de serviços.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Instale o Azure Functions Core Tools seguindo as instruções para seu sistema operacional em [Trabalhar com o Azure Functions Core Tools](/azure/azure-functions/functions-run-local#v2). Ignore os comentários no artigo sobre o gerenciador de pacotes do Chocolatey, os quais não são necessários para concluir este tutorial.

Ao instalar o Node.js, use as opções padrão e *não* selecione a opção para instalar automaticamente as ferramentas necessárias.  Além disso, certifique-se de usar a opção `-g` com os comandos `npm install` para que o Core Tools esteja disponível para os comandos subsequentes.

> [!TIP]
> O Core Tools é escrito em .NET Core, e o pacote Core Tools é instalado da melhor maneira por meio do gerenciador de pacotes do Node.js, o npm. Por isso, é necessário instalar o .NET Core e o Node.js, mesmo que seja para trabalhar com o Azure Functions em Python. No entanto, você pode ignorar o requisito do .NET Core usando "pacotes de extensão", conforme descrito na documentação mencionada anteriormente. Seja qual for o caso, você precisa instalar esses componentes apenas uma vez. Depois disso, o Visual Studio Code solicita automaticamente que você instale as atualizações.

### <a name="visual-studio-code-python-and-the-azure-functions-extension"></a>Visual Studio Code, Python e a extensão Azure Functions

Instale o seguinte software:

- Uma versão de 64 bits do Python 3.6, 3.7 ou 3.8 conforme exigido pelo Azure Functions. Instale o Python do site [python.org](https://www.python.org/downloads). Ao instalar, selecione **Adicionar o Python 3.x ao PATH** e use as opções padrão selecionando a opção **Instalar agora**. No Windows, selecione também **Desabilitar limite de comprimento do caminho** no final do processo.
- [Visual Studio Code](https://code.visualstudio.com/).
- A [extensão do Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python), conforme descrito em [Tutorial de Python no Visual Studio Code – Pré-requisitos](https://code.visualstudio.com/docs/python/python-tutorial).
- A [extensão Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions). Para obter informações gerais, visite o [repositório do GitHub vscode-azurefunctions](https://github.com/Microsoft/vscode-azurefunctions).

    > [!NOTE]
    > A extensão Azure Functions está incluída no [pacote de extensão de Ferramentas do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack).

### <a name="sign-in-to-azure"></a>Entrar no Azure

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

### <a name="verify-prerequisites"></a>Verificar pré-requisitos

Para verificar se todas as ferramentas do Azure Functions estão instaladas, abra a Paleta de Comandos do Visual Studio Code (**F1**), selecione o comando **Terminal: Criar Terminal Integrado** e, depois que o terminal for aberto, execute o comando `func`:

![Verificar pré-requisitos do Azure Functions Core Tools](media/tutorial-vs-code-serverless-python/check-azure-functions-tools-prerequisites-in-visual-studio-code.png)

A saída que começa com o logotipo do Azure Functions (você precisa rolar a saída para cima) indica que o Azure Functions Core Tools está presente.

Se o comando `func` não for reconhecido, execute `npm install -g azure-functions-core-tools` novamente e verifique se a instalação teve êxito. Certifique-se também de usar a opção `-g` com o comando de instalação. Caso contrário, o npm instalará o pacote somente na pasta atual.

O comando `func` funciona por meio do arquivo *func.cmd* que está instalado na pasta global do Node.js. Para ver o local dessa pasta, execute `npm -l` e examine o local no final da saída.

> [!div class="nextstepaction"]
> [Entrei no Azure – prossiga para a etapa 2 >>>](tutorial-vs-code-serverless-python-02.md)

Algum problema? Envie um problema do GitHub usando a seção de comentários "Esta página" na parte inferior da página.
