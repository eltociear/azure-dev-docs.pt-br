---
title: Como autenticar aplicativos Python com os serviços do Azure
description: Como adquirir os objetos de credencial necessários para autenticar um aplicativo Python com os serviços do Azure usando as bibliotecas do Azure
ms.date: 08/18/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 746a948077c7def12aae5053355c445b7592eae0
ms.sourcegitcommit: 4824cea71195b188b4e8036746f58bf8b70dc224
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89753743"
---
# <a name="how-to-authenticate-and-authorize-python-apps-on-azure"></a>Como autenticar e autorizar aplicativos Python no Azure

A maioria dos aplicativos de nuvem implantados no Azure precisa acessar outros recursos do Azure, como armazenamento, bancos de dados, segredos armazenados etc. Para acessar esses recursos, o aplicativo precisa ser autenticado e autorizado:

- A **autenticação** confirma a identidade do aplicativo com o Azure Active Directory.

- A **autorização** determina quais operações o aplicativo autenticado pode executar em qualquer recurso especificado. As operações autorizadas são definidas pelas **funções** atribuídas à identidade do aplicativo nesse recurso. Em alguns casos, como no Azure Key Vault, a autorização também é determinada por **políticas de acesso** adicionais que são atribuídas à identidade do aplicativo.

Este artigo explica os detalhes de autenticação e autorização:

- Como atribuir uma identidade do aplicativo
- Como conceder permissões a uma identidade
- Como e quando ocorrem a autenticação e a autorização
- Os diferentes meios de autenticar um aplicativo com o Azure usando as bibliotecas do Azure. O uso de `DefaultAzureCredential` é recomendado, mas não obrigatório.

## <a name="how-to-assign-an-app-identity"></a>Como atribuir uma identidade do aplicativo

No Azure, uma identidade do aplicativo é definida por uma **entidade de serviço**. (Uma entidade de serviço é um tipo específico de "entidade de segurança" que é usada para identificar um aplicativo ou um serviço, ou seja, um trecho de código, em oposição a um usuário humano ou um grupo de usuários.)

A entidade de serviço envolvida depende do local de execução do aplicativo, conforme descrito nas seções a seguir.

### <a name="identity-when-running-the-app-on-azure"></a>Identidade durante a execução do aplicativo no Azure

Durante a execução na nuvem (por exemplo, em produção), um aplicativo usa com mais frequência uma **identidade gerenciada atribuída ao sistema**. Com uma [identidade gerenciada](/azure/active-directory/managed-identities-azure-resources/overview), você usa o nome do aplicativo ao atribuir funções e permissões aos recursos. O Azure gerencia a entidade de serviço subjacente e autentica o aplicativo nesses outros recursos do Azure automaticamente. Como resultado, você não precisa lidar com a entidade de serviço diretamente. Além disso, o código do aplicativo nunca precisa lidar com tokens de acesso, segredos ou cadeias de conexão para recursos do Azure, o que reduz o risco de vazamento ou comprometimento, de outro modo, dessas informações.

A configuração da identidade gerenciada depende do serviço usado para hospedar seu aplicativo. Veja o artigo [Serviços que dão suporte à identidade gerenciada](/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities) para obter links para instruções referentes a cada serviço. Para aplicativos Web implantados no Serviço de Aplicativo do Azure, habilite sua identidade gerenciada por meio da opção **Identidade** > **Atribuída ao sistema** no portal do Azure ou usando o comando `az webapp identity assign` na CLI do Azure.

Caso não possa usar a identidade gerenciada, registre o aplicativo manualmente no Azure Active Directory. O registro atribui uma entidade de serviço ao aplicativo, que você usará ao atribuir funções e permissões. Para obter mais informações, consulte [Register an application (Registrar um aplicativo)](/azure/active-directory/develop/quickstart-register-app).

### <a name="identity-when-running-the-app-locally"></a>Identidade durante a execução local do aplicativo

Durante o desenvolvimento, muitas vezes, o ideal é executar e depurar o código de aplicativo em uma estação de trabalho de desenvolvedor enquanto o código ainda acessa os recursos do Azure na nuvem. Nesse caso, crie uma entidade de serviço separada por meio do Azure Active Directory especificamente para o desenvolvimento local. Novamente, atribua funções e permissões a essa entidade de serviço para os recursos em questão. Normalmente, essa identidade de desenvolvimento é autorizada a acessar apenas os recursos de não produção.

