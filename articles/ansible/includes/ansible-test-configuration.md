---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 09/15/2020
ms.author: tarcher
ms.openlocfilehash: 5351445b63618d072ecf4cf8beeffc9a071bd84d
ms.sourcegitcommit: bfaeacc2fb68f861a9403585d744e51a8f99829c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90682029"
---
Esta seção mostra como criar um grupo de recursos de teste dentro da nova configuração do Ansible. Se você não precisar fazer isso, poderá ignorar esta seção.

### <a name="create-an-azure-resource-group"></a>Criar um grupo de recursos do Azure

1. Salve o código a seguir como `create_rg.yml`.

    ```yaml
    ---
    - hosts: localhost
      connection: local
      tasks:
        - name: Creating resource group - "{{ name }}"
          azure_rm_resourcegroup:
            name: "{{ name }}"
            location: "{{ location }}"
          register: rg
        - debug:
            var: rg
    ```

1. Executar o guia estratégico usando [ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html). Substitua os espaços reservados pelo nome e pelo local do grupo de recursos a ser criado.

    ```bash
    ansible-playbook create_rg.yml --extra-vars "name=<resource_group_name> location=<resource_group_location>"
    ```

    **Observações**:

    - Devido à variável `register` e à seção `debug` do guia estratégico, os resultados são exibidos quando o comando é concluído.

### <a name="delete-an-azure-resource-group"></a>Excluir um grupo de recursos do Azure

[!INCLUDE [ansible-delete-resource-group.md](ansible-delete-resource-group.md)]
