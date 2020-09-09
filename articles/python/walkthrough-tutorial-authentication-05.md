---
title: 'Passo a passo, parte 5: autenticar aplicativos Python com os serviços do Azure'
description: Uma discussão sobre as dependências do aplicativo principal (principalmente as bibliotecas do SDK do Azure), as instruções de importação necessárias e as variáveis de ambiente que espera ter definido.
ms.date: 08/24/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 2da18ac4b1d27e976d0713fba16dbfc0ba644168
ms.sourcegitcommit: 324da872a9dfd4c55b34739824fc6a6598f2ae12
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379452"
---
# <a name="part-5-main-app-dependencies-import-statements-and-environment-variables"></a>Parte 5: dependências, instruções de importação e variáveis de ambiente do aplicativo principal

[Página anterior: exemplo de implementação do aplicativo principal](walkthrough-tutorial-authentication-04.md)

Esta parte examina as bibliotecas do Python trazidas para o aplicativo principal e as variáveis de ambiente exigidas pelo código. Quando implantado no Azure, você usa as configurações de aplicativo no Serviço de Aplicativo do Azure para fornecer variáveis de ambiente.

## <a name="dependencies-and-import-statements"></a>Dependências e instruções de importação

O código do aplicativo exige uma série de bibliotecas: Flask, a biblioteca de solicitações HTTP padrão e as bibliotecas do Azure para Active Directory ([azure.identity](/python/api/overview/azure/identity-readme?view=azure-python)), Key Vault ([azure.keyvault.secrets](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python)) e armazenamento de fila ([azure.storage.queue](/python/api/overview/azure/storage-queue-readme?view=azure-python)). Essas bibliotecas são incluídas no arquivo *requirements.txt* do aplicativo:

```txt
flask
requests
azure.identity
azure.keyvault.secrets
azure.storage.queue
```

Quando você implanta o aplicativo no Serviço de Aplicativo do Azure, o Azure instala automaticamente esses requisitos no servidor host. Ao executar localmente, você os instala em seu ambiente com `pip install -r requirements.txt`.

Na parte superior do código, portanto, estarão as instruções de importação necessárias para as partes que estamos usando das bibliotecas:

```python
from flask import Flask, request, jsonify
import requests, random, string, os
from datetime import datetime
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient
from azure.storage.queue import QueueClient
```

## <a name="environment-variables"></a>Variáveis de ambiente

O código do aplicativo depende de quatro variáveis de ambiente:

| Variável | Valor |
| --- | --- |
| THIRD_PARTY_API_ENDPOINT | A URL da API de terceiros, como `https://msdocs-api-example.azurewebsites.net/api/RandomNumber`, descrita na [Parte 3](walkthrough-tutorial-authentication-03.md). |
| KEY_VAULT_URL | A URL do Azure Key Vault em que você armazenou a chave de acesso para a API de terceiros. |
| THIRD_PARTY_API_SECRET_NAME | O nome do segredo no Key Vault que contém a chave de acesso para a API de terceiros. |
| STORAGE_QUEUE_URL | A URL de uma Fila de Armazenamento do Microsoft Azure que foi configurada no Azure, como `https://msdocsmainappexample.queue.core.windows.net/code-requests` (confira a [Parte 4](walkthrough-tutorial-authentication-04.md)). Como o nome da fila está incluído no final da URL, você não o verá em nenhuma parte do código. |

Ao executar localmente, você cria essas variáveis em qualquer Shell de comando que estiver usando. Se você implantar o aplicativo em uma máquina virtual, criará variáveis semelhantes do lado do servidor.

No entanto, ao implantar no Serviço de Aplicativo do Azure, você não tem acesso ao próprio servidor. Nesse caso, você cria *configurações de aplicativo* com os mesmos nomes, que aparecem para o aplicativo como variáveis de ambiente. 

Os scripts de provisionamento criam essas configurações usando o comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set), da CLI do Azure. Todas as quatro variáveis são definidas com apenas um comando.

Para criar configurações por meio do portal do Azure, confira [Configurar um aplicativo do Serviço de Aplicativo no portal do Azure](/azure/app-service/configure-common).

> [!div class="nextstepaction"]
> [Parte 6 – Código de inicialização do aplicativo principal >>>](walkthrough-tutorial-authentication-06.md)
