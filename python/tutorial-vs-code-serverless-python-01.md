---
title: 'Tutorial: Criar e implantar o Azure Functions sem servidor no Python com o VS Code'
description: Etapa 1 do tutorial, introdução e pré-requisitos.
ms.topic: conceptual
ms.date: 09/02/2019
ms.custom: seo-python-october2019
ms.openlocfilehash: b9aecefc05db95aa121e496c9c993a5bba373ef7
ms.sourcegitcommit: a9ec05f964009409fe6e54b95c9d548f805a518e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2020
ms.locfileid: "78215325"
---
# <a name="tutorial-create-and-deploy-serverless-azure-functions-in-python-with-visual-studio-code"></a>Tutorial: Criar e implantar o Azure Functions sem servidor em Python com o Visual Studio Code

Neste artigo, você usa o Visual Studio Code e a extensão Azure Functions para criar um ponto de extremidade HTTP sem servidor com o Python e para adicionar uma conexão (ou "associação") ao armazenamento.

O Azure Functions executa seu código em um ambiente sem servidor, sem precisar provisionar uma máquina virtual nem publicar um aplicativo Web. A extensão do Azure Functions para Visual Studio Code simplifica muito o processo de usar funções ao tratar automaticamente de muitas questões de configuração.

Se você encontrar problemas com alguma das etapas deste tutorial, adoraríamos ouvir os detalhes. Use o botão **Encontrei um problema** no final de cada artigo para enviar comentários.

## <a name="prerequisites"></a>Pré-requisitos

- Uma [assinatura do Azure](#azure-subscription).
- [Azure Functions Core Tools](#azure-functions-core-tools).
- [Visual Studio Code com a extensão Azure Functions](#visual-studio-code-python-and-the-azure-functions-extension).

### <a name="azure-subscription"></a>Assinatura do Azure

Se não tiver uma assinatura do Azure, [inscreva-se agora](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-functions-extension&mktingSource=vscode-tutorial-functions-extension) para obter uma conta gratuita por 30 dias, com US$ 200 em créditos do Azure, para experimentar qualquer combinação de serviços.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Instale o Azure Functions Core Tools seguindo as instruções para seu sistema operacional em [Trabalhar com o Azure Functions Core Tools](/azure/azure-functions/functions-run-local#v2). Ignore os comentários no artigo sobre o gerenciador de pacotes do Chocolately, os quais não são necessários para concluir este tutorial.

Ao instalar o Node.js, use as opções padrão e *não* selecione a opção para instalar automaticamente as ferramentas necessárias.  Além disso, certifique-se de usar a opção `-g` com os comandos `npm install` para que o Core Tools esteja disponível para os comandos subsequentes.

> [!TIP]
> O Core Tools é escrito em .NET Core, e o pacote Core Tools é instalado da melhor maneira por meio do gerenciador de pacotes do Node.js, o npm. Por isso, é necessário instalar o .NET Core e o Node.js, mesmo que seja para trabalhar com o Azure Functions em Python. No entanto, você pode ignorar o requisito do .NET Core usando "pacotes de extensão", conforme descrito na documentação mencionada anteriormente. Seja qual for o caso, você precisa instalar esses componentes apenas uma vez. Depois disso, o Visual Studio Code solicita automaticamente que você instale as atualizações.

### <a name="visual-studio-code-python-and-the-azure-functions-extension"></a>Visual Studio Code, Python e a extensão Azure Functions

Instale o seguinte software:

- Python 3.7 ou Python 3.6 conforme exigido pelo Azure Functions. O [Python 3.7.5](https://www.python.org/downloads/release/python-375/) e o [Python 3.6.8](https://www.python.org/downloads/release/python-368/) são as versões compatíveis mais recentes. Role para baixo nessas páginas para localizar os instaladores. Ao instalar, selecione **Adicionar o Python 3.x ao PATH** e use as opções padrão selecionando a opção **Instalar agora**. No Windows, selecione também **Desabilitar limite de comprimento do caminho** no final do processo.
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

[Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=vscode-functions-python&step=01-verify-prerequisites)
