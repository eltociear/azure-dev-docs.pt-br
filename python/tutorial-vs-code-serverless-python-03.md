---
title: Examine os arquivos de código do Python para o Azure Functions no Visual Studio Code
description: 'Tutorial, etapa 3: compreensão do código do Python no modelo fornecido pelo Azure Functions.'
services: functions
author: kraigb
manager: barbkess
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: kraigb
ms.openlocfilehash: b1ed7f69656797822b17c11bcf0f2ece0caa09e2
ms.sourcegitcommit: 86aec15e2d25b5b706092feb4271fb958c29d5fa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71707347"
---
# <a name="examine-the-code-files"></a>Examinar os arquivos de código

[Etapa anterior: criar a função](tutorial-vs-code-serverless-python-02.md)

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

## <a name="__initpy__"></a>\_\_init.py\_\_

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

- Você deve importar `func` de `azure.functions`; a importação do módulo de registro em log é opcional, mas recomendada.
- A função do Python `main` obrigatória recebe um objeto `func.HttpRequest` chamado `req` e retorna um valor do tipo `func.HttpResponse`. Você pode saber mais sobre as funcionalidades desses objetos nas referências do [func.HttpRequest](/python/api/azure-functions/azure.functions.httprequest?view=azure-python) e do [func.HttpResponse](/python/api/azure-functions/azure.functions.httpresponse?view=azure-python).
- Em seguida, o corpo de `main` processa a solicitação e gera uma resposta. Nesse caso, o código procura um parâmetro `name` na URL. Se isso falhar, ele verificará se o corpo da solicitação contém JSON (usando `func.HttpRequest.get_json`) e se o JSON contém a um valor `name` (usando o método `get` do objeto JSON retornado por `get_json`).
- Se um nome for encontrado, o código retornará a cadeia de caracteres "Olá" com o nome anexado; caso contrário, retornará uma mensagem de erro.

> [!div class="nextstepaction"]
> [Examinei os arquivos de código](tutorial-vs-code-serverless-python-04.md)

[Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=vscode-functions-python&step=03-examine-code-files)
