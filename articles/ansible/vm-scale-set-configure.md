---
title: Tutorial – Configurar Conjuntos de Dimensionamento de Máquinas Virtuais no Azure usando o Ansible
description: Saiba como usar o Ansible para criar e configurar conjuntos de dimensionamento de máquinas virtuais no Azure
keywords: ansible, azure, devops, bash, guia estratégico, máquina virtual, conjunto de dimensionamento de máquinas virtuais, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.custom: devx-track-ansible
ms.openlocfilehash: 357de61099b8b3673022857a933a18632cff7e40
ms.sourcegitcommit: bfaeacc2fb68f861a9403585d744e51a8f99829c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90681862"
---
# <a name="tutorial-configure-virtual-machine-scale-sets-in-azure-using-ansible"></a>Tutorial: Configurar conjuntos de dimensionamento de máquinas virtuais no Azure usando o Ansible

[!INCLUDE [ansible-27-note.md](includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vm-scale-set.md](../includes/open-source-devops-intro-vm-scale-set.md)]

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Configurar os recursos para uma VM
> * Configurar um conjunto de dimensionamento
> * Dimensionar o conjunto de dimensionamento aumentando as instâncias da VM 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-a-scale-set"></a>Configurar um conjunto de dimensionamento

O código do guia estratégico nesta seção define os seguintes recursos:

* Um **grupo de recursos** no qual todos os recursos serão implantados
* Uma **rede virtual** no espaço de endereço 10.0.0.0/16
* Uma **sub-rede** dentro da rede virtual
* Um **endereço IP público** que permite acessar recursos pela Internet
* Um **grupo de segurança de rede** que controla o fluxo de entrada e saída de tráfego de rede do conjunto de dimensionamento
* Um **balanceador de carga**, que distribui o tráfego entre um conjunto de VMs definidas usando regras do balanceador de carga
* Um **conjunto de dimensionamento de máquinas virtuais** que usa todos os recursos criados

Há duas maneiras de obter o guia estratégico de exemplo:

* [Baixe o guia estratégico](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-create.yml) e salve-o em `vmss-create.yml`.
* Crie um novo arquivo chamado `vmss-create.yml` e copie-o para o conteúdo a seguir:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    vmss_lb_name: myScaleSetLb
    location: eastus
    admin_username: azureuser
    admin_password: "{{ admin_password }}"
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    - name: Create virtual network
      azure_rm_virtualnetwork:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        address_prefixes: "10.0.0.0/16"
    - name: Add subnet
      azure_rm_subnet:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        address_prefix: "10.0.1.0/24"
        virtual_network: "{{ vmss_name }}"
    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ vmss_name }}"
    - name: Create Network Security Group that allows SSH
      azure_rm_securitygroup:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        rules:
          - name: SSH
            protocol: Tcp
            destination_port_range: 22
            access: Allow
            priority: 1001
            direction: Inbound

    - name: Create a load balancer
      azure_rm_loadbalancer:
        name: "{{ vmss_lb_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        public_ip: "{{ vmss_name }}"
        probe_protocol: Tcp
        probe_port: 8080
        probe_interval: 10
        probe_fail_count: 3
        protocol: Tcp
        load_distribution: Default
        frontend_port: 80
        backend_port: 8080
        idle_timeout: 4
        natpool_frontend_port_start: 50000
        natpool_frontend_port_end: 50040
        natpool_backend_port: 22
        natpool_protocol: Tcp

    - name: Create Scale Set
      azure_rm_virtualmachinescaleset:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        vm_size: Standard_DS1_v2
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        ssh_password_enabled: true
        capacity: 2
        virtual_network_name: "{{ vmss_name }}"
        subnet_name: "{{ vmss_name }}"
        upgrade_policy: Manual
        tier: Standard
        managed_disk_type: Standard_LRS
        os_disk_caching: ReadWrite
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          version: latest
        load_balancer: "{{ vmss_lb_name }}"
        data_disks:
          - lun: 0
            disk_size_gb: 20
            managed_disk_type: Standard_LRS
            caching: ReadOnly
          - lun: 1
            disk_size_gb: 30
            managed_disk_type: Standard_LRS
            caching: ReadOnly
