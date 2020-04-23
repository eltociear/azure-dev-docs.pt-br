---
title: Tutorial – Atualizar a imagem personalizada dos Conjuntos de Dimensionamento de Máquinas Virtuais do Azure usando o Ansible
description: Saiba como usar o Ansible para atualizar um conjunto de dimensionamento de máquinas virtuais no Azure com a imagem personalizada
keywords: ansible, azure, devops, bash, guia estratégico, máquina virtual, conjunto de dimensionamento de máquinas virtuais, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: ae25012346c7d6fbc65c43fad6b3b228eedc9e7c
ms.sourcegitcommit: eabc9e3fb8ad0f067be5ed878c2eacebd461b6ce
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81743827"
---
# <a name="tutorial-update-the-custom-image-of-azure-virtual-machine-scale-sets-using-ansible"></a>Tutorial: Atualizar a imagem personalizada dos conjuntos de dimensionamento de máquinas virtuais do Azure usando o Ansible

[!INCLUDE [ansible-27-note.md](includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-vm-scale-set.md](../includes/open-source-devops-intro-vm-scale-set.md)]

Depois que uma VM for implantada, configure-a com o software de que seu aplicativo precisa. Em vez de executar essa tarefa de configuração para cada VM, você poderá criar uma imagem personalizada. Uma imagem personalizada é um instantâneo de uma VM existente que inclui qualquer software instalado. Quando você [configurar um conjunto de dimensionamento](./vm-scale-set-configure.md), especifique a imagem que será usada para VMs do conjunto de dimensionamento. Ao usar uma imagem personalizada, cada instância de VM será configurada de forma idêntica para seu aplicativo. Às vezes, poderá ser necessário atualizar a imagem personalizada de seu conjunto de dimensionamento. Essa tarefa é o foco deste tutorial.

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Configurar duas VMs com HTTPD
> * Criar uma imagem personalizada de uma VM existente
> * Criar um conjunto de dimensionamento a partir de uma imagem
> * Atualizar a imagem personalizada

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-two-vms"></a>Configurar duas VMs

O código do guia estratégico nesta seção cria duas máquinas virtuais com HTTPD instalado em ambas. 

A página `index.html` de cada VM exibe uma cadeia de caracteres de teste:

* A primeira VM exibe o valor `Image A`
* A segunda VM exibe o valor `Image B`

Essa cadeia de caracteres serve para imitar a configuração de cada VM com um software diferente.

Há duas maneiras de obter o guia estratégico de exemplo:

* [Baixe o guia estratégico](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/01-create-vms.yml) e salve-o em `create_vms.yml`.
* Crie um novo arquivo chamado `create_vms.yml` e copie-o para o conteúdo a seguir:

```yml
- name: Create two VMs (A and B) with HTTPS
  hosts: localhost
  connection: local
  vars:
    vm_name: vmforimage
    admin_username: testuser
    admin_password: Pass123$$$abx!
    location: eastus
  tasks:
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      address_prefixes: "10.0.0.0/16"

  - name: Create subnets for VM A and B
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      virtual_network: "{{ vm_name }}"
      name: "{{ vm_name }}"
      address_prefix: "10.0.1.0/24"

  - name: Create Network Security Group that allows HTTP
    azure_rm_securitygroup:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      rules:
        - name: HTTP
          protocol: Tcp
          destination_port_range: 80
          access: Allow
          priority: 1002
          direction: Inbound

  - name: Create public IP addresses for VM A and B
    azure_rm_publicipaddress:
      resource_group: "{{ resource_group }}"
      allocation_method: Static
      name: "{{ vm_name }}_{{ item }}"
    loop:
      - A
      - B
    register: pip_output

  - name: Create virtual network inteface cards for VM A and B
    azure_rm_networkinterface:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}_{{ item }}"
      virtual_network: "{{ vm_name }}"
      subnet: "{{ vm_name }}"
      public_ip_name: "{{ vm_name }}_{{ item }}"
      security_group: "{{ vm_name }}"
    loop:
      - A
      - B

  - name: Create VM A and B
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}{{ item }}"
      admin_username: "{{ admin_username }}"
      admin_password: "{{ admin_password }}"
      vm_size: Standard_B1ms
      network_interfaces: "{{ vm_name }}_{{ item }}"
      image:
        offer: UbuntuServer
        publisher: Canonical
        sku: 16.04-LTS
        version: latest
    loop:
      - A
      - B

  - name: Create VM Extension
    azure_rm_virtualmachineextension:
      resource_group: "{{ resource_group }}"
      name: testVMExtension
      virtual_machine_name: "{{ vm_name }}{{ item }}"
      publisher: Microsoft.Azure.Extensions
      virtual_machine_extension_type: CustomScript
      type_handler_version: 2.0
      auto_upgrade_minor_version: true
      settings: {"commandToExecute": "sudo apt-get -y install apache2"}
    loop:
      - A
      - B

  - name: Create VM Extension
    azure_rm_virtualmachineextension:
      resource_group: "{{ resource_group }}"
      name: testVMExtension
      virtual_machine_name: "{{ vm_name }}{{ item }}"
      publisher: Microsoft.Azure.Extensions
      virtual_machine_extension_type: CustomScript
      type_handler_version: 2.0
      auto_upgrade_minor_version: true
      settings: {"commandToExecute": "printf '<html><body><h1>Image {{ item }}</h1></body></html>' >> index.html; sudo cp index.html /var/www/html/"}
    loop:
      - A
      - B

  - debug:
      msg: "Public IP Address A: {{ pip_output.results[0].state.ip_address }}"

  - debug:
      msg: "Public IP Address B: {{ pip_output.results[1].state.ip_address }}"
```

