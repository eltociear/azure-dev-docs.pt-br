---
title: Provisionar o Armazenamento do Microsoft Azure com as bibliotecas do Azure para Python
description: Use o SDK do Azure para bibliotecas do Python para provisionar um contêiner de blob em uma conta de Armazenamento do Microsoft Azure e, em seguida, carregue um arquivo para esse contêiner.
ms.date: 05/29/2020
ms.topic: conceptual
ms.openlocfilehash: 648abeb105f1c575d5454af09cd2279c059d7b98
ms.sourcegitcommit: c6642cae6fdb5e3025ed66fcd4ef89792c3b436a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2020
ms.locfileid: "86405717"
---
# <a name="example-provision-azure-storage-using-the-azure-libraries-for-python"></a>Exemplo: Provisionar o Armazenamento do Azure usando as bibliotecas do Azure para Python

Neste artigo, você aprenderá a usar as bibliotecas de gerenciamento do Azure em um script do Python para provisionar um grupo de recursos que contém a conta de Armazenamento do Microsoft Azure e um contêiner de armazenamento de Blobs. ([Comandos de CLI do Azure equivalentes](#for-reference-equivalent-azure-cli-commands) são fornecidos posteriormente neste artigo.)

Depois de provisionar os recursos, consulte [Exemplo: Usar o Armazenamento do Microsoft Azure](azure-sdk-example-storage-use.md) para usar as bibliotecas de clientes do Azure no código do aplicativo Python para carregar um arquivo nesse contêiner de armazenamento de Blobs.

Todos os comandos neste artigo funcionam da mesma forma no bash do Linux/Mac OS e nos shells de comando do Windows, a menos que haja uma observação.

## <a name="1-set-up-your-local-development-environment"></a>1: Configurar seu ambiente de desenvolvimento

Se ele ainda não foi configurado, siga todas as instruções em [Configurar o ambiente de desenvolvimento do Python local para o Azure](configure-local-development-environment.md).

Não se esqueça de criar uma entidade de serviço para o desenvolvimento local e de criar e ativar um ambiente virtual para este projeto.

## <a name="2-install-the-needed-azure-library-packages"></a>2: Instalar os pacotes de biblioteca do Azure necessários

1. Crie um arquivo *requirements. txt* que liste as bibliotecas de gerenciamento usadas neste exemplo:

    ```txt
    azure-mgmt-resource
    azure-mgmt-storage
    azure-cli-core
    ```

1. Em seu terminal com o ambiente virtual ativado, instale os requisitos:

    ```cmd
    pip install -r requirements.txt
    ```

## <a name="3-write-code-to-provision-storage-resources"></a>3: Escrever um código para provisionar recursos de armazenamento

Esta seção descreve como provisionar recursos de armazenamento do código Python. Se preferir, você também poderá provisionar recursos por meio do portal do Azure ou por meio dos [comandos equivalentes da CLI do Azure](#for-reference-equivalent-azure-cli-commands).

Crie um arquivo Python com o nome *provision_blob.py* com o código a seguir. Os comentários explicam os detalhes:

```python
import os, random

# Import the needed management objects from the libraries. The azure.common library
# is installed automatically with the other libraries.
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.storage import StorageManagementClient

# Obtain the management object for resources, using the credentials from the CLI login.
resource_client = get_client_from_cli_profile(ResourceManagementClient)

# Constants we need in multiple places: the resource group name and the region
# in which we provision resources. You can change these values however you want.
RESOURCE_GROUP_NAME = "PythonAzureExample-Storage-rg"
LOCATION = "centralus"

# Step 1: Provision the resource group.
rg_result = resource_client.resource_groups.create_or_update(RESOURCE_GROUP_NAME,
    { "location": LOCATION })

print(f"Provisioned resource group {rg_result.name}")

# For details on the previous code, see Example: Provision a resource group
# at https://docs.microsoft.com/azure/developer/python/azure-sdk-example-resource-group

# Step 2: Provision the storage account, starting with a management object.

storage_client = get_client_from_cli_profile(StorageManagementClient)

# This example uses the CLI profile credentials because we assume the script
# is being used to provision the resource in the same way the Azure CLI would be used.

STORAGE_ACCOUNT_NAME = f"pythonazurestorage{random.randint(1,100000):05}"

# You can replace the storage account here with any unique name. A random number is used
# by default, but note that the name changes every time you run this script.
# The name must be 3-24 lower case letters and numbers only.


# Check if the account name is available. Storage account names must be unique across
# Azure because they're used in URLs.
availability_result = storage_client.storage_accounts.check_name_availability(STORAGE_ACCOUNT_NAME)

if not availability_result.name_available:
    print(f"Storage name {STORAGE_ACCOUNT_NAME} is already in use. Try another name.")
    exit()

# The name is available, so provision the account
poller = storage_client.storage_accounts.create(RESOURCE_GROUP_NAME, STORAGE_ACCOUNT_NAME,
    {
        "location" : LOCATION,
        "kind": "StorageV2",
        "sku": {"name": "Standard_LRS"}
    }
)

# Long-running operations return a poller object; calling poller.result()
# waits for completion.
account_result = poller.result()
print(f"Provisioned storage account {account_result.name}")

# Step 3: Retrieve the account's primary access key and generate a connection string.
keys = storage_client.storage_accounts.list_keys(RESOURCE_GROUP_NAME, STORAGE_ACCOUNT_NAME)

print(f"Primary key for storage account: {keys.keys[0].value}")

conn_string = f"DefaultEndpointsProtocol=https;EndpointSuffix=core.windows.net;AccountName={STORAGE_ACCOUNT_NAME};AccountKey={keys.keys[0].value}"

print(f"Connection string: {conn_string}")

# Step 4: Provision the blob container in the account (this call is synchronous)
CONTAINER_NAME = "blob-container-01"
container = storage_client.blob_containers.create(RESOURCE_GROUP_NAME, STORAGE_ACCOUNT_NAME, CONTAINER_NAME, {})

# The fourth argument is a required BlobContainer object, but because we don't need any
# special values there, so we just pass empty JSON.

print(f"Provisioned blob container {container.name}")
```

Esse código usa os métodos de autenticação baseados em CLI (`get_client_from_cli_profile`), pois demonstra ações que você pode efetuar diretamente com a CLI do Azure. Em ambos os casos, você usará a mesma identidade para autenticação.

Para colocar esse código em um script de produção, você deve usar `DefaultAzureCredential` (recomendado) ou um método baseado na entidade de serviço, como descrito em [Como autenticar aplicativos Python com os serviços do Azure](azure-sdk-authenticate.md).

### <a name="reference-links-for-classes-used-in-the-code"></a>Links de referência para classes usadas no código

- [ResourceManagementClient (azure.mgmt.resource)](/python/api/azure-mgmt-resource/azure.mgmt.resource.resourcemanagementclient?view=azure-python)
- [StorageManagementClient (azure.mgmt.storage)](/python/api/azure-mgmt-storage/azure.mgmt.storage.storagemanagementclient?view=azure-python)

## <a name="4-run-the-script"></a>4. Executar o script

```cmd
python provision_blob.py
```

O script levará alguns minutos para ser concluído.

## <a name="5-verify-the-resources"></a>5: Verificar os recursos

1. Abra o [portal do Azure](https://portal.azure.com) para verificar se o grupo de recursos e a conta de armazenamento foram provisionados como esperado. Talvez seja necessário selecionar **Mostrar tipos ocultos** no grupo de recursos para ver uma conta de armazenamento provisionada de um script Python:

    ![Página do portal do Azure para o novo grupo de recursos, mostrando a conta de armazenamento](media/azure-sdk-example-storage/portal-show-hidden-types.png)

1. Selecione a conta de armazenamento e, em seguida, selecione **Serviço Blob** > **Contêineres** no menu à esquerda para verificar se o "bloc-container-01" aparece:

    ![Página do portal do Azure para a conta de armazenamento mostrando o contêiner de blob](media/azure-sdk-example-storage/portal-show-blob-containers.png)

1. Se você quiser tentar usar esses recursos provisionados do código do aplicativo, continue com [Exemplo: Usar o Armazenamento do Microsoft Azure](azure-sdk-example-storage-use.md).

Para obter mais um exemplo que usa a biblioteca de gerenciamento do Armazenamento do Microsoft Azure, consulte [Gerenciar amostra de Armazenamento do Python](https://docs.microsoft.com/samples/azure-samples/storage-python-manage/storage-python-manage/).

### <a name="for-reference-equivalent-azure-cli-commands"></a>Para referência: comandos equivalentes da CLI do Azure

Os comandos a seguir da CLI do Azure executam as mesmas etapas de provisionamento do script Python:

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
rem Provision the resource group

az group create -n PythonAzureExample-Storage-rg -l centralus

rem Provision the storage account

az storage account create -g PythonAzureExample-Storage-rg -l centralus ^
    -n pythonazurestorage12345 --kind StorageV2 --sku Standard_LRS

rem Retrieve the connection string

az storage account show-connection-string -g PythonAzureExample-Storage-rg ^
    -n pythonazurestorage12345

rem Provision the blob container; NOTE: this command assumes you have an environment variable
rem named AZURE_STORAGE_CONNECTION_STRING with the connection string for the storage account.

set AZURE_STORAGE_CONNECTION_STRING=<connection_string>
az storage container create --account-name pythonazurestorage12345 -n blob-container-01
```

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
# Provision the resource group

az group create -n PythonAzureExample-Storage-rg -l centralus

# Provision the storage account

az storage account create -g PythonAzureExample-Storage-rg -l centralus \
    -n pythonazurestorage12345 --kind StorageV2 --sku Standard_LRS

# Retrieve the connection string

az storage account show-connection-string -g PythonAzureExample-Storage-rg \
    -n pythonazurestorage12345

# Provision the blob container; NOTE: this command assumes you have an environment variable
# named AZURE_STORAGE_CONNECTION_STRING with the connection string for the storage account.

AZURE_STORAGE_CONNECTION_STRING=<connection_string>
az storage container create --account-name pythonazurestorage12345 -n blob-container-01
```

---

## <a name="6-clean-up-resources"></a>6: Limpar os recursos

Deixe os recursos no lugar se desejar seguir o artigo [Exemplo: Use o Armazenamento do Microsoft Azure](azure-sdk-example-storage-use.md) para usar esses recursos no código do aplicativo.

Caso contrário, execute o comando a seguir para evitar cobranças contínuas em sua assinatura.

```azurecli
az group delete -n PythonAzureExample-Storage-rg
```

Você também pode usar o método [`ResourceManagementClient.resource_groups.delete`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.resourcegroupsoperations?view=azure-python#delete-resource-group-name--custom-headers-none--raw-false--polling-true----operation-config-) para excluir um grupo de recursos do código.

## <a name="see-also"></a>Confira também

- [Exemplo: Usar o Armazenamento do Microsoft Azure](azure-sdk-example-storage-use.md)
- [Exemplo: Provisionar um grupo de recursos](azure-sdk-example-resource-group.md)
- [Exemplo: Provisionar um aplicativo Web e implantar o código](azure-sdk-example-web-app.md)
- [Exemplo: Provisionar e consultar um banco de dados](azure-sdk-example-database.md)
- [Exemplo: Provisionar uma máquina virtual >>>](azure-sdk-example-virtual-machines.md)
