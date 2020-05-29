---
title: Provisionar e usar o Armazenamento do Microsoft Azure com o SDK do Azure para Python
description: Use o SDK do Azure para bibliotecas do Python para provisionar um contêiner de blob em uma conta de Armazenamento do Microsoft Azure e, em seguida, carregue um arquivo para esse contêiner.
ms.date: 05/12/2020
ms.topic: conceptual
ms.openlocfilehash: 904ca66f6e4c065fa0705d2e35b8a7bb46396a0d
ms.sourcegitcommit: b69db02c3358ce7899cef105508e3d17fafc84b2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83759939"
---
# <a name="example-use-the-azure-sdk-with-azure-storage"></a>Exemplo: Usar o SDK do Azure com o Armazenamento do Microsoft Azure

Neste artigo, você aprenderá a usar as bibliotecas de gerenciamento do SDK do Azure em um script do Python para provisionar um grupo de recursos que contém a conta do Armazenamento do Microsoft Azure e um contêiner de armazenamento de Blobs. Em seguida, você aprenderá a usar as bibliotecas de cliente do SDK do Azure no código do aplicativo Python para carregar um arquivo nesse contêiner de armazenamento de blobs.

Todos os comandos neste artigo funcionam da mesma forma no bash do Linux/Mac OS e nos shells de comando do Windows, a menos que haja uma observação.

## <a name="1-set-up-your-local-development-environment"></a>1: Configurar seu ambiente de desenvolvimento

Se ele ainda não foi configurado, siga todas as instruções em [Configurar o ambiente de desenvolvimento do Python local para o Azure](configure-local-development-environment.md).

Não se esqueça de criar uma entidade de serviço para o desenvolvimento local e de criar e ativar um ambiente virtual para este projeto.

## <a name="2-install-the-needed-management-libraries"></a>2: Instalar as bibliotecas de gerenciamento necessárias

1. Crie um arquivo *requirements. txt* que liste as bibliotecas de gerenciamento usadas neste exemplo:

    ```txt
    azure-mgmt-resource
    azure-mgmt-storage
    azure-cli-core
    ```

1. Em seu terminal com o ambiente virtual ativado, instale os requisitos:

    ```bash
    pip install -r requirements.txt
    ```

## <a name="3-write-code-to-provision-storage-resources"></a>3: Escrever um código para provisionar recursos de armazenamento

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
RESOURCE_GROUP_NAME = "PythonSDKExample-Storage-rg"
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

STORAGE_ACCOUNT_NAME = f"pythonsdkstorage{random.randint(1,100000):05}"

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

## <a name="4-run-the-script"></a>4. Executar o script:

```bash
python provision_blob.py
```

O script levará alguns minutos para ser concluído.

## <a name="5-verify-the-resources"></a>5: Verificar os recursos

