---
title: SDK do Azure para Python
description: Visão geral dos recursos e das funcionalidades do SDK do Azure para Python que ajudam os desenvolvedores a serem mais produtivos ao provisionar, usar e gerenciar recursos do Azure.
ms.date: 03/17/2020
ms.topic: conceptual
ms.openlocfilehash: 3d24a512420610f37285a03fe6a39d81e97510ee
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82026109"
---
# <a name="azure-sdk-for-python"></a>SDK do Azure para Python

O SDK do Azure de software livre para Python simplifica o provisionamento, o uso e o gerenciamento dos recursos do Azure com base no código de aplicativo do Python. O SDK é compatível com Python 2.7 e Python 3.5.3 ou posterior.

O SDK é composto por bibliotecas de componentes individuais, cada uma das quais você instala usando `pip install <library>`. Instruções mais detalhadas são encontradas em [Instalar o SDK](azure-sdk-install.md). A [Documentação do SDK do Azure para Python](https://azure.github.io/azure-sdk-for-python/) fornece os nomes das bibliotecas.

Você também pode seguir o passo a passo, [Introdução ao SDK do Azure para Python](azure-sdk-get-started.yml), para experimentar as bibliotecas por conta própria.

> [!TIP]
> Para obter informações sobre as alterações no SDK, consulte as [notas sobre a versão do SDK](https://azure.github.io/azure-sdk/).

[!INCLUDE [chrome-note](includes/chrome-note.md)]

## <a name="management-and-client-libraries"></a>Bibliotecas de clientes e de gerenciamento

O SDK do Azure para Python contém dois tipos de bibliotecas:

- As bibliotecas de *gerenciamento* ajudam a provisionar e gerenciar os recursos do Azure, como você pode fazer por meio do [portal do Azure](https://portal.azure.com) ou com ferramentas de linha de comando, tais como a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) e o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/). As bibliotecas de gerenciamento são geralmente usadas em scripts de configuração e de implantação. (Na verdade, a CLI do Azure propriamente dita é escrita com as bibliotecas de gerenciamento do Python.)

- As bibliotecas de *clientes* fornecem os meios para que o código do aplicativo interaja com serviços já provisionados usando expressões idiomáticas do Python naturais. Nos bastidores, as bibliotecas de clientes usam as APIs REST do Azure, mas usando o SDK, você não precisa se preocupar com os detalhes do REST.

As seções a seguir fornecem mais detalhes e exemplos para cada categoria.

## <a name="manage-azure-resources-with-management-libraries"></a>Gerenciar recursos do Azure com bibliotecas de gerenciamento

As bibliotecas de gerenciamento do SDK do Azure para Python, cada uma das quais é nomeada `azure-mgmt-<service name>`, ajudam você a criar, provisionar e gerenciar os recursos do Azure.

Por exemplo, suponha que você queira criar uma instância do SQL Server. Primeiro, instale a biblioteca de gerenciamento apropriada:

```bash
pip install azure-mgmt-sql
```

No código Python, importe a biblioteca:

```python
from azure.mgmt.sql import SqlManagementClient
```

Em seguida, crie o objeto cliente de gerenciamento usando suas credenciais (confira [Autenticar com o SDK](azure-sdk-authenticate.md)) e a ID da assinatura do Azure:

```python
sql_client = SqlManagementClient(credentials, subscription_id)
```

Por fim, use esse objeto de cliente de gerenciamento para criar o recurso, usando um nome de grupo de recursos apropriado, o nome do servidor, o local e as credenciais de administrador:

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

Para obter detalhes sobre como trabalhar com cada biblioteca de gerenciamento, veja o arquivo *README.md* ou *README.rst* localizado na pasta de projeto da biblioteca no [repositório GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk) do SDK. Também é possível encontrar trechos de código adicionais na [documentação de referência](/python/api?view=azure-python) e nas [Amostras do Azure](https://docs.microsoft.com/samples/browse/?languages=python&products=azure).

## <a name="connect-and-use-azure-services-with-client-libraries"></a>Conectar e usar os serviços do Azure com bibliotecas de clientes

As *bibliotecas de clientes* do SDK do Azure ajudam você a se conectar a recursos existentes do Azure e usá-los em seus aplicativos, como carregar arquivos, acessar dados de tabela ou trabalhar com vários Serviços Cognitivos do Azure. Com o SDK, você trabalha com esses recursos usando paradigmas de programação do Python conhecidos em vez de usar a API REST genérica do serviço. (Os serviços que não têm uma API REST não são representados por uma biblioteca de clientes.)

Por exemplo, suponha que você implantou um aplicativo Web para o Serviço de Aplicativo do Azure e deseja adicionar a capacidade de carregar um arquivo em uma conta do Armazenamento do Azure. A primeira etapa é instalar a biblioteca apropriada:

```bash
pip install azure-storage-blob
```

Em seguida, importe a biblioteca em seu código:

```python
from azure.storage.blob import BlobClient
```

Por fim, use a API da biblioteca para se conectar e carregar os dados. Neste exemplo, a cadeia de conexão e o nome do contêiner já estão provisionados em sua conta de armazenamento. O nome do blob é o mesmo que você atribui aos dados carregados:

```python
blob = BlobClient.from_connection_string("my_connection_string", container_name="mycontainer", blob_name="my_blob")

with open("./SampleSource.txt", "rb") as data:
    blob.upload_blob(data)
```

Para obter detalhes sobre como trabalhar com cada biblioteca de gerenciamento, veja o arquivo *README.md* ou *README.rst* localizado na pasta de projeto da biblioteca no [repositório GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk) do SDK. Também é possível encontrar trechos de código adicionais na [documentação de referência](/python/api?view=azure-python) e nas [Amostras do Azure](https://docs.microsoft.com/samples/browse/?languages=python&products=azure).

### <a name="the-azure-core-library"></a>A biblioteca Principal do Azure

Atualmente estamos atualizando as bibliotecas de clientes do SDK do Azure para Python para compartilhar padrões de nuvem comuns, como protocolos de autenticação, registro em log, rastreamento, protocolos de transporte, respostas em buffer e novas tentativas. Essa funcionalidade compartilhada está presente na biblioteca [azure-core](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/core/azure-core). As bibliotecas que atualmente funcionam com a biblioteca principal estão listadas na página [Versões mais recentes do SDK do Azure](https://azure.github.io/azure-sdk/releases/latest/#python-packages).

Para obter detalhes sobre a Biblioteca Principal do Azure e as respectivas diretrizes, veja as [Diretrizes do Python: Introdução](https://azure.github.io/azure-sdk/python_introduction.html).

## <a name="get-help-and-give-feedback"></a>Obter ajuda e fazer comentários

- Visite a [documentação do SDK do Azure para Python](https://aka.ms/python-docs)
- Poste perguntas para a comunidade no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sdk-python)
- Problemas em aberto no SDK no [GitHub](https://github.com/Azure/azure-sdk-for-python/issues)
- Mencione [@AzureSDK](https://twitter.com/AzureSdk/) no Twitter
