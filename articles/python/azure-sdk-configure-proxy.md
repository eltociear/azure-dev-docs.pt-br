---
title: Configurar proxies ao usar bibliotecas do Azure
description: Use variáveis de ambiente HTTP[S]_PROXY para definir um proxy para um script ou aplicativo inteiro ou use argumentos nomeados opcionais para construtores de cliente ou métodos de operação.
ms.date: 06/16/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 86eaa5b8dc0ddfb529643a5c015938344582e62b
ms.sourcegitcommit: 980efe813d1f86e7e00929a0a3e1de83514ad7eb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87982698"
---
# <a name="how-to-configure-proxies-for-the-azure-libraries"></a>Como configurar proxies para as bibliotecas do Azure

Uma URL do servidor proxy tem o formato `http[s]://[username:password@]<ip_address_or_domain>:<port>/` em que a combinação nome de usuário:senha é opcional.

Em seguida, você pode configurar um proxy globalmente usando variáveis de ambiente ou pode especificar um proxy passando um argumento chamado `proxies` para um construtor de cliente individual ou método de operação.

## <a name="global-configuration"></a>Configuração global

Para configurar um proxy globalmente para seu script ou aplicativo, defina variáveis de ambiente `HTTP_PROXY` ou `HTTPS_PROXY` com a URL do servidor. Essas variáveis funcionam em qualquer versão das bibliotecas do Azure.

Essas variáveis de ambiente serão ignoradas se você passar o parâmetro `use_env_settings=False` para um construtor de objeto de cliente ou método de operação.

### <a name="from-python-code"></a>No código Python

```python
import os
os.environ["HTTP_PROXY"] = "http://10.10.1.10:1180"

# Alternate URL and variable forms:
# os.environ["HTTP_PROXY"] = "http://username:password@10.10.1.10:1180"
# os.environ["HTTPS_PROXY"] = "http://10.10.1.10:1180"
# os.environ["HTTPS_PROXY"] = "http://username:password@10.10.1.10:1180"
```

### <a name="from-the-cli"></a>Na CLI

# <a name="cmd"></a>[cmd](#tab/cmd)

```cmd
rem Non-authenticated HTTP server:
set HTTP_PROXY=http://10.10.1.10:1180

rem Authenticated HTTP server:
set HTTP_PROXY=http://username:password@10.10.1.10:1180

rem Non-authenticated HTTPS server:
set HTTPS_PROXY=http://10.10.1.10:1180

rem Authenticated HTTPS server:
set HTTPS_PROXY=http://username:password@10.10.1.10:1180
```

# <a name="bash"></a>[Bash](#tab/bash)

```bash
# Non-authenticated HTTP server:
HTTP_PROXY=http://10.10.1.10:1180

# Authenticated HTTP server:
HTTP_PROXY=http://username:password@10.10.1.10:1180

# Non-authenticated HTTPS server:
HTTPS_PROXY=http://10.10.1.10:1180

# Authenticated HTTPS server:
HTTPS_PROXY=http://username:password@10.10.1.10:1180
```

---

## <a name="per-client-or-per-method-configuration"></a>Configuração por cliente ou por método

Para configurar um proxy para um objeto de cliente ou método de operação específico, especifique um servidor proxy com um argumento chamado `proxies`.

Por exemplo, o código do artigo [Exemplo: usar o armazenamento do Azure](azure-sdk-example-storage.md) a seguir especifica um proxy HTTPS com credenciais de usuário com o construtor `BlobClient`. Nesse caso, o objeto vem da biblioteca azure.storage.blob, que é baseada no azure.core.

```python
# Earlier code omitted for brevity

blob_client = BlobClient(storage_url, container_name="blob-container-01",
    blob_name="sample-blob.txt", credential=credential,
    proxies={ "https": "https://username:password@10.10.1.10:1180" }
)

# Other forms that the proxy URL might take:
# proxies={ "http": "http://10.10.1.10:1180" }
# proxies={ "http": "http://username:password@10.10.1.10:1180" }
# proxies={ "https": "https://10.10.1.10:1180" }
```
