---
title: Preparar um aplicativo para implantação no Serviço de Aplicativo do Azure no Linux usando o Visual Studio Code
description: Etapa 2 do tutorial, configurar seu aplicativo
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: kraigb
ms.openlocfilehash: ab1609d6d0940172d61a61a31f4dbfabc868c023
ms.sourcegitcommit: d6575ac86449380b5a9c6c66aa722cb33ed53438
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71186133"
---
# <a name="prepare-your-app"></a>Preparação do aplicativo

[Etapa anterior: pré-requisitos](tutorial-deploy-app-service-on-linux-01.md)

Se já tiver um aplicativo com o qual gostaria de trabalhar, verifique se você tem um arquivo *requirements.txt* que descreve suas dependências, incluindo estruturas como Flask ou Django.

Se ainda não tiver um aplicativo, use uma das opções abaixo. Certifique-se de verificar se o aplicativo é executado localmente.

## <a name="minimal-flask-app"></a>Aplicativo Flask mínimo

Esta seção descreve o aplicativo Flask mínimo usado neste passo a passo.

1. Crie uma nova pasta, abra-a no VS Code e adicione um arquivo chamado *hello.py* com o conteúdo abaixo. O objeto de aplicativo é nomeado `myapp` propositalmente para demonstrar como os nomes são usados no comando de inicialização do Serviço de Aplicativo, conforme você aprenderá mais tarde.

    ```python
    from flask import Flask
    myapp = Flask(__name__)

    @myapp.route("/")
    def hello():
        return "Hello Flask, on Azure App Service for Linux"
    ```

1. Crie um arquivo chamado *requirements.txt* com o conteúdo abaixo:

    ```text
    Flask==1.1.1
    ```

1. Siga as instruções em [Tutorial do Flask – Criar um ambiente de projeto para o Flask](https://code.visualstudio.com/docs/python/tutorial-flask#create-a-project-environment-for-flask) para criar um ambiente virtual com o Flask instalado no qual você possa executar o aplicativo localmente.

1. Para executar este aplicativo, use os seguintes comandos (dependendo de seu sistema operacional). A variável de ambiente FLASK_APP informa ao Flask onde encontrar o objeto de aplicativo.

    ```ps
    set FLASK_APP=hello:myapp
    flask run
    ```

    ```bash
    export FLASK_APP=hello:myapp
    flask run
    ```

    Em seguida, você pode abrir o aplicativo em um navegador usando a URL `http://127.0.0.1:5000/`.

## <a name="vs-code-flask-tutorial-sample"></a>Exemplo de tutorial do Flask para VS Code

Baixe ou clone [python-sample-vscode-flask-tutorial](https://github.com/Microsoft/python-sample-vscode-flask-tutorial), que é o resultado se seguir o [Tutorial do Flask](https://code.visualstudio.com/docs/python/tutorial-flask).

## <a name="vs-code-django-tutorial-sample"></a>Exemplo de tutorial do Django para VS Code

Baixe ou clone [python-sample-vscode-django-tutorial](https://github.com/Microsoft/python-sample-vscode-django-tutorial), que é o resultado se seguir o [Tutorial do Django](https://code.visualstudio.com/docs/python/tutorial-django).

Se seu aplicativo Django usar um banco de dados SQLite local como este exemplo, você precisará incluir uma cópia inicializada e preenchida previamente do arquivo *db.sqlite3* em seu repositório. Isso é necessário porque, no momento, o Serviço de Aplicativo para Linux não tem um meio de executar o comando `migrate` do Django como parte da implantação, portanto, você precisa implantar um banco de dados pré-criado. Ainda assim, o banco de dados é efetivamente somente leitura; gravar no banco de dados também causa erros.

A melhor opção, em qualquer caso, é usar um banco de dados separado que é implantado e inicializado independentemente do código do aplicativo.

> [!div class="nextstepaction"]
> [Meu aplicativo está pronto](tutorial-deploy-app-service-on-linux-03.md)

[Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-python&step=02-prepare-app)
