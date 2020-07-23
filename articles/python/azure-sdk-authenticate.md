---
title: Como autenticar aplicativos Python com os serviços do Azure
description: Como adquirir os objetos de credencial necessários para autenticar um aplicativo Python com os serviços do Azure usando as bibliotecas do Azure
ms.date: 05/12/2020
ms.topic: conceptual
ms.openlocfilehash: 1694f85b2ad41a12865bb3d367b1b8498ab474bb
ms.sourcegitcommit: 04ee2325e3efd9b7797102b4cd9d5db009c38a42
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86945814"
---
# <a name="how-to-authenticate-python-apps-with-azure-services"></a>Como autenticar aplicativos Python com os serviços do Azure

Ao escrever o código do aplicativo usando as bibliotecas do Azure para Python, use o seguinte padrão para acessar os recursos do Azure:

1. Adquira uma credencial (normalmente uma operação única).
1. Use a credencial para adquirir um objeto de cliente apropriado para um recurso.
1. Tente acessar ou modificar o recurso por meio do objeto de cliente, que gera uma solicitação HTTP para a API REST do recurso.

A solicitação para a API REST é o ponto no qual o Azure autentica a identidade do aplicativo, conforme descrito pelo objeto de credencial. Em seguida, o Azure verifica se a identidade está autorizada a executar a ação solicitada. Se a identidade não tiver autorização, a operação falhará. (A concessão de permissões depende do tipo de recurso, como Azure Key Vault, Armazenamento do Azure etc. Para obter mais informações, consulte a documentação para o tipo de recurso.)

A identidade envolvida nesses processos, ou seja, a identidade descrita pelo objeto de credenciais, geralmente é definida por uma *entidade de segurança* que representa um usuário, grupo, serviço ou aplicativo. Vários métodos de autenticação descritos neste artigo usam uma entidade de segurança explícita, que normalmente é conhecida como uma *entidade de serviço*.

No entanto, para a maioria dos aplicativos em nuvem, é recomendável usar o objeto `DefaultAzureCredential`, conforme explicado na primeira seção, porque ele libera completamente a manipulação de uma entidade de serviço para o aplicativo.

[!INCLUDE [chrome-note](includes/chrome-note.md)]

## <a name="authenticate-with-defaultazurecredential"></a>Autenticar com DefaultAzureCredential

```python
import os
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

# Obtain the credential object. When run locally, DefaultAzureCredential relies
# on environment variables named AZURE_CLIENT_ID, AZURE_CLIENT_SECRET, and AZURE_TENANT_ID.
credential = DefaultAzureCredential()

# Create the client object using the credential
#
# **NOTE**: SecretClient here is only an example; the same process
# applies to all other Azure client libraries.

vault_url = os.environ["KEY_VAULT_URL"]
secret_client = SecretClient(vault_url=vault_url, credential=credential)

# Attempt to retrieve a secret value. The operation fails if the principal
# cannot be authenticated or is not authorized for the operation in question.
retrieved_secret = client.get_secret("secret-name-01")
```

A classe [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) da biblioteca [`azure-identity`](/python/api/azure-identity/azure.identity?view=azure-python) fornece os meios mais simples e recomendados de autenticação.

O código anterior usa o `DefaultAzureCredential` ao acessar o Azure Key Vault, em que a URL do Key Vault está disponível em uma variável de ambiente chamada `KEY_VAULT_URL`. O código implementa claramente o padrão descrito no início do artigo: adquirir um objeto de credencial, criar um objeto de cliente do SDK e tentar executar uma operação usando esse objeto de cliente.