Execute o guia estratégico usando o comando `ansible-playbook`, substituindo `myrg` pelo nome do grupo de recursos:

```bash
ansible-playbook create-vms.yml --extra-vars "resource_group=myrg"
```

Devido às seções `debug` do guia estratégico, o comando `ansible-playbook` imprimirá o endereço IP de cada VM. Copie esses endereços IP para uso posterior.

![Endereços IP da máquina virtual](media/vm-scale-set-update-image/ip-addresses.png)

## <a name="connect-to-the-two-vms"></a>Conectar-se a duas VMs

Nesta seção, você se conectará a cada VM. Conforme mencionado na seção anterior, as cadeias de caracteres `Image A` e `Image B` imitam duas VMs distintas com configurações diferentes.

Usando os endereços IP da seção anterior, conecte-se às duas VMs:

![Captura de tela da máquina virtual A](media/vm-scale-set-update-image/vm-a.png)

![Captura de tela da máquina virtual B](media/vm-scale-set-update-image/vm-b.png)

## <a name="create-images-from-each-vm"></a>Criar imagens de cada VM

Neste ponto, você tem duas VMs com configurações ligeiramente diferentes (seus arquivos `index.html`).

O código do guia estratégico nesta seção cria uma imagem personalizada para cada VM:

* `image_vmforimageA` – Imagem personalizada criada para a VM que exibe `Image A` em sua home page.
* `image_vmforimageB` – Imagem personalizada criada para a VM que exibe `Image B` em sua home page.

Há duas maneiras de obter o guia estratégico de exemplo:

* [Baixe o guia estratégico](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/02-capture-images.yml) e salve-o em `capture-images.yml`.
* Crie um novo arquivo chamado `capture-images.yml` e copie-o para o conteúdo a seguir:

```yml
- name: Capture VM Images
  hosts: localhost
  connection: local
  vars:
    vm_name: vmforimage
  tasks:

  - name: Stop and generalize VMs
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}{{ item }}"
      generalized: yes
    loop:
      - A
      - B

  - name: Create an images from a VMs
    azure_rm_image:
      resource_group: "{{ resource_group }}"
      name: "image_{{ vm_name }}{{ item }}"
      source: "{{ vm_name }}{{ item }}"
    loop:
      - A
      - B
```

Execute o guia estratégico usando o comando `ansible-playbook`, substituindo `myrg` pelo nome do grupo de recursos:

```bash
ansible-playbook capture-images.yml --extra-vars "resource_group=myrg"
```

## <a name="create-scale-set-using-image-a"></a>Criar um conjunto de dimensionamento usando a imagem A

Nesta seção, um guia estratégico é usado para configurar os seguintes recursos do Azure:

* Endereço IP público
* Balanceador de carga
* Conjunto de dimensionamento que faz referência a `image_vmforimageA`

Há duas maneiras de obter o guia estratégico de exemplo:

* [Baixe o guia estratégico](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/03-create-vmss.yml) e salve-o em `create-vmss.yml`.
* Crie um novo arquivo chamado `create-vmss.yml` e copie-o para o conteúdo a seguir:"

```yml
---
- hosts: localhost
  vars:
    vmss_name: vmsstest
    location: eastus
    admin_username: vmssadmin
    admin_password: User123!!!abc
    vm_name: vmforimage
    image_name: "image_vmforimageA"

  tasks:

    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ vmss_name }}"
      register: pip_output

    - name: Create a load balancer
      azure_rm_loadbalancer:
        name: "{{ vmss_name }}lb"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        public_ip: "{{ vmss_name }}"
        probe_protocol: Tcp
        probe_port: 80
        probe_interval: 10
        probe_fail_count: 3
        protocol: Tcp
        load_distribution: Default
        frontend_port: 80
        backend_port: 80
        idle_timeout: 4
        natpool_frontend_port_start: 50000
        natpool_frontend_port_end: 50040
        natpool_backend_port: 22
        natpool_protocol: Tcp

    - name: Create a scale set
      azure_rm_virtualmachinescaleset:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        vm_size: Standard_DS1_v2
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        ssh_password_enabled: true
        capacity: 2
        virtual_network_name: "{{ vm_name }}"
        subnet_name: "{{ vm_name }}"
        upgrade_policy: Manual
        tier: Standard
        managed_disk_type: Standard_LRS
        os_disk_caching: ReadWrite
        image:
          name: "{{ image_name }}"
          resource_group: "{{ resource_group }}"
        load_balancer: "{{ vmss_name }}lb"

    - debug:
        msg: "Scale set public IP address: {{ pip_output.state.ip_address }}"
```

