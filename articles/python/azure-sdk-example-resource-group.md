---
title: Provisionar um grupo de recursos usando as bibliotecas do Azure para Python
description: Use a biblioteca de gerenciamento de recursos no SDK do Azure para Python para criar um grupo de recursos a partir do código Python.
ms.date: 05/29/2020
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 459311e76f70e10a5da62c4205a9843427f76cec
ms.sourcegitcommit: 2f98cf2a394d4fd82ddc917ac1041c1dc08473b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89275110"
---
# <a name="example-use-the-azure-libraries-to-provision-a-resource-group"></a>Exemplo: Usar as bibliotecas do Azure para provisionar um grupo de recursos

Este exemplo demonstra como usar as bibliotecas de gerenciamento do SDK do Azure em um script Python para provisionar um grupo de recursos. (O [comando equivalente da CLI do Azure](#for-reference-equivalent-azure-cli-commands) será apresentado posteriormente neste artigo. Se preferir usar o portal do Azure, confira [Criar grupos de recursos](/azure/azure-resource-manager/management/manage-resource-groups-portal).)

Todos os comandos neste artigo funcionam da mesma forma no bash do Linux/Mac OS e nos shells de comando do Windows, a menos que haja uma observação.

## <a name="1-set-up-your-local-development-environment"></a>1: Configurar seu ambiente de desenvolvimento

Se ele ainda não foi configurado, siga todas as instruções em [Configurar o ambiente de desenvolvimento do Python local para o Azure](configure-local-development-environment.md).

Não se esqueça de criar uma entidade de serviço para o desenvolvimento local e de criar e ativar um ambiente virtual para este projeto.

## <a name="2-install-the-azure-library-packages"></a>2: Instalar os pacotes de biblioteca do Azure

Crie um arquivo chamado *requirements.txt* com o conteúdo abaixo:

```text
azure-mgmt-resource
azure-cli-core
```

Em um terminal ou prompt de comando com o ambiente virtual ativado, instale os requisitos:

```cmd
pip install -r requirements.txt
```

## <a name="3-write-code-to-provision-a-resource-group"></a>3: Escrever um código para provisionar um grupo de recursos

Crie um arquivo Python com o nome *provision_rg.py* e o código a seguir. Os comentários explicam os detalhes:

```python
# Import the needed management objects from the libraries. The azure.common library
# is installed automatically with the other libraries.
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource import ResourceManagementClient

# Obtain the management object for resources, using the credentials from the CLI login.
resource_client = get_client_from_cli_profile(ResourceManagementClient)

# Provision the resource group.
rg_result = resource_client.resource_groups.create_or_update(
    "PythonAzureExample-ResourceGroup-rg",
    {
        "location": "centralus"
    }
)

# Within the ResourceManagementClient is an object named resource_groups,
# which is of class ResourceGroupsOperations, which contains methods like
# create_or_update.
#
# The second parameter to create_or_update here is technically a ResourceGroup
# object. You can create the object directly using ResourceGroup(location=LOCATION)
# or you can express the object as inline JSON as shown here. For details,
# see Inline JSON pattern for object arguments at
# https://docs.microsoft.com/azure/developer/python/azure-sdk-overview#inline-json-pattern-for-object-arguments.

print(f"Provisioned resource group {rg_result.name} in the {rg_result.location} region")

# The return value is another ResourceGroup object with all the details of the
# new group. In this case the call is synchronous: the resource group has been
# provisioned by the time the call returns.

# Optional line to delete the resource group
#resource_client.resource_groups.delete("PythonAzureExample-ResourceGroup-rg")
```

Esse código usa os métodos de autenticação baseados em CLI (`get_client_from_cli_profile`), pois demonstra ações que você pode efetuar diretamente com a CLI do Azure. Em ambos os casos, você usará a mesma identidade para autenticação.

Para colocar esse código em um script de produção, você deve usar `DefaultAzureCredential` (recomendado) ou um método baseado na entidade de serviço, como descrito em [Como autenticar aplicativos Python com os serviços do Azure](azure-sdk-authenticate.md).

### <a name="reference-links-for-classes-used-in-the-code"></a>Links de referência para classes usadas no código

- [ResourceManagementClient (azure.mgmt.resource)](/python/api/azure-mgmt-resource/azure.mgmt.resource.resourcemanagementclient?view=azure-python)

## <a name="4-run-the-script"></a>4: Executar o script

```cmd
python provision_rg.py
```

## <a name="5-verify-the-resource-group"></a>5: Verificar o grupo de recursos

Você pode verificar se o grupo existe pelo portal do Azure ou da CLI do Azure.

- Portal do Azure: abra o [Portal do Azure](https://portal.azure.com), selecione **Grupos de recursos** e veja se o grupo aparece na lista. Se o portal já estiver aberto, use o comando **Atualizar** para atualizar a lista.

- CLI do Azure: execute o comando a seguir:

    ```azurecli
    az group show -n PythonAzureExample-ResourceGroup-rg
    ```

## <a name="6-clean-up-resources"></a>6: Limpar os recursos

```azurecli
az group delete -n PythonAzureExample-ResourceGroup-rg
```

Execute esse comando se você não precisar manter o grupo de recursos provisionado neste exemplo. Os grupos de recursos não incorrem em encargos contínuos na sua assinatura, mas recomendamos limpar os grupos que você não esteja usando ativamente.

Você também pode usar o método [`ResourceManagementClient.resource_groups.delete`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.resourcegroupsoperations?view=azure-python#delete-resource-group-name--custom-headers-none--raw-false--polling-true----operation-config-) para excluir um grupo de recursos do código.

### <a name="for-reference-equivalent-azure-cli-commands"></a>Para referência: comandos equivalentes da CLI do Azure

Os comandos a seguir da CLI do Azure executam as mesmas etapas de provisionamento do script Python:

```azurecli
az group create -n PythonAzureExample-ResourceGroup-rg -l centralus
```

## <a name="see-also"></a>Confira também

- [Exemplo: Provisionar o Armazenamento do Microsoft Azure](azure-sdk-example-storage.md)
- [Exemplo: Usar o Armazenamento do Microsoft Azure](azure-sdk-example-storage-use.md)
- [Exemplo: Provisionar um aplicativo Web e implantar o código](azure-sdk-example-web-app.md)
- [Exemplo: Provisionar e consultar um banco de dados](azure-sdk-example-database.md)
- [Exemplo: Provisionar uma máquina virtual >>>](azure-sdk-example-virtual-machines.md)
