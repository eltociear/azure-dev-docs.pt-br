---
title: Guia de Início Rápido – Configurar o Ansible usando o Azure Cloud Shell
description: Neste Início Rápido, saiba como realizar várias tarefas do Ansible com o Bash no Azure Cloud Shell
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.date: 08/31/2020
ms.custom: devx-track-ansible
ms.openlocfilehash: 42d7b57d9890bce3a432fba3c6fdcf3080ddb63c
ms.sourcegitcommit: 2f98cf2a394d4fd82ddc917ac1041c1dc08473b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89275150"
---
# <a name="quickstart-configure-ansible-using-azure-cloud-shell"></a>Início Rápido: Configurar o Ansible usando o Azure Cloud Shell

[!INCLUDE [annsible-intro.md](includes/ansible-intro.md)]

Este artigo descreve a introdução ao Ansible do ambiente do [Azure Cloud Shell](/azure/cloud-shell/overview).

## <a name="configure-your-environment"></a>Configurar seu ambiente

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Configurar o Azure Cloud Shell** – se você não estiver familiarizado com o Azure Cloud Shell, confira [Início Rápido do Bash no Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart).

[!INCLUDE [open-cloud-shell.md](../includes/open-cloud-shell.md)]

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
