---
title: Criar e implantar o Azure Functions sem servidor em Python com o Visual Studio Code
description: Etapa 1 do tutorial, introdução e pré-requisitos.
services: functions
author: kraigb
manager: barbkess
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: kraigb
ms.openlocfilehash: bf6dd180d5fabc04c8fe3bf09c8277bb7b22ea47
ms.sourcegitcommit: 86aec15e2d25b5b706092feb4271fb958c29d5fa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71710201"
---
# <a name="deploy-python-to-azure-functions-with-visual-studio-code"></a>Implantar Python no Azure Functions com o Visual Studio Code

Neste tutorial, você usa o Visual Studio Code e a extensão Azure Functions para criar um ponto de extremidade HTTP sem servidor com o Python e para adicionar uma conexão (ou "associação") ao armazenamento. O Azure Functions executa seu código em um ambiente sem servidor, sem precisar provisionar uma máquina virtual nem publicar um aplicativo Web. A extensão do Azure Functions para Visual Studio Code simplifica muito o processo de usar funções ao tratar automaticamente de muitas questões de configuração.

Se você encontrar problemas com alguma das etapas deste tutorial, adoraríamos ouvir os detalhes. Use o botão **Encontrei um problema** no final de cada artigo para enviar comentários.

## <a name="prerequisites"></a>Pré-requisitos

- Uma [assinatura do Azure](#azure-subscription).
- [Visual Studio Code com a extensão Azure Functions](#visual-studio-code-python-and-the-azure-functions-extension).
- [Azure Functions Core Tools](#azure-functions-core-tools).

### <a name="azure-subscription"></a>Assinatura do Azure

Se não tiver uma assinatura do Azure, [inscreva-se agora](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-functions-extension&mktingSource=vscode-tutorial-functions-extension) para obter uma conta gratuita por 30 dias, com US$ 200 em créditos do Azure, para experimentar qualquer combinação de serviços.

### <a name="visual-studio-code-python-and-the-azure-functions-extension"></a>Visual Studio Code, Python e a extensão Azure Functions

Instale o seguinte software:

- Python 3.6.x, conforme exigido pelo Azure Functions. O [Python 3.6.8](https://www.python.org/downloads/release/python-368/) é a versão 3.6.x mais recente.
- [Visual Studio Code](https://code.visualstudio.com/).
- A [extensão do Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python), conforme descrito em [Tutorial de Python no Visual Studio Code – Pré-requisitos](https://code.visualstudio.com/docs/python/python-tutorial).
- A [extensão Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions). Para obter informações gerais, visite o [repositório do GitHub vscode-azurefunctions](https://github.com/Microsoft/vscode-azurefunctions).

> [!NOTE]
> A extensão Azure Functions está incluída no [pacote de extensão de Ferramentas do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack).

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Siga as instruções referentes ao seu sistema operacional em [Trabalhar com o Azure Functions Core Tools](/azure/azure-functions/functions-run-local#v2). As ferramentas são escritas em .NET Core e o pacote Core Tools é melhor instalado usando o gerenciador de pacotes do Node.js, o npm. Por isso, você precisa instalar o .NET Core e o Node.js, mesmo para código Python. No entanto, você pode ignorar o requisito do .NET Core usando "pacotes de extensão", conforme descrito na documentação mencionada anteriormente. Seja qual for o caso, você precisa instalar esses componentes apenas uma vez. Depois disso, o Visual Studio Code solicita automaticamente que você instale as atualizações.

### <a name="sign-in-to-azure"></a>Entrar no Azure

[!INCLUDE [azure-sign-in](includes/azure-sign-in.md)]

### <a name="verify-prerequisites"></a>Verificar pré-requisitos

Para verificar se todas as ferramentas do Azure Functions estão instaladas, abra a Paleta de Comandos do Visual Studio Code (**F1**), selecione o comando **Terminal: Criar Terminal Integrado** e, depois que o terminal for aberto, execute o comando `func`:

![Verificando os pré-requisitos do Azure Functions Core Tools](media/tutorial-vs-code-serverless-python/check-prereqs.png)

A saída que começa com o logotipo do Azure Functions (você precisa rolar a saída para cima) indica que o Azure Functions Core Tools está presente.

Se o comando `func` não for reconhecido, verifique se a pasta onde você instalou o Azure Functions Core Tools está incluída na variável de ambiente PATH.

> [!div class="nextstepaction"]
> [Entrei no Azure](tutorial-vs-code-serverless-python-02.md)

[Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=vscode-functions-python&step=01-verify-prerequisites)
