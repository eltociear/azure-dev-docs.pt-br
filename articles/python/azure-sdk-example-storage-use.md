---
title: Provisionar e usar o Armazenamento do Microsoft Azure com o SDK do Azure para Python
description: Use o SDK do Azure para bibliotecas do Python para acessar um contêiner de blob pré-provisionado em uma conta de Armazenamento do Microsoft Azure e, em seguida, carregue um arquivo para esse contêiner.
ms.date: 05/29/2020
ms.topic: conceptual
ms.openlocfilehash: 50d9b781e0ec3a1a9f752a9ec193720d99b88f4d
ms.sourcegitcommit: db56786f046a3bde1bd9b0169b4f62f0c1970899
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84329654"
---
# <a name="example-use-the-azure-libraries-with-azure-storage"></a>Exemplo: Usar as bibliotecas do Azure com o Armazenamento do Microsoft Azure

Esse exemplo demonstrou como usar as bibliotecas de cliente do Azure no código do aplicativo Python para carregar um arquivo nesse contêiner de armazenamento de Blobs. O exemplo supõe que você tenha provisionado os recursos mostrados no [Exemplo: Provisione o Armazenamento do Microsoft Azure](azure-sdk-example-storage.md).

Todos os comandos neste artigo funcionam da mesma forma no bash do Linux/Mac OS e nos shells de comando do Windows, a menos que haja uma observação.

## <a name="1-set-up-your-local-development-environment"></a>1: Configurar seu ambiente de desenvolvimento

Se ele ainda não foi configurado, siga todas as instruções em [Configurar o ambiente de desenvolvimento do Python local para o Azure](configure-local-development-environment.md).

Não se esqueça de criar uma entidade de serviço para o desenvolvimento local e de criar e ativar um ambiente virtual para este projeto.

## <a name="2-install-library-packages"></a>2: Instalar pacotes de biblioteca

1. Em seu arquivo *requirements.txt*, adicione uma linha para os pacotes de biblioteca de clientes necessários e salve o arquivo:

    ```text
    azure-storage-blob
    azure-identity
    ```

1. No seu terminal ou prompt de comando, reinstale os requisitos:

    ```cmd
    pip install -r requirements.txt
    ```

## <a name="3-create-a-file-to-upload"></a>3: Criar um arquivo para carregar

Crie um arquivo de origem chamado *sample-source.txt* (como o código espera), com conteúdo semelhante ao seguinte:

```text
Hello there, Azure Storage. I'm a friendly file ready to be stored in a blob.
```

## <a name="4-use-blob-storage-from-app-code"></a>4: Usar o armazenamento de blobs do código do aplicativo

As seções a seguir (numeradas 4a e 4b) demonstram dois meios para acessar o contêiner de blob.

