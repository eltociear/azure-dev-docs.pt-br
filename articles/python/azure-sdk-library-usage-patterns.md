---
title: Padrões de uso com as bibliotecas do Azure para Python
description: Uma visão geral dos padrões de uso comum com as bibliotecas do SDK do Azure para Python
ms.date: 06/09/2020
ms.topic: conceptual
ms.openlocfilehash: d1cd1b1c965fdf5b6907c9842260d4c029d625f5
ms.sourcegitcommit: b3e506c6f140d91e6fdd9dcadf22ab1aa67f6978
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2020
ms.locfileid: "84942405"
---
# <a name="azure-libraries-for-python-usage-patterns"></a>Bibliotecas do Azure para padrões de uso do Python

O SDK do Azure para Python é composto unicamente por muitas bibliotecas independentes listadas no [índice de pacote do SDK do Python](azure-sdk-library-package-index.md).

Todas as bibliotecas compartilham determinadas características e padrões de uso comuns, como a instalação e o uso de JSON embutido para argumentos de objeto.

## <a name="library-installation"></a>Instalação de biblioteca

Para instalar um pacote de biblioteca específico, basta usar `pip install`:

```cmd
# Install the management library for Azure Storage
pip install azure-mgmt-storage
```

```cmd
# Install the client library for Azure Storage
pip install azure-storage-blob
```

`pip install` recupera a versão mais recente de uma biblioteca no seu ambiente atual do Python.

Você também pode usar `pip` para desinstalar bibliotecas e instalar versões específicas, incluindo versões prévias. Para mais informações, consulte [Como instalar pacotes de biblioteca do Azure para Python](azure-sdk-install.md).

## <a name="asynchronous-operations"></a>Operações assíncronas

