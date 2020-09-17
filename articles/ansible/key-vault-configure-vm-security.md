---
title: Tutorial – Usar o Azure Key Vault com uma máquina virtual do Linux no Ansible
description: Saiba como usar o Ansible para configurar a segurança da VM usando o Azure Key Vault
keywords: ansible, azure, devops, cofre de chaves, segurança, credenciais, segredos, chaves, certificados, módulos do ansible para azure, grupo de recursos, azure_rm_resourcegroup,
ms.topic: tutorial
ms.date: 04/20/2020
ms.custom: devx-track-ansible
ms.openlocfilehash: 4891b277f8c1f9fcd7fe4c1d54ed13b39f19d2e4
ms.sourcegitcommit: bfaeacc2fb68f861a9403585d744e51a8f99829c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90682018"
---
# <a name="tutorial-use-azure-key-vault-with-a-linux-virtual-machine-in-ansible"></a>Tutorial: Usar o Azure Key Vault com uma máquina virtual do Linux no Ansible

[!INCLUDE [ansible-29-note.md](includes/ansible-29-note.md)]

Este tutorial mostra como usar a coleção do Ansible para módulos do Azure usando o [Azure Key Vault](/azure/key-vault/general/overview). O Azure Key Vault permite centralizar o armazenamento de credenciais, como segredos, chaves e certificados do aplicativo. A desassociação de credenciais e código do aplicativo ajuda o sistema a ficar mais seguro. Além disso, a implementação de um padrão de gerenciamento de credenciais giratório com datas de expiração automáticas se torna muito mais gerenciável.

> [!div class="checklist"]
>
> * Usar a CLI do Azure para obter a assinatura do Azure e os valores da entidade de serviço
> * Armazenar valores de chave como variáveis de ambiente do Linux
> * Obter variáveis de ambiente do Linux de um guia estratégico do Ansible
> * Criar um cofre de chave
> * Definir uma política de acesso para um cofre de chaves
> * Usar o portal do Azure para adicionar uma política de acesso a um cofre de chaves
> * Criar um segredo do cofre de chaves
> * Usar o módulo shell do Ansible para obter um segredo do cofre de chaves
> * Criar uma máquina virtual junto com todos os componentes constituintes

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
[!INCLUDE [ansible-configure-azure-collection.md](includes/ansible-configure-azure-collection.md)]
    
## <a name="get-azure-subscription-info"></a>Obter informações de assinatura do Azure

Use a CLI do Azure para obter as informações de assinatura necessárias do Azure exigidas ao usar os módulos do Ansible para o Azure. 

