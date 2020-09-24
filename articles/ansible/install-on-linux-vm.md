---
title: Guia de Início Rápido – Configurar o Ansible usando a CLI do Azure
description: Neste Início Rápido, saiba como instalar e configurar o Ansible para gerenciar recursos do Azure no Ubuntu, no CentOS e no SLES
keywords: ansible, azure, devops, bash, cloudshell, playbook, azure cli
ms.topic: quickstart
ms.date: 08/13/2020
ms.custom: devx-track-ansible,devx-track-cli
ms.openlocfilehash: bdda836789e9230cffdc14a6ee4bd87ddb2ce5ef
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831163"
---
# <a name="quickstart-configure-ansible-using-azure-cli"></a>Início Rápido: Configurar o Ansible usando a CLI do Azure

Este guia de início rápido mostra como instalar o [Ansible](https://docs.ansible.com/) usando a CLI do Azure.

Neste início rápido, você concluiu estas tarefas:

> [!div class="checklist"]
> * Criar um par de chaves SSH
> * Criar um grupo de recursos
> * Criar uma máquina virtual do CentOS 
> * Instalar o Ansible na máquina virtual
> * Conectar-se à máquina virtual via SSH
> * Configurar o Ansible na máquina virtual

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-sp.md](../includes/open-source-devops-prereqs-create-service-principal.md)]
- **Acesso ao Linux ou a uma máquina virtual do Linux** - Se você não tiver acesso a um computador do Linux, crie uma [máquina virtual do Linux](/azure/virtual-network/quick-create-cli).

## <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH

Ao se conectar a VMs do Linux, você pode usar a autenticação de senha ou a autenticação baseada em chave. A autenticação baseada em chave é mais segura do que o uso de senhas. Assim, este artigo usa a autenticação baseada em chave.

Com a autenticação baseada em chave, há duas chaves:

- **Chave pública**: a chave pública é armazenada no host, por exemplo, na sua VM (como neste artigo)
- **Chave privada**: a chave privada permite que você se conecte com segurança ao host. Ela é efetivamente sua senha e deve ser protegida como tal.
        
As etapas a seguir orientarão você na criação de um par de chaves SSH.

1. Entre no [portal do Azure](https://portal.azure.com).

1. Abra o [Azure Cloud Shell](/azure/cloud-shell/overview) e, se não tiver feito isso ainda, mude para **Bash**.

1. Crie uma chave SSH usando [ssh-keygen](https://www.ssh.com/ssh/keygen/).

    ```bash
    ssh-keygen -m PEM -t rsa -b 2048 -C "azureuser@azure" -f ~/.ssh/ansible_rsa -N ""
    ```

    **Observações**:

    - O comando `ssh-keygen` exibe a localização dos arquivos de chave gerados. Você precisará desse nome de diretório ao criar a máquina virtual.
    - A chave pública é armazenada em `ansible_rsa.pub`, e a chave privada é armazenada em `ansible_rsa`.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Criar um grupo de recursos usando [az group create](/cli/azure/group#az-group-create). Talvez seja necessário substituir o parâmetro `--location` pelo valor apropriado para o seu ambiente.

    ```azurecli
    az group create --name QuickstartAnsible-rg --location eastus
    ```

1. Crie uma máquina virtual com [az vm create](/cli/azure/vm#az-vm-create).

    ```azurecli
    az vm create \
    --resource-group QuickstartAnsible-rg \
    --name QuickstartAnsible-vm \
    --image OpenLogic:CentOS:7.7:latest \
    --admin-username azureuser \
    --ssh-key-values <ssh_public_key_filename>
    ```

1. Confirme a criação (e o estado) da nova máquina virtual usando [az vm list](/cli/azure/vm#az-vm-list).

    ```azurecli
    az vm list -d -o table --query "[?name=='QuickstartAnsible-vm']"
    ```

    **Observações**:

    - A saída do comando `az vm list` inclui o endereço IP público usado para se conectar via SSH à máquina virtual.

## <a name="install-ansible-on-the-virtual-machine"></a>Instalar o Ansible na máquina virtual

Execute o script de instalação do Ansible usando [az vm extension set](/cli/azure/vm/extension?#az-vm-extension-set).

```azurecli
az vm extension set \
 --resource-group QuickstartAnsible-rg \
 --vm-name QuickstartAnsible-vm \
 --name customScript \
 --publisher Microsoft.Azure.Extensions \
 --version 2.1 \
 --settings '{"fileUris":["https://raw.githubusercontent.com/MicrosoftDocs/mslearn-ansible-control-machine/master/configure-ansible-centos.sh"]}' \
 --protected-settings '{"commandToExecute": "./configure-ansible-centos.sh"}'
```

**Observações:**

- Após a conclusão, o comando `az vm extension` exibirá os resultados da execução do script de instalação.

## <a name="connect-to-your-virtual-machine-via-ssh"></a>Conectar-se à sua máquina virtual via SSH

Usando o comando SSH, conecte-se à sua máquina virtual.

```azurecli
ssh -i <ssh_private_key_filename> azureuser@<vm_ip_address>
```

## <a name="create-azure-credentials"></a>Criar as credenciais do Azure

Para configurar as credenciais do Ansible, você precisará ter as seguintes informações:

* ID da assinatura do Azure
* Os valores da entidade de serviço

Se você estiver usando o Ansible Tower ou o Jenkins, declare os valores da entidade de serviço como variáveis de ambiente.

Configure as credenciais do Ansible usando uma das seguintes técnicas:

- [Criar um arquivo de credenciais do Ansible](#file-credentials)
- [Usar variáveis de ambiente Ansible](#env-credentials)

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/> Criar arquivo de credenciais do Ansible

Nesta seção, você criará um arquivo de credenciais local para fornecer credenciais para o Ansible.

Para obter mais informações sobre como definir as credenciais do Ansible, confira [Como fornecer credenciais para os módulos do Azure](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

1. Para um ambiente de desenvolvimento, crie um arquivo chamado `credentials` na máquina virtual do host:

    ```bash
    mkdir ~/.azure
    vi ~/.azure/credentials
    ```

1. Insira as linhas a seguir no arquivo. Substitua os espaços reservados pelos valores da entidade de serviço.

    ```bash
    [default]
    subscription_id=<your-subscription_id>
    client_id=<security-principal-appid>
    secret=<security-principal-password>
    tenant=<security-principal-tenant>
    ```

1. Salve e feche o arquivo.

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Usar variáveis de ambiente Ansible

Nesta seção, você exportará os valores da entidade de serviço para configurar suas credenciais do Ansible.

1. Abra uma janela do terminal.

1. Exporte os valores da entidade de serviço:

    ```bash
    export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
    export AZURE_CLIENT_ID=<security-principal-appid>
    export AZURE_SECRET=<security-principal-password>
    export AZURE_TENANT=<security-principal-tenant>
    ```

## <a name="test-ansible-installation"></a>Testar a instalação do Ansible

Agora você tem uma máquina virtual com o Ansible instalado e configurado.

[!INCLUDE [ansible-test-configuration.md](includes/ansible-test-configuration.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Ansible no Azure](./index.yml)