Execute o guia estratégico usando o comando `ansible-playbook`, substituindo `myrg` pelo nome do grupo de recursos:

```bash
ansible-playbook create-vmss.yml --extra-vars "resource_group=myrg"
```

Devido às seções `debug` do guia estratégico, o comando `ansible-playbook` imprimirá o endereço IP de cada conjunto de dimensionamento. Copie esse endereço IP para uso posterior.

![Endereço IP público](media/vm-scale-set-update-image/public-ip.png)

## <a name="connect-to-the-scale-set"></a>Conectar-se a um conjunto de dimensionamento

Nesta seção, você se conectará ao conjunto de dimensionamento. 

Usando os endereços IP da seção anterior, conecte-se ao conjunto de dimensionamento.

Conforme mencionado na seção anterior, as cadeias de caracteres `Image A` e `Image B` imitam duas VMs distintas com configurações diferentes.

O conjunto de dimensionamento faz referência à imagem personalizada chamada `image_vmforimageA`. A imagem personalizada `image_vmforimageA` foi criada a partir da VM cuja home page exibe `Image A`.

Como resultado, você vê uma home page que exibe `Image A`:

![O conjunto de dimensionamento está associado à primeira máquina virtual.](media/vm-scale-set-update-image/initial-vm-scale-set.png)

Deixe a janela do navegador aberta enquanto continua para a próxima seção.

## <a name="change-custom-image-in-scale-set-and-upgrade-instances"></a>Alterar a imagem personalizada no conjunto de dimensionamento e atualizar as instâncias

O código do guia estratégico nesta seção altera a imagem do conjunto de dimensionamento de `image_vmforimageA` para `image_vmforimageB`. Além disso, todas as máquinas virtuais atuais implantadas pelo conjunto de dimensionamento são atualizadas.

Há duas maneiras de obter o guia estratégico de exemplo:

* [Baixe o guia estratégico](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/04-update-vmss-image.yml) e salve-o em `update-vmss-image.yml`.
* Crie um novo arquivo chamado `update-vmss-image.yml` e copie-o para o conteúdo a seguir:

```yml
- name: Update scale set image reference
  hosts: localhost
  connection: local
  vars:
    vmss_name: vmsstest
    image_name: image_vmforimageB
    admin_username: vmssadmin
    admin_password: User123!!!abc
  tasks:

  - name: Update scale set - second image
    azure_rm_virtualmachinescaleset:
      resource_group: "{{ resource_group }}"
      name: "{{ vmss_name }}"
      vm_size: Standard_DS1_v2
      admin_username: "{{ admin_username }}"
      admin_password: "{{ admin_password }}"
      ssh_password_enabled: true
      capacity: 3
      virtual_network_name: "{{ vmss_name }}"
      subnet_name: "{{ vmss_name }}"
      upgrade_policy: Manual
      tier: Standard
      managed_disk_type: Standard_LRS
      os_disk_caching: ReadWrite
      image:
        name: "{{ image_name }}"
        resource_group: "{{ resource_group }}"
      load_balancer: "{{ vmss_name }}lb"

  - name: List all of the instances
    azure_rm_virtualmachinescalesetinstance_facts:
      resource_group: "{{ resource_group }}"
      vmss_name: "{{ vmss_name }}"
    register: instances

  - debug:
      var: instances

  - name: manually upgrade all the instances 
    azure_rm_virtualmachinescalesetinstance:
      resource_group: "{{ resource_group }}"
      vmss_name: "{{ vmss_name }}"
      instance_id: "{{ item.instance_id }}"
      latest_model: yes
    with_items: "{{ instances.instances }}"
```

Execute o guia estratégico usando o comando `ansible-playbook`, substituindo `myrg` pelo nome do grupo de recursos:

```bash
ansible-playbook update-vmss-image.yml --extra-vars "resource_group=myrg"
```

Volte para o navegador e atualize a página. 

Você verá que a imagem personalizada subjacente da máquina virtual é atualizada.

![O conjunto de dimensionamento está associado à segunda máquina virtual](media/vm-scale-set-update-image/updated-vm-scale-set.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, exclua os recursos criados neste artigo. 

Salve o seguinte código como `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myrg
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Ansible no Azure](/azure/ansible)