1. Obtenha a ID da assinatura do Azure e a ID de locatário da assinatura do Azure usando o comando `az account show`. Para o espaço reservado `<Subscription>`, especifique o nome da assinatura do Azure ou a ID da assinatura do Azure. O comando exibirá muitos dos valores de chave associados à assinatura padrão do Azure. Se você tiver várias assinaturas, talvez seja necessário definir a assinatura atual por meio do comando [az account set](/cli/azure/account#az-account-set). Na saída do comando, anote os valores **ID** e **tenantID**.

    ```azurecli
    az account show --subscription "<Subscription>" --query tenantId
    ```

1. Se você não tiver uma entidade de serviço para a assinatura do Azure. [crie uma entidade de serviço do Azure com a CLI do Azure](/cli/azure/create-an-azure-service-principal-azure-cli). Na saída do comando, anote o valor **appId**.

1. Obtenha a ID de objeto da entidade de serviço usando o comando `az ad sp show`. Para o espaço reservado `<ApplicationID>`, especifique a appId de entidade de serviço. O parâmetro `--query` indica qual valor imprimir em *stdout*. Nesse caso, é a ID de objeto da entidade de serviço.

    ```azurecli
    az ad sp show --id <ApplicationID> --query objectId
    ```
    
1. Armazene os seguintes valores como variáveis de ambiente: ID da assinatura do Azure, ID de locatário da assinatura do Azure e ID de objeto da entidade de serviço. O modo como você executa o guia estratégico, o local no qual você armazena os valores de assinatura e de credencial e como você recupera esses valores são baseados em seu ambiente específico. Para fins desta demonstração, usei o Azure Cloud Shell e armazenei os valores do Azure necessários em `~/.bashrc` adicionando as seguintes linhas ao final do arquivo:

    ```bash
    export AZ_SUBSCRIPTION_ID="<subscriptionID>"
    export AZ_TENANT_ID="<tenantID>"
    export AZ_OBJECT_ID="<objectID>"
    export AZ_CLIENT_ID="<appID>"
    ```

## <a name="declare-the-azure-collection"></a>Declarar a coleção do Azure

Depois de [baixar a coleção do Azure mais recente](#prerequisites), especifique uso dela por meio da chave `collections`.

```yml
- hosts: all
  collections:
    - azure.azcollection
```

## <a name="create-azure-resource-group-for-the-key-vault"></a>Criar grupo de recursos do Azure para o cofre de chaves

O snippet do guia estratégico a seguir cria um grupo de recursos exclusivamente nomeado no qual o cofre de chaves será criado. 

```yml
---
- hosts: localhost
  tasks:
    - name: Prepare random postfix
      set_fact:
        rpfx: "{{ 10000 | random }}"
      run_once: yes
      
- hosts: localhost
  vars:
    kv_rg: kv_rg_{{ rpfx }}
    kv_rg_loc: eastus

  tasks:
    - name: Set facts
      set_fact:
        az_sub_id: "{{ lookup('env', 'AZ_SUBSCRIPTION_ID') }}"

    - name: Create a resource group to hold the key vault
      azure_rm_resourcegroup:
        subscription_id: "{{ az_sub_id }}"
        name: "{{ kv_rg }}"
        location: "{{ kv_rg_loc }}"

    - debug:
        msg: "New resource group = {{ kv_rg }}"
```

**Observações:**

- Nesta demonstração, o cofre de chaves é criado como o único recurso em um grupo de recursos. É uma prática comum separar o cofre de chaves dos recursos que o usam. Esse padrão ajuda a impedir a exclusão acidental do cofre de chaves ao excluir outros recursos.
- Como o nome do cofre de chaves deve ser exclusivo no Azure, a demonstração cria um valor *postfix* aleatório. Esse valor é acrescentado ao nome do grupo de recursos do cofre de chaves e ao cofre de chaves (criado na próxima seção). O código na tarefa `Prepare random postfix` gera o valor postfix aleatório que é atribuído à variável `rpfx`.
- Na tarefa `Set facts`, o comando `lookup` é usado para recuperar a ID da assinatura do Azure armazenada como uma variável de ambiente.
- O módulo [azure_rm_resourcegroup](https://docs.ansible.com/ansible/latest/modules/azure_rm_resourcegroup_module.html) é usado para criar o grupo de recursos.
- Uma tarefa `debug` no final do guia estratégico exibe o nome do novo grupo de recursos.

## <a name="create-a-key-vault"></a>Criar um cofre de chave

Conforme mencionado na seção anterior, o nome do cofre de chaves deve ser exclusivo no Azure. Como tal, o snippet do guia estratégico a seguir atribui à variável `kv` a concatenação do valor `kv` e `rpfx` literal.

```yml
vars:
  kv: "kv{{ rpfx }}"
  kv_rg: "kv_rg_{{ rpfx }}"

tasks:
  - name: Set facts
    set_fact:
      az_object_id: "{{ lookup('env', 'AZ_OBJECT_ID') }}"
      az_tenant_id: "{{ lookup('env', 'AZ_TENANT_ID') }}"

  - name: Create a key vault
    azure_rm_keyvault:
      subscription_id: "{{ az_sub_id }}"
      resource_group: "{{ kv_rg }}"
      vault_name: "{{ kv }}"
      vault_tenant: "{{ az_tenant_id }}"
      enabled_for_deployment: yes
      sku:
        name: standard
        family: A
      access_policies:
        - object_id: "{{ az_object_id }}"
          tenant_id: "{{ az_tenant_id }}"
          secrets:
            - get
            - list
            - set
  - debug:
      msg: "New key vault name = {{ kv }} within the {{ kv_rg }} resource group"

```

**Observações:**

- O [módulo azure_rm_keyvault](https://docs.ansible.com/ansible/latest/modules/azure_rm_keyvault_module.html) é usado para criar o cofre de chaves.
- Ao criar a política de acesso como parte do cofre de chaves, foram fornecidas uma ID de objeto e uma ID de locatário. Esses valores definem uma política de acesso para uma entidade de serviço específica (associada a uma assinatura do Azure). No entanto, navegar até o portal do Azure e tentar exibir os segredos do cofre de chaves podem resultar em mensagens de erro. Essas mensagens podem ser semelhantes a: "A "Lista" de operações não está habilitada nesta política de acesso do cofre de chaves." e "Você não está autorizado a exibir esses conteúdos." O recebimento dessas mensagens indica que você, como um usuário do Active Directory, não tem acesso. A próxima seção mostra como adicionar uma política de acesso para você mesmo ao cofre de chaves.
- Uma tarefa `debug` no final do guia estratégico exibe o nome do novo cofre de chaves.

## <a name="add-yourself-to-key-vault-access-policy"></a>Adicionar-se à política de acesso do cofre de chaves

Se você quiser exibir os segredos do cofre de chaves ou se estiver trabalhando nas etapas de demonstração, será necessário adicionar uma política de acesso para sua ID do Azure Active Directory. As seguintes etapas explicam como adicionar ao cofre de chaves uma política de acesso para você mesmo como um usuário:

1. Navegue até o [Portal do Azure](https://portal.azure.com).

1. Na caixa de pesquisa principal da página, digite `key vaults` e, em **Serviços**, selecione **Cofres de chaves**.

1. Selecione o cofre de chaves criado na seção anterior. (O nome foi impresso para stdout do guia estratégico.)

1. Selecione **Políticas de acesso**.

1. Uma única política de acesso é exibida com sua ID do Azure Active Directory que representa a entidade de serviço especificada.

1. Selecione **Adicionar Política de Acesso**.

1. Selecione **Selecionar entidade de segurança**.

1. Na guia **Entidade de segurança**, na caixa de pesquisa, insira seu endereço de email.

1. Na lista filtrada, selecione a entrada apropriada.

1. As informações para o usuário selecionado são copiadas na lista **Membro selecionado**. Selecione **Selecionar**.

1. Selecione as opções apropriadas para **Permissões de chave**, **Permissões de segredo** e **Permissões de certificado**. Para essa demonstração, é suficiente selecionar **Permissões de segredo** e, em seguida, **Obter**, **Listar** e **Definir**.

1. Selecione **Adicionar**.

1. A nova política de acesso para o usuário selecionado agora é exibida na página **Políticas de acesso**.

1. Clique em **Salvar**.

1. Selecione **Notificações** no canto superior direito do portal. Aguarde até que a política de acesso seja atualizada antes de continuar para a próxima etapa.

## <a name="create-a-key-vault-secret"></a>Criar um segredo do cofre de chaves

O seguinte snippet do guia estratégico do Ansible mostra como criar um segredo do cofre de chaves:

```yml
  vars:
    kv_secret_name: testsecret
    kv_secret_value: MySecret007$

  tasks:
    - name: Set facts
      set_fact:
        az_client_id: "{{ lookup('env', 'AZ_CLIENT_ID') }}"

    - name: Create a secret
      azure_rm_keyvaultsecret:
        subscription_id: "{{ az_sub_id }}"
        client_id: "{{ az_client_id }}"
        keyvault_uri: "{{ kv_uri }}"
        secret_name: "{{ kv_secret_name }}"
        secret_value: "{{ kv_secret_value }}"
```

**Observações:**

- o [módulo azure_rm_keyvaultsecret](https://docs.ansible.com/ansible/latest/modules/azure_rm_keyvaultsecret_module.html) é usado para criar o segredo do cofre de chaves.
- Para simplificar, a demonstração inclui `secret_name` e `secret_value`. No entanto, os guias estratégicos são arquivos IaC (infraestrutura como código), assim como qualquer código-fonte para seu projeto. Assim, valores como esses não devem ser armazenados em arquivos de texto não criptografados quando usados em ambientes de produção.
- Depois de executar esse código, a guia **Segredos** do cofre de chaves listará o segredo recém-adicionado denominado `testsecret`. Para exibi-lo, selecione o segredo, a versão atual e **Mostrar Valor do Segredo**.

## <a name="get-a-key-vault-secret"></a>Obter um segredo do cofre de chaves

O seguinte snippet do guia estratégico do Ansible mostra como obter a versão mais recente de um segredo do cofre de chaves:

```yml
  vars:
    kv_secret_name: testsecret
    kv_secret_value: MySecret007$

tasks:
    - name: Get latest version of a secret
      azure_rm_keyvaultsecret_info:
        vault_uri: "{{ kv_uri }}"
        name: "{{ kv_secret_name }}"
      register: output
    - debug:
        var: output['secrets'][0]['secret']
```

**Observações:**

- o **módulo azure_rm_keyvaultsecret_info** é usado para obter o segredo do cofre de chaves. Esse módulo só estará disponível se você estiver usando a coleção do Ansible para módulos do Azure. 
- Se você receber um erro ao executar este snippet, verifique se você seguiu todas as instruções na [seção Pré-requisitos](#prerequisites).
- Para simplificar, a demonstração inclui `secret_name` e `secret_value`. No entanto, os guias estratégicos são arquivos IaC (infraestrutura como código), assim como qualquer código-fonte para seu projeto. Assim, valores como esses não devem ser armazenados em arquivos de texto não criptografados quando usados em ambientes de produção.

## <a name="run-the-complete-playbook"></a>Executar o guia estratégico completo

Quando você tiver o cofre de chaves e o segredo dele estabelecidos, poderá usar essas informações ao proteger recursos do Azure, como máquinas virtuais. O guia estratégico do Ansible a seguir executa as tarefas mostradas ao longo deste tutorial e cria uma máquina virtual completa. 

```yml
---
- hosts: localhost
  tasks:
    - name: Prepare random postfix
      set_fact:
        rpfx: "{{ 10000 | random }}"
      run_once: yes
      
- hosts: localhost
  collections:
    - azure.azcollection
  vars:
    kv_rg: kv_rg_{{ rpfx }}
    kv_rg_loc: eastus
    kv: "kv{{ rpfx }}"
    kv_uri: "https://{{ kv }}.vault.azure.net"
    kv_secret_name: testsecret
    kv_secret_value: MySecret007$

    # Test VM vars
    test_vm_rg: kv_test_vm_rg
    test_vm_rg_loc: eastus
    test_vm: kvtestvm
    test_vm_vnet: "kv_test_vm_vnet"
    test_vm_subnet: kv_test_vm_subnet
    test_vm_public_ip: kv_test_vm_public_ip
    test_vm_nsg: kv_test_vm_nsg
    test_vm_nsg_list: 
      - name: Allow-SSH
        access: Allow
        protocol: Tcp
        direction: Inbound
        priority: 300
        port: 22 
        source_address_prefix: Internet
      - name: Allow-HTTP
        access: Allow
        protocol: Tcp
        direction: Inbound
        priority: 100
        port: 80
        source_address_prefix: Internet 
    test_vm_nic: kv_test_vnic
    admin_username: testadmin

  tasks:
    - name: Set facts
      set_fact:
        az_sub_id: "{{ lookup('env', 'AZ_SUBSCRIPTION_ID') }}"
        az_object_id: "{{ lookup('env', 'AZ_OBJECT_ID') }}"
        az_tenant_id: "{{ lookup('env', 'AZ_TENANT_ID') }}"
        az_client_id: "{{ lookup('env', 'AZ_CLIENT_ID') }}"

    - name: Create a resource group to hold the Key Vault instance
      azure_rm_resourcegroup:
        subscription_id: "{{ az_sub_id }}"
        name: "{{ kv_rg }}"
        location: "{{ kv_rg_loc }}"

    - debug:
        msg: "New resource group = {{ kv_rg }}"

    - name: Create instance of Key Vault
      azure_rm_keyvault:
        subscription_id: "{{ az_sub_id }}"
        resource_group: "{{ kv_rg }}"
        vault_name: "{{ kv }}"
        vault_tenant: "{{ az_tenant_id }}"
        enabled_for_deployment: yes
        sku:
          name: standard
          family: A
        access_policies:
          - object_id: "{{ az_object_id }}"
            tenant_id: "{{ az_tenant_id }}"
            secrets:
              - get
              - list
              - set

    - debug:
        msg: "New Key Vault instance name = {{ kv }} within the {{ kv_rg }} resource group"

    - name: Create a secret
      azure_rm_keyvaultsecret:
        subscription_id: "{{ az_sub_id }}"
        client_id: "{{ az_client_id }}"
        keyvault_uri: "{{ kv_uri }}"
        secret_name: "{{ kv_secret_name }}"
        secret_value: "{{ kv_secret_value }}"

    - name: Register Key Vault provider.
      shell:
        az provider register -n Microsoft.KeyVault

    - name: Get latest version of a secret
      azure_rm_keyvaultsecret_info:
        vault_uri: "{{ kv_uri }}"
        name: "{{ kv_secret_name }}"
      register: output
    - debug:
        var: output['secrets'][0]['secret']

    - name: Create resource group for test VM.
      azure_rm_resourcegroup:
        subscription_id: "{{ az_sub_id }}"
        name: "{{ test_vm_rg }}"
        location: "{{ test_vm_rg_loc }}"

    - name: Create virtual network.
      azure_rm_virtualnetwork:
        subscription_id: "{{ az_sub_id }}"
        resource_group: "{{ test_vm_rg }}"
        name: "{{ test_vm_vnet }}"
        address_prefixes: "172.16.0.0/16"

    - name: Create subset within virtual network.
      azure_rm_subnet:
        subscription_id: "{{ az_sub_id }}"
        resource_group: "{{ test_vm_rg }}"
        virtual_network_name: "{{ test_vm_vnet }}"
        name: "{{ test_vm_subnet }}"
        address_prefix_cidr:  "172.16.10.0/24"

    - name: Create public IP address.
      azure_rm_publicipaddress:
        subscription_id: "{{ az_sub_id }}"
        resource_group: "{{ test_vm_rg }}"
        allocation_method: Static
        name: "{{ test_vm_public_ip }}"

    - name: Create Network Security Group and rules.
      azure_rm_securitygroup:
        subscription_id: "{{ az_sub_id }}"
        resource_group: "{{ test_vm_rg }}"
        name: "{{ test_vm_nsg}}"
        rules:
          - name: "{{ item.name }}"
            access: "{{ item.access }}"
            protocol: "{{ item.protocol }}"
            direction: "{{ item.direction }}"
            destination_port_range: "{{ item.port }}"
            priority: "{{ item.priority }}"
            source_address_prefix: "{{ item.source_address_prefix }}"
      loop: "{{ test_vm_nsg_list }}"

    - name: Create virtual network interface card (NIC).
      azure_rm_networkinterface:
        subscription_id: "{{ az_sub_id }}"
        resource_group: "{{ test_vm_rg }}"
        name: "{{ test_vm_nic }}"
        virtual_network: "{{ test_vm_vnet }}"
        subnet: "{{ test_vm_subnet }}"
        ip_configurations:
          - name: ipconfig
            public_ip_address_name: "{{ test_vm_public_ip }}"
            primary: yes
        security_group: "{{ test_vm_nsg }}"

    - name: Create virtual machine.
      azure_rm_virtualmachine:
        subscription_id: "{{ az_sub_id }}"
        resource_group: "{{ test_vm_rg }}"
        name: "{{ test_vm }}"
        admin_username: " {{ admin_username }} "
        admin_password: " {{ output['secrets'][0]['secret'] }}"
        vm_size: Standard_B1ms
        network_interfaces: "{{ test_vm_nic }}"
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          version: latest

```

**Observações:**

- A senha de *administrador* para a máquina virtual é definida como o segredo do cofre de chaves.
- Sua capacidade de executar todo o guia estratégico de uma vez depende do seu ambiente de teste. Talvez seja necessário adicionar-se manualmente à política de acesso do cofre de chaves antes de criar a chave. Essa tarefa é explicada nas seções [Criar um cofre de chaves](#create-a-key-vault) e [Adicionar-se à política de acesso do cofre de chaves](#add-yourself-to-key-vault-access-policy).
- Como é possível ver, muitos módulos diferentes do Ansible são usados para criar uma máquina virtual do Azure e todos os componentes constituintes dela. Para obter mais informações sobre os vários módulos do Ansible usados para criar uma máquina virtual, use a seguinte lista:
    - [Módulo do grupo de recursos do Azure (azure_rm_resourcegroup)](https://docs.ansible.com/ansible/latest/modules/azure_rm_resourcegroup_module.html)
    - [Módulo de rede virtual do Azure (azure_rm_virtualnetwork)](https://docs.ansible.com/ansible/latest/modules/azure_rm_virtualnetwork_module.html)
    - [Módulo de sub-rede de rede virtual do Azure (azure_rm_subnet)](https://docs.ansible.com/ansible/latest/modules/azure_rm_subnet_module.html)
    - [Módulo de IP público do Azure (azure_rm_publicipaddress)](https://docs.ansible.com/ansible/latest/modules/azure_rm_publicipaddress_module.html)
    - [Módulo do grupo de segurança de rede do Azure (azure_rm_securitygroup)](https://docs.ansible.com/ansible/latest/modules/azure_rm_securitygroup_module.html)
    - [Adaptador de rede do Azure (azure_rm_networkinterface)](https://docs.ansible.com/ansible/latest/modules/azure_rm_networkinterface_module.html)
    - [Máquina virtual do Azure (azure_rm_virtualmachine)](https://docs.ansible.com/ansible/latest/modules/azure_rm_virtualmachine_module.html)
    
## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, exclua os recursos criados neste artigo. Substitua o espaço reservado `<kv_rg>` pelo grupo de recursos usado para manter o cofre de chaves de demonstração. 

```yml  
- hosts: localhost  
  vars: 
    kv_rg: <kv_rg>  
    test_vm_rg: kv_test_vm_rg   
  tasks:    
    - name: Delete the key vault resource group 
      azure_rm_resourcegroup:   
        name: "{{ kv_rg }}" 
        force_delete_nonempty: yes  
        state: absent   
    - name: Delete the test vm resource group   
      azure_rm_resourcegroup:   
        name: "{{ test_vm_rg }}"    
        force_delete_nonempty: yes  
        state: absent
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Visão geral da segurança do Azure Key Vault](/azure/key-vault/general/overview-security)
