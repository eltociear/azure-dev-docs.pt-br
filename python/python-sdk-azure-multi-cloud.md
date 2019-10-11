---
title: Conectar-se a todas as regiões – SDK do Azure para Python em várias nuvens
description: no Azure em todas as regiões
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 02/22/2018
ms.topic: conceptual
ms.devlang: python
ms.custom: seo-python-october2019
ms.openlocfilehash: 03c2a42463332cbeaffdcc7373c04967018af804
ms.sourcegitcommit: bed07b313eeab51281d1a6d4eba67a75524b2f57
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72172499"
---
# <a name="multi-cloud-connect-to-all-regions-with-the-azure-sdk-for-python"></a>Várias nuvens: Conectar-se a todas as regiões com o SDK do Azure para Python

É possível usar o SDK do Azure para Python para conectar-se a todas as regiões em que o Azure [está disponível](https://azure.microsoft.com/regions/services).

Por padrão, o SDK do Azure para Python está configurado para se conectar ao Azure público.

## <a name="using-predeclared-cloud-definition"></a>Usando definições de nuvem declaradas previamente

> [!IMPORTANT]
> O pacote `msrestazure` deve ser superior ou igual a 0.4.11 para esta seção.

Você pode usar o módulo `azure_cloud` do `msrestazure`

```python
from msrestazure.azure_cloud import AZURE_CHINA_CLOUD
from msrestazure.azure_active_directory import UserPassCredentials
from azure.mgmt.resource import ResourceManagementClient

credentials = UserPassCredentials(
    login,
    password,
    cloud_environment=AZURE_CHINA_CLOUD
)
client = ResourceManagementClient(
    credentials,
    subscription_id,
    base_url=AZURE_CHINA_CLOUD.endpoints.resource_manager
)
``` 
  
As definições de nuvem disponíveis são
  - AZURE_PUBLIC_CLOUD
  - AZURE_CHINA_CLOUD
  - AZURE_US_GOV_CLOUD
  - AZURE_GERMAN_CLOUD

## <a name="using-your-own-cloud-definition-eg-azure-stack"></a>Usando sua própria definição de nuvem (por exemplo, Azure Stack)
O ARM tem um ponto de extremidade de metadados para ajudá-lo:

```python
from msrestazure.azure_cloud import get_cloud_from_metadata_endpoint
from msrestazure.azure_active_directory import UserPassCredentials
from azure.mgmt.resource import ResourceManagementClient

mystack_cloud = get_cloud_from_metadata_endpoint("https://myazurestack-arm-endpoint.com")
credentials = UserPassCredentials(
    login,
    password,
    cloud_environment=mystack_cloud
)
client = ResourceManagementClient(
    credentials,
    subscription_id,
    base_url=mystack_cloud.endpoints.resource_manager
)
```
## <a name="using-adal"></a>Usando o ADAL

Para se conectar a outra região, algumas coisas precisam ser consideradas:

- Qual é o ponto de extremidade no qual se solicita um token (autenticação)?
- Qual é o ponto de extremidade no qual vou usar esse token (uso)?

Este é um exemplo genérico:

```python
import adal
from msrestazure.azure_active_directory import AdalAuthentication
from azure.mgmt.resource import ResourceManagementClient

# Service Principal
tenant = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
client_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
password = 'password'

# Public Azure - default values
authentication_endpoint = 'https://login.microsoftonline.com/'
azure_endpoint = 'https://management.azure.com/'
    
context = adal.AuthenticationContext(authentication_endpoint+tenant)
credentials = AdalAuthentication(
    context.acquire_token_with_client_credentials,
    azure_endpoint,
    client_id,
    password
)
subscription_id = '33333333-3333-3333-3333-333333333333'

resource_client = ResourceManagementClient(
    credentials,
    subscription_id,
    base_url=azure_endpoint
)
```

### <a name="azure-government"></a>Azure Government
```python
import adal
from msrestazure.azure_active_directory import AdalAuthentication
from azure.mgmt.resource import ResourceManagementClient

# Service Principal
tenant = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
client_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
password = 'password'

# Government
authentication_endpoint = 'https://login.microsoftonline.us/'
azure_endpoint = 'https://management.usgovcloudapi.net/'
    
context = adal.AuthenticationContext(authentication_endpoint+tenant)
credentials = AdalAuthentication(
    context.acquire_token_with_client_credentials,
    azure_endpoint,
    client_id,
    password
)
subscription_id = '33333333-3333-3333-3333-333333333333'

resource_client = ResourceManagementClient(
    credentials,
    subscription_id,
    base_url=azure_endpoint
)
```

### <a name="azure-germany"></a>Azure Alemanha
```python
import adal
from msrestazure.azure_active_directory import AdalAuthentication
from azure.mgmt.resource import ResourceManagementClient

# Service Principal
tenant = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
client_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
password = 'password'

# Azure Germany
authentication_endpoint = 'https://login.microsoftonline.de/'
azure_endpoint = 'https://management.microsoftazure.de/'
    
context = adal.AuthenticationContext(authentication_endpoint+tenant)
credentials = AdalAuthentication(
    context.acquire_token_with_client_credentials,
    azure_endpoint,
    client_id,
    password
)
subscription_id = '33333333-3333-3333-3333-333333333333'

resource_client = ResourceManagementClient(
    credentials,
    subscription_id,
    base_url=azure_endpoint
)
```

### <a name="azure-china"></a>Azure China
```python
import adal
from msrestazure.azure_active_directory import AdalAuthentication
from azure.mgmt.resource import ResourceManagementClient

# Service Principal
tenant = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
client_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
password = 'password'

# Azure China
authentication_endpoint = 'https://login.chinacloudapi.cn/'
azure_endpoint = 'https://management.chinacloudapi.cn/'
    
context = adal.AuthenticationContext(authentication_endpoint+tenant)
credentials = AdalAuthentication(
    context.acquire_token_with_client_credentials,
    azure_endpoint,
    client_id,
    password
)
subscription_id = '33333333-3333-3333-3333-333333333333'

resource_client = ResourceManagementClient(
    credentials,
    subscription_id,
    base_url=azure_endpoint
)
```
