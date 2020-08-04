---
title: Início Rápido – Introdução ao Terraform usando o PowerShell
description: Nesse início rápido, você aprende a como instalar e configurar o Terraform para criar recursos do Azure.
keywords: azure devops terraform install configure windows init plan apply execution login rbac service principal automated script powershell
ms.topic: quickstart
ms.date: 07/27/2020
ms.openlocfilehash: 40663f23d79066354cb7a78318eba7a8998676c5
ms.sourcegitcommit: cf23d382eee2431a3958b1c87c897b270587bde0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87400654"
---
# <a name="quickstart-get-started-with-terraform-using-powershell"></a>Início Rápido: Introdução ao Terraform usando o PowerShell
 
[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

Este artigo descreve como começar a usar o [Terraform no Azure](https://www.terraform.io/docs/providers/azurerm/index.html) usando o PowerShell.

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="configure-your-environment"></a>Configurar seu ambiente

1. O último módulo do PowerShell que permite a interação com os recursos do Azure é chamado de [módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az). Ao usar o módulo Az do Azure PowerShell, o PowerShell 7 (ou posterior) é a versão recomendada em todas as plataformas. Se você tiver o PowerShell instalado, poderá verificar a versão inserindo o comando a seguir em um prompt do PowerShell.

    ```powershell
    $PSVersionTable.PSVersion
    ```

1. [Instalar o PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-7). Esta demonstração foi testada usando o PowerShell 7.0.2 no Windows 10.

1. Para o [Terraform se autenticar no Azure](https://www.terraform.io/docs/providers/azurerm/guides/azure_cli.html), você precisa [instalar a CLI do Azure](/cli/azure/install-azure-cli-windows?view=azure-cli-latest). Esta demonstração foi testada usando a CLI do Azure versão 2.9.1.

1. [Baixe o Terraform](https://www.terraform.io/downloads.html).

1. Depois do download, extraia o executável para um diretório de sua escolha.

1. [Atualize o caminho global do seu sistema](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows) para o executável.

1. Verifique a configuração do caminho global com o comando `terraform`.

    ```powershell
    terraform
    ```

    **Observações**:
    - Se o executável do Terraform for encontrado, ele listará a sintaxe e os comandos disponíveis.

## <a name="create-an-azure-service-principal"></a>Criar uma entidade de serviço do Azure

Ao usar o PowerShell e o Terraform, você deverá fazer logon usando uma entidade de serviço.

para fazer logon em uma assinatura do Azure usando uma entidade de serviço, primeiro você precisará ter acesso a uma entidade de serviço. Se você já tiver uma entidade de serviço, ignore esta seção.

Há muitas opções ao [criar uma entidade de serviço com o PowerShell](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps). Para este artigo, criaremos uma entidade de serviço com uma função **Colaborador**. A função **Colaborador** (a função padrão) tem permissões completas de leitura e gravação em uma conta do Azure. Para obter mais informações sobre o Controle de Acesso Baseado em Função (RBAC) e funções, confira [RBAC: funções internas](/azure/active-directory/role-based-access-built-in-roles).

Chamar [New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADServicePrincipal) cria uma entidade de serviço para a assinatura especificada. Após a conclusão bem-sucedida, as informações da entidade de serviço, como os nomes de entidade de serviço e o nome de exibição, são exibidas. Quando você chama `New-AzADServicePrincipal` sem especificar credenciais de autenticação, uma senha é gerada automaticamente. No entanto, essa senha não é exibida, pois ela é retornada em um tipo `SecureString`. Assim, você precisa chamar `New-AzADServicePrincipal` com os resultados indo para uma variável. Em seguida, você pode converter a variável em texto sem formatação para exibi-la.

1. Obtenha a ID da assinatura do Azure que você deseja usar. Se você não souber a ID da assinatura, poderá obter o valor no [portal do Azure](https://portal.azure.com/).

    1. Faça logon no [Portal do Azure](https://portal.azure.com/).
    1. Em **Serviços do Azure**, selecione **Assinaturas**.
    1. A listagem de tabelas de assinaturas contém uma coluna com a ID de cada assinatura.

1. Inicie o PowerShell.

1. Criar uma entidade de serviço usando [New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal). Substitua `<azure_subscription_id>` pela ID da assinatura do Azure que deseja usar.

    ```powershell
    $sp = New-AzADServicePrincipal -Scope /subscriptions/<azure_subscription_id>
    ```

1. Exiba os nomes da entidade de serviço.

    ```powershell
    $sp.ServicePrincipalNames
    ```

1. Exiba a senha gerada automaticamente como texto, [ConvertFrom-SecureString](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/convertfrom-securestring).

    ```powershell
    $UnsecureSecret = ConvertFrom-SecureString -SecureString $sp.Secret -AsPlainText
    ```

**Observações**:

- Os valores de senha e nomes da entidade de serviço são necessários para fazer logon na assinatura usando sua entidade de serviço.
- Se for perdida, a senha não poderá ser recuperada. Por isso, você deve armazenar sua senha em um local seguro. Se você esquecer a senha, você precisará [redefinir as credenciais da entidade de serviço](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps#reset-credentials).

## <a name="log-in-to-azure-using-a-service-principal"></a>Fazer logon no Azure usando uma entidade de serviço

Para fazer logon em uma assinatura do Azure usando uma entidade de serviço, chame [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount), especificando um objeto do tipo [PsCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential).

1. Inicie o PowerShell.

1. Obtenha um objeto [PsCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential) usando uma das técnicas a seguir.

    1. Chame [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential) e insira um nome da entidade de serviço e uma senha quando solicitado:

        ```powershell
        $psCredential = Get-Credential
        ```

    1. Construa um objeto `PsCredential` na memória. Substitua os espaços reservados pelos valores apropriados na entidade de serviço. Esse padrão é como você faria logon por meio de um script.

        ```powershell
        $spName = "<service_principle_name>"
        $spPassword = ConvertTo-SecureString "<service_principle_password>" -AsPlainText -Force
        $spCredential = New-Object System.Management.Automation.PSCredential($spName , $spPassword)
        ```

1. Chame `Connect-AzAccount`, passando o objeto `PsCredential`. Substitua o espaço reservado `<azure_subscription_tenant_id>` pela ID de locatário da assinatura do Azure.

    ```powershell
    Connect-AzAccount -Credential $spCredential -Tenant "<azure_subscription_tenant_id>" -ServicePrincipal
    ```

## <a name="create-a-terraform-configuration-file"></a>Criar um arquivo de configuração Terraform

Nesta seção, você codificará um arquivo de configuração do Terraform que cria um grupo de recursos do Azure.

1. Crie um diretório para manter os arquivos Terraform para esta demonstração.

    ```powershell
    mkdir QuickstartTerraformTest
    ```

1. Altere os diretórios para o diretório de demonstração.

    ```powershell
    cd QuickstartTerraformTest
    ```

1. Usando seu editor favorito, crie um arquivo de configuração do Terraform. Este artigo usa o [Visual Studio Code](https://code.visualstudio.com/Download).

    ```powershell
    code QuickstartTerraformTest.tf
    ```

1. Cole o código HCL a seguir no novo arquivo. Consulte as observações após a listagem de códigos para obter mais detalhes.

    ```hcl
    provider "azurerm" {
      # The "feature" block is required for AzureRM provider 2.x.
      # If you're using version 1.x, the "features" block isn't allowed.
      version = "~>2.0"
      features {}
    }

    resource "azurerm_resource_group" "rg" {
      name     = "QuickstartTerraformTest-rg"
      location = "eastus"
    }
    ```

    **Observações**:
    - O bloco do fornecedor especifica que o [provedor do Azure (azurerm)](https://www.terraform.io/docs/providers/azurerm/index.html) é usado.
    - No bloco do provedor `azurerm`, os atributos `version` e `features` são definidos. Como o comentário diz, seu uso é específico da versão. Para obter mais informações sobre como definir esses atributos, confira [v2.0 do provedor AzureRM](https://www.terraform.io/docs/providers/azurerm/guides/2.0-upgrade-guide.html).
    - A única [declaração de recurso](https://www.terraform.io/docs/configuration/resources.html) é para um tipo de recurso de [azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html). Os dois argumentos necessários para azure_resource_group são nome e localização.

## <a name="set-environment-variables"></a>Definir variáveis de ambiente

Para que o Terraform use a assinatura do Azure pretendida, defina variáveis de ambiente. Você pode definir as variáveis de ambiente no nível de sistema do Windows ou em uma sessão específica do PowerShell. Se você quiser definir as variáveis de ambiente para uma sessão específica, use o código a seguir. Substitua os espaços reservados pelos valores adequados do seu ambiente.

```powershell
$env:ARM_CLIENT_ID=<service_principle_app_id>
$env:ARM_SUBSCRIPTION_ID=<azure_subscription_id>
$env:ARM_TENANT_ID=<azure_subscription_tenant_id>
```

## <a name="create-and-apply-a-terraform-execution-plan"></a>Criar e aplicar um plano de execução Terraform

Nesta seção, você cria um *plano de execução* e o aplica à sua infraestrutura de nuvem.

1. Inicialize a implantação do Terraform com [terraform init](https://www.terraform.io/docs/commands/init.html). Esta etapa faz o download dos módulos do Azure necessários para criar um grupo de recursos do Azure.

    ```powershell
    terraform init
    ```

1. Execute [plano do Terraform](https://www.terraform.io/docs/commands/plan.html) para criar um plano de execução do seu arquivo de configuração do Terraform.

    ```powershell
    terraform plan -out QuickstartTerraformTest.tfplan
    ```

    **Observações:**
    - O comando `terraform plan` cria um plano de execução, mas não o executa. Em vez disso, ele determina quais ações são necessárias para criar a configuração especificada em seus arquivos de configuração. Esse padrão permite que você verifique se o plano de execução corresponde às suas expectativas antes de fazer qualquer alteração nos recursos reais.
    - O parâmetro opcional `-out` permite que você especifique um arquivo de saída para o plano. Usar o parâmetro `-out` garante que o plano que você examinou seja exatamente o que é aplicado.
    - Para ler mais sobre a persistência de planos de execução e segurança, confira a [seção de aviso de segurança](https://www.terraform.io/docs/commands/plan.html#security-warning).

1. Execute a [aplicação do Terraform](https://www.terraform.io/docs/commands/apply.html) para aplicar o plano de execução.

    ```powershell
    terraform apply QuickstartTerraformTest.tfplan
    ```

1. Depois que o plano de execução for aplicado, você poderá testar se o grupo de recursos foi criado com êxito usando [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/Get-AzResourceGroup).

    ```powershell
    Get-AzResourceGroup -Name QuickstartTerraformTest-rg
    ```

    Se for bem-sucedido, o comando exibirá várias propriedades do grupo de recursos recém-criado.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, exclua os recursos criados neste artigo.

1. Execute o [plano do Terraform](https://www.terraform.io/docs/commands/plan.html) para criar um plano de execução para destruir os recursos indicados no arquivo de configuração do Terraform.

    ```powershell
    terraform plan -destroy -out QuickstartTerraformTest.destroy.tfplan
    ```

    **Observações:**
    - O comando `terraform plan` cria um plano de execução, mas não o executa. Em vez disso, ele determina quais ações são necessárias para criar a configuração especificada em seus arquivos de configuração. Esse padrão permite que você verifique se o plano de execução corresponde às suas expectativas antes de fazer qualquer alteração nos recursos reais.
    - O parâmetro `-destroy` gera um plano para destruir os recursos.
    - O parâmetro opcional `-out` permite que você especifique um arquivo de saída para o plano. Usar o parâmetro `-out` garante que o plano que você examinou seja exatamente o que é aplicado.
    - Para ler mais sobre a persistência de planos de execução e segurança, confira a [seção de aviso de segurança](https://www.terraform.io/docs/commands/plan.html#security-warning).

1. Execute a [aplicação do Terraform](https://www.terraform.io/docs/commands/apply.html) para aplicar o plano de execução.

    ```powershell
    terraform apply QuickstartTerraformTest.destroy.tfplan
    ```

1. Verifique se o grupo de recursos foi excluído usando [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/Get-AzResourceGroup).

    ```powershell
    Get-AzResourceGroup -Name QuickstartTerraformTest-rg
    ```

    **Observações**:
    - Se ele for bem-sucedido, `Get-AzResourceGroup` exibirá uma mensagem informando que o grupo de recursos não existe.

1. Altere os diretórios para o diretório pai e remova o diretório de demonstração. O parâmetro `-r` remove o conteúdo do diretório antes de remover o diretório. O conteúdo do diretório inclui o arquivo de configuração que você criou anteriormente e os arquivos de estado do Terraform.

    ```powershell
    cd .. && rm -r QuickstartTerraformTest
    ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar uma VM do Azure com o Terraform](create-linux-virtual-machine-with-infrastructure.md)