---
title: SDK do Azure para Python
description: Visão geral dos recursos e das funcionalidades do SDK do Azure para Python que ajudam os desenvolvedores a serem mais produtivos ao trabalhar com os serviços do Azure.
ms.date: 10/30/2019
ms.topic: conceptual
ms.openlocfilehash: 7a70f6050890bc384bac0d58ba3f0aa2e5e553cb
ms.sourcegitcommit: 5c65d22b5203b0c17806463d349a6ede93a99fa0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2019
ms.locfileid: "75010583"
---
# <a name="azure-sdk-for-python"></a>SDK do Azure para Python

O SDK do Azure para Python simplifica o uso e o gerenciamento dos recursos do Azure com base no código de aplicativo do Python. O SDK é compatível com Python 2.7 e Python 3.5.3 ou posterior.

Você instala o SDK ao instalar qualquer uma de suas bibliotecas de componentes individuais usando `pip install <library>`. É possível ver a lista de bibliotecas no [índice de pacote do SDK do Azure para Python](https://github.com/Azure/azure-sdk-for-python/blob/master/packages.md)

Para obter instruções mais detalhadas sobre como instalar as bibliotecas e importá-las em projetos, consulte [Instalar o SDK](python-sdk-azure-install.md). Em seguida, examine a [Introdução ao SDK](python-sdk-azure-get-started.yml) para configurar a autenticação e executar o código de exemplo em sua própria assinatura do Azure.

> [!TIP]
> Para obter informações sobre as alterações no SDK, consulte as [notas sobre a versão do SDK](https://azure.github.io/azure-sdk/).

[!INCLUDE [chrome-note](includes/chrome-note.md)]

## <a name="connect-and-use-azure-services"></a>Conectar e usar os serviços do Azure

Várias *bibliotecas de clientes* no SDK ajudam você a se conectar a recursos existentes do Azure e usá-los em seus aplicativos, como carregar arquivos, acessar dados de tabela ou trabalhar com vários Serviços Cognitivos do Azure. Com o SDK, você trabalha com esses recursos usando paradigmas de programação do Python conhecidos em vez de usar a API REST genérica do serviço.

Por exemplo, suponha que você deseja carregar um blob para uma conta de Armazenamento do Azure provisionada anteriormente. A primeira etapa é instalar a biblioteca apropriada:

```bash
pip install azure-storage-blob
```

Em seguida, importe a biblioteca em seu código:

```python
from azure.storage.blob import BlobClient
```

Por fim, use a API da biblioteca para se conectar e carregar os dados. Neste exemplo, a cadeia de conexão e o nome do contêiner já estão provisionados em sua conta de armazenamento. O nome do blob é o mesmo que você atribui aos dados carregados:

```python
blob = BlobClient.from_connection_string("my_connection_string", container="mycontainer", blob="my_blob")

with open("./SampleSource.txt", "rb") as data:
    blob.upload_blob(data)
```

Para obter detalhes sobre como trabalhar com cada biblioteca específica, consulte o arquivo *README.md* ou *README.rst* localizado na pasta de projeto da biblioteca em nosso [repositório GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk). Confira também os [Exemplos do Azure](https://docs.microsoft.com/samples/browse/?languages=python) disponíveis.

Também é possível encontrar trechos de código adicionais na [documentação de referência](/python/api?view=azure-python)

### <a name="the-azure-core-library"></a>A biblioteca Principal do Azure

Atualmente, estamos atualizando nossas bibliotecas de clientes Python para compartilhar funcionalidades importantes, como tentativas, registro em log, protocolos de transporte, protocolos de autenticação etc. Essa funcionalidade compartilhada está presente na biblioteca [azure-core](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/core/azure-core). Para obter detalhes sobre a biblioteca e suas diretrizes, consulte as [Diretrizes do Python: Introdução](https://azure.github.io/azure-sdk/python_introduction.html).

As bibliotecas que atualmente funcionam com a biblioteca Principal são as seguintes:

- `azure-storage-blob`
- `azure-storage-queue`
- `azure-keyvault-keys`
- `azure-keyvault-secrets`

## <a name="manage-azure-resources"></a>Gerenciar recursos do Azure

O SDK do Azure para Python também inclui várias bibliotecas para ajudar você a criar, provisionar e, caso contrário, gerenciar os próprios recursos do Azure. Nos referimos a elas como *bibliotecas de gerenciamento*. Cada biblioteca de gerenciamento é denominada `azure-mgmt-<service name>`. Com as bibliotecas de gerenciamento é possível escrever códigos Python para realizar as mesmas tarefas usando o [portal do Azure](https://portal.azure.com) ou a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

Por exemplo, suponha que você queira criar uma instância do SQL Server. Primeiro, instale a biblioteca de gerenciamento apropriada:

```bash
pip install azure-mgmt-sql
```

No código Python, importe a biblioteca:

```python
from azure.mgmt.sql import SqlManagementClient

```

Em seguida, crie o objeto cliente de gerenciamento usando suas credenciais e a ID de assinatura do Azure:

```python
sql_client = SqlManagementClient(credentials, subscription_id)
```

Por fim, use esse objeto cliente para criar o recurso, usando um nome de grupo de recursos apropriado, o nome do servidor, o local e as credenciais de administrador:

```python
server = sql_client.servers.create_or_update(
    'myresourcegroup',
    'myservername',
    {
        'location': 'eastus',
        'version': '12.0', # Required for create
        'administrator_login': 'mysecretname', # Required for create
        'administrator_login_password': 'HusH_Sec4et' # Required for create
    }
)
```

Assim como nas bibliotecas de clientes é possível encontrar detalhes sobre como trabalhar com cada biblioteca de gerenciamento no *README.md* ou no arquivo *README.rst* localizado na pasta de projeto da biblioteca em nosso [repositório GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk).

Também é possível encontrar trechos de código adicionais na [documentação de referência](/python/api?view=azure-python). 

## <a name="get-help-and-give-feedback"></a>Obter ajuda e fazer comentários

- Visite a [documentação do SDK do Azure para Python](https://aka.ms/python-docs)
- Poste perguntas para a comunidade no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sdk-python)
- Problemas em aberto no SDK no [GitHub](https://github.com/Azure/azure-sdk-for-python/issues)
- Tweet em [@AzureSDK](https://twitter.com/AzureSdk/)
