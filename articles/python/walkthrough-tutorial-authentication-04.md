---
title: 'Passo a passo, parte 4: autenticar aplicativos Python com os serviços do Azure'
description: Uma visão geral da implementação do aplicativo principal, incluindo todo o seu código.
ms.date: 08/24/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 0b7ffa1fb855d4b676813f49c545071209f6ce79
ms.sourcegitcommit: 324da872a9dfd4c55b34739824fc6a6598f2ae12
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379453"
---
# <a name="part-4-example-main-application-implementation"></a>Parte 4: exemplo de implementação do aplicativo principal

[Página anterior: exemplo de implementação de API de terceiros](walkthrough-tutorial-authentication-03.md)

O aplicativo principal em nosso cenário é um aplicativo Flask simples que é implantado no Serviço de Aplicativo do Azure. O aplicativo fornece um ponto de extremidade de API pública chamado */api/v1/getcode*, que gera um código para alguma outra finalidade no aplicativo (digamos, com a autenticação de dois fatores para usuários humanos).

Para ver o ponto de extremidade em ação, visite [https://msdocs-main-app-example.azurewebsites.net/api/v1/getcode](https://msdocs-main-app-example.azurewebsites.net/api/v1/getcode) em um navegador ou faça uma solicitação usando ondulação.

O aplicativo principal também fornece uma home page simples que exibe um link para o ponto de extremidade de API. Você pode ver essa parte do aplicativo em [https://msdocs-main-app-example.azurewebsites.net](https://msdocs-main-app-example.azurewebsites.net).

O script de provisionamento do exemplo executa as seguintes etapas:

1. Criar o host do Serviço de Aplicativo e implantar o código com o comando [`az webapp up`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-up) da CLI do Azure.

1. Provisionar uma Conta de Armazenamento do Microsoft Azure para o aplicativo principal (usando [`az storage account create`](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)).

1. Criar na conta de armazenamento uma Fila denominada "solicitações de código" (usando [`az storage queue create`](/cli/azure/storage/queue?view=azure-cli-latest#az-storage-queue-create)).

1. Para garantir que o aplicativo tenha permissão para gravar na fila, use [`az role assignment create`](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) para atribuir a função "Colaborador de Dados da Fila de Armazenamento" ao aplicativo. Para mais informações sobre como atribuir funções, confira [Como atribuir permissões de função](how-to-assign-role-permissions.md).

Veja a seguir o código do aplicativo principal; as explicações de detalhes importantes serão dadas nas próximas partes desta série.

```python
from flask import Flask, request, jsonify
import requests, random, string, os
from datetime import datetime
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential
from azure.storage.queue import QueueClient

app = Flask(__name__)

# Retrieve the URL of the third-party API endpoint we invoke.
number_url = os.environ["THIRD_PARTY_API_ENDPOINT"]

# Authenticate with Azure. First, obtain the credential object. To run locally,
# you must have the necessary environment variables set up to provide the
# local service principal information. When deployed to the cloud, the app must
# have managed identity enabled in Azure App Service.
credential = DefaultAzureCredential()

# Next, get a client object for the Key Vault. The client object is strictly
# a client-side construct provided by the Azure libraries as a layer on top
# of the Azure REST API.
key_vault_url = os.environ["KEY_VAULT_URL"]
keyvault_client = SecretClient(vault_url=key_vault_url, credential=credential)

# Obtain the secret: for this step to work you must add the app's identity to
# the key vault's access policies for secret management. When deployed to the cloud
# the identity is the name of the app in App Service; when running locally, the
# identity is the local service principal.
api_secret_name = os.environ["THIRD_PARTY_API_SECRET_NAME"]
vault_secret = keyvault_client.get_secret(api_secret_name)

# The "secret" from Key Vault is an object with multiple properties. The access key
# we want for the third-party API is in the secret's value property.
access_key = vault_secret.value

#Set up the Storage queue client to which we write messages
queue_url = os.environ["STORAGE_QUEUE_URL"]
queue_client = QueueClient.from_queue_url(queue_url=queue_url, credential=credential)


@app.route('/', methods=['GET'])
def home():
    return f'Home page of the main app. Make a request to <a href="./api/v1/getcode">/api/v1/getcode</a>.'


def random_char(num):
       return ''.join(random.choice(string.ascii_letters) for x in range(num))


@app.route('/api/v1/getcode', methods=['GET'])
def get_code():
    headers = {
        'Content-Type': 'application/json',
        'x-functions-key': access_key
        }

    r = requests.get(url = number_url, headers = headers)

    if (r.status_code != 200):
        return "Could not get you a code.", r.status_code

    data = r.json()
    chars1 = random_char(3)
    chars2 = random_char(3)
    code_value = f"{chars1}-{data['value']}-{chars2}"
    code = { "code": code_value, "timestamp" : str(datetime.utcnow()) }

    # Log a queue message with the code for, say, a process that invalidates
    # the code after a certain period of time.
    queue_client.send_message(code)

    return jsonify(code)


if __name__ == '__main__':
    app.run()
```

> [!div class="nextstepaction"]
> [Parte 5 – Dependências, importações e variáveis de ambiente do aplicativo principal > > >](walkthrough-tutorial-authentication-05.md)
