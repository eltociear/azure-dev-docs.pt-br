---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 09/15/2020
ms.author: tarcher
ms.openlocfilehash: 4c4bb046496f80a7280aed429a8cd251e533a8db
ms.sourcegitcommit: bfaeacc2fb68f861a9403585d744e51a8f99829c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90681654"
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

1. Execute [az group delete](https://docs.microsoft.com/cli/azure/group#az_group_delete) para excluir o grupo de recursos. Todos os recursos dentro do grupo de recursos serão excluídos.

    ```azurecli
    az group delete --name <resource_group>
    ```

1. Verifique se o grupo de recursos foi excluído usando [az group show](https://docs.microsoft.com/cli/azure/group#az_group_show).

    ```azurecli
    az group show --name <resource_group>
    ```

#### <a name="azure-powershell"></a>[PowerShell do Azure](#tab/azure-powershell)

1. Execute [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/Remove-AzResourceGroup) para excluir o grupo de recursos. Todos os recursos dentro do grupo de recursos serão excluídos.

    ```azurepowershell
    Remove-AzResourceGroup -Name <resource_group>
    ```

1. Verifique se o grupo de recursos foi excluído usando [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/Get-AzResourceGroup).

    ```azurepowershell
    Get-AzResourceGroup -Name <resource_group>
    ```

---
