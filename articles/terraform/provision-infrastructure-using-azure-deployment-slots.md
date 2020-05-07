---
title: Tutorial - Usando Terraform para provisionar infraestrutura com slots de implantação do Azure
description: Saiba como usar o Terraform com slots de implantação de provedor do Azure.
keywords: azure devops terraform deployment slots
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 8f5597684a9d61474685f8117fa584405fa0979e
ms.sourcegitcommit: 756e4873f904db954a56c20ebb2f1f5116ee4596
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82171952"
---
# <a name="tutorial-provision-infrastructure-with-azure-deployment-slots-using-terraform"></a>Tutorial: Usando Terraform para provisionar infraestrutura com slots de implantação do Azure

É possível usar os [slots de implantação do Azure](/azure/app-service/deploy-staging-slots) para alternar entre diferentes versões do aplicativo. Essa capacidade ajuda a minimizar o impacto de implantações interrompidas. 

Este artigo ilustra um exemplo de uso de slots de implantação ao guiá-lo pela implantação de dois aplicativos por meio de GitHub e Azure. Um aplicativo está hospedado em um slot de produção. O segundo aplicativo está hospedado em um slot de teste. (Os nomes "produção" e "preparo" são arbitrários. Eles podem ser qualquer um que seja apropriado para seu cenário.) Após configurar os slots de implantação, você usa o Terraform para alternar entre os dois slots, conforme necessário.

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Conta do GitHub**: é necessário ter uma conta do [GitHub](https://www.github.com) para criar fork e usar o repositório GitHub de teste.

## <a name="create-and-apply-the-terraform-plan"></a>Criar e aplicar o plano do Terraform

1. Navegue até o [Portal do Azure](https://portal.azure.com).

1. Abra o [Azure Cloud Shell](/azure/cloud-shell/overview). Se você não selecionou um ambiente anteriormente, selecione **Bash** como o ambiente.

    ![Aviso do Cloud Shell](./media/provision-infrastructure-using-azure-deployment-slots/azure-portal-cloud-shell-button-min.png)

1. Altere os diretórios para o diretório `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Crie um diretório com o nome `deploy`.

    ```bash
    mkdir deploy
    ```

1. Crie um diretório com o nome `swap`.

    ```bash
    mkdir swap
    ```

1. Use o comando bash `ls` para verificar se ambos os diretórios foram criados com êxito.

    ![Cloud Shell após a criação de diretórios](./media/provision-infrastructure-using-azure-deployment-slots/cloud-shell-after-creating-dirs.png)

1. Altere os diretórios para o diretório `deploy`.

    ```bash
    cd deploy
    ```

1. No Cloud Shell, crie um arquivo chamado `deploy.tf`.

    ```bash
    code deploy.tf
    ```

1. Cole o código a seguir no editor:

    ```hcl
    # Configure the Azure provider
    provider "azurerm" { 
        # The "feature" block is required for AzureRM provider 2.x. 
        # If you are using version 1.x, the "features" block is not allowed.
        version = "~>2.0"
        features {}
    }

    resource "azurerm_resource_group" "slotDemo" {
        name = "slotDemoResourceGroup"
        location = "westus2"
    }

    resource "azurerm_app_service_plan" "slotDemo" {
        name                = "slotAppServicePlan"
        location            = azurerm_resource_group.slotDemo.location
        resource_group_name = azurerm_resource_group.slotDemo.name
        sku {
            tier = "Standard"
            size = "S1"
        }
    }

    resource "azurerm_app_service" "slotDemo" {
        name                = "slotAppService"
        location            = azurerm_resource_group.slotDemo.location
        resource_group_name = azurerm_resource_group.slotDemo.name
        app_service_plan_id = azurerm_app_service_plan.slotDemo.id
    }

    resource "azurerm_app_service_slot" "slotDemo" {
        name                = "slotAppServiceSlotOne"
        location            = azurerm_resource_group.slotDemo.location
        resource_group_name = azurerm_resource_group.slotDemo.name
        app_service_plan_id = azurerm_app_service_plan.slotDemo.id
        app_service_name    = azurerm_app_service.slotDemo.name
    }
    ```

1. Salve o arquivo ( **&lt;Ctrl>S**) e saia do editor ( **&lt;Ctrl>Q**).

1. Agora que você criou o arquivo, verifique o conteúdo.

    ```bash
    cat deploy.tf
    ```

1. Inicialize o Terraform.

    ```bash
    terraform init
    ```

1. Crie o plano do Terraform.

    ```bash
    terraform plan
    ```

1. Provisione os recursos definidos no arquivo de configuração `deploy.tf`. (Confirme a ação inserindo `yes` no prompt.)

    ```bash
    terraform apply
    ```

1. Feche a janela do Cloud Shell.

1. No menu principal do Portal do Azure, selecione **Grupos de recursos**.

    ![Seleção "Grupos de recursos" no portal](./media/provision-infrastructure-using-azure-deployment-slots/resource-groups-menu-option.png)

1. Na guia **Grupos de recursos**, selecione **slotDemoResourceGroup**.

    ![Grupo de recursos criado pelo Terraform](./media/provision-infrastructure-using-azure-deployment-slots/resource-group.png)

Agora, você vê todos os recursos que o Terraform criou.

![Recursos criados pelo Terraform](./media/provision-infrastructure-using-azure-deployment-slots/resources.png)

## <a name="fork-the-test-project"></a>Criar fork do projeto de teste

Antes de testar a criação e a troca dentro e fora dos slots de implantação, você precisa criar fork do projeto de teste do GitHub.

1. Navegue até o [repositório awesome-terraform no GitHub](https://github.com/Azure/awesome-terraform).

1. Crie fork do repositório **awesome-terraform**.

    ![Crie fork do repositório awesome-terraform do GitHub](./media/provision-infrastructure-using-azure-deployment-slots/fork-repo.png)

1. Siga todos os avisos para criar fork para seu ambiente.

## <a name="deploy-from-github-to-your-deployment-slots"></a>Implantar do GitHub para seus slots de implantação

Após criar fork do repositório do projeto de teste, configure os slots de implantação por meio das etapas a seguir:

1. No menu principal do Portal do Azure, selecione **Grupos de recursos**.

1. Selecione **slotDemoResourceGroup**.

1. Selecione **slotAppService**.

1. Selecione **Opções de implantação**.

    ![Opções de implantação para um recurso de Serviço de Aplicativo](./media/provision-infrastructure-using-azure-deployment-slots/deployment-options.png)

1. Na guia **Opção de implantação**, selecione **Escolher Fonte** e, em seguida, selecione **GitHub**.

    ![Selecionar fonte de implantação](./media/provision-infrastructure-using-azure-deployment-slots/select-source.png)

1. Depois que o Azure faz a conexão e exibe todas as opções, selecione **Autorização**.

1. Na guia **Autorização**, selecione **Autorizar** e forneça as credenciais que o Azure precisa para acessar a conta do GitHub. 

1. Depois que o Azure validar as credenciais do GitHub, será exibida uma mensagem informando que o processo de autorização foi concluído. Selecione **OK** para fechar a guia **Autorização**.

1. Selecione **Escolha sua organização** e selecione sua organização.

1. Selecione **Escolher projeto**.

1. Na guia **Escolher projeto**, selecione o projeto **awesome-terraform**.

    ![Escolher o projeto awesome-terraform](./media/provision-infrastructure-using-azure-deployment-slots/choose-project.png)

1. Selecione **Escolher branch**.

1. Na guia **Escolher branch**, selecione **mestre**.

    ![Escolher o branch mestre](./media/provision-infrastructure-using-azure-deployment-slots/choose-branch-master.png)

1. Na guia **Opção de implantação**, selecione **OK**.

Neste ponto, você implantou o slot de produção. Para implantar o slot de preparo, execute as etapas anteriores com as seguintes modificações:

- Na etapa 3, selecione o recurso **slotAppServiceSlotOne**.

- Na etapa 13, selecione o branch de trabalho em vez do branch mestre.

    ![Escolha o branch de trabalho](./media/provision-infrastructure-using-azure-deployment-slots/choose-branch-working.png)

## <a name="test-the-app-deployments"></a>Testar as implantações de aplicativo

Nas seções anteriores, você configurou dois slots--**slotAppService** e **slotAppServiceSlotOne**--para implantar a partir de diferentes branches no GitHub. Vamos visualizar os aplicativos Web para validar que foram implantados com êxito.

1. No menu principal do Portal do Azure, selecione **Grupos de recursos**.

1. Selecione **slotDemoResourceGroup**.

1. Selecione **slotAppService** ou **slotAppServiceSlotOne**.

1. Na página de visão geral, selecione **URL**.

    ![Selecione a URL na guia de visão para renderizar o aplicativo](./media/provision-infrastructure-using-azure-deployment-slots/resource-url.png)

1. Dependendo do aplicativo selecionado, você verá os seguintes resultados:
    - aplicativo Web **slotAppService** - Página azul com um título de página de **Slot Demo App 1**. 
    - aplicativo Web **slotAppServiceSlotOne** - Página verde com um título de página de **Slot Demo App 2**.

    ![Visualizar os aplicativos para testar se foram implantados corretamente](./media/provision-infrastructure-using-azure-deployment-slots/app-preview.png)

## <a name="swap-the-two-deployment-slots"></a>Trocar os dois slots de implantação

Para testar a troca dos dois slots de implantação, execute as seguintes etapas:
 
1. Alterne para a guia do navegador que está executando o **slotAppService** (o aplicativo com a página azul). 

1. Retorne para o Portal do Azure em uma guia separada.

1. Abra o Azure Cloud Shell.

1. Altere os diretórios para o diretório **clouddrive/swap**.

    ```bash
    cd clouddrive/swap
    ```

1. No Cloud Shell, crie um arquivo chamado `swap.tf`.

    ```bash
    code swap.tf
    ```

1. Cole o código a seguir no editor:

    ```hcl
    # Configure the Azure provider
    provider "azurerm" { 
        # The "feature" block is required for AzureRM provider 2.x. 
        # If you are using version 1.x, the "features" block is not allowed.
        version = "~>2.0"
        features {}
    }

    # Swap the production slot and the staging slot
    resource "azurerm_app_service_active_slot" "slotDemoActiveSlot" {
        resource_group_name   = "slotDemoResourceGroup"
        app_service_name      = "slotAppService"
        app_service_slot_name = "slotappServiceSlotOne"
    }
    ```

1. Salve o arquivo ( **&lt;Ctrl>S**) e saia do editor ( **&lt;Ctrl>Q**).

1. Inicialize o Terraform.

    ```bash
    terraform init
    ```

1. Crie o plano do Terraform.

    ```bash
    terraform plan
    ```

1. Provisione os recursos definidos no arquivo de configuração `swap.tf`. (Confirme a ação inserindo `yes` no prompt.)

    ```bash
    terraform apply
    ```

1. Após o Terraform ter trocado os slots, retorne ao navegador. Atualize a página. 

O aplicativo Web no slot de preparo **slotAppServiceSlotOne** foi alternado pelo slot de produção e agora é renderizado em verde. 

![Os slots de implantação foram trocados](./media/provision-infrastructure-using-azure-deployment-slots/slots-swapped.png)

Para retornar à versão de produção original do aplicativo, reaplique o plano do Terraform criado a partir do arquivo de configuração `swap.tf`.

```bash
terraform apply
```

Depois que o aplicativo for alternado, você verá a configuração original.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Saiba mais sobre como usar o Terraform no Azure](/azure/terraform)