O [primeiro método (4a)](#4a-use-blob-storage-with-authentication) autentica o aplicativo com `DefaultAzureCredential` conforme descrito em [Como autenticar aplicativos Python](azure-sdk-authenticate.md#authenticate-with-defaultazurecredential). Com esse método, você precisa primeiro atribuir as permissões apropriadas para a identidade do aplicativo, que é a prática recomendada.

O [segundo método (4b)](#4b-use-blob-storage-with-a-connection-string) usa uma cadeia de conexão para acessar a conta de armazenamento diretamente. Embora esse método pareça mais simples, ele tem duas desvantagens significativas:

- Uma cadeia de conexão autentica inerentemente o agente de conexão com a *conta* de armazenamento em vez de conectar com os recursos individuais dentro dessa conta. Como resultado, uma cadeia de conexão concede uma autorização mais ampla do que pode ser necessária.

- Uma cadeia de conexão contém uma chave de acesso em texto sem formatação e, portanto, apresenta possíveis vulnerabilidades se ela estiver construída ou protegida incorretamente. Se essa cadeia de conexão for exposta, ela poderá ser usada para acessar uma ampla gama de recursos na conta de Armazenamento.

Por esses motivos, recomendamos usar o método de autenticação no código de produção.

### <a name="4a-use-blob-storage-with-authentication"></a>4a: Usar o armazenamento de blobs com autenticação

1. Criar uma variável de ambiente com nome `STORAGE_BLOB_URL`:

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```cmd
    set STORAGE_BLOB_URL=https://pythonazurestorage12345.blob.core.windows.net
    ```

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    STORAGE_BLOB_URL=https://pythonazurestorage12345.blob.core.windows.net
    ```

    ---

    Substitua "pythonazurestorage12345" pelo nome da sua conta de armazenamento específica.

    Essa variável de ambiente é usada somente por este exemplo e não é usada pelas bibliotecas do Azure.

1. Crie um arquivo chamado*use_blob_auth.py* com o seguinte código. Os comentários explicam as etapas.

    ```python
    import os
    from azure.identity import DefaultAzureCredential

    # Import the client object from the Azure library
    from azure.storage.blob import BlobClient

    credential = DefaultAzureCredential()

    # Retrieve the storage blob service URL, which is of the form
    # https://pythonazurestorage12345.blob.core.windows.net/
    storage_url = os.environ["STORAGE_BLOB_URL"]

    # Create the client object using the storage URL and the credential
    blob_client = BlobClient(storage_url, container_name="blob-container-01", blob_name="sample-blob.txt", credential=credential)

    # Open a local file and upload its contents to Blob Storage
    with open("./sample-source.txt", "rb") as data:
        blob_client.upload_blob(data)
    ```

    Links de referência:
      - [DefaultAzureCredential (azure.identity)](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python)
      - [BlobClient (azure.storage.blob)](/python/api/azure-storage-blob/azure.storage.blob.blobclient?view=azure-python)

1. Tentativa de executar o código (que falha intencionalmente):

    ```cmd
    python use_blob_auth.py
    ```

    Como a entidade de serviço local que está sendo usada não tem permissão para acessar o contêiner de blob, você verá o erro: "Esta solicitação não está autorizada a executar esta operação usando essa permissão".

1. Para conceder permissões para o contêiner à entidade de serviço, use o comando da CLI do Azure [az role assignment create](/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create) (é longo!):

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```azurecli
    az role assignment create --assignee %AZURE_CLIENT_ID% ^
        --role "Storage Blob Data Contributor" ^
        --scope "/subscriptions/%AZURE_SUBSCRIPTION_ID%/resourceGroups/PythonAzureExample-Storage-rg/providers/Microsoft.Storage/storageAccounts/pythonazurestorage12345/blobServices/default/containers/blob-container-01"
    ```

    # <a name="bash"></a>[Bash](#tab/bash)

    ```azurecli
    az role assignment create --assignee $AZURE_CLIENT_ID \
        --role "Storage Blob Data Contributor" \
        --scope "/subscriptions/$AZURE_SUBSCRIPTION_ID/resourceGroups/PythonAzureExample-Storage-rg/providers/Microsoft.Storage/storageAccounts/pythonazurestorage12345/blobServices/default/containers/blob-container-01"
    ```

    ---

    O argumento `--scope` identifica o local em que essa atribuição de função se aplica. Neste exemplo, você concede a função "Colaborador de Dados de Blob de Armazenamento" ao contêiner *específico* chamado "blob-container-01".

    Substitua `pythonazurestorage12345` pelo nome exato da sua conta de armazenamento. Você também pode ajustar o nome do grupo de recursos e do contêiner de blob, se necessário. Se você usar o nome errado, verá o erro "Não é possível executar a operação solicitada no recurso aninhado. Recurso pai 'pythonazurestorage12345' não encontrado."

    O argumento `--scope` neste comando também usa as variáveis de ambiente AZURE_CLIENT_ID e AZURE_SUBSCRIPTION_ID, que você já deve ter definido em seu ambiente local para sua entidade de serviço, seguindo [Configurar seu ambiente de desenvolvimento Python local para Azure](configure-local-development-environment.md).

1. Depois de aguardar um minuto ou dois para as permissões se propagarem, execute o código novamente para verificar se ele funciona agora. Se você vir o erro de permissão novamente, aguarde um pouco mais e tente executar o código outra vez.

Para obter mais informações sobre escopos e atribuições de funções, consulte [Como atribuir permissões de função](how-to-assign-role-permissions.md).

### <a name="4b-use-blob-storage-with-a-connection-string"></a>4b: Usar o armazenamento de blobs com uma cadeia de conexão

1. Crie uma variável de ambiente chamada `AZURE_STORAGE_CONNECTION_STRING`, o valor que é a cadeia de conexão completa para a conta de armazenamento. (Essa variável de ambiente também é usada por vários comentários CLI do Azure.)

1. Crie um arquivo Python com o nome *use_blob_conn_string.py* com o seguinte código. Os comentários explicam as etapas.

    ```python
    import os

    # Import the client object from the Azure library
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

## <a name="5-verify-blob-creation"></a>5. Verificar a criação de blob

Depois de executar o código usando qualquer um dos métodos, vá para o [portal do Azure](https://portal.azure.com), navegue até o contêiner de blobs para verificar se existe um novo blob chamado *sample-blob.txt* com o mesmo conteúdo do arquivo *sample-source.txt*:

![Página do portal do Azure para o contêiner de blob, mostrando o arquivo carregado](media/azure-sdk-example-storage/portal-blob-container-file.png)

## <a name="6-clean-up-resources"></a>6: Limpar os recursos

```azurecli
az group delete -n PythonAzureExample-Storage-rg
```

Execute esse comando se você não precisar manter os recursos provisionados neste exemplo e quiser evitar encargos contínuos na sua assinatura.

Você também pode usar o método [`ResourceManagementClient.resource_groups.delete`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.resourcegroupsoperations?view=azure-python#delete-resource-group-name--custom-headers-none--raw-false--polling-true----operation-config-) para excluir um grupo de recursos do código.

## <a name="see-also"></a>Confira também

- [Exemplo: Provisionar um grupo de recursos](azure-sdk-example-resource-group.md)
- [Exemplo: Provisionar um aplicativo Web e implantar o código](azure-sdk-example-web-app.md)
- [Exemplo: Provisionar o Armazenamento do Microsoft Azure](azure-sdk-example-storage.md)
- [Exemplo: Provisionar e usar um banco de dados MySQL](azure-sdk-example-database.md)
- [Exemplo: Provisionar uma máquina virtual >>>](azure-sdk-example-virtual-machines.md)
