---
title: 'Etapa 3: Examinar os arquivos de código do Python para o Azure Functions no VS Code'
description: 'Tutorial, etapa 3: compreensão do código do Python no modelo fornecido pelo Azure Functions.'
ms.topic: conceptual
ms.date: 09/02/2019
ms.custom: seo-python-october2019
ms.openlocfilehash: 77dc4cb44158ded1dd5c6d1e19afb48272177a12
ms.sourcegitcommit: 1bd9ec6a4115e9162e33b76a933869788e6ab702
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80441331"
---
# <a name="3-examine-the-python-code-files-in-visual-studio-code"></a>3: Examinar os arquivos de código Python no Visual Studio Code

[Etapa anterior: criar a função](tutorial-vs-code-serverless-python-02.md)

Examine os arquivos do Python na pasta de funções usando o Visual Studio Code.

Na subpasta da função recém-criada há três arquivos: o *\_\_init\_\_.py* contém o código da função, o *function.json* descreve a função no Azure Functions e o *sample.dat* é um arquivo de dados de exemplo. Você pode excluir o *sample.dat* se desejar, pois ele existe apenas para mostrar que é possível adicionar outros arquivos à subpasta.

Vamos examinar primeiro o arquivo *function.json* e, em seguida, o código em *\_\_init\_\_.py*.

## <a name="functionjson"></a>function.json

O arquivo function.json fornece as informações de configuração necessárias para o ponto de extremidade do Azure Functions:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

A propriedade `scriptFile` identifica o arquivo de inicialização para o código, o qual deve conter uma função do Python chamada `main`. Você pode fatorar o código em vários arquivos, contanto que o arquivo especificado aqui contenha uma função `main`.

O elemento `bindings` contém dois objetos, um para descrever as solicitações de entrada e o outro para descrever a resposta HTTP. Para solicitações de entrada (`"direction": "in"`), a função responde às solicitações HTTP GET ou POST e não requer autenticação. A resposta (`"direction": "out"`) é uma resposta HTTP que retorna qualquer valor retornado da função do Python `main`.

## <a name="__init__py"></a>\_\_init\_\_.py

Quando você cria uma função, o Azure Functions fornece o código do Python padrão no *\_\_Init\_\_.py*:

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

As partes importantes do código são as seguintes:

- É necessário importar o módulo `azure.functions`; a importação do módulo de log é opcional, mas recomendada.
- A função do Python `main` obrigatória recebe um objeto `func.HttpRequest` chamado `req` e retorna um valor do tipo `func.HttpResponse`. Você pode saber mais sobre as funcionalidades desses objetos nas referências do [func.HttpRequest](/python/api/azure-functions/azure.functions.httprequest?view=azure-python) e do [func.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse?view=azure-python).
- Em seguida, o corpo de `main` processa a solicitação e gera uma resposta. Nesse caso, o código procura um parâmetro `name` na URL. Se isso falhar, ele verificará se o corpo da solicitação contém JSON (usando `func.HttpRequest.get_json`) e se o JSON contém a um valor `name` (usando o método `get` do objeto JSON retornado por `get_json`).
- Se um nome for encontrado, o código retornará a cadeia de caracteres "Olá" com o nome anexado; caso contrário, retornará uma mensagem de erro.

> [!div class="nextstepaction"]
> [Examinei os arquivos de código – prossiga para a etapa 4 >>>](tutorial-vs-code-serverless-python-04.md)

[Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=vscode-functions-python&step=03-examine-code-files)
