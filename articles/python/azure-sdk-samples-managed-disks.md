---
title: Managed Disks
description: Criar, redimensionar e atualizar um disco gerenciado.
ms.topic: conceptual
ms.date: 6/15/2017
ms.custom: devx-track-python
ms.openlocfilehash: b1973276f6cf080006f61ddc3a2805c345591e89
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831522"
---
# <a name="managed-disks"></a>Managed Disks

O Azure Managed Disks oferece gerenciamento de discos simplificado, escalabilidade avançada, bem como segurança e escala aprimoradas. Ele retira a noção de conta de armazenamento para discos, permitindo que os clientes façam o dimensionamento sem se preocupar com as limitações associadas às contas de armazenamento. Esta postagem fornece uma breve introdução e referência sobre o consumo do serviço do Python.

Da perspectiva de um desenvolvedor, a experiência do Managed Disks na CLI do Azure é única em relação à linguagem quando comparada com a CLI em outras ferramentas multiplataforma. Você pode usar o SDK do [Azure Python](https://azure.microsoft.com/develop/python/) e o [pacote do azure-mgmt-compute 0.33.0](https://pypi.python.org/pypi/azure-mgmt-compute) para administrar Managed Disks. Você pode criar um cliente de computação usando este [tutorial](/python/api/overview/azure/virtualmachines?view=azure-python).

## <a name="standalone-managed-disks"></a>Managed Disks Autônomos

Você pode facilmente criar Managed Disks autônomos de várias maneiras.

### <a name="create-an-empty-managed-disk"></a>Criar um Managed Disk vazio

```python
from azure.mgmt.compute.models import DiskCreateOption

async_creation = compute_client.disks.create_or_update(
    'my_resource_group',
    'my_disk_name',
    {
        'location': 'eastus',
        'disk_size_gb': 20,
        'creation_data': {
            'create_option': DiskCreateOption.empty
        }
    }
)
disk_resource = async_creation.result()
```

### <a name="create-a-managed-disk-from-blob-storage"></a>Criar um Managed Disk com base no armazenamento de blobs

```python
from azure.mgmt.compute.models import DiskCreateOption

async_creation = compute_client.disks.create_or_update(
    'my_resource_group',
    'my_disk_name',
    {
        'location': 'eastus',
        'creation_data': {
            'create_option': DiskCreateOption.import_enum,
            'source_uri': 'https://bg09.blob.core.windows.net/vm-images/non-existent.vhd'
        }
    }
)
disk_resource = async_creation.result()
```

### <a name="create-a-managed-disk-image-from-blob-storage"></a>Criar uma imagem de disco gerenciado com base no Armazenamento de Blobs

```python
from azure.mgmt.compute.models import DiskCreateOption

async_creation = compute_client.images.create_or_update(
    'my_resource_group',
    'my_image_name',
    {
        'location': 'eastus',
        'storage_profile': {
           'os_disk': {
              'os_type': 'Linux',
              'os_state': "Generalized",
              'blob_uri': 'https://bg09.blob.core.windows.net/vm-images/non-existent.vhd',
              'caching': "ReadWrite",
           }
        }
    }
)
image_resource = async_creation.result()
```

### <a name="create-a-managed-disk-from-your-own-image"></a>Criar um Managed Disk com base na sua própria imagem

```python
from azure.mgmt.compute.models import DiskCreateOption

# If you don't know the id, do a 'get' like this to obtain it
managed_disk = compute_client.disks.get(self.group_name, 'myImageDisk')
async_creation = compute_client.disks.create_or_update(
    'my_resource_group',
    'my_disk_name',
    {
        'location': 'eastus',
        'creation_data': {
            'create_option': DiskCreateOption.copy,
            'source_resource_id': managed_disk.id
        }
    }
)

disk_resource = async_creation.result()
```

## <a name="virtual-machine-with-managed-disks"></a>Máquina virtual com Managed Disks

Você pode criar uma Máquina Virtual com um Managed Disk implícito para uma imagem de disco específica. A criação foi simplificada com a criação implícita de Managed Disks sem especificar todos os detalhes de disco. Você não precisa se preocupar com a criação e gerenciamento das Contas de Armazenamento.

Um Managed Disk é criado implicitamente ao criar a VM a partir de uma imagem do sistema operacional no Azure. No parâmetro ``storage_profile``, ``os_disk`` agora é opcional e você não precisa criar uma conta de armazenamento como uma pré-condição necessária para criar uma Máquina Virtual.

```python
storage_profile = azure.mgmt.compute.models.StorageProfile(
    image_reference = azure.mgmt.compute.models.ImageReference(
        publisher='Canonical',
        offer='UbuntuServer',
        sku='16.04-LTS',
        version='latest'
    )
)
```

Esse parâmetro ``storage_profile`` é opcional. Para obter um exemplo completo sobre como criar uma VM no Python (incluindo rede etc.), confira o [tutorial completo sobre a VM no Python](https://github.com/Azure-Samples/virtual-machines-python-manage).

Você também pode criar um ``storage_profile`` usando sua própria imagem:

```python
# If you don't know the id, do a 'get' like this to obtain it
image = compute_client.images.get(self.group_name, 'myImageDisk')
storage_profile = azure.mgmt.compute.models.StorageProfile(
    image_reference = azure.mgmt.compute.models.ImageReference(
        id = image.id
    )
)
```

Você pode facilmente anexar um Managed Disk provisionado anteriormente.

```python
vm = compute.virtual_machines.get(
    'my_resource_group',
    'my_vm'
)
managed_disk = compute_client.disks.get('my_resource_group', 'myDisk')
vm.storage_profile.data_disks.append({
    'lun': 12, # You choose the value, depending of what is available for you
    'name': managed_disk.name,
    'create_option': DiskCreateOptionTypes.attach,
    'managed_disk': {
        'id': managed_disk.id
    }
})
async_update = compute_client.virtual_machines.create_or_update(
    'my_resource_group',
    vm.name,
    vm,
)
async_update.wait()
```

## <a name="virtual-machine-scale-sets-with-managed-disks"></a>Conjuntos de dimensionamento de máquinas virtuais com o Managed Disks

Antes dos Managed Disks, é necessário criar uma conta de armazenamento manualmente para todas as VMs que você deseja dentro dp seu Conjunto de Dimensionamento e, em seguida, usar o parâmetro de lista ``vhd_containers`` para fornecer todo o nome da conta de armazenamento para o API REST do Conjunto de Dimensionamento. O guia de transição oficial está disponível neste artigo `<https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-convert-template-to-md>`.

Agora com o Managed Disk, você não precisa gerenciar nenhuma conta de armazenamento. Se você está acostumado com o SDK do Python do conjunto de dimensionamento de máquinas virtuais, seu ``storage_profile`` agora pode ser exatamente o mesmo que aquele usado na criação da VM:

```python
'storage_profile': {
    'image_reference': {
        "publisher": "Canonical",
        "offer": "UbuntuServer",
        "sku": "16.04-LTS",
        "version": "latest"
    }
},
```

O exemplo completo sendo:

```python
naming_infix = "PyTestInfix"
vmss_parameters = {
    'location': self.region,
    "overprovision": True,
    "upgrade_policy": {
        "mode": "Manual"
    },
    'sku': {
        'name': 'Standard_A1',
        'tier': 'Standard',
        'capacity': 5
    },
    'virtual_machine_profile': {
        'storage_profile': {
            'image_reference': {
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "16.04-LTS",
                "version": "latest"
            }
        },
        'os_profile': {
            'computer_name_prefix': naming_infix,
            'admin_username': 'Foo12',
            'admin_password': 'BaR@123!!!!',
        },
        'network_profile': {
            'network_interface_configurations' : [{
                'name': naming_infix + 'nic',
                "primary": True,
                'ip_configurations': [{
                    'name': naming_infix + 'ipconfig',
                    'subnet': {
                        'id': subnet.id
                    }
                }]
            }]
        }
    }
}

# Create VMSS test
result_create = compute_client.virtual_machine_scale_sets.create_or_update(
    'my_resource_group',
    'my_scale_set',
    vmss_parameters,
)
vmss_result = result_create.result()
```

## <a name="other-operations-with-managed-disks"></a>Outras operações com Managed Disks

### <a name="resizing-a-managed-disk"></a>Redimensionar um Managed Disk

```python
managed_disk = compute_client.disks.get('my_resource_group', 'myDisk')
managed_disk.disk_size_gb = 25
async_update = self.compute_client.disks.create_or_update(
    'my_resource_group',
    'myDisk',
    managed_disk
)
async_update.wait()
```

### <a name="update-the-storage-account-type-of-the-managed-disks"></a>Atualizar o tipo de conta de armazenamento dos Managed Disks

```python
from azure.mgmt.compute.models import StorageAccountTypes

managed_disk = compute_client.disks.get('my_resource_group', 'myDisk')
managed_disk.account_type = StorageAccountTypes.standard_lrs
async_update = self.compute_client.disks.create_or_update(
    'my_resource_group',
    'myDisk',
    managed_disk
)
async_update.wait()
```

### <a name="create-an-image-from-blob-storage"></a>Criar uma imagem usando um armazenamento de blobs

```python
async_create_image = compute_client.images.create_or_update(
    'my_resource_group',
    'myImage',
    {
        'location': 'westus',
        'storage_profile': {
            'os_disk': {
                'os_type': 'Linux',
                'os_state': "Generalized",
                'blob_uri': 'https://bg09.blob.core.windows.net/vm-images/non-existent.vhd',
                'caching': "ReadWrite",
            }
        }
    }
)
image = async_create_image.result()
```

### <a name="create-a-snapshot-of-a-managed-disk-that-is-currently-attached-to-a-virtual-machine"></a>Criar um instantâneo de um Managed Disk que está atualmente anexado a uma máquina virtual

```python
managed_disk = compute_client.disks.get('my_resource_group', 'myDisk')
async_snapshot_creation = self.compute_client.snapshots.create_or_update(
        'my_resource_group',
        'mySnapshot',
        {
            'location': 'westus',
            'creation_data': {
                'create_option': 'Copy',
                'source_uri': managed_disk.id
            }
        }
    )
snapshot = async_snapshot_creation.result()
```