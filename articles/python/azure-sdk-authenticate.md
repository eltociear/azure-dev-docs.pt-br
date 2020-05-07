---
title: Autenticar aplicativos usando as bibliotecas de gerenciamento do Azure para Python
description: Autenticar um aplicativo Python com os serviços do Azure usando as bibliotecas do SDK de gerenciamento do Azure
ms.date: 01/16/2020
ms.topic: conceptual
ms.custom: seo-python-october2019
ms.openlocfilehash: e972d0159f97feddf4dd773d69b422634c3966c1
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "80441791"
---
# <a name="authenticate-by-using-the-azure-management-libraries-for-python"></a>Autenticar usando as bibliotecas de gerenciamento do Azure para Python

Neste artigo, você aprenderá a usar as bibliotecas de gerenciamento do SDK do Python para autenticar um aplicativo com o Azure AD (Azure Active Directory) usando uma entidade de serviço. A entidade de serviço é a identidade de um aplicativo que está registrado no Azure AD e permite que o aplicativo acesse ou modifique recursos de acordo com permissões próprias.

Para registrar os aplicativos, primeiro é necessário criar um Active Directory com um locatário apropriado para sua organização. É possível fazer isso seguindo as instruções em [Criar um novo locatário no Azure Active Directory](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant). Quando o Active Directory estiver pronto, siga o artigo de [Instruções: Use o portal para criar um aplicativo do Azure AD e uma entidade de serviço que possam acessar recursos](/azure/active-directory/develop/howto-create-service-principal-portal), nos quais seja possível registrar um aplicativo, [recuperar as IDs de locatário e do aplicativo (cliente) para a entidade de serviço](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) e configurar um [segredo de aplicativo](/azure/active-directory/develop/howto-create-service-principal-portal#create-a-new-application-secret), com o qual será possível se autenticar usando um código Python.

Quando você tiver os valores será possível usar essas credenciais para se autenticar de várias maneiras usando as bibliotecas do SDK do Python. O resultado de cada método é o objeto cliente do SDK que você usa ao acessar outros recursos do código.

É altamente recomendável armazenar a ID do locatário, a ID do cliente e o segredo no [Azure KeyVault](/azure/key-vault/) para que esses valores não estejam em nenhum lugar de seus sistemas nem no controle do código-fonte. Você pode recuperar os valores facilmente e sempre que precisar.

[!INCLUDE [chrome-note](includes/chrome-note.md)]

## <a name="authenticate-with-a-json-file"></a><a name="mgmt-auth-file"></a>Autenticar com um arquivo JSON

Neste método, você cria um arquivo JSON que contém as credenciais necessárias para a entidade de serviço e depois cria o objeto cliente do SDK usando as informações no arquivo.

1. Crie um arquivo JSON (com o nome que quiser, como *app_credentials. JSON*) com o formato a seguir. Substitua os quatro espaços reservados por sua ID da assinatura do Azure, pela ID de locatário do Azure AD, pela ID do aplicativo (cliente) e pelo segredo:

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

    > [!TIP]
    > É possível recuperar um arquivo de credenciais com sua ID da assinatura existente ao entrar no Azure usando o comando [az login](/cli/azure/reference-index#az-login) e depois o comando [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac):
    >
    > ```azurecli
    > az login
    > az ad sp create-for-rbac --sdk-auth > credentials.json
    > ```
    >
    > Em seguida será possível substituir os valores `tenantId`, `clientId`, e `clientSecret` para um aplicativo específico em vez de usar os valores de uso geral.

1. Salve esse arquivo em um local seguro, onde seu código possa acessá-lo.

1. Use o método [get_client_from_auth_file](/python/api/azure-common/azure.common.client_factory?view=azure-python#get-client-from-auth-file-client-class--auth-path-none----kwargs-) para criar o objeto cliente, substituindo `<path_to_file>` pelo caminho para o arquivo JSON:

    ```python
    from azure.common.client_factory import get_client_from_auth_file
    from azure.mgmt.compute import ComputeManagementClient

    client = get_client_from_auth_file(ComputeManagementClient, auth_path=<path_to_file>)
    ```

1. Como alternativa é possível armazenar o caminho para o arquivo em uma variável de ambiente chamada `AZURE_AUTH_LOCATION` e omitir o argumento `auth_path`.

## <a name="authenticate-with-a-json-dictionary"></a>Autenticar com um dicionário JSON

Em vez de usar um arquivo, conforme descrito na seção anterior, é possível criar o JSON necessário em uma variável e chamar [get_client_from_json_dict](/python/api/azure-common/azure.common.client_factory?view=azure-python#get-client-from-json-dict-client-class--config-dict----kwargs-). Nesse caso, a ID do locatário, a ID do cliente e o segredo deverão ser sempre armazenados em um local seguro, como o [Azure KeyVault](/azure/key-vault/).

```python
   from azure.common.client_factory import get_client_from_auth_file
   from azure.mgmt.compute import ComputeManagementClient

    # Retrieve tenant_id, client_id, and client_secret from Azure KeyVault

   config_dict = {
       "subscriptionId": "bfc42d3a-65ca-11e7-95cf-ecb1d756380e",
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
   client = get_client_from_json_dict(ComputeManagementClient, config_dict)
```

## <a name="authenticate-with-token-credentials"></a><a name="mgmt-auth-token"></a>Autenticar com credenciais do token

Supondo que você recupere as credenciais de um armazenamento seguro, como o [Azure KeyVault](/azure/key-vault/), primeiro crie um objeto [ServicePrincipalCredentials] e, em seguida, crie uma instância do cliente usando essas credenciais e sua ID da assinatura:

```python
from azure.mgmt.compute import ComputeManagementClient
from azure.common.credentials import ServicePrincipalCredentials

# Retrieve credentials from secure storage. Never hard-code credentials into code.

credentials = ServicePrincipalCredentials(tenant = <tenant_id>,
    client_id = <client_id>, secret = <secret>)

client = ComputeManagementClient(credentials, <subscription_id>)
```

Caso precise de mais controle, use o [ADAL (Biblioteca de Autenticação do Active Directory) do Azure para Python](https://github.com/AzureAD/azure-activedirectory-library-for-python) e o wrapper ADAL do SDK:

```python
from azure.mgmt.compute import ComputeManagementClient
import adal
from msrestazure.azure_active_directory import AdalAuthentication
from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD

# Retrieve credentials from secure storage. Never hard-code credentials into code.

LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

context = adal.AuthenticationContext(LOGIN_ENDPOINT + '/' + TENANT_ID)

credentials = AdalAuthentication(context.acquire_token_with_client_credentials,
    RESOURCE, <client_id>, <secret>)

client = ComputeManagementClient(credentials, <subscription_id>)
```

> [!NOTE]
> Ao usar uma nuvem soberana do Azure, especifique a URL base apropriada (usando uma constante em `msrestazure.azure_cloud`) ao criar o cliente de gerenciamento:
>
> ```python
> client = ComputeManagementClient(credentials, subscription_id,
>     base_url=AZURE_CHINA_CLOUD.endpoints.resource_manager)
> ```

### <a name="authenticate-with-token-credentials-deprecated"></a><a name="mgmt-auth-legacy"></a>Autenticar com credenciais do token (preterido)

Antes do [ADAL (Biblioteca de Autenticação do Active Directory) do Azure para Python](https://github.com/AzureAD/azure-activedirectory-library-for-python) ficar disponível, você usou a classe `UserPassCredentials`. O uso dessa classe foi considerado preterido e deve ser evitado, pois ela não é compatível com a autenticação de dois fatores.

```python
from azure.common.credentials import UserPassCredentials

# DEPRECATED - legacy purposes only - use ADAL instead
credentials = UserPassCredentials(
    'user@domain.com',
    'my_smart_password'
)
```

## <a name="authenticate-with-azure-managed-identities"></a><a name="mgmt-auth-msi"></a>Autenticar com as Identidades Gerenciadas do Azure

A Identidade Gerenciada do Azure é uma maneira simples de um recurso se autenticar no Azure sem usar credenciais específicas.

Para usar as identidades gerenciadas é necessário se conectar ao Azure de outro recurso do Azure, como uma Função do Azure ou uma máquina virtual. Para saber como configurar uma identidade gerenciada para um recurso, confira [Configurar identidades gerenciadas para recursos do Azure](/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm) e [Como usar identidades gerenciadas para os recursos do Azure](/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in).

```python
from msrestazure.azure_active_directory import MSIAuthentication
from azure.mgmt.resource import ResourceManagementClient, SubscriptionClient

# Create MSI Authentication
credentials = MSIAuthentication()

# Create a Subscription Client
subscription_client = SubscriptionClient(credentials)
subscription = next(subscription_client.subscriptions.list())
subscription_id = subscription.subscription_id

# Create a Resource Management client
client = ResourceManagementClient(credentials, subscription_id)

# List resource groups as an example. The only limit is what role and policy are assigned to this MSI token.
for resource_group in client.resource_groups.list():
    print(resource_group.name)
```

## <a name="cli-based-authentication-development-purposes-only"></a><a name="mgmt-auth-cli"></a>Autenticação baseada na CLI (somente para fins de desenvolvimento)

O SDK é capaz de criar um cliente usando a assinatura ativa da CLI do Azure após você executar `az login`. O SDK usa a ID da assinatura padrão ou é possível definir a assinatura usando [az account](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli)

Essa opção deverá ser usada somente para fins de desenvolvimento.

```python
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.compute import ComputeManagementClient

client = get_client_from_cli_profile(ComputeManagementClient)
```
