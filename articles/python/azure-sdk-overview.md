---
title: Usar o SDK do Azure para Python
description: Visão geral dos recursos e das funcionalidades do SDK do Azure para Python que ajudam os desenvolvedores a serem mais produtivos ao provisionar, usar e gerenciar recursos do Azure.
ms.date: 05/13/2020
ms.topic: conceptual
ms.openlocfilehash: 856487b06e7a84b0659126d8959ce45b5309a103
ms.sourcegitcommit: fbbc341a0b9e17da305bd877027b779f5b0694cc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83631542"
---
# <a name="use-the-azure-sdk-for-python"></a>Usar o SDK do Azure para Python

O SDK do Azure de software livre para Python simplifica o provisionamento, o gerenciamento e o uso dos recursos do Azure com base no código de aplicativo do Python.

## <a name="the-details-you-really-want-to-know"></a>Os detalhes que você realmente quer saber

- O SDK é compatível com Python 2.7 e Python 3.5.3 ou posterior e foi testado com o PyPy 5.4+.

- O SDK é composto por mais de 180 bibliotecas Python individuais relacionadas a serviços específicos do Azure.

- Para instalar as bibliotecas de que precisa com `pip install <library_name>`, use os nomes de biblioteca na [lista de versões](https://azure.github.io/azure-sdk/releases/latest/all/python.html). Para obter mais detalhes, consulte [Instalar bibliotecas do SDK do Azure](azure-sdk-install.md).

- Há bibliotecas de "gerenciamento" e de "cliente" distintas (às vezes chamadas de bibliotecas de "plano de gerenciamento" e de "plano de dados"). Cada conjunto atende a diferentes finalidades e é usado por diferentes tipos de código. Para obter mais informações, consulte as seguintes seções deste artigo:
  - [Provisionar e gerenciar recursos do Azure com bibliotecas de gerenciamento](#provision-and-manage-azure-resources-with-management-libraries)
  - [Conectar e usar os recursos do Azure com bibliotecas de clientes](#connect-to-and-use-azure-resources-with-client-libraries)

- A documentação do SDK é encontrada na [Referência do SDK do Azure para Python](/python/api/overview/azure/?view=azure-python), que é organizada por serviço do Azure ou no [navegador de API do Python](/python/api/?view=azure-python), que é organizado por nome de pacote. No momento, pode ser necessário clicar em várias camadas para obter as classes e os métodos do seu interesse. Queremos nos desculpar por essa experiência insatisfatória. Estamos trabalhando para melhorá-la.

- Para experimentar as bibliotecas por conta própria, comece pelo [Exemplo: Criar um grupo de recursos](azure-sdk-example-resource-group.md) e, em seguida, [Exemplo: Usar o Armazenamento do Microsoft Azure](azure-sdk-example-storage.md).

### <a name="non-essential-but-still-interesting-details"></a>Informações não essenciais, mas ainda interessantes

- Como a CLI do Azure é escrita em Python usando as bibliotecas de gerenciamento do SDK, tudo que é possível fazer com os comandos da CLI do Azure também pode ser feito com um script Python. Dito isso, os comandos da CLI fornecem muitos recursos úteis, como a execução de várias tarefas ao mesmo tempo, o tratamento automático de operações assíncronas, a formatação de saída como cadeias de conexão e assim por diante. Consequentemente, usar a CLI (ou seu equivalente, o Azure PowerShell) para scripts automatizados de provisionamento e gerenciamento pode ser significativamente mais conveniente do que escrever o código Python equivalente, a menos que você queira ter um grau muito maior de controle sobre o processo.

- O SDK do Azure para Python é uma camada do Python sobre a API REST subjacente do Azure, permitindo que você use essas APIs por meio de paradigmas conhecidos do Python. No entanto, você sempre pode usar a API REST diretamente do código Python, se desejar.

- Você pode encontrar o código-fonte do DSK em [https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python). Como um projeto de software livre, contribuições são bem-vindas!

- Embora você possa usar o SDK com interpretadores, como IronPython e Jython que nós não testamos, é possível encontrar problemas isolados e incompatibilidades.

- O repositório de origem da documentação do SDK é encontrado em [https://github.com/MicrosoftDocs/azure-docs-sdk-python/](https://github.com/MicrosoftDocs/azure-docs-sdk-python/).

- Atualmente estamos atualizando as bibliotecas do SDK do Azure para Python para compartilhar padrões de nuvem comuns, como protocolos de autenticação, registro em log, rastreamento, protocolos de transporte, respostas em buffer e novas tentativas.

  - Essa funcionalidade compartilhada está presente na biblioteca [azure-core](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/core/azure-core).

  - As bibliotecas que atualmente funcionam com a biblioteca principal estão listadas nas [Versões mais recentes do SDK do Azure para Python](https://azure.github.io/azure-sdk/releases/latest/#python). Essas bibliotecas, principalmente as bibliotecas de cliente, são, às vezes, chamadas de "faixa 2".

  - As bibliotecas de gerenciamento que ainda não foram atualizadas são chamadas, às vezes, de "faixa 1".

- Para obter detalhes sobre as diretrizes que aplicamos ao SDK, consulte as [Diretrizes do Python: Introdução](https://azure.github.io/azure-sdk/python_introduction.html).

## <a name="provision-and-manage-azure-resources-with-management-libraries"></a>Provisionar e gerenciar recursos do Azure com bibliotecas de gerenciamento

As bibliotecas de *gerenciamento* do SDK (ou “plano de gerenciamento”) com nomes que começam com `azure-mgmt-` ajudam a criar, provisionar e de gerenciar recursos do Azure a partir de scripts do Python. Todos os serviços do Azure têm bibliotecas de gerenciamento correspondentes.

Com as bibliotecas de gerenciamento, é possível escrever scripts de configuração e de implantação para executar as mesmas tarefas que podem ser acessadas pelo [portal do Azure](https://portal.azure.com) ou pela [CLI do Azure](/cli/azure/install-azure-cli). (Como observamos anteriormente, a CLI do Azure é escrita em Python e usa as bibliotecas de gerenciamento para implementar seus vários comandos.)

Para obter detalhes sobre como trabalhar com cada biblioteca de gerenciamento, veja o arquivo *README.md* ou *README.rst* localizado na pasta de projeto da biblioteca no [repositório GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk) do SDK. Também é possível encontrar trechos de código adicionais na [documentação de referência](/python/api?view=azure-python) e nas [Amostras do Azure](https://docs.microsoft.com/samples/browse/?languages=python&products=azure).

## <a name="connect-to-and-use-azure-resources-with-client-libraries"></a>Conectar e usar os recursos do Azure com bibliotecas de clientes

As bibliotecas de *cliente* do SDK (ou “plano de dados”) ajudam a escrever código de aplicativos Python para interagir com serviços já provisionados. O SDK oferece bibliotecas de cliente somente para os serviços que dão suporte a uma API de cliente.

Para obter detalhes sobre como trabalhar com cada biblioteca de clientes, veja o arquivo *README.md* ou *README.rst* localizado na pasta de projeto da biblioteca no [repositório GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk) do SDK. Também é possível encontrar trechos de código adicionais na [documentação de referência](/python/api?view=azure-python) e nas [Amostras do Azure](https://docs.microsoft.com/samples/browse/?languages=python&products=azure).

## <a name="inline-json-pattern-for-object-arguments"></a>Padrão JSON embutido para argumentos de objeto

Muitas operações no SDK do Azure dão suporte a um padrão comum que permite expressar argumentos de objeto como objetos discretos ou como JSON embutido.

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

Ao usar o JSON, o SDK converte automaticamente o JSON embutido para o tipo de objeto apropriado para o argumento em questão.

Os objetos também podem ter argumentos de objeto aninhados. Nesse caso, você também pode usar o JSON aninhado.

Por exemplo, vamos supor que você tenha uma instância de objeto [`KeyVaultManagementClient`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.keyvaultmanagementclient?view=azure-python) e chama seu método [`create_or_update`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.operations.vaultsoperations?view=azure-python#create-or-update-resource-group-name--vault-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-). Nesse caso, o terceiro argumento é do tipo [`VaultCreateOrUpdateParameters`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.vaultcreateorupdateparameters?view=azure-python), que por sua vez contém um argumento do tipo [`VaultProperties`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.vaultproperties?view=azure-python). `VaultProperties`, por sua vez, contém argumentos de objeto do tipo [`Sku`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.sku?view=azure-python) e [`list[AccessPolicyEntry`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.accesspolicyentry?view=azure-python). Uma `Sku` contém um objeto [`SkuName`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.skuname?view=azure-python) e cada `AccessPolicyEntry` contém um objeto [`Permissions`](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault.v2019_09_01.models.permissions?view=azure-python).

Chamar `create_or_update` com objetos inseridos usa um código como o seguinte (supondo que `tenant_id` e `object_id` já estejam definidos):

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

Como ambas as formas são totalmente equivalentes, você pode escolher a que preferir e pode até mesmo misturá-las.

Se seu JSON não for formado corretamente, você normalmente receberá o erro “DeserializationError: não é possível desserializar para object: type, AttributeError: objeto 'str' não tem atributo 'get'". Uma causa comum desse erro é fornecer uma única cadeia de caracteres para uma propriedade quando o SDK espera um objeto JSON aninhado. Por exemplo, o uso de `"sku": "standard"` no exemplo anterior gera esse erro porque o parâmetro `sku` é um objeto `Sku` que espera o objeto embutido JSON, nesse caso `{ "name": "standard"}`, que é mapeado para o tipo `SkuName` esperado.

## <a name="next-step"></a>Próxima etapa

> [!div class="nextstepaction"]
> [Instalar bibliotecas de SDK >>>](azure-sdk-install.md)

## <a name="get-help-and-connect-with-the-sdk-team"></a>Obtenha ajuda e conecte-se com a equipe do SDK

- Visite a [documentação do SDK do Azure para Python](https://aka.ms/python-docs)
- Poste perguntas para a comunidade no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sdk-python)
- Problemas em aberto no SDK no [GitHub](https://github.com/Azure/azure-sdk-for-python/issues)
- Mencione [@AzureSDK](https://twitter.com/AzureSdk/) no Twitter