Novamente, a autenticação e a autorização não acontecem até a etapa final. Criar o objeto do SDK [`SecretClient`](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python) não envolve nenhuma comunicação com o recurso em questão; o objeto `SecretClient` é apenas um wrapper em volta da API REST do Azure subjacente e existe apenas na memória de runtime do aplicativo. Só quando você chama o método [`get_secret`](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#get-secret-name--version-none----kwargs-) que o objeto cliente gera a chamada da API REST apropriada para o Azure. O ponto de extremidade do Azure para `get_secret` autentica a identidade do chamador e verifica a autorização.

Quando o código é implantado e executado no Azure, `DefaultAzureCredential` automaticamente usa a identidade atribuída ("gerenciada") pelo sistema atribuído que você pode habilitar para o aplicativo em qualquer serviço que esteja hospedando-o. Por exemplo, para um aplicativo Web implantado no Serviço de Aplicativo do Azure, você habilita sua identidade gerenciada por meio da opção **Identidade** > **Sistema atribuída** no portal do Azure ou usando o comando `az webapp identity assign` na CLI do Azure. As permissões para recursos específicos, como o Armazenamento do Azure ou Azure Key Vault, também são atribuídas a essa identidade usando o portal do Azure ou a CLI do Azure. Nesses casos, essa identidade gerenciada pelo Azure maximiza a segurança porque você nunca lida com uma entidade de serviço explícita em seu código.

Quando você executa o código localmente, `DefaultAzureCredential` usa automaticamente a entidade de serviço descrita pelas variáveis de ambiente chamadas `AZURE_TENANT_ID`, `AZURE_CLIENT_ID` e `AZURE_CLIENT_SECRET`. Em seguida, o objeto cliente do SDK inclui esses valores (com segurança) no cabeçalho da solicitação HTTP ao chamar o ponto de extremidade da API. Nenhuma alteração de código é necessária. Para obter detalhes sobre como criar a entidade de serviço e configurar as variáveis de ambiente, consulte [Configurar seu ambiente de desenvolvimento Python local para o Azure - configurar a autenticação](configure-local-development-environment.md#configure-authentication).

Nos dois casos, a identidade envolvida deve receber permissões para o recurso apropriado, descrito na documentação dos serviços individuais. Para obter detalhes sobre as permissões de Key Vault, como seria necessário para o código anterior, consulte [Fornecer autenticação de Key Vault com uma política de acesso de controle](/azure/key-vault/general/group-permissions-for-apps).

<a name="cli-auth-note"></a>
> [!IMPORTANT]
> No futuro, `DefaultAzureCredential` usará a identidade conectada à CLI do Azure por meio de `az login` se as variáveis de ambiente da entidade de serviço não estiverem disponíveis. Se você for o proprietário ou o administrador da sua assinatura, a conclusão prática desse recurso é que seu código tem acesso inerente à maioria dos recursos nessa assinatura sem a necessidade de atribuir permissões específicas. Esse comportamento é conveniente para experimentação. No entanto, é altamente recomendável que você use entidades de serviço específicas e atribua permissões específicas ao começar a escrever o código de produção, pois você aprenderá a atribuir permissões exatas a identidades diferentes e pode validar com precisão essas permissões em ambientes de teste, antes de implantar na produção.

### <a name="using-defaultazurecredential-with-sdk-management-libraries"></a>Usando o DefaultAzureCredential com bibliotecas de gerenciamento do SDK

```python
# WARNING: this code presently fails with current release libraries!

from azure.identity import DefaultAzureCredential

# azure.mgmt.resource is an Azure SDK management library
from azure.mgmt.resource import SubscriptionClient

# Attempt to retrieve the subscription ID
credential = DefaultAzureCredential()
subscription_client = SubscriptionClient(credential)

# The following line produces a "no attribute 'signed_session'" error:
subscription = next(subscription_client.subscriptions.list())

print(subscription.subscription_id)
```

No momento, o `DefaultAzureCredential` funciona apenas com bibliotecas do cliente do SDK do Azure ("plano de dados") e versões prévias das bibliotecas de gerenciamento do SDK do Azure (ou seja, a versão prévia mais recente de bibliotecas cujos nomes começam com `azure-mgmt`), como mostrado neste exemplo de código. Ou seja, com as bibliotecas de versão atuais, a chamada para `subscription_client.subscriptions.list()` falha com o erro vago de "O objeto 'DefaultAzureCredential' não tem o atributo 'signed_session'". Esse erro ocorre porque as bibliotecas de gerenciamento do SDK atuais pressupõem que o objeto de credencial contém uma propriedade `signed_session`, ausente em `DefaultAzureCredential`.

Você pode contornar o erro usando bibliotecas de gerenciamento de versão prévia, conforme descrito na postagem no blog, [Apresentando novas versões prévias para as bibliotecas de gerenciamento do Azure](https://devblogs.microsoft.com/azure-sdk/introducing-new-previews-for-azure-management-libraries/).

Como alternativa, você pode usar os seguintes métodos:

1. Use um dos outros métodos de autenticação descritos nas seções subsequentes deste artigo, que podem funcionar bem para o código que usa *somente* bibliotecas de gerenciamento do SDK e que não serão implantadas na nuvem; nesse caso, você pode confiar apenas nas entidades de serviço locais.

1. Em vez de `DefaultAzureCredential`, use a classe [CredentialWrapper (cred_wrapper. py)](https://gist.github.com/lmazuel/cc683d82ea1d7b40208de7c9fc8de59d) fornecida por um membro da equipe de engenharia do SDK do Azure. Quando as bibliotecas de gerenciamento atualizadas saírem da versão prévia, você poderá simplesmente voltar para `DefaultAzureCredential`. Esse método tem a vantagem de que você pode usar a mesma credencial com as bibliotecas de cliente e de gerenciamento do SDK e funciona tanto localmente quanto na nuvem.

    Supondo que você tenha baixado uma cópia de *cred_wrapper. py* na pasta do projeto, o código anterior seria exibido da seguinte maneira:

    ```python
    from cred_wrapper import CredentialWrapper
    from azure.mgmt.resource import SubscriptionClient

    credential = CredentialWrapper()
    subscription_client = SubscriptionClient(credential)
    subscription = next(subscription_client.subscriptions.list())
    print(subscription.subscription_id)
    ```

    Novamente, quando as bibliotecas de gerenciamento atualizadas saírem da versão prévia, você poderá usar o `DefaultAzureCredential` diretamente.

## <a name="other-authentication-methods"></a>Outros métodos de autenticação

Embora `DefaultAzureCredential` seja o método de autenticação recomendado para a maioria dos cenários, outros métodos estão disponíveis com as seguintes advertências:

- A maioria dos métodos funciona com entidades de serviço explícitas e não aproveita a identidade gerenciada para o código que é implantado na nuvem. Quando usado com o código de produção, gerencie e mantenha entidades de serviço distintas para seus aplicativos de nuvem.

- Alguns métodos, como a autenticação baseada em CLI, funcionam apenas com scripts locais e não podem ser usados com o código de produção.

As entidades de serviço para aplicativos implantados na nuvem são gerenciadas em suas assinaturas Active Directory. Para obter mais informações, confira [Como gerenciar entidades de serviço](how-to-manage-service-principals.md).

### <a name="authenticate-with-a-json-file"></a>Autenticar com um arquivo JSON

Neste método, você cria um arquivo JSON que contém as credenciais necessárias para a entidade de serviço. Em seguida, você cria um objeto de cliente do SDK usando esse arquivo. Esse método pode ser usado localmente e na nuvem. 

1. Crie um arquivo JSON com o seguinte formato:

    ```json
    {
        "subscriptionId": "<azure_aubscription_id>",
        "tenantId": "<tenant_id>",
        "clientId": "<application_id>",
        "clientSecret": "<application_secret>",
        "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
        "resourceManagerEndpointUrl": "https://management.azure.com/",
        "activeDirectoryGraphResourceId": "https://graph.windows.net/",
        "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
        "galleryEndpointUrl": "https://gallery.azure.com/",
        "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

    Substitua os quatro espaços reservados por sua ID da assinatura do Azure, pela ID de locatário, pela ID do cliente e pelo segredo do cliente:

    > [!TIP]
    > Conforme explicado em [Configurar seu ambiente de desenvolvimento local](configure-local-development-environment.md#create-a-service-principal-and-environment-variables-for-development), você pode usar o comando [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) com o parâmetro `--sdk-auth` para gerar esse formato JSON diretamente.

1. Salve o arquivo com um nome como *credentials.json* em um local seguro que seu código possa acessar. Para manter suas credenciais seguras, não deixe de omitir esse arquivo do controle do código-fonte e não compartilhe-o com outros desenvolvedores. Ou seja, a ID do locatário, a ID do cliente e o segredo do cliente de uma entidade de serviço devem sempre permanecer isolados na estação de trabalho de desenvolvimento.

1. Crie uma variável de ambiente chamada `AZURE_AUTH_LOCATION` com o caminho para o arquivo JSON como valor.

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```cmd
    set AZURE_AUTH_LOCATION=../credentials.json
    ```

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    AZURE_AUTH_LOCATION="../credentials.json"
    ```

    ---

    Esses exemplos pressupõem que o arquivo JSON seja nomeado como *credentials.json* e esteja localizado na pasta pai do seu projeto.


1. Use o método [get_client_from_auth_file](/python/api/azure-common/azure.common.client_factory?view=azure-python#get-client-from-auth-file-client-class--auth-path-none----kwargs-) para criar o objeto cliente:

    ```python
    from azure.common.client_factory import get_client_from_auth_file
    from azure.mgmt.resource import SubscriptionClient

    # This form of get_client_from_auth_file relies on the AZURE_AUTH_LOCATION
    # environment variable.
    subscription_client = get_client_from_auth_file(SubscriptionClient)

    subscription = next(subscription_client.subscriptions.list())
    print(subscription.subscription_id)
    ```

Como alternativa, você pode especificar o caminho diretamente no código usando o argumento `auth_path`; nesse caso, a variável de ambiente não é necessária:

```python
subscription_client = get_client_from_auth_file(SubscriptionClient, auth_path="../credentials.json")
```

### <a name="authenticate-with-a-json-dictionary"></a>Autenticar com um dicionário JSON

```python
import os
from azure.common.client_factory import get_client_from_json_dict
from azure.mgmt.resource import SubscriptionClient

# Retrieve the IDs and secret to use in the JSON dictionary
subscription_id = os.environ["AZURE_SUBSCRIPTION_ID"]
tenant_id = os.environ["AZURE_TENANT_ID"]
client_id = os.environ["AZURE_CLIENT_ID"]
client_secret = os.environ["AZURE_CLIENT_SECRET"]

config_dict = {
   "subscriptionId": subscription_id,
    "tenantId": tenant_id,
   "clientId": client_id,
   "clientSecret": client_secret,
   "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
   "resourceManagerEndpointUrl": "https://management.azure.com/",
   "activeDirectoryGraphResourceId": "https://graph.windows.net/",
   "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
   "galleryEndpointUrl": "https://gallery.azure.com/",
   "managementEndpointUrl": "https://management.core.windows.net/"
}

subscription_client = get_client_from_json_dict(SubscriptionClient, config_dict)

subscription = next(subscription_client.subscriptions.list())
print(subscription.subscription_id)
```

Em vez de usar um arquivo, conforme descrito na seção anterior, é possível criar os dados de JSON necessários em uma variável e chamar [get_client_from_json_dict](/python/api/azure-common/azure.common.client_factory?view=azure-python#get-client-from-json-dict-client-class--config-dict----kwargs-). Esse código pressupõe que você criou as variáveis de ambiente descritas em [Configurar seu ambiente de desenvolvimento local](configure-local-development-environment.md#create-a-service-principal-and-environment-variables-for-development). Para o código implantado na nuvem, você pode criar essas variáveis de ambiente na VM do servidor ou como configurações do aplicativo, ao usar o serviço de plataforma, como o Serviço de Aplicativo do Azure e Azure Functions.

Você também pode armazenar valores no Azure Key Vault e recuperá-los em tempo de execução em vez de usar variáveis de ambiente.

### <a name="authenticate-with-token-credentials"></a>Fazer autenticação com credenciais do token

```python
import os
from azure.mgmt.resource import SubscriptionClient
from azure.common.credentials import ServicePrincipalCredentials

# Retrieve the IDs and secret to use with ServicePrincipalCredentials
subscription_id = os.environ["AZURE_SUBSCRIPTION_ID"]
tenant_id = os.environ["AZURE_TENANT_ID"]
client_id = os.environ["AZURE_CLIENT_ID"]
client_secret = os.environ["AZURE_CLIENT_SECRET"]

credential = ServicePrincipalCredentials(tenant=tenant_id, client_id=client_id, secret=client_secret)

subscription_client = SubscriptionClient(credential)

subscription = next(subscription_client.subscriptions.list())
print(subscription.subscription_id)
```

Nesse método, você cria um objeto [`ServicePrincipalCredentials`](/python/api/msrestazure/msrestazure.azure_active_directory.serviceprincipalcredentials?view=azure-python) usando as credenciais obtidas do armazenamento seguro, como Azure Key Vault ou variáveis de ambiente. O código anterior pressupõe que você criou as variáveis de ambiente descritas em [Configurar seu ambiente de desenvolvimento local](configure-local-development-environment.md#create-a-service-principal-and-environment-variables-for-development).

Com esse método, você pode usar uma [nuvem soberana ou nacional do Azure](/azure/active-directory/develop/authentication-national-cloud) em vez da nuvem pública do Azure especificando um argumento `base_url` para o objeto cliente:

```python
from msrestazure.azure_cloud import AZURE_CHINA_CLOUD

#...

subscription_client = SubscriptionClient(credentials, base_url=AZURE_CHINA_CLOUD.endpoints.resource_manager)
```

As constantes de nuvem soberana são encontradas na biblioteca [msrestazure. azure_cloud](https://github.com/Azure/msrestazure-for-python/blob/master/msrestazure/azure_cloud.py).

### <a name="authenticate-with-token-credentials-and-an-adal-context"></a>Autenticar com credenciais do token e um contexto ADAL

Caso precise de mais controle, ao usar as credenciais do token, use a [ADAL (Biblioteca de Autenticação do Microsoft Active Directory) do Azure para Python](https://github.com/AzureAD/azure-activedirectory-library-for-python) e o wrapper ADAL do SDK:

```python
import os, adal
from azure.mgmt.resource import SubscriptionClient
from msrestazure.azure_active_directory import AdalAuthentication
from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD

# Retrieve the IDs and secret to use with ServicePrincipalCredentials
subscription_id = os.environ["AZURE_SUBSCRIPTION_ID"]
tenant_id = os.environ["AZURE_TENANT_ID"]
client_id = os.environ["AZURE_CLIENT_ID"]
client_secret = os.environ["AZURE_CLIENT_SECRET"]

LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

context = adal.AuthenticationContext(LOGIN_ENDPOINT + '/' + tenant_id)

credential = AdalAuthentication(context.acquire_token_with_client_credentials,
    RESOURCE, client_id, client_secret)

subscription_client = SubscriptionClient(credential)

subscription = next(subscription_client.subscriptions.list())
print(subscription.subscription_id)
```

Se você precisar da biblioteca ADAL, execute `pip install adal`.

Com esse método, você pode usar uma [nuvem soberana ou nacional do Azure](/azure/active-directory/develop/authentication-national-cloud) em vez da nuvem pública do Azure.

```python
from msrestazure.azure_cloud import AZURE_CHINA_CLOUD

# ...

LOGIN_ENDPOINT = AZURE_CHINA_CLOUD.endpoints.active_directory
RESOURCE = AZURE_CHINA_CLOUD.endpoints.active_directory_resource_id
```

Basta substituir `AZURE_PUBLIC_CLOUD` pela constante de nuvem soberana apropriada da [biblioteca msrestazure.azure_cloud](https://github.com/Azure/msrestazure-for-python/blob/master/msrestazure/azure_cloud.py).

### <a name="cli-based-authentication-development-purposes-only"></a>Autenticação baseada em CLI (para fins de desenvolvimento somente)

```python
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource import SubscriptionClient

subscription_client = get_client_from_cli_profile(SubscriptionClient)

subscription = next(subscription_client.subscriptions.list())
print(subscription.subscription_id)
```

Nesse método, você cria um objeto de cliente usando as credenciais do usuário conectado com o comando CLI do Azure `az login`.

O SDK usa a ID da assinatura padrão, ou é possível definir a assinatura usando [`az account`](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli)

Esse método deve ser usado somente para fins de experimentação e desenvolvimento antecipados, porque um usuário conectado normalmente tem privilégios de administrador ou proprietário e pode acessar a maioria dos recursos sem nenhuma permissão adicional. Para obter mais informações, consulte a observação anterior sobre como [usar credenciais de CLI com `DefaultAzureCredential`](#cli-auth-note).

### <a name="deprecated-authenticate-with-userpasscredentials"></a>Preterido: Autenticar com UserPassCredentials

Antes da [ADAL (Biblioteca de Autenticação do Microsoft Azure Active Directory) do Azure para Python](https://github.com/AzureAD/azure-activedirectory-library-for-python) ser disponibilizada, você usou a classe [`UserPassCredentials`](/python/api/msrestazure/msrestazure.azure_active_directory.userpasscredentials?view=azure-python), atualmente preterida. Essa classe não permite a autenticação de dois fatores e não deve mais ser usada.

## <a name="see-also"></a>Confira também

- [Configurar o ambiente de desenvolvimento do Python local para o Azure](configure-local-development-environment.md)
- [Exemplo: Provisionar um grupo de recursos](azure-sdk-example-resource-group.md)
- [Exemplo: Provisionar e usar o Armazenamento do Microsoft Azure](azure-sdk-example-storage.md)
- [Exemplo: Provisionar um aplicativo Web e implantar o código](azure-sdk-example-web-app.md)
- [Exemplo: Provisionar e usar um banco de dados MySQL](azure-sdk-example-database.md)
- [Exemplo: Provisionar uma máquina virtual >>>](azure-sdk-example-virtual-machines.md)
