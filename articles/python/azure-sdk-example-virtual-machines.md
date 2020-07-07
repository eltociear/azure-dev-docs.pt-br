---
title: Provisionar uma máquina virtual usando as bibliotecas do Azure para Python
description: Como provisionar uma máquina virtual do Azure usando o Python e as bibliotecas de gerenciamento do SDK do Azure.
ms.date: 05/29/2020
ms.topic: conceptual
ms.openlocfilehash: 4b11caa66eb297225b4b61000575a8a9c48edb19
ms.sourcegitcommit: 5ebbc1c06f98e29a146764661efbf34957020fe8
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2020
ms.locfileid: "84716084"
---
# <a name="example-use-the-azure-libraries-to-provision-a-virtual-machine"></a>Exemplo: Usar as bibliotecas do Azure para provisionar uma máquina virtual

Este exemplo demonstra como usar as bibliotecas de gerenciamento do SDK do Azure em um script Python para criar um grupo de recursos que contém uma máquina virtual Linux. ([Comandos de CLI do Azure equivalentes](#for-reference-equivalent-azure-cli-commands) são fornecidos posteriormente neste artigo.)

Todos os comandos neste artigo funcionam da mesma forma no bash do Linux/Mac OS e nos shells de comando do Windows, a menos que haja uma observação.

> [!NOTE]
> O provisionamento de uma máquina virtual por meio de código é um processo de várias etapas que envolve o provisionamento de vários outros recursos que a máquina virtual precisa. Se esse código for simplesmente executado da linha de comando, será muito mais fácil usar o comando [`az vm create`](/cli/azure/vm?view=azure-cli-latest#az-vm-create) que provisiona automaticamente esses recursos secundários com valores padrão para qualquer configuração que você decidir omitir. Os únicos argumentos necessários são um grupo de recursos, nome da VM, nome da imagem e credenciais de logon. Para obter mais informações, consulte [Criação rápida de uma máquina virtual com a CLI do Azure](/azure/virtual-machines/scripts/virtual-machines-windows-cli-sample-create-vm-quick-create).

## <a name="1-set-up-your-local-development-environment"></a>1: Configurar seu ambiente de desenvolvimento

Se ele ainda não foi configurado, siga todas as instruções em [Configurar o ambiente de desenvolvimento do Python local para o Azure](configure-local-development-environment.md).

Não se esqueça de criar uma entidade de serviço para o desenvolvimento local e de criar e ativar um ambiente virtual para este projeto.

## <a name="2-install-the-needed-azure-library-packages"></a>2: Instalar os pacotes de biblioteca do Azure necessários

1. Crie um arquivo *requirements. txt* que liste as bibliotecas de gerenciamento usadas neste exemplo:

    ```txt
    azure-mgmt-resource
    azure-mgmt-network
    azure-mgmt-compute
    azure-cli-core
    ```

1. Em um terminal ou prompt de comando com o ambiente virtual ativado, instale as bibliotecas de gerenciamento listadas no arquivo *requirements.txt*:

    ```cmd
    pip install -r requirements.txt
    ```

## <a name="3-write-code-to-provision-a-virtual-machine"></a>3: Escrever o código para provisionar uma máquina virtual

Crie um arquivo Python com o nome *provision_vm.py* e o código a seguir. Os comentários explicam os detalhes:

```python
# Import the needed management objects from the libraries. The azure.common library
# is installed automatically with the other libraries.
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.network import NetworkManagementClient
from azure.mgmt.compute import ComputeManagementClient

print(f"Provisioning a virtual machine...some operations might take a minute or two.")

# Step 1: Provision a resource group

# Obtain the management object for resources, using the credentials from the CLI login.
resource_client = get_client_from_cli_profile(ResourceManagementClient)

# Constants we need in multiple places: the resource group name and the region
# in which we provision resources. You can change these values however you want.
RESOURCE_GROUP_NAME = "PythonAzureExample-VM-rg"
LOCATION = "centralus"

# Provision the resource group.
rg_result = resource_client.resource_groups.create_or_update(RESOURCE_GROUP_NAME,
    {
        "location": LOCATION
    }
)


print(f"Provisioned resource group {rg_result.name} in the {rg_result.location} region")

# For details on the previous code, see Example: Provision a resource group
# at https://docs.microsoft.com/azure/developer/python/azure-sdk-example-resource-group

# Step 2: provision a virtual network

# A virtual machine requires a network interface client (NIC). A NIC requires
# a virtual network and subnet along with an IP address. Therefore we must provision
# these downstream components first, then provision the NIC, after which we
# can provision the VM.

# Network and IP address names
VNET_NAME = "python-example-vnet"
SUBNET_NAME = "python-example-subnet"
IP_NAME = "python-example-ip"
IP_CONFIG_NAME = "python-example-ip-config"
NIC_NAME = "python-example-nic"

# Obtain the management object for networks
network_client = get_client_from_cli_profile(NetworkManagementClient)

# Provision the virtual network and wait for completion
poller = network_client.virtual_networks.create_or_update(RESOURCE_GROUP_NAME,
    VNET_NAME,
    {
        "location": LOCATION,
        "address_space": {
            "address_prefixes": ["10.0.0.0/16"]
        }
    }
)

vnet_result = poller.result()

print(f"Provisioned virtual network {vnet_result.name} with address prefixes {vnet_result.address_space.address_prefixes}")

# Step 3: Provision the subnet and wait for completion
poller = network_client.subnets.create_or_update(RESOURCE_GROUP_NAME, 
    VNET_NAME, SUBNET_NAME,
    { "address_prefix": "10.0.0.0/24" }
)
subnet_result = poller.result()

print(f"Provisioned virtual subnet {subnet_result.name} with address prefix {subnet_result.address_prefix}")

# Step 4: Provision an IP address and wait for completion
poller = network_client.public_ip_addresses.create_or_update(RESOURCE_GROUP_NAME,
    IP_NAME,
    {
        "location": LOCATION,
        "sku": { "name": "Standard" },
        "public_ip_allocation_method": "Static",
        "public_ip_address_version" : "IPV4"
    }
)

ip_address_result = poller.result()

print(f"Provisioned public IP address {ip_address_result.name} with address {ip_address_result.ip_address}")

# Step 5: Provision the network interface client
poller = network_client.network_interfaces.create_or_update(RESOURCE_GROUP_NAME,
    NIC_NAME, 
    {
        "location": LOCATION,
        "ip_configurations": [ {
            "name": IP_CONFIG_NAME,
            "subnet": { "id": subnet_result.id },
            "public_ip_address": {"id": ip_address_result.id }
        }]
    }
)

nic_result = poller.result()

print(f"Provisioned network interface client {nic_result.name}")

# Step 6: Provision the virtual machine

# Obtain the management object for virtual machines
compute_client = get_client_from_cli_profile(ComputeManagementClient)

VM_NAME = "ExampleVM"
USERNAME = "azureuser"
PASSWORD = "ChangePa$$w0rd24"

print(f"Provisioning virtual machine {VM_NAME}; this operation might take a few minutes.")

# Provision the VM specifying only minimal arguments, which defaults to an Ubuntu 18.04 VM
# on a Standard DS1 v2 plan with a public IP address and a default virtual network/subnet.

poller = compute_client.virtual_machines.create_or_update(RESOURCE_GROUP_NAME, VM_NAME,
    {
        "location": LOCATION,
        "storage_profile": {
            "image_reference": {
                "publisher": 'Canonical',
                "offer": "UbuntuServer",
                "sku": "16.04.0-LTS",
                "version": "latest"
            }
        },
        "hardware_profile": {
            "vm_size": "Standard_DS1_v2"
        },
        "os_profile": {
            "computer_name": VM_NAME,
            "admin_username": USERNAME,
            "admin_password": PASSWORD
        },
        "network_profile": {
            "network_interfaces": [{
                "id": nic_result.id,
            }]
        }
    }
)

vm_result = poller.result()

print(f"Provisioned virtual machine {vm_result.name}")
```

Esse código usa os métodos de autenticação baseados em CLI (`get_client_from_cli_profile`), pois demonstra ações que você pode efetuar diretamente com a CLI do Azure. Em ambos os casos, você usará a mesma identidade para autenticação.

Para colocar esse código em um script de produção (por exemplo, para automatizar o gerenciamento da VM), você deve usar `DefaultAzureCredential` (recomendado) ou um método baseado na entidade de serviço, como descrito em [Como autenticar aplicativos Python com os serviços do Azure](azure-sdk-authenticate.md).

### <a name="reference-links-for-classes-used-in-the-code"></a>Links de referência para classes usadas no código

- [ResourceManagementClient (azure.mgmt.resource)](/python/api/azure-mgmt-resource/azure.mgmt.resource.resourcemanagementclient?view=azure-python)
- [NetworkManagementClient (azure.mgmt.network)](/python/api/azure-mgmt-network/azure.mgmt.network.networkmanagementclient?view=azure-python)
- [ComputeManagementClient (azure.mgmt.compute)](/python/api/azure-mgmt-compute/azure.mgmt.compute.computemanagementclient?view=azure-python)

## <a name="4-run-the-script"></a>4. Executar o script

```cmd
python provision_vm.py
```

O processo de provisionamento leva alguns minutos para ser concluído.

## <a name="5-verify-the-resources"></a>5. Verificar os recursos

Abra o [portal do Azure](https://portal.azure.com), navegue até o grupo de recursos "PythonSDKExample-VM-rg" e anote a máquina virtual, o disco virtual, o grupo de segurança de rede, o endereço IP público, o adaptador de rede e a rede virtual:

![A página do portal do Azure do novo grupo de recursos mostrando a máquina virtual e os recursos relacionados](media/azure-sdk-example-virtual-machines/portal-vm-resources.png)

### <a name="for-reference-equivalent-azure-cli-commands"></a>Para referência: comandos equivalentes da CLI do Azure

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
rem Provision the resource group

az group create -n PythonAzureExample-VM-rg -l centralus

rem Provision a virtual network and subnet

az network vnet create -g PythonAzureExample-VM-rg -n python-example-vnet ^
    --address-prefix 10.0.0.0/16 --subnet-name python-example-subnet ^
    --subnet-prefix 10.0.0.0/24

rem Provision a public IP address

az network public-ip create -g PythonAzureExample-VM-rg -n python-example-ip ^
    --allocation-method Dynamic --version IPv4

rem Provision a network interface client

az network nic create -g PythonAzureExample-VM-rg --vnet-name python-example-vnet ^
    --subnet python-example-subnet -n python-example-nic ^
    --public-ip-address python-example-ip

rem Provision the virtual machine

az vm create -g PythonAzureExample-VM-rg -n ExampleVM -l "centralus" ^
    --nics python-example-nic --image UbuntuLTS ^
    --admin-username azureuser --admin-password ChangePa$$w0rd24
```

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
# Provision the resource group

az group create -n PythonAzureExample-VM-rg -l centralus

# Provision a virtual network and subnet

az network vnet create -g PythonAzureExample-VM-rg -n python-example-vnet \
    --address-prefix 10.0.0.0/16 --subnet-name python-example-subnet \
    --subnet-prefix 10.0.0.0/24

# Provision a public IP address

az network public-ip create -g PythonAzureExample-VM-rg -n python-example-ip \
    --allocation-method Dynamic --version IPv4

# Provision a network interface client

az network nic create -g PythonAzureExample-VM-rg --vnet-name python-example-vnet \
    --subnet python-example-subnet -n python-example-nic \
    --public-ip-address python-example-ip

# Provision the virtual machine

az vm create -g PythonAzureExample-VM-rg -n ExampleVM -l "centralus" \
    --nics python-example-nic --image UbuntuLTS \
    --admin-username azureuser --admin-password ChangePa$$w0rd24

```

---

## <a name="6-clean-up-resources"></a>6: Limpar os recursos

```azurecli
az group delete -n PythonAzureExample-VM-rg
```

Execute esse comando se você não precisar manter os recursos criados neste exemplo e quiser evitar encargos contínuos na sua assinatura.

## <a name="see-also"></a>Confira também

- [Exemplo: Provisionar um grupo de recursos](azure-sdk-example-resource-group.md)
- [Exemplo: Provisionar o Armazenamento do Microsoft Azure](azure-sdk-example-storage.md)
- [Exemplo: Usar o Armazenamento do Microsoft Azure](azure-sdk-example-storage-use.md)
- [Exemplo: Provisionar um aplicativo Web e implantar o código](azure-sdk-example-web-app.md)
- [Exemplo: Provisionar e consultar um banco de dados](azure-sdk-example-database.md)

O seguinte contêiner de recursos tem exemplos mais abrangentes de uso do Python para criar uma máquina virtual:

- [Criar e gerenciar VMs do Windows no Azure usando Python](/azure/virtual-machines/windows/python). Você pode usar este exemplo para criar VMs do Linux alterando o parâmetro `storage_profile`.
- [Amostras de Gerenciamento de Máquinas Virtuais do Azure – Python](https://github.com/Azure-Samples/virtual-machines-python-manage) (GitHub). O exemplo demonstra operações de gerenciamento adicionais, como iniciar e reiniciar uma VM, parar e excluir uma VM, aumentar o tamanho do disco e gerenciar discos de dados.