Muitas operações que você invoca por meio de objetos de cliente e gerenciamento de clientes (como [`WebSiteManagementClient.web_apps.create_or_update`](/python/api/azure-mgmt-web/azure.mgmt.web.v2019_08_01.operations.webappsoperations?view=azure-python#create-or-update-resource-group-name--name--site-envelope--custom-headers-none--raw-false--polling-true----operation-config-)) retornam um objeto do tipo `AzureOperationPoller[<type>]` em que `<type>` é específico para a operação em questão.

Um tipo de retorno [`AzureOperationPoller`](/python/api/msrestazure/msrestazure.azure_operation.azureoperationpoller?view=azure-python) significa que a operação é assíncrona. Você deve chamar o método `result` do instrumento de sondagem para aguardar o resultado real da operação ficar disponível.

O seguinte código, extraído de [Exemplo: Provisionar e implantar um aplicativo Web](azure-sdk-example-web-app.md), mostra um exemplo de como usar o instrumento de sondagem para aguardar um resultado:

```python
poller = app_service_client.web_apps.create_or_update(RESOURCE_GROUP_NAME,
    WEB_APP_NAME,
    {
        "location": LOCATION,
        "server_farm_id": plan_result.id,
        "site_config": {
            "linux_fx_version": "python|3.8"
        }
    }
)

web_app_result = poller.result()
```

Nesse caso, o valor retornado de `create_or_update` é do tipo `AzureOperationPoller[Site]`, o que significa que o valor retornado de `poller.result()` é um objeto [Site](/python/api/azure-mgmt-web/azure.mgmt.web.v2019_08_01.models.site?view=azure-python).

## <a name="exceptions"></a>Exceções

Em geral, as bibliotecas do Azure geram exceções quando as operações não são executadas conforme o esperado, incluindo solicitações HTTP com falha para a API REST do Azure. Para o código do aplicativo, você pode usar blocos `try...except` em relação a operações de biblioteca.

Para obter mais informações sobre o tipo de exceções que podem ser geradas, confira a documentação da operação em questão.

## <a name="logging"></a>Registro em log

As bibliotecas do Azure mais recentes usam a biblioteca `logging` padrão do Python para gerar a saída de log. Você pode definir o nível de registros em log para bibliotecas individuais, grupos de bibliotecas ou todas as bibliotecas. Depois de registrar um manipulador de fluxo de log, você poderá habilitar o registro em log para um objeto de cliente específico ou uma operação específica. Para obter mais informações, confira [Registrar em log nas bibliotecas do Azure](azure-sdk-logging.md).

## <a name="proxy-configuration"></a>Configuração de Proxy

Para especificar um proxy, você pode usar variáveis de ambiente ou argumentos opcionais. Para obter mais informações, confira [Como configurar proxies](azure-sdk-configure-proxy.md).

## <a name="optional-arguments-for-client-objects-and-methods"></a>Argumentos opcionais para objetos e métodos de cliente

Na documentação de referência da biblioteca, você geralmente vê um argumento `**kwargs` ou `**operation_config**` na assinatura de um construtor de objeto de cliente ou de um método de operação específico. Esses espaços reservados indicam que o objeto ou método em questão pode dar suporte a argumentos nomeados adicionais. Normalmente, a documentação de referência indica os argumentos específicos que você pode usar. Há também alguns argumentos gerais que frequentemente têm suporte, conforme descrito nas seções a seguir.

### <a name="arguments-for-libraries-based-on-azurecore"></a>Argumentos para bibliotecas baseadas no azure.core

Esses argumentos se aplicam a essas bibliotecas listadas em [Python – Novas Bibliotecas](https://azure.github.io/azure-sdk/releases/latest/#python).

| Nome                       | Type | Padrão     | Descrição |
| ---                        | ---  | ---         | ---         |
| logging_enable             | bool | Falso       | Habilita o registro em log. Para obter mais informações, confira [Registrar em log nas bibliotecas do Azure](azure-sdk-logging.md). |
| proxies                    | dict | {}          | URLs do servidor proxy. Para obter mais informações, confira [Como configurar proxies](azure-sdk-configure-proxy.md). |
| use_env_settings           | bool | True        | Se for True, permitirá o uso de variáveis de ambiente `HTTP_PROXY` e `HTTPS_PROXY` para proxies. Se for False, as variáveis de ambiente serão ignoradas. Para obter mais informações, confira [Como configurar proxies](azure-sdk-configure-proxy.md). |
| connection_timeout         | INT  | 300         | O tempo limite em segundos para estabelecer uma conexão com os pontos de extremidade da API REST do Azure. |
| read_timeout               | INT  | 300         | O tempo limite em segundos para concluir uma operação da API REST do Azure (ou seja, aguardando uma resposta). |
| retry_total                | INT  | 10          | O número de tentativas de repetição permitidas para chamadas à API REST. Use `retry_total=0` para desabilitar repetições. |
| retry_mode                 | enum | exponential | Aplica o tempo de repetição de maneira linear ou exponencial. Se for 'single', as repetições serão feitas em intervalos regulares. Se for 'exponential', cada repetição aguardará o dobro da repetição anterior. |

Bibliotecas individuais não são obrigadas a dar suporte a nenhum desses argumentos, portanto sempre consulte a documentação de referência para cada biblioteca para obter detalhes exatos.

### <a name="arguments-for-non-core-libraries"></a>Argumentos para bibliotecas não principais

| Nome               | Type | Padrão | Descrição |
| ---                | ---  | ---     | ---         |
| verificar             | bool | True    | Verifique o certificado SSL. |
| cert               | str  | Nenhum    | Caminho para o certificado local para verificação do lado do cliente. |
| tempo limite            | INT  | 30      | Tempo limite para estabelecer uma conexão de servidor em segundos. |
| allow_redirects    | bool | Falso   | Habilite redirecionamentos. |
| max_redirects      | INT  | 30      | Número máximo de redirecionamentos permitidos. |
| proxies            | dict | {}      | URL do servidor proxy. Para obter mais informações, confira [Como configurar proxies](azure-sdk-configure-proxy.md). |
| use_env_proxies    | bool | Falso   | Habilite a leitura de configurações de proxy de variáveis de ambiente local. |
| retries            | INT  | 10      | O número total de tentativas de repetição permitidas. |
| enable_http_logger | bool | Falso   | Habilite logs de HTTP no modo de depuração. |

## <a name="inline-json-pattern-for-object-arguments"></a>Padrão JSON embutido para argumentos de objeto

Muitas operações nas bibliotecas do Azure permitem que você expresse argumentos de objeto como objetos discretos ou como JSON embutido.

Por exemplo, suponha que você tenha um objeto [`ResourceManagementClient`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.resourcemanagementclient?view=azure-python) por meio do qual você cria um grupo de recursos com seu método [`create_or_update`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.resourcegroupsoperations?view=azure-python#create-or-update-resource-group-name--parameters--custom-headers-none--raw-false----operation-config-)). O segundo argumento para esse método é do tipo [`ResourceGroup`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.models.resourcegroup?view=azure-python).

Para chamar `create_or_update` você pode criar uma instância discreta do `ResourceGroup` diretamente com seus argumentos necessários (`location` neste caso):

```python
rg_result = resource_client.resource_groups.create_or_update(
    "PythonSDKExample-rg",
    ResourceGroup(location="centralus")
)
```

Como alternativa, você pode passar os mesmos parâmetros como JSON embutido:

```python
rg_result = resource_client.resource_groups.create_or_update(
    "PythonSDKExample-rg",
    {
      "location": "centralus"
    }
)
```

Ao usar o JSON, as bibliotecas do Azure convertem automaticamente o JSON embutido para o tipo de objeto apropriado para o argumento em questão.

Os objetos também podem ter argumentos de objeto aninhados. Nesse caso, você também pode usar o JSON aninhado.

Por exemplo, vamos supor que você tenha uma instância de objeto [`KeyVaultManagementClient`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.keyvaultmanagementclient?view=azure-python) e chama seu método [`create_or_update`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.operations.vaultsoperations?view=azure-python#create-or-update-resource-group-name--vault-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-). Nesse caso, o terceiro argumento é do tipo [`VaultCreateOrUpdateParameters`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.vaultcreateorupdateparameters?view=azure-python), que por sua vez contém um argumento do tipo [`VaultProperties`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.vaultproperties?view=azure-python). `VaultProperties`, por sua vez, contém argumentos de objeto do tipo [`Sku`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.sku?view=azure-python) e [`list[AccessPolicyEntry`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.accesspolicyentry?view=azure-python). Uma `Sku` contém um objeto [`SkuName`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.skuname?view=azure-python) e cada `AccessPolicyEntry` contém um objeto [`Permissions`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.permissions?view=azure-python).

Para chamar `create_or_update` com objetos inseridos, use um código como o a seguir (supondo que `tenant_id` e `object_id` já estejam definidos). Você também pode criar os objetos necessários antes da chamada de função.

```python
operation = keyvault_client.vaults.create_or_update(
    "PythonSDKExample-rg",
    "keyvault01",
    VaultCreateOrUpdateParameters(
        location="centralus",
        properties=VaultProperties(
            tenant_id=tenant_id,
            sku=Sku(name="standard"),
            access_policies=[
                AccessPolicyEntry(
                    tenant_id=tenant_id,
                    object_id=object_id,
                    permissions=Permissions(keys=['all'], secrets=['all'])
                )
            ]
        )
    )
)
```

A mesma chamada que usa JSON embutido aparece da seguinte maneira:

```python
operation = keyvault_client.vaults.create_or_update(
    "PythonSDKExample-rg",
    "keyvault01",
    {
        'location': 'centralus',
        'properties': {
            'sku': {
                'name': 'standard'
            },
            'tenant_id': tenant_id,
            'access_policies': [{
                'object_id': object_id,
                'tenant_id': tenant_id,
                'permissions': {
                    'keys': ['all'],
                    'secrets': ['all']
                }
            }]
        }
    }
)
```

Como ambas as formas são equivalentes, você pode escolher qual preferir e pode até mesmo misturá-las.

Se seu JSON não for formado corretamente, você normalmente receberá o erro “DeserializationError: não é possível desserializar para object: type, AttributeError: objeto 'str' não tem atributo 'get'". Uma causa comum desse erro é fornecer uma cadeia de caracteres única para uma propriedade quando a biblioteca espera um objeto JSON aninhado. Por exemplo, o uso de `"sku": "standard"` no exemplo anterior gera esse erro porque o parâmetro `sku` é um objeto `Sku` que espera o objeto embutido JSON, nesse caso `{ "name": "standard"}`, que é mapeado para o tipo `SkuName` esperado.

## <a name="next-steps"></a>Próximas etapas

Agora que você reconhece os padrões comuns para usar as bibliotecas do Azure para Python, consulte os seguintes exemplos independentes para explorar cenários específicos de gerenciamento e de biblioteca de clientes:

- [Exemplo: Criar um grupo de recursos >>>](azure-sdk-example-resource-group.md)
- [Exemplo: Usar o Armazenamento do Microsoft Azure](azure-sdk-example-storage.md)
- [Exemplo: Provisionar um aplicativo Web e implantar o código](azure-sdk-example-web-app.md)
- [Exemplo: Provisionar e consultar um banco de dados](azure-sdk-example-database.md)
- [Exemplo: Provisionar uma máquina virtual >>>](azure-sdk-example-virtual-machines.md)

Você pode experimentar esses exemplos em qualquer ordem, pois eles não são sequenciais nem interdependentes.
