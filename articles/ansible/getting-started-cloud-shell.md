---
title: Guia de Início Rápido – Configurar o Ansible usando o Azure Cloud Shell
description: Neste Início Rápido, saiba como realizar várias tarefas do Ansible com o Bash no Azure Cloud Shell
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.date: 09/14/2020
ms.custom: devx-track-ansible
ms.openlocfilehash: ddc4ba9f473e5e8d727996a4e902210f0d3055f6
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831182"
---
# <a name="quickstart-configure-ansible-using-azure-cloud-shell"></a>Início Rápido: Configurar o Ansible usando o Azure Cloud Shell

[!INCLUDE [annsible-intro.md](includes/ansible-intro.md)]

Este artigo descreve a introdução ao Ansible do ambiente do [Azure Cloud Shell](/azure/cloud-shell/overview).

## <a name="configure-your-environment"></a>Configurar seu ambiente

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Configurar o Azure Cloud Shell** – se você não estiver familiarizado com o Azure Cloud Shell, confira [Início Rápido do Bash no Azure Cloud Shell](/azure/cloud-shell/quickstart).

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

## <a name="test-ansible-installation"></a>Testar a instalação do Ansible

Agora você configurou o Ansible para uso no Cloud Shell!

[!INCLUDE [ansible-test-configuration.md](includes/ansible-test-configuration.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Início Rápido: Configurar uma máquina virtual no Azure usando o Ansible](./vm-configure.md)