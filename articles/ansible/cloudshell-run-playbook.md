---
title: Início Rápido – Executar guias estratégicos do Ansible por meio do Bash no Azure Cloud Shell
description: Neste Início Rápido, saiba como realizar várias tarefas do Ansible com o Bash no Azure Cloud Shell
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.date: 04/30/2019
ms.openlocfilehash: e5da7532b3bed43190509168827ac88f5b7b10be
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "81743725"
---
# <a name="quickstart-run-ansible-playbooks-via-bash-in-azure-cloud-shell"></a>Início Rápido: Executar guias estratégicos do Ansible por meio do Bash no Azure Cloud Shell

O Azure Cloud Shell é um shell interativo e acessível pelo navegador para o gerenciamento de recursos do Azure. O Cloud Shell permite que você use uma linha de comando do PowerShell ou do Bash. Neste artigo, você usará o Bash no Azure Cloud Shell para executar um guia estratégico do Ansible.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Configurar o Azure Cloud Shell** – se você não estiver familiarizado com o Azure Cloud Shell, confira [Início Rápido do Bash no Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart).
[!INCLUDE [cloud-shell-try-it.md](../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Configuração automática de credencial

Quando conectado ao Cloud Shell, o Ansible autentica com o Azure para gerenciar a infraestrutura sem qualquer configuração adicional. 

Ao trabalhar com várias assinaturas, especifique a assinatura usada pelo Ansible exportando a variável de ambiente `AZURE_SUBSCRIPTION_ID`. 

Para listar todas as suas assinaturas do Azure, execute o seguinte comando:

```azurecli-interactive
az account list
```

Usando sua ID da assinatura do Azure, defina a `AZURE_SUBSCRIPTION_ID` da seguinte maneira:

```console
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## <a name="verify-the-configuration"></a>Verificar a configuração
Para verificar a configuração bem-sucedida, use o Ansible para criar um grupo de recursos do Azure.

[!INCLUDE [create-resource-group-with-ansible.md](includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Início Rápido: Configurar uma máquina virtual no Azure usando o Ansible](./vm-configure.md)