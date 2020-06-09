---
title: Padrões de uso com as bibliotecas do Azure para Python
description: Uma visão geral dos padrões de uso comum com as bibliotecas do SDK do Azure para Python
ms.date: 05/26/2020
ms.topic: conceptual
ms.openlocfilehash: f712dc41233b8301e370c9eb63786d8e2d7f8c70
ms.sourcegitcommit: efab6be74671ea4300162e0b30aa8ac134d3b0a9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84256271"
---
# <a name="azure-libraries-for-python-usage-patterns"></a>Bibliotecas do Azure para padrões de uso do Python

O SDK do Azure para Python é composto unicamente de muitas bibliotecas independentes listadas na [página de índice do SDK do Azure para Python](https://azure.github.io/azure-sdk/releases/latest/all/python.html).

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
- [Exemplo: Provisionar uma máquina virtual >>>](azure-sdk-example-virtual-machines.md)

Você pode experimentar esses exemplos em qualquer ordem, pois eles não são sequenciais nem interdependentes.
