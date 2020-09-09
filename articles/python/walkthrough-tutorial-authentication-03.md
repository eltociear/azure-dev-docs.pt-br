---
title: 'Passo a passo, parte 3: autenticar aplicativos Python com os serviços do Azure'
description: Um exame do exemplo de implementação de API de terceiros usando o Azure Functions e como o ponto de extremidade é protegido por uma chave de acesso.
ms.date: 08/24/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 84078a455843cb28f80a633bb5344bc5ab645ac7
ms.sourcegitcommit: 324da872a9dfd4c55b34739824fc6a6598f2ae12
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379456"
---
# <a name="part-3-example-third-party-api-implementation"></a>Parte 3: exemplo de implementação de API de terceiros

[Página anterior: desafios de autenticação](walkthrough-tutorial-authentication-02.md)

Em nosso cenário de exemplo, o ponto de extremidade público do aplicativo principal usa uma API de terceiros protegida por uma chave de acesso. Esta seção mostra uma implementação da API de terceiros usando o Azure Functions, mas a API pode ser implementada de outras maneiras e implantada em um servidor de nuvem ou host da Web diferente. O único aspecto importante é que o ponto de extremidade protegido por uma chave de acesso específica deve ser incluído em qualquer solicitação do cliente. Qualquer aplicativo que invocar essa API deverá gerenciar com segurança essa chave.

Para fins de demonstração, essa API é implantada no ponto de extremidade `https://msdocs-api-example.azurewebsites.net/api/RandomNumber`. No entanto, para chamar a API, você deverá fornecer a chave de acesso `d0c5atM1cr0s0ft` em um parâmetro da URL `?code=` ou em uma propriedade `'x-functions-key'` do cabeçalho HTTP. Por exemplo, experimente esta URL em um navegador ou em uma ondulação: [https://msdocs-api-example.azurewebsites.net/api/RandomNumber?code=d0c5atM1cr0s0ft](https://msdocs-api-example.azurewebsites.net/api/RandomNumber?code=d0c5atM1cr0s0ft).

Se a chave de acesso for válida, o ponto de extremidade retornará uma resposta JSON que contém apenas uma propriedade, "value", cujo valor deverá ser um número entre 1 e 999, como `{"value": 959}`.

O ponto de extremidade é implementado no Python e implantado no Azure Functions. O código será o seguinte:

```python
import logging
import random
import json

import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('RandomNumber invoked via HTTP trigger.')

    random_value = random.randint(1, 1000)
    dict = { "value" : random_value }
    return func.HttpResponse(json.dumps(dict))
```

No repositório de exemplo, esse código é encontrado em *third_party_api/RandomNumber/\_\_init\_\_.py*. A pasta, *RandomNumber*, fornece o nome da função e *\_\_init\_\_.py* contém o código. Outro arquivo dessa pasta, *function.json*, descreve quando a função é disparada. Outros arquivos da pasta pai *third_party_api* fornecem detalhes para o "aplicativo" do Azure Function que hospeda a própria função.

Para implantar o código, o script de provisionamento do exemplo executa as seguintes etapas:

1. Crie uma conta de armazenamento de backup para o Azure Functions com o comando [`az storage account create`](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) da CLI do Azure.

1. Crie um "aplicativo" do Azure Functions com o comando [`az function app create`](/cli/azure/functionapp?view=azure-cli-latest#az-functionapp-create) da CLI do Azure.

1. Após esperar 60 segundos para o host ser totalmente provisionado, implante o código usando o comando do [Azure Functions Core Tools](/azure/azure-functions/functions-run-local?tabs=linux%2Ccsharp%2Cbash), [`func azure functionapp publish`](/azure/azure-functions/functions-run-local?tabs=linux%2Ccsharp%2Cbash#project-file-deployment)

1. Atribua a chave de acesso, `d0c5atM1cr0s0ft`, à função. (Confira [Protegendo o Azure Functions](/azure/azure-functions/security-concepts) para obter um segundo plano sobre as teclas de função.)

    No script de provisionamento, essa etapa é realizada por meio de uma chamada à API REST para a [API de gerenciamento de chaves do Functions](https://github.com/Azure/azure-functions-host/wiki/Key-management-API), pois a CLI do Azure não é compatível com esse recurso específico no momento. Para chamar essa API REST, o script de provisionamento deve primeiro usar outra chamada à API REST para recuperar a chave mestra do aplicativo de funções.

    Você também pode atribuir chaves de acesso por meio do [portal do Azure](https://portal.azure.com). Na página do aplicativo do Functions, selecione **Functions** e, em seguida, selecione a função específica a ser protegida (denominada `RandomNumber` neste exemplo). Na página da função, selecione **chaves de função** para abrir a página em que você pode criar e gerenciar essas chaves.

> [!div class="nextstepaction"]
> [Parte 4 – Exemplo de implementação do aplicativo principal >>>](walkthrough-tutorial-authentication-04.md)