Para obter detalhes sobre como criar a entidade de serviço local e disponibilizá-la para as bibliotecas do Azure, confira [Configurar seu ambiente de desenvolvimento local](configure-local-development-environment.md). Depois de concluir essa configuração única, você poderá executar o mesmo código do aplicativo localmente e na nuvem sem nenhuma modificação específica do ambiente.

Cada desenvolvedor deve ter sua própria entidade de serviço protegida em sua conta de usuário na sua estação de trabalho. Ela nunca deve ser armazenada em um repositório de controle do código-fonte. Se uma entidade de serviço for roubada ou comprometida, você poderá excluí-la com facilidade para revogar todas as permissões dela e depois recriar a entidade de serviço para esse desenvolvedor. Para obter mais informações, confira [Como gerenciar entidades de serviço](how-to-manage-service-principals.md).

> [!NOTE]
> Embora seja possível executar um aplicativo usando as próprias credenciais de usuário do Azure, isso não ajudará você a estabelecer as permissões de recurso específicas das quais o seu aplicativo precisará quando ele for implantado na nuvem. É muito melhor configurar uma entidade de serviço para desenvolvimento e atribuir a ela as funções e as permissões necessárias, que você poderá então replicar usando a identidade gerenciada ou a entidade de serviço do aplicativo implantado.

## <a name="assign-roles-and-permissions-to-an-identity"></a>Atribuir funções e permissões a uma identidade

Depois de conhecer as identidades do aplicativo no Azure e, ao executá-lo localmente, use o RBAC (controle de acesso baseado em função) para conceder permissões por meio do portal do Azure ou da CLI do Azure. Para obter detalhes completos, confira [Como atribuir permissões de função a uma identidade de aplicativo ou uma entidade de serviço](how-to-assign-role-permissions.md)

## <a name="when-does-authentication-and-authorization-occur"></a>Quando ocorrem a autenticação e a autorização?

Ao escrever o código do aplicativo usando as bibliotecas (SDK) do Azure para Python, use o seguinte padrão para acessar os recursos do Azure:

1. Adquira uma credencial, que descreve a identidade do aplicativo, usando um dos métodos descritos mais adiante neste artigo.

1. Use a credencial para adquirir um objeto de cliente para o recurso de interesse. (Cada tipo de recurso tem o próprio objeto de cliente nas bibliotecas do Azure, para o qual você fornece a URL do recurso.)

1. Tente acessar ou modificar o recurso por meio do objeto de cliente, que gera uma solicitação HTTP para a API REST do recurso. A chamada à API é o ponto no qual o Azure autentica a identidade do aplicativo e verifica a autorização.

O código a seguir descreve e demonstra essas etapas tentando acessar o Azure Key Vault.

```python
import os
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

# Acquire the resource URL. In this code we assume the resource URL is in an
# environment variable, KEY_VAULT_URL in this case.

vault_url = os.environ["KEY_VAULT_URL"]


# Acquire a credential object for the app identity. When running in the cloud,
# DefaultAzureCredential uses the app's managed identity or user-assigned service principal.
# When run locally, DefaultAzureCredential relies on environment variables named
# AZURE_CLIENT_ID, AZURE_CLIENT_SECRET, and AZURE_TENANT_ID.

credential = DefaultAzureCredential()


# Acquire an appropriate client object for the resource identified by the URL. The
# client object only stores the given credential at this point but does not attempt
# to authenticate it.
#
# **NOTE**: SecretClient here is only an example; the same process applies to all
# other Azure client libraries.

secret_client = SecretClient(vault_url=vault_url, credential=credential)

# Attempt to perform an operation on the resource using the client object (in
# this case, retrieve a secret from Key Vault). The operation fails for any of
# the following reasons:
#
# 1. The information in the credential object is invalid (for example, the AZURE_CLIENT_ID
#    environment variable cannot be found).
# 2. The app identity cannot be authenticated using the information in the credential object.
# 3. The app identity is not authorized to perform the requested operation on the
#    resource (identified in this case by the vault_url.

retrieved_secret = secret_client.get_secret("secret-name-01")
```

