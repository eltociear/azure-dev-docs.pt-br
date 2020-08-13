---
title: Configurar o log nas bibliotecas do Azure para Python
description: As bibliotecas do Azure usam o módulo padrão de log do Python, que é configurado por biblioteca ou por operação.
ms.date: 06/04/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 0c08ba9c514bf9bca3c982ccf3ef3182f203e247
ms.sourcegitcommit: 980efe813d1f86e7e00929a0a3e1de83514ad7eb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87983308"
---
# <a name="configure-logging-in-the-azure-libraries-for-python"></a>Configurar o log nas bibliotecas do Azure para Python

As bibliotecas do Azure para Python [baseadas na página azure.core](azure-sdk-library-package-index.md#libraries-using-azurecore) fornecem a saída de log usando a biblioteca padrão de [log](https://docs.python.org/3/library/logging.html) do Python.

A saída do agente não está habilitada por padrão. Para habilitar o log:

1. Adquira o objeto de log para a biblioteca desejada e defina o nível de registros em log.
1. Registre um manipulador para o fluxo de log.
1. Habilite o log passando um parâmetro `logging_enable=True` para um construtor de objeto de cliente ou para um método específico.

Como regra geral, o melhor recurso para entender o uso do log nas bibliotecas é procurar o código-fonte do SDK em [github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python). Incentivamos você a clonar esse repositório localmente para que possa pesquisar com facilidade os detalhes quando necessário, conforme sugerido nas seções a seguir.

## <a name="set-logging-levels"></a>Definir os níveis de registros em log

```python
import logging

# ...

# Acquire the logger for a library (azure.storage.blob in this example)
logger = logging.getLogger('azure.storage.blob')

# Set the desired logging level
logger.setLevel(logging.DEBUG)
```

- Este exemplo adquire o agente para a biblioteca `azure.storage.blob` e define o nível de registros em log como `logging.DEBUG`.

- Você pode chamar `logger.setLevel` a qualquer momento para alterar o nível de registros em log de diferentes segmentos do código.

Para definir um nível para outra biblioteca, use o nome da biblioteca na chamada `logging.getLogger`. Por exemplo, a biblioteca azure-eventhubs fornece um agente chamado `azure.eventhubs`, a biblioteca azure-storage-queue fornece um agente chamado `azure.storage.queue` etc. (Com frequência, o código-fonte do SDK usa a instrução `logging.getLogger(__name__)`, que adquire um agente usando o nome do módulo que o contém.)

Use também namespaces mais gerais. Por exemplo,

```python
import logging

# Set the logging level for all azure-storage-* libraries
logger = logging.getLogger('azure.storage')
logger.setLevel(logging.INFO)

# Set the logging level for all azure-* libraries
logger = logging.getLogger('azure')
logger.setLevel(logging.ERROR)
```

Use o método `logger.isEnabledFor` para verificar se um nível de registros em log específico está habilitado:

```python
print(f"Logger enabled for ERROR={logger.isEnabledFor(logging.ERROR)}, " \
    f"WARNING={logger.isEnabledFor(logging.WARNING)}, " \
    f"INFO={logger.isEnabledFor(logging.INFO)}, " \
    f"DEBUG={logger.isEnabledFor(logging.DEBUG)}")
```

Os níveis de registros em log são os mesmos que os [níveis padrão da biblioteca de log](https://docs.python.org/3/library/logging.html#levels). A seguinte tabela descreve o uso geral desses níveis de registros em log nas bibliotecas do Azure para Python:

| Nível de log             | Uso típico |
| ---                       | ---         |
| logging.ERROR             | Falhas em que é improvável que o aplicativo seja recuperado (como memória insuficiente). |
| logging.WARNING (padrão) | Uma função não consegue executar a tarefa pretendida (mas não quando a função pode ser recuperada, como a repetição de uma chamada à API REST). Normalmente, as funções registram um aviso em log ao gerar exceções. O nível de aviso habilita automaticamente o nível de erro. |
| logging.INFO              | A função opera normalmente ou uma chamada de serviço é cancelada. Os eventos de informações normalmente incluem solicitações, respostas e cabeçalhos. O nível de informação habilita automaticamente os níveis de erro e de aviso. |
| logging.DEBUG             | Informações detalhadas que são normalmente usadas para a solução de problemas. Depuração é o único nível de registros em log que inclui informações confidenciais, como chaves de conta em cabeçalhos. A saída de depuração normalmente inclui um rastreamento de pilha para exceções. O nível de depuração habilita automaticamente os níveis de informação, de aviso e erro. |
| logging.NOTSET            | Desabilita todo o log. |

### <a name="library-specific-logging-level-behavior"></a>Comportamento do nível de registros em log específico da biblioteca

O comportamento exato do log em cada nível depende da biblioteca em questão. Algumas bibliotecas, como azure.eventhub, executam o log extensivo, enquanto outras bibliotecas fazem muito pouco.

A melhor maneira de examinar o log exato de uma biblioteca é procurar os níveis de registros em log no código-fonte do SDK do Azure para Python:

1. Na pasta do repositório, navegue até a pasta *sdk* e até a pasta do serviço específico de interesse.

1. Nessa pasta, pesquise uma das seguintes cadeias de caracteres:

    - `_LOGGER.error`
    - `_LOGGER.warning`
    - `_LOGGER.info`
    - `_LOGGER.debug`

## <a name="register-a-log-stream-handler"></a>Registrar um manipulador de fluxo de log

Para capturar a saída de log, registre, pelo menos, um manipulador de fluxo de log no código:

```python
import logging

# Direct logging output to stdout. Without adding a handler,
# no logging output is captured.
handler = logging.StreamHandler(stream=sys.stdout)
logger.addHandler(handler)
```

Este exemplo registra um manipulador que direciona a saída de log para StdOut. Você pode usar outros tipos de manipuladores, conforme descrito em [logging.handlers](https://docs.python.org/3/library/logging.handlers.html) na documentação do Python.

## <a name="enable-logging-for-a-client-object-or-operation"></a>Habilitar o log para uma operação ou um objeto de cliente

Mesmo depois de definir um conjunto de nível de registros em log e registrar um manipulador, você ainda precisará instruir as bibliotecas do Azure para habilitar o log para um construtor de objeto de cliente ou um método de operação.

### <a name="enable-logging-for-a-client-object"></a>Habilitar o log de um objeto de cliente

```python
from azure.storage.blob import BlobClient
from azure.identity import DefaultAzureCredential

# endpoint is the Blob storage URL.
client = BlobClient(endpoint, DefaultAzureCredential(), logging_enable=True)
```

A habilitação do log para um objeto de cliente habilita o log para todas as operações invocadas por meio desse objeto.

### <a name="enable-logging-for-an-operation"></a>Habilitar o log de uma operação

```python
from azure.storage.blob import BlobClient
from azure.identity import DefaultAzureCredential

# Logging is not enabled on this client.
# endpoint is the Blob storage URL.
client = BlobClient(endpoint, DefaultAzureCredential())

# Enable logging for only this operation
client.create_container("container01", logging_enable=True)
```

## <a name="example-logging-output"></a>Saída de log de exemplo

O seguinte código é mostrado no [Exemplo: Usar uma conta de armazenamento](azure-sdk-example-storage-use.md) com a adição da habilitação do log DEBUG (comentários omitidos para fins de brevidade):

```python
import os, sys, logging
from azure.identity import DefaultAzureCredential
from azure.storage.blob import BlobClient

logger = logging.getLogger('azure.storage.blob')
logger.setLevel(logging.DEBUG)

handler = logging.StreamHandler(stream=sys.stdout)
logger.addHandler(handler)

credential = DefaultAzureCredential()
storage_url = os.environ["AZURE_STORAGE_BLOB_URL"]

blob_client = BlobClient(storage_url, container_name="blob-container-01",
    blob_name="sample-blob.txt", credential=credential)

with open("./sample-source.txt", "rb") as data:
    blob_client.upload_blob(data, logging_enable=True)
```

A saída de log é a seguinte:

<pre>
Request URL: 'https://pythonsdkstorage12345.blob.core.windows.net/blob-container-01/sample-blob.txt'
Request method: 'PUT'
Request headers:
    'Content-Type': 'application/octet-stream'
    'Content-Length': '79'
    'x-ms-version': '2019-07-07'
    'x-ms-blob-type': 'BlockBlob'
    'If-None-Match': '*'
    'x-ms-date': 'Mon, 01 Jun 2020 22:54:14 GMT'
    'x-ms-client-request-id': 'd081f88e-a45a-11ea-b9eb-0c5415dfd03a'
    'User-Agent': 'azsdk-python-storage-blob/12.3.1 Python/3.8.3 (Windows-10-10.0.18362-SP0)'
    'Authorization': '*****'
Request body:
b"Hello there, Azure Storage. I'm a friendly file ready to be stored in a blob.\r\n"
Response status: 201
Response headers:
    'Content-Length': '0'
    'Content-MD5': 'kvMIzjEi6O8EqTVnZJNakQ=='
    'Last-Modified': 'Mon, 01 Jun 2020 22:54:14 GMT'
    'ETag': '"0x8D8067EB52FF7BC"'
    'Server': 'Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0'
    'x-ms-request-id': '5df479b1-f01e-00d0-5b67-382916000000'
    'x-ms-client-request-id': 'd081f88e-a45a-11ea-b9eb-0c5415dfd03a'
    'x-ms-version': '2019-07-07'
    'x-ms-content-crc64': 'QmecNePSHnY='
    'x-ms-request-server-encrypted': 'true'
    'Date': 'Mon, 01 Jun 2020 22:54:14 GMT'
Response content:
</pre>