1. Abra o [portal do Azure](https://portal.azure.com) para verificar se o grupo de recursos e a conta de armazenamento foram provisionados como esperado. Talvez seja necessário selecionar **Mostrar tipos ocultos** no grupo de recursos para ver uma conta de armazenamento provisionada de um script Python:

    ![Página do portal do Azure para o novo grupo de recursos, mostrando a conta de armazenamento](media/azure-sdk-example-storage/portal-show-hidden-types.png)

1. Selecione a conta de armazenamento e, em seguida, selecione **Serviço Blob** > **Contêineres** no menu à esquerda para verificar se o "bloc-container-01" aparece:

    ![Página do portal do Azure para a conta de armazenamento mostrando o contêiner de blob](media/azure-sdk-example-storage/portal-show-blob-containers.png)

Para obter mais um exemplo que usa a biblioteca de gerenciamento do Armazenamento do Microsoft Azure, consulte [Gerenciar amostra de Armazenamento do Python](https://docs.microsoft.com/samples/azure-samples/storage-python-manage/storage-python-manage/).

### <a name="for-reference-equivalent-azure-cli-commands"></a>Para referência: comandos equivalentes da CLI do Azure

Os comandos a seguir da CLI do Azure executam as mesmas etapas de provisionamento do script Python:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
# Provision the resource group

az group create -n PythonSDKExample-Storage-rg -l centralus

# Provision the storage account

az storage account create -g PythonSDKExample-Storage-rg -l centralus \
    -n pythonsdkstorage12345 --kind StorageV2 --sku Standard_LRS

# Retrieve the connection string

az storage account show-connection-string -g PythonSDKExample-Storage-rg \
    -n pythonsdkstorage12345

# Provision the blob container; NOTE: this command assumes you have an environment variable
# named AZURE_STORAGE_CONNECTION_STRING with the connection string for the storage account.

AZURE_STORAGE_CONNECTION_STRING=<connection_string>
az storage container create --account-name pythonsdkstorage12345 -n blob-container-01
```

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
# Provision the resource group

az group create -n PythonSDKExample-Storage-rg -l centralus

# Provision the storage account

az storage account create -g PythonSDKExample-Storage-rg -l centralus ^
    -n pythonsdkstorage12345 --kind StorageV2 --sku Standard_LRS

# Retrieve the connection string

az storage account show-connection-string -g PythonSDKExample-Storage-rg ^
    -n pythonsdkstorage12345

# Provision the blob container; NOTE: this command assumes you have an environment variable
# named AZURE_STORAGE_CONNECTION_STRING with the connection string for the storage account.

set AZURE_STORAGE_CONNECTION_STRING=<connection_string>
az storage container create --account-name pythonsdkstorage12345 -n blob-container-01
```

---

## <a name="6-use-resources-through-the-sdk-client-libraries"></a>6: Usar recursos por meio das bibliotecas de cliente do SDK

As seções a seguir mostram duas maneiras de acessar o contêiner de blob provisionado na seção anterior. Esses exemplos carregam especificamente um blob de arquivo para esse contêiner usando as bibliotecas de cliente de SDK apropriadas.

Siga as [etapas comuns](#common-steps-for-both-methods) para testar o código você mesmo.

O primeiro método autentica o aplicativo com `DefaultAzureCredential` conforme descrito em [Como autenticar aplicativos Python](azure-sdk-authenticate.md#authenticate-with-defaultazurecredential). Com esse método, você precisa primeiro atribuir as permissões apropriadas para a identidade do aplicativo, que é a prática recomendada.

O segundo método usa uma cadeia de conexão para acessar a conta de armazenamento diretamente. Embora esse método pareça mais simples, ele tem duas desvantagens significativas:

- Uma cadeia de conexão autentica inerentemente o agente de conexão com a *conta* de armazenamento em vez de conectar com os recursos individuais dentro dessa conta. Como resultado, uma cadeia de conexão concede uma autorização mais ampla do que pode ser necessária.

- Uma cadeia de conexão contém uma chave de acesso em texto sem formatação e, portanto, apresenta possíveis vulnerabilidades se ela estiver construída ou protegida incorretamente. Se essa cadeia de conexão for exposta, ela poderá ser usada para acessar uma ampla gama de recursos na conta de Armazenamento.

Por esses motivos, o código de produção deve usar o método de autenticação. Para experimentação, no entanto, a cadeia de conexão pode ser usada.

### <a name="common-steps-for-both-methods"></a>Etapas comuns para ambos os métodos

1. Em seu arquivo *requirements.txt*, adicione uma linha para as bibliotecas de cliente necessárias e salve o arquivo:

    ```text
    azure-storage-blob
    azure-identity
    ```

1. No seu terminal ou prompt de comando, reinstale os requisitos:

    ```bash
    pip install -r requirements.txt
    ```

1. Crie um arquivo de origem chamado *sample-source.txt* (como o código espera), com conteúdo semelhante ao seguinte:

    ```text
    Hello there, Azure Storage. I'm a friendly file ready to be stored in a blob.
    ```

### <a name="use-blob-storage-with-authentication"></a>Usar o armazenamento de blobs com autenticação

1. Criar uma variável de ambiente com nome `AZURE_STORAGE_BLOB_URL`:

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    AZURE_STORAGE_BLOB_URL=https://pythonsdkstorage12345.blob.core.windows.net
    ```

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```cmd
    set AZURE_STORAGE_BLOB_URL=https://pythonsdkstorage12345.blob.core.windows.net
    ```

    ---

    Substitua "pythonsdkstorage12345" pelo nome da sua conta de armazenamento específica.

1. Crie um arquivo chamado*use_blob_auth.py* com o seguinte código. Os comentários explicam as etapas.

    ```python
    import os
    from azure.identity import DefaultAzureCredential

    # Import the client object from the SDK library
    from azure.storage.blob import BlobClient

    credential = DefaultAzureCredential()

    # Retrieve the storage blob service URL, which is of the form
    # https://pythonsdkstorage12345.blob.core.windows.net/
    storage_url = os.environ["AZURE_STORAGE_BLOB_URL"]

    # Create the client object using the storage URL and the credential
    blob_client = BlobClient(storage_url, container_name="blob-container-01", blob_name="sample-blob.txt", credential=credential)

    # Open a local file and upload its contents to Blob Storage
    with open("./sample-source.txt", "rb") as data:
        blob_client.upload_blob(data)
    ```

1. Tentativa de executar o código (que falha intencionalmente):

    ```bash
    python use_blob_auth.py
    ```

    Como a entidade de serviço local que está sendo usada não tem permissão para acessar o contêiner de blob, você verá o erro: "Esta solicitação não está autorizada a executar esta operação usando essa permissão".

1. Para conceder permissões para o contêiner à entidade de serviço, use o comando da CLI do Azure [az role assignment create](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) (é longo!):

    # <a name="bash"></a>[Bash](#tab/bash)

    ```azurecli
    az role assignment create --assignee $AZURE_CLIENT_ID \
        --role "Storage Blob Data Contributor" \
        --scope "/subscriptions/$AZURE_SUBSCRIPTION_ID/resourceGroups/PythonSDKExample-Storage-rg/providers/Microsoft.Storage/storageAccounts/pythonsdkstorage12345/blobServices/default/containers/blob-container-01"
    ```

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```azurecli
    az role assignment create --assignee %AZURE_CLIENT_ID% ^
        --role "Storage Blob Data Contributor" ^
        --scope "/subscriptions/%AZURE_SUBSCRIPTION_ID%/resourceGroups/PythonSDKExample-Storage-rg/providers/Microsoft.Storage/storageAccounts/pythonsdkstorage12345/blobServices/default/containers/blob-container-01"
    ```

    ---

    O argumento `--scope` identifica o local em que essa atribuição de função se aplica. Neste exemplo, você concede a função "Colaborador de Dados de Blob de Armazenamento" ao contêiner *específico* chamado "blob-container-01".

    Substitua `pythonsdkstorage12345` pelo nome exato da sua conta de armazenamento. Você também pode ajustar o nome do grupo de recursos e do contêiner de blob, se necessário. Se você usar o nome errado, verá o erro "Não é possível executar a operação solicitada no recurso aninhado. Recurso pai 'pythonsdkstorage12345' not found."

    O argumento `--scope` neste comando também usa as variáveis de ambiente AZURE_CLIENT_ID e AZURE_SUBSCRIPTION_ID, que você já deve ter definido em seu ambiente local para sua entidade de serviço, seguindo [Configurar seu ambiente de desenvolvimento Python local para Azure](configure-local-development-environment.md).

1. Depois de aguardar um minuto ou dois para as permissões se propagarem, execute o código novamente para verificar se ele funciona agora. Se você vir o erro de permissão novamente, aguarde um pouco mais e tente executar o código outra vez.

Para obter mais informações sobre escopos e atribuições de funções, consulte [Como atribuir permissões de função](how-to-assign-role-permissions.md).

### <a name="use-blob-storage-with-a-connection-string"></a>Usar o armazenamento de blobs com uma cadeia de conexão

1. Crie um arquivo Python com o nome *use_blob_conn_string.py* com o seguinte código. Os comentários explicam as etapas.

    ```python
    import os

    # Import the client object from the SDK library
    from azure.storage.blob import BlobClient

    # Retrieve the connection string from an environment variable.
    conn_string = os.environ["AZURE_STORAGE_CONNECTION_STRING"]

    # Create the client object for the resource identified by the connection string,
    # indicating also the blob container and the name of the specific blob we want.
    blob_client = BlobClient.from_connection_string(conn_string, container_name="blob-container-01", blob_name="sample-blob.txt")

    # Open a local file and upload its contents to Blob Storage
    with open("./sample-source.txt", "rb") as data:
        blob_client.upload_blob(data)
    ```

1. Executar o código:

    ```bash
    python use_blob_conn_string.py
    ```

Novamente, embora esse método seja simples, uma cadeia de conexão autoriza todas as operações em uma conta de armazenamento. Com o código de produção, é melhor usar permissões específicas, conforme descrito na seção anterior.

### <a name="verify-blob-creation"></a>Verificar a criação de blob

Depois de executar o código usando qualquer um dos métodos, vá para o [portal do Azure](https://portal.azure.com), navegue até o contêiner de blobs para verificar se existe um novo blob chamado *sample-blob.txt* com o mesmo conteúdo do arquivo *sample-source.txt*:

![Página do portal do Azure para o contêiner de blob, mostrando o arquivo carregado](media/azure-sdk-example-storage/portal-blob-container-file.png)

## <a name="7-clean-up-resources"></a>7: Limpar os recursos

```azurecli
az group delete -n PythonSDKExample-Storage-rg
```

Execute esse comando se você não precisar manter os recursos provisionados neste exemplo e quiser evitar encargos contínuos na sua assinatura.

Você também pode usar o método [`ResourceManagementClient.resource_groups.delete`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.resourcegroupsoperations?view=azure-python#delete-resource-group-name--custom-headers-none--raw-false--polling-true----operation-config-) para excluir um grupo de recursos do código.

## <a name="next-step"></a>Próxima etapa

> [!div class="nextstepaction"]
> [Exemplo: Provisionar um aplicativo Web e implantar o código >>>](azure-sdk-example-web-app.md)