Novamente, nenhuma autenticação ou autorização ocorrerá até que o código faça uma solicitação específica à API REST do Azure por meio de um objeto de cliente. A instrução usada para criar a `DefaultAzureCredential` (confira a próxima seção) apenas cria um objeto do lado do cliente na memória, mas não executa nenhuma outra verificação. 

A criação do objeto [`SecretClient`](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient) do SDK também não envolve nenhuma comunicação com o recurso em questão. O objeto `SecretClient` é apenas um wrapper em torno da API REST subjacente do Azure e existe apenas na memória do runtime do aplicativo. 

É apenas quando o código chama o método [`get_secret`](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient#get-secret-name--version-none----kwargs-) que o objeto de cliente gera a chamada apropriada à API REST do Azure. O ponto de extremidade do Azure para `get_secret` autentica a identidade do chamador e verifica a autorização.

## <a name="authenticate-with-defaultazurecredential"></a>Autenticar com DefaultAzureCredential

Para a maioria dos aplicativos, a classe [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential) da biblioteca [`azure-identity`](/python/api/azure-identity/azure.identity) fornece os meios mais simples e recomendados de autenticação. A `DefaultAzureCredential` usa a identidade gerenciada do aplicativo na nuvem e carrega uma entidade de serviço local com base em variáveis de ambiente automaticamente durante a execução local.

```python
import os
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

# Acquire the resource URL
vault_url = os.environ["KEY_VAULT_URL"]

# Aquire a credential object
credential = DefaultAzureCredential()

# Acquire a client object
secret_client = SecretClient(vault_url=vault_url, credential=credential)

# Attempt to perform an operation
retrieved_secret = secret_client.get_secret("secret-name-01")
```

O código anterior usa um objeto `DefaultAzureCredential` ao acessar o Azure Key Vault, em que a URL do Key Vault está disponível em uma variável de ambiente chamada `KEY_VAULT_URL`. O código implementa claramente o padrão de uso de biblioteca típico: adquirir um objeto de credencial, criar um objeto de cliente apropriado para o recurso do Azure e tentar executar uma operação nesse recurso usando esse objeto de cliente. Novamente, a autenticação e a autorização não ocorrem até essa etapa final.

Quando o código é implantado e executado no Azure, a `DefaultAzureCredential` automaticamente usa a identidade gerenciada atribuída ao sistema, que você pode habilitar para o aplicativo em qualquer serviço que o esteja hospedando. As permissões para recursos específicos, como o Armazenamento do Azure ou o Azure Key Vault, são atribuídas a essa identidade por meio do portal do Azure ou da CLI do Azure. Nesses casos, essa identidade gerenciada pelo Azure maximiza a segurança porque você nunca lida com uma entidade de serviço explícita em seu código.

Quando você executa o código localmente, `DefaultAzureCredential` usa automaticamente a entidade de serviço descrita pelas variáveis de ambiente chamadas `AZURE_TENANT_ID`, `AZURE_CLIENT_ID` e `AZURE_CLIENT_SECRET`. Em seguida, o objeto de cliente inclui esses valores (com segurança) no cabeçalho da solicitação HTTP ao chamar o ponto de extremidade da API. Nenhuma alteração de código é necessária na execução local ou na nuvem. Para obter detalhes sobre como criar a entidade de serviço e configurar as variáveis de ambiente, consulte [Configurar seu ambiente de desenvolvimento Python local para o Azure - configurar a autenticação](configure-local-development-environment.md#configure-authentication).

Em ambos os casos, a identidade envolvida precisa receber permissões no recurso apropriado. O processo geral é descrito em [Como atribuir permissões de função](how-to-assign-role-permissions.md); encontre as especificações na documentação dos serviços individuais. Para obter detalhes sobre as permissões do Key Vault, conforme necessário para o código anterior, confira [Fornecer autenticação do Key Vault com uma política de controle de acesso](/azure/key-vault/general/group-permissions-for-apps).

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

Em todos os casos, a entidade de serviço ou o usuário apropriado precisa ter as permissões apropriadas nos recursos e na operação em questão.

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
    > Conforme explicado em [Configurar seu ambiente de desenvolvimento local](configure-local-development-environment.md#create-a-service-principal-and-environment-variables-for-development), você pode usar o comando [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) com o parâmetro `--sdk-auth` para gerar esse formato JSON diretamente.

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

1. Use o método [get_client_from_auth_file](/python/api/azure-common/azure.common.client_factory#get-client-from-auth-file-client-class--auth-path-none----kwargs-) para criar o objeto cliente:

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

Em vez de usar um arquivo, conforme descrito na seção anterior, é possível criar os dados de JSON necessários em uma variável e chamar [get_client_from_json_dict](/python/api/azure-common/azure.common.client_factory#get-client-from-json-dict-client-class--config-dict----kwargs-). Esse código pressupõe que você criou as variáveis de ambiente descritas em [Configurar seu ambiente de desenvolvimento local](configure-local-development-environment.md#create-a-service-principal-and-environment-variables-for-development). Para o código implantado na nuvem, você pode criar essas variáveis de ambiente na VM do servidor ou como configurações do aplicativo, ao usar o serviço de plataforma, como o Serviço de Aplicativo do Azure e Azure Functions.

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

Nesse método, você cria um objeto [`ServicePrincipalCredentials`](/python/api/msrestazure/msrestazure.azure_active_directory.serviceprincipalcredentials) usando as credenciais obtidas do armazenamento seguro, como Azure Key Vault ou variáveis de ambiente. O código anterior pressupõe que você criou as variáveis de ambiente descritas em [Configurar seu ambiente de desenvolvimento local](configure-local-development-environment.md#create-a-service-principal-and-environment-variables-for-development).

Com esse método, você pode usar uma [nuvem soberana ou nacional do Azure](/azure/active-directory/develop/authentication-national-cloud) em vez da nuvem pública do Azure especificando um argumento `base_url` para o objeto cliente:

```python
from msrestazure.azure_cloud import AZURE_CHINA_CLOUD

#...

subscription_client = SubscriptionClient(credentials, base_url=AZURE_CHINA_CLOUD.endpoints.resource_manager)
```

Encontre as constantes de nuvem soberana na [biblioteca msrestazure.azure_cloud](https://github.com/Azure/msrestazure-for-python/blob/master/msrestazure/azure_cloud.py).

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

Caso precise da biblioteca ADAL, execute `pip install adal`.

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

Nesse método, você cria um objeto de cliente usando as credenciais do usuário conectado com o comando CLI do Azure `az login`. O aplicativo será autorizado para qualquer e todas as operações como o usuário.

O SDK usa a ID de assinatura padrão ou você pode definir a assinatura antes de executar o código usando [`az account`](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli). Se você precisar se referir a assinaturas diferentes no mesmo script, use os métodos ['get_client_from_auth_file'](#authenticate-with-a-json-file) ou [`get_client_from_json_dict`](#authenticate-with-a-json-dictionary) descritos anteriormente neste artigo.

A função `get_client_from_cli_profile` deve ser usada somente para fins de experimentação e desenvolvimento antecipados, pois um usuário conectado normalmente tem privilégios de administrador ou proprietário e pode acessar a maioria dos recursos sem nenhuma permissão adicional. Para obter mais informações, consulte a observação anterior sobre como [usar credenciais de CLI com `DefaultAzureCredential`](#cli-auth-note).

### <a name="deprecated-authenticate-with-userpasscredentials"></a>Preterido: Autenticar com UserPassCredentials

Antes da [ADAL (Biblioteca de Autenticação do Microsoft Azure Active Directory) do Azure para Python](https://github.com/AzureAD/azure-activedirectory-library-for-python) ser disponibilizada, você usou a classe [`UserPassCredentials`](/python/api/msrestazure/msrestazure.azure_active_directory.userpasscredentials), atualmente preterida. Essa classe não permite a autenticação de dois fatores e não deve mais ser usada.

## <a name="see-also"></a>Confira também

- [Configurar o ambiente de desenvolvimento do Python local para o Azure](configure-local-development-environment.md)
- [Como atribuir permissões de função](how-to-assign-role-permissions.md)
- [Exemplo: Provisionar um grupo de recursos](azure-sdk-example-resource-group.md)
- [Exemplo: Provisionar e usar o Armazenamento do Microsoft Azure](azure-sdk-example-storage.md)
- [Exemplo: Provisionar um aplicativo Web e implantar o código](azure-sdk-example-web-app.md)
- [Exemplo: Provisionar e usar um banco de dados MySQL](azure-sdk-example-database.md)
- [Exemplo: Provisionar uma máquina virtual >>>](azure-sdk-example-virtual-machines.md)
