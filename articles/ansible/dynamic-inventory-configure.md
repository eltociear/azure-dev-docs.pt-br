---
title: Tutorial – Configurar inventários dinâmicos de seus recursos do Azure usando o Ansible
description: Saiba como usar o Ansible para gerenciar seus inventários dinâmicos do Azure
keywords: ansible, azure, devops, bash, cloudshell, inventário dinâmico
ms.topic: tutorial
ms.date: 10/23/2019
ms.custom: devx-track-ansible
ms.openlocfilehash: 53cf2e25a89081b314826cda4322e2556fc38c39
ms.sourcegitcommit: bfaeacc2fb68f861a9403585d744e51a8f99829c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90682067"
---
# <a name="tutorial-configure-dynamic-inventories-of-your-azure-resources-using-ansible"></a>Tutorial: Configurar inventários dinâmicos de seus recursos do Azure usando o Ansible

O Ansible pode ser usado para extrair informações de inventário de várias fontes (incluindo as origens de nuvem, como o Azure) em um *inventário dinâmico*. 

[!INCLUDE [ansible-tutorial-goals.md](includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Configurar duas máquinas virtuais de teste
> * Marcar uma das máquinas virtuais
> * Instalar o Nginx nas máquinas virtuais marcadas
> * Configurar um inventário dinâmico que inclua os recursos configurados do Azure

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-test-vms"></a>Criar as VMs de teste

1. Entre no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Abra o [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

1. Crie um grupo de recursos do Azure para manter as máquinas virtuais para este tutorial.

    > [!IMPORTANT]
    > O grupo de recursos do Azure criado nesta etapa deve ter um nome inteiramente em minúsculo. Caso contrário, a geração do inventário dinâmico falhará.

    ```azurecli-interactive
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. Crie duas máquinas virtuais do Linux no Azure usando uma das seguintes técnicas:

    - **Guia estratégico do Ansible**: o artigo [Criar uma máquina virtual básica no Azure com o Ansible](./vm-configure.md) ilustra como criar uma máquina virtual a partir de um guia estratégico do Ansible. Se você usar um guia estratégico para definir uma ou ambas as máquinas virtuais, verifique se a conexão SSH é usada em vez de uma senha.

    - **CLI do Azure** – emita cada um dos seguintes comandos no Cloud Shell para criar as duas máquinas virtuais:

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm1 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm2 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

## <a name="tag-a-vm"></a>Marcar uma VM

Você pode [usar marcas para organizar os recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli) por categorias definidas pelo usuário.

### <a name="using-ansible-version--28"></a>Usar o Ansible versão < 2.8
Insira o seguinte comando [az resource tag](/cli/azure/resource#az-resource-tag) para marcar a máquina virtual `ansible-inventory-test-vm1` com a chave `nginx`:

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

### <a name="using-ansible-version--28"></a>Usar o Ansible versão >= 2.8
Insira o seguinte comando [az resource tag](/cli/azure/resource#az-resource-tag) para marcar a máquina virtual `ansible-inventory-test-vm1` com a chave `Ansible=nginx`:

```azurecli-interactive
az resource tag --tags Ansible=nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>Gerar um inventário dinâmico

Assim que as máquinas virtuais estiverem definidas (e marcadas), é hora de gerar o inventário dinâmico.

### <a name="using-ansible-version--28"></a>Usar o Ansible versão < 2.8

O Ansible fornece um script Python chamado [azure_rm.py](https://github.com/ansible-collections/community.general/blob/main/scripts/inventory/azure_rm.py) que gera um inventário dinâmico de recursos do Azure. As etapas a seguir o orientam a usar o script `azure_rm.py` para conectar-se às suas duas máquinas virtuais do Azure de teste:

1. Use o comando `wget` do GNU para recuperar o script `azure_rm.py`:

    ```python
    wget https://raw.githubusercontent.com/ansible-collections/community.general/main/scripts/inventory/azure_rm.py
    ```

1. Use o comando `chmod` para alterar as permissões de acesso para o script `azure_rm.py`. O comando a seguir usa o parâmetro `+x` para permitir a execução do arquivo especificado (`azure_rm.py`):

    ```python
    chmod +x azure_rm.py
    ```

1. Use o [comando ansible](https://docs.ansible.com/ansible/2.4/ansible.html) para se conectar ao grupo de recursos: 

    ```python
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
    ```

1. Uma vez conectado, você verá resultados semelhantes à seguinte saída:

    ```output
    ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ansible-inventory-test-vm2 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

### <a name="ansible-version--28"></a>Ansible versão >= 2.8

A partir da versão 2.8, o Ansible fornece um [plug-in de inventário dinâmico do Azure](https://github.com/ansible/ansible/blob/stable-2.9/lib/ansible/plugins/inventory/azure_rm.py). As etapas a seguir mostram o uso do plug-in:

1. O plug-in de inventário requer um arquivo de configuração. O arquivo de configuração deve terminar em `azure_rm` e ter uma extensão `yml` ou `yaml`. Para exemplo neste tutorial, salve o seguinte guia estratégico como `myazure_rm.yml`:

    ```yml
        plugin: azure_rm
        include_vm_resource_groups:
        - ansible-inventory-test-rg
        auth_source: auto
    
        keyed_groups:
        - prefix: tag
          key: tags
    ```

1. Execute o seguinte comando para fixar VMs no grupo de recursos:

    ```bash
    ansible all -m ping -i ./myazure_rm.yml
    ```

1. Ao executar o comando anterior, você pode receber o seguinte erro:

    ```output
    Failed to connect to the host via ssh: Host key verification failed.
    ```
    
    Se você receber o erro "verificação da chave do host", adicione a seguinte linha ao arquivo de configuração do Ansible. O arquivo de configuração do Ansible está localizado em `/etc/ansible/ansible.cfg` ou `~/.ansible.cfg`.

    ```bash
    host_key_checking = False
    ```

1. Depois de executar o guia estratégico, você verá resultados semelhantes à seguinte saída:
  
    ```output
    ansible-inventory-test-vm1_0324 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ansible-inventory-test-vm2_8971 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

## <a name="enable-the-vm-tag"></a>Habilitar a marca da VM

### <a name="if-youre-using-ansible--28"></a>Se você estiver usando o Ansible < 2.8,

- Depois de definir uma marca, você precisa "habilitar" essa marca. Uma maneira de habilitar uma marca é exportando a marca para uma variável de ambiente `AZURE_TAGS` por meio do comando `export`:

    ```console
    export AZURE_TAGS=nginx
    ```
    
- Execute o comando a seguir:

    ```bash
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping
    ```
    
    Agora você vê apenas uma máquina virtual (aquela cuja marca corresponde ao valor exportado para a variável de ambiente `AZURE_TAGS`):

    ```output
       ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

### <a name="if-youre-using-ansible---28"></a>Se você estiver usando o Ansible >= 2.8

- Execute o comando `ansible-inventory -i myazure_rm.yml --graph` para obter a seguinte saída:

    ```output
        @all:
          |--@tag_Ansible_nginx:
          |  |--ansible-inventory-test-vm1_9e2f
          |--@ungrouped:
          |  |--ansible-inventory-test-vm2_7ba9
    ```

- Você também pode executar o seguinte comando para testar a conexão com a VM Nginx:
  
    ```bash
    ansible -i ./myazure_rm.yml -m ping tag_Ansible_nginx
    ```


## <a name="set-up-nginx-on-the-tagged-vm"></a>Configurar Nginx na VM marcada

A finalidade das marcas é habilitar a capacidade de trabalhar de forma rápida e fácil com subgrupos das máquinas virtuais. Por exemplo, digamos que você deseja instalar o Nginx apenas em máquinas virtuais para as quais você atribuiu uma marca de `nginx`. As etapas a seguir ilustram como é fácil:

1. Criar um arquivo chamado `nginx.yml`:

   ```console
   code nginx.yml
   ```

1. Cole o código de exemplo a seguir no editor:

    ```yml
        ---
        - name: Install and start Nginx on an Azure virtual machine
          hosts: all
          become: yes
          tasks:
          - name: install nginx
            apt: pkg=nginx state=present
            notify:
            - start nginx
    
          handlers:
            - name: start nginx
              service: name=nginx state=started
    ```

1. Salve o arquivo e saia do editor.

1. Executar o guia estratégico usando [ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)

   - Ansible < 2.8:

     ```bash
     ansible-playbook -i azure_rm.py nginx.yml
     ```

   - Ansible >= 2.8:

     ```bash
     ansible-playbook  -i ./myazure_rm.yml  nginx.yml --limit=tag_Ansible_nginx
     ```

1. Depois de executar o guia estratégico, você verá resultados semelhantes aos seguintes:

    ```output
    PLAY [Install and start Nginx on an Azure virtual machine] 

    TASK [Gathering Facts] 
    ok: [ansible-inventory-test-vm1]

    TASK [install nginx] 
    changed: [ansible-inventory-test-vm1]

    RUNNING HANDLER [start nginx] 
    ok: [ansible-inventory-test-vm1]

    PLAY RECAP 
    ansible-inventory-test-vm1 : ok=3    changed=1    unreachable=0    failed=0
    ```

## <a name="test-nginx-installation"></a>Testar a instalação do Nginx

Esta seção ilustra uma técnica para testar se o Nginx está instalado em sua máquina virtual.

1. Use o comando [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm#az-vm-list-ip-addresses) para recuperar o endereço IP da máquina virtual `ansible-inventory-test-vm1`. O valor retornado (endereço IP da máquina virtual) é usado como o parâmetro para o comando SSH para se conectar à máquina virtual.

    ```azurecli-interactive
    ssh `az vm list-ip-addresses \
    -n ansible-inventory-test-vm1 \
    --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress -o tsv`
    ```

1. Enquanto conectado à máquina virtual `ansible-inventory-test-vm1`, execute o comando [nginx -v](https://nginx.org/en/docs/switches.html) para determinar se o Nginx está instalado.

    ```console
    nginx -v
    ```

1. Assim que executa o comando `nginx -v`, você vê a versão do Nginx (segunda linha) que indica se o Nginx está instalado.

    ```output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)

    tom@ansible-inventory-test-vm1:~$
    ```

1. Clique na combinação de teclas `<Ctrl>D` para desconectar a sessão SSH.

1. Realizar as etapas anteriores para a máquina virtual `ansible-inventory-test-vm2` gera uma mensagem informativa que indica onde você pode obter o Nginx (o que implica que você não o tem instalado neste momento):

    ```output
    tom@ansible-inventory-test-vm2:~$ nginx -v
    The program 'nginx' can be found in the following packages:
    * nginx-core
    * nginx-extras
    * nginx-full
    * nginx-lightTry: sudo apt install <selected package>
    tom@ansible-inventory-test-vm2:~$
    ```

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [ansible-delete-resource-group.md](includes/ansible-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Início Rápido: Configurar máquinas virtuais do Linux no Azure usando o Ansible](./vm-configure.md)