```

Antes de executar o guia estratégico, confira as observações a seguir:

* Na seção `vars`, substitua o espaço reservado `{{ admin_password }}` pela sua própria senha.

Executar o guia estratégico usando [ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)

```bash
ansible-playbook vmss-create.yml
```

Depois de executar o guia estratégico, você verá resultados semelhantes aos seguintes:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Create a resource group] 
changed: [localhost]

TASK [Create virtual network] 
changed: [localhost]

TASK [Add subnet] 
changed: [localhost]

TASK [Create public IP address] 
changed: [localhost]

TASK [Create Network Security Group that allows SSH] 
changed: [localhost]

TASK [Create a load balancer] 
changed: [localhost]

TASK [Create Scale Set] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=8    changed=7    unreachable=0    failed=0

```

## <a name="view-the-number-of-vm-instances"></a>Exibir o número de instâncias de VM

O [conjunto de dimensionamento configurado](#configure-a-scale-set) atualmente possui duas instâncias. As etapas a seguir são usadas para confirmar esse valor:

1. Entre no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Navegue até o conjunto de dimensionamento que você configurou.

1. Você verá o nome do conjunto de dimensionamento com o número de instâncias entre parênteses: `Standard_DS1_v2 (2 instances)`

1. Você também pode verificar o número de instâncias com o [Azure Cloud Shell](https://shell.azure.com/) executando o seguinte comando:

    ```azurecli-interactive
    az vmss show -n myScaleSet -g myResourceGroup --query '{"capacity":sku.capacity}' 
    ```

    Os resultados da execução do comando da CLI do Azure no Cloud Shell mostram que agora existem três instâncias: 

    ```bash
    {
      "capacity": 3,
    }
    ```

## <a name="scale-out-a-scale-set"></a>Escalar horizonralmente um conjunto de dimensionamento

O código do guia estratégico nesta seção recupera informações sobre o conjunto de dimensionamento e altera sua capacidade de dois para três.

Há duas maneiras de obter o guia estratégico de exemplo:

* [Baixe o guia estratégico](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss/vmss-scale-out.yml) e salve-o em `vmss-scale-out.yml`.
* Crie um novo arquivo chamado `vmss-scale-out.yml` e copie-o para o conteúdo a seguir:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
  tasks: 
    - name: Get scaleset info
      azure_rm_virtualmachine_scaleset_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        format: curated
      register: output_scaleset

    - name: Dump scaleset info
      debug:
        var: output_scaleset

    - name: Modify scaleset (change the capacity to 3)
      set_fact:
        body: "{{ output_scaleset.ansible_facts.azure_vmss[0] | combine({'capacity': 3}, recursive=True) }}"

    - name: Update something in that scale set
      azure_rm_virtualmachinescaleset: "{{ body }}"
```

Executar o guia estratégico usando [ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)

```bash
ansible-playbook vmss-scale-out.yml
```

Depois de executar o guia estratégico, você verá resultados semelhantes aos seguintes:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Get scaleset info] 
ok: [localhost]

TASK [Dump scaleset info] 
ok: [localhost] => {
    "output_scaleset": {
        "ansible_facts": {
            "azure_vmss": [
                {
                    ......
                }
            ]
        },
        "changed": false,
        "failed": false
    }
}

TASK [Modify scaleset (set upgradePolicy to Automatic and capacity to 3)] 
ok: [localhost]

TASK [Update something in that scale set] 
changed: [localhost]

PLAY RECAP 
localhost                  : ok=5    changed=1    unreachable=0    failed=0
```

## <a name="verify-the-results"></a>Verifique os resultados

Verifique os resultados do seu trabalho por meio do portal do Azure:

1. Entre no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Navegue até o conjunto de dimensionamento que você configurou.

1. Você verá o nome do conjunto de dimensionamento com o número de instâncias entre parênteses: `Standard_DS1_v2 (3 instances)` 

1. Você também pode verificar a alteração com o [Azure Cloud Shell](https://shell.azure.com/) executando o seguinte comando:

    ```azurecli-interactive
    az vmss show -n myScaleSet -g myResourceGroup --query '{"capacity":sku.capacity}' 
    ```

    Os resultados da execução do comando da CLI do Azure no Cloud Shell mostram que agora existem três instâncias: 

    ```bash
    {
      "capacity": 3,
    }
    ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Tutorial: Implantar aplicativos nos conjuntos de dimensionamento de máquinas virtuais no Azure usando o Ansible](./vm-scale-set-deploy-app.md)