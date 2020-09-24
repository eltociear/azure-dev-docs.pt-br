---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 09/15/2020
ms.author: tarcher
ms.openlocfilehash: 885764615beed7a623db03499b4ff6a6ab705ba7
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831162"
---
#### <a name="ansible"></a>[Ansible](#tab/ansible)

1. Salve o código a seguir como `delete_rg.yml`.

    ```yml
    ---
    - hosts: localhost
      tasks:
        - name: Deleting resource group - "{{ name }}"
          azure_rm_resourcegroup:
            name: "{{ name }}"
            state: absent
          register: rg
        - debug:
            var: rg
    ```

1. Execute o guia estratégico usando o comando [ansible-playbook](https://docs.ansible.com/ansible/latest/user_guide/playbooks.html). Substitua o espaço reservado pelo nome do grupo de recursos a ser excluído. Todos os recursos dentro do grupo de recursos serão excluídos.

    ```bash
    ansible-playbook delete_rg.yml --extra-vars "name=<resource_group>"
    ```

    **Observações**:

    - Devido à variável `register` e à seção `debug` do guia estratégico, os resultados são exibidos quando o comando é concluído.
    
#### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Execute [az group delete](/cli/azure/group#az_group_delete) para excluir o grupo de recursos. Todos os recursos dentro do grupo de recursos serão excluídos.

    ```azurecli
    az group delete --name <resource_group>
    ```

1. Verifique se o grupo de recursos foi excluído usando [az group show](/cli/azure/group#az_group_show).

    ```azurecli
    az group show --name <resource_group>
    ```

#### <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

1. Execute [Remove-AzResourceGroup](/powershell/module/az.resources/Remove-AzResourceGroup) para excluir o grupo de recursos. Todos os recursos dentro do grupo de recursos serão excluídos.

    ```azurepowershell
    Remove-AzResourceGroup -Name <resource_group>
    ```

1. Verifique se o grupo de recursos foi excluído usando [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup).

    ```azurepowershell
    Get-AzResourceGroup -Name <resource_group>
    ```

---