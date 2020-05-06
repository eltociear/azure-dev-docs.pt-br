---
title: Início Rápido – Instalar e configurar o Terraform para provisionar recursos do Azure
description: Saiba como instalar e configurar o Terraform para criar recursos do Azure.
keywords: azure devops terraform instalar configurar
ms.topic: quickstart
ms.date: 04/26/2020
ms.openlocfilehash: e395227171417ccf73ef073a6067732fb11a418d
ms.sourcegitcommit: 756e4873f904db954a56c20ebb2f1f5116ee4596
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82169732"
---
# <a name="quickstart-install-and-configure-terraform-to-provision-azure-resources"></a>Início Rápido: Instalar e configurar o Terraform para provisionar recursos do Azure
 
O Terraform fornece uma maneira fácil de definir, visualizar e implantar a infraestrutura de nuvem usando uma [linguagem de modelagem simples](https://www.terraform.io/docs/configuration/syntax.html). Este artigo descreve as etapas necessárias para usar o Terraform para provisionar recursos no Azure.

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="install-terraform"></a>Instalar o Terraform

Por padrão, a versão mais recente do Terraform é instalada para uso no [Azure Cloud Shell](/azure/cloud-shell/overview). Se você escolher a instalação local do Terraform, siga esta etapa. Caso contrário, passe para [Configurar o acesso do Terraform ao Azure](#configure-terraform-access-to-azure).

1. [Instale o Terraform](https://www.terraform.io/downloads.html) especificando o pacote apropriado para o sistema operacional.
1. O download contém um arquivo executável. Defina um caminho global para o executável com base no sistema operacional:
    - [Linux ou MacOS](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux)
    - [Windows](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows).
1. Verifique a configuração do caminho global com o comando `terraform`. Se o Terraform for encontrado e executado, uma lista de opções disponíveis dele exibirá:

    ```console
    azureuser@Azure:~$ terraform
    Usage: terraform [--version] [--help] <command> [args]
    ```
    
## <a name="configure-terraform-access-to-azure"></a>Configurar o acesso do Terraform ao Azure

Para permitir que o Terraform provisione recursos para o Azure, crie uma [entidade de serviço do Azure AD](/cli/azure/create-an-azure-service-principal-azure-cli). A entidade de serviço concede seus scripts Terraform para provisionar recursos em sua assinatura do Azure.

Se você tiver várias assinaturas do Azure, primeiro consulte sua conta com [az account list](/cli/azure/account#az-account-list) para obter uma lista de valores de ID da assinatura e ID do locatário:

```azurecli-interactive
az account list --query "[].{name:name, subscriptionId:id, tenantId:tenantId}"
```

Para usar uma assinatura selecionada, defina a assinatura para esta sessão com [az account set](/cli/azure/account#az-account-set). Configure a variável de ambiente `SUBSCRIPTION_ID`para conter o valor do campo`id` retornado da assinatura que você deseja usar:

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

Agora você pode criar uma entidade de serviço para uso com o Terraform. Use [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) e defina o *escopo* para sua assinatura da seguinte maneira:

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

`appId`, `password`, `sp_name` e `tenant` são retornados. Anote `appId` e `password`.

## <a name="configure-terraform-environment-variables"></a>Configurar as variáveis de ambiente do Terraform

Para configurar o Terraform para usar sua entidade de serviço do Azure AD, defina as seguintes variáveis de ambiente, que são usadas pelos [módulos do Azure Terraform](https://registry.terraform.io/modules/Azure). Você também pode definir o ambiente se estiver trabalhando com uma nuvem do Azure diferente do público do Azure.

- `ARM_SUBSCRIPTION_ID`
- `ARM_CLIENT_ID`
- `ARM_CLIENT_SECRET`
- `ARM_TENANT_ID`
- `ARM_ENVIRONMENT`

Você pode usar o seguinte script de shell de amostra para definir essas variáveis:

```bash
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id

# Not needed for public, required for usgovernment, german, china
export ARM_ENVIRONMENT=public
```

## <a name="run-a-sample-script"></a>Executar um script de exemplo

Crie um arquivo `test.tf` em um diretório vazio e cole o script a seguir.

```hcl
provider "azurerm" {
  # The "feature" block is required for AzureRM provider 2.x. 
  # If you are using version 1.x, the "features" block is not allowed.
  version = "~>2.0"
  features {}
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Salve o arquivo e, em seguida, inicialize a implantação do Terraform. Esta etapa faz o download dos módulos do Azure necessários para criar um grupo de recursos do Azure.

```bash
terraform init
```

A saída deverá ser semelhante ao seguinte exemplo:

```console
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

Você pode visualizar as ações a serem completadas pelo script Terraform com `terraform plan`. Quando estiver pronto para criar o grupo de recursos, aplique seu plano Terraform da seguinte maneira:

```bash
terraform apply
```

A saída deverá ser semelhante ao seguinte exemplo:

```console
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.rg
      id:       <computed>
      location: "westus"
      name:     "testResourceGroup"
      tags.%:   <computed>

azurerm_resource_group.rg: Creating...
  location: "" => "westus"
  name:     "" => "testResourceGroup"
  tags.%:   "" => "<computed>"
azurerm_resource_group.rg: Creation complete after 1s
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você instalou o Terraform ou usou o Cloud Shell para configurar as credenciais do Azure e começar a criar recursos em sua assinatura do Azure. Para criar uma implantação mais completa do Terraform no Azure, consulte o seguinte artigo:

> [!div class="nextstepaction"]
> [Criar uma VM do Azure com o Terraform](create-linux-virtual-machine-with-infrastructure.md)