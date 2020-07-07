---
title: Início Rápido – Introdução ao Terraform usando o Windows
description: Nesse início rápido, você aprende a como instalar e configurar o Terraform para criar recursos do Azure.
keywords: azure devops terraform install configure windows init plan apply execution login rbac service principal automated script cli powershell
ms.topic: quickstart
ms.date: 06/12/2020
ms.openlocfilehash: d28a79af9a59551153f297cebfb0c51ed2e72838
ms.sourcegitcommit: 2d6c9687b39e33a6b5e980d9a375c9f8f1f2cab7
ms.contentlocale: pt-BR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84783798"
---
# <a name="quickstart-getting-started-with-terraform-using-windows"></a>Início Rápido: Introdução ao Terraform usando o Windows
 
[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="configure-your-environment"></a>Configurar seu ambiente

1. O PowerShell 7 (ou posterior) é a versão recomendada do PowerShell para uso com o Azure PowerShell em todas as plataformas, incluindo o Windows. Se você tiver o PowerShell instalado, poderá verificar a versão inserindo o seguinte em um prompt do PowerShell:

    ```powershell
    $PSVersionTable.PSVersion
    ```
    
1. Siga as instruções no artigo [Instalar o PowerShell no Windows](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-7) para instalar a versão mais recente do PowerShell.

1. Este artigo usa o [módulo Az do Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az). Siga as instruções no artigo [Instalar a CLI do Azure no Windows](/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

1. Abra uma instância da versão do PowerShell que você acabou de instalar.

## <a name="log-into-azure"></a>Fazer logon no Azure

Há várias opções que permitem que você faça logon em uma assinatura do Azure.

- [Fazer logon na sua conta Microsoft](#log-into-your-microsoft-account)
- [Fazer logon usando uma entidade de serviço do Azure](#log-into-azure-using-an-azure-service-principal)

### <a name="log-into-your-microsoft-account"></a>Entre em sua conta Microsoft

Chamar [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount) sem nenhum parâmetro exibe uma URL e um código. Navegue até a URL, insira o código e siga as instruções para entrar no Azure usando sua conta Microsoft. Quando você estiver conectado, volte ao portal.

```powershell
Connect-AzAccount
```

Observações:
- Após o logon bem-sucedido, `Connect-AzAccount` exibe uma assinatura do Azure padrão associada à conta Microsoft conectada. Para saber como alternar para outra assinatura do Azure, consulte a seção [Especificar a assinatura do Azure atual](#specify-the-current-azure-subscription).

### <a name="log-into-azure-using-an-azure-service-principal"></a>Fazer logon no Azure usando uma entidade de serviço do Azure

**Crie uma entidade de serviço do Azure**: para fazer logon em uma assinatura do Azure usando uma entidade de serviço, primeiro você precisará ter acesso a uma entidade de serviço. Se você já tiver uma entidade de serviço, ignore esta parte da seção.

Ferramentas automatizadas que implantam ou usam os serviços do Azure, como o Terraform, sempre têm permissões restritas. Em vez de os aplicativos entrarem como um usuário com privilégio total, o Azure oferece as entidades de serviço. Mas e se você não tiver uma entidade de serviço com a qual entrar? Nesse cenário, você pode entrar usando suas credenciais de usuário e, em seguida, criar uma entidade de serviço. Depois que a entidade de serviço for criada, você poderá usar suas informações para futuras tentativas de logon.

Há muitas opções ao [criar uma entidade de serviço com o PowerShell](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps). Para este artigo, criaremos uma entidade de serviço com uma função **Colaborador**. A função **Colaborador** (a função padrão) tem permissões completas de leitura e gravação em uma conta do Azure. Para obter mais informações sobre o Controle de Acesso Baseado em Função (RBAC) e funções, confira [RBAC: funções internas](/azure/active-directory/role-based-access-built-in-roles).

Chamar [New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADServicePrincipal) cria uma entidade de serviço para a assinatura especificada. Após a conclusão bem-sucedida, as informações da entidade de serviço, como os nomes de entidade de serviço e o nome de exibição, são exibidas. Quando você chama `New-AzADServicePrincipal` sem especificar credenciais de autenticação, uma senha é gerada automaticamente. No entanto, essa senha não é exibida, pois ela é retornada em um tipo `SecureString`. Portanto, você precisa chamar `New-AzADServicePrincipal` com os resultados indo para uma variável. Em seguida, você pode consultar a variável com relação à senha.

1. Insira o comando a seguir, substituindo `<subscription_id>` pela ID da conta de assinatura que deseja usar.

    ```powershell
    $sp = New-AzADServicePrincipal -Scope /subscriptions/<subscription_id>
    ```

1. Insira o seguinte para exibir os nomes da entidade de serviço:

    ```powershell
    $sp.ServicePrincipalNames
    ```

1. Chame `ConvertFrom-SecureString` para exibir a senha como texto:

    ```powershell
    $UnsecureSecret = ConvertFrom-SecureString -SecureString $sp.Secret -AsPlainText
    ```

Observações:
- Neste ponto, você conhece os nomes e a senha da entidade de serviço. Esses valores são necessários para fazer logon na assinatura usando sua entidade de serviço.
- Se for perdida, a senha não poderá ser recuperada. Por isso, você deve armazenar sua senha em um local seguro. Se você esquecer a senha, você precisará [redefinir as credenciais da entidade de serviço](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps#reset-credentials).

**Use uma entidade de serviço do Azure para fazer logon**: para fazer logon em uma assinatura do Azure usando uma entidade de serviço, chame `Connect-AzAccount` e passe um objeto do tipo [PsCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential). Há duas opções: interativo e script.

- **Padrão interativo**: você chama [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential) e insere as credenciais quando elas são solicitadas. A chamada a `Get-Credential` retorna um objeto `PsCredential`que você passa para `Connect-AzAccount`.

    1. Chame `Get-Credential` e insira manualmente um nome e uma senha da entidade de serviço:

        ```powershell
        $Credential = Get-Credential
        ```

    2. Chame `Connect-AzAccount`, passando o objeto `PsCredential`. (Substitua o espaço reservado `<azureSubscriptionTenantId>` pela ID de locatário da assinatura do Azure.)

        ```powershell
        Connect-AzAccount -Credential $Credential -Tenant <azureSubscriptionTenantId> -ServicePrincipal
        ```

- **Padrão do script**: você constrói um objeto `PsCredential` e o passa para `Connect-AzConnect`.

    1. Construa um `Get-Credential`. (Substitua os espaços reservados pelos valores adequados para sua assinatura e sua entidade de serviço do Azure.)

        ```powershell
        $spName = "<servicePrincipalName>"
        $spPassword = ConvertTo-SecureString "<servicePrincipalPassword>" -AsPlainText -Force
        $psCredential = New-Object System.Management.Automation.PSCredential($spName , $spPassword)
        ```

    1. Chame `Connect-AzAccount`, passando o objeto `PsCredential` construído:

        ```powershell
        Connect-AzAccount -Credential $psCredential -TenantId "<azureSubscriptionTenantId>"  -ServicePrincipal
        ```

## <a name="specify-the-current-azure-subscription"></a>Especifique a assinatura do Azure atual

Conforme explicado na seção anterior, duas das maneiras de entrar no Azure são os seguintes cenários:

- **Entrar usando uma conta Microsoft**: Uma conta Microsoft pode ser associada a várias assinaturas do Azure, como a assinatura padrão. A assinatura padrão é a que você usa se entrar e não mudar para outra.
- **Entre usando uma entidade de serviço Azure**: Uma entidade de serviço é específica para uma assinatura do Azure. Lembre-se de que as informações de assinatura são exibidas quando você entra.

As etapas a seguir abordam o primeiro cenário em que você realiza as seguintes tarefas:

- Exibir a assinatura atual do Azure
- Listar todas as assinaturas do Azure disponíveis para o conta Microsoft atual
- Alternar para outra assinatura do Azure

1. Para ver a assinatura atual do Azure, use [Get-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/get-azcontext).

    ```powershell
    Get-AzContext
    ```

1. Se você tiver acesso a várias assinaturas do Azure disponíveis, use `Get-AzContext -ListAvailable`:

    ```powershell
    Get-AzContext -ListAvailable | Select Name
    ```

1. Os nomes de contexto gerados automaticamente podem ser difíceis. Para tornar os nomes de contexto mais legíveis (e mais fáceis de usar como parâmetros), você pode renomear os contextos. A renomeação de um contexto é feita por meio de [Rename-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/rename-azcontext).

    ```powershell
    Rename-AzContext -SourceName <current_context_name> -TargetName <new_context_Name>
    ```

1. Para usar uma assinatura específica do Azure para a sessão atual do PowerShell, use [Select-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/select-azcontext).

    ```powershell
    Select-AzContext <context_name>
    ```

## <a name="configure-terraform"></a>Configurar o Terraform

1. [Baixe o Terraform](https://www.terraform.io/downloads.html).

1. Depois do download, extraia o executável para um diretório de sua escolha.

1. [Atualize o caminho global do seu sistema](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows) para o executável.

1. Verifique a configuração do caminho global com o comando `terraform`. Se o executável do Terraform for encontrado, uma lista de opções disponíveis do Terraform exibirá:

    ```powershell
    terraform
    ```

    Se o executável do Terraform for encontrado, ele listará a sintaxe e os comandos disponíveis:

    ```output
    Usage: terraform [-version] [-help] <command> [args]

    The available commands for execution are listed below.
    The most common, useful commands are shown first, followed by
    less common or more advanced commands. If you're just getting
    started with Terraform, stick with the common commands. For the
    other commands, please read the help and docs before usage.
    ...
    ```

## <a name="create-a-terraform-configuration-file"></a>Criar um arquivo de configuração Terraform

Nesta seção, você aprenderá a criar um arquivo de configuração do Terraform que cria um grupo de recursos do Azure.

1. Crie um diretório para manter os arquivos Terraform para esta demonstração.

1. Altere os diretórios para o diretório de demonstração.

1. Em seu editor favorito, crie um arquivo de configuração do Terraform chamado `QuickstartTerraformTest.tf`.

1. Cole o seguinte HCL no novo arquivo.

    ```hcl
    provider "azurerm" {
      # The "feature" block is required for AzureRM provider 2.x.
      # If you are using version 1.x, the "features" block is not allowed.
      version = "~>2.0"
      features {}
    }
    resource "azurerm_resource_group" "rg" {
            name = "QuickstartTerraformTest-rg"
            location = "eastus"
    }
    ```

    Observações:
    - O bloco do fornecedor especifica que o [provedor do Azure (azurerm)](https://www.terraform.io/docs/providers/azurerm/index.html) é usado.
    - Dentro do bloco do provedor azurerm, os atributos de versão e recursos são definidos. Como o comentário diz, seu uso é específico da versão. Para obter mais informações sobre como definir esses atributos para seu ambiente, consulte [v 2.0 do provedor AzureRM](https://www.terraform.io/docs/providers/azurerm/guides/2.0-upgrade-guide.html).
    - A única [declaração de recurso](https://www.terraform.io/docs/configuration/resources.html) é para um tipo de recurso de [azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html). Os dois argumentos necessários para azure_resource_group são nome e localização.

## <a name="create-and-apply-a-terraform-execution-plan"></a>Criar e aplicar um plano de execução Terraform

Depois que você criar os arquivos de configuração, esta seção explicará como criar um *plano de execução* e aplicá-lo à sua infraestrutura de nuvem.

1. Inicialize a implantação do Terraform com [terraform init](https://www.terraform.io/docs/commands/init.html). Esta etapa faz o download dos módulos do Azure necessários para criar um grupo de recursos do Azure.

    ```powershell
    terraform init
    ```
    
1. O Terraform permite que você visualize as ações a serem concluídas com [terraform plan](https://www.terraform.io/docs/commands/plan.html).

    ```powershell
    terraform plan
    ```

    **Observações:**
    - O comando `terraform plan` cria um plano de execução, mas não o executa. Em vez disso, ele determina quais ações são necessárias para criar a configuração especificada em seus arquivos de configuração.
    - O comando `terraform plan` permite que você verifique se o plano de execução corresponde às suas expectativas antes de fazer qualquer alteração nos recursos reais.
    - O parâmetro opcional `-out` permite que você especifique um arquivo de saída para o plano. Para obter mais informações sobre como usar o parâmetro `-out`, consulte a seção [Manter planos de execução para implantação posterior](#persist-an-execution-plan-for-later-deployment).

1. Aplique o plano de execução com [terraform apply](https://www.terraform.io/docs/commands/apply.html).

    ```powershell
    terraform apply
    ```
    
1. O Terraform mostra o que acontecerá se você aplicar o plano de execução e exige que você confirme executá-lo. Confirme o comando inserindo `yes` e pressionando a tecla **Enter**.

1. Depois de confirmar a execução da reprodução, teste se o grupo de recursos foi criado com êxito usando [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show).

    ```powershell
    Get-AzResourceGroup -Name QuickstartTerraformTest-rg
    ```

    Se for bem-sucedido, o comando exibirá várias propriedades do grupo de recursos recém-criado.

## <a name="persist-an-execution-plan-for-later-deployment"></a>Manter um plano de execução para implantação posterior

Na seção anterior, você viu como executar o [terraform plan](https://www.terraform.io/docs/commands/plan.html) para criar um plano de execução. Em seguida, você viu que usar o [terraform apply](https://www.terraform.io/docs/commands/apply.html) aplica esse plano. Esse padrão funciona bem quando as etapas são interativas e sequenciais.

Para cenários mais complexos, você pode manter o plano de execução para um arquivo. Mais tarde, ou até de um computador diferente, você poderá aplicar esse plano de execução.

Se você usa esse recurso, é recomendável que você leia o artigo [Executar Terraform em automação](https://learn.hashicorp.com/terraform/development/running-terraform-in-automation).

As etapas a seguir ilustram o padrão básico para usar esse recurso:

1. Execute [terraform init](https://www.terraform.io/docs/commands/init.html).

    ```powershell
    terraform init
    ```

1. Execute `terraform plan` com o parâmetro `-out`.

    ```powershell
    terraform plan -out QuickstartTerraformTest.tfplan
    ```

1. Execute `terraform apply`, especificando o nome do arquivo da etapa anterior.

    ```powershell
    terraform apply QuickstartTerraformTest.tfplan
    ```

Observações:
- Para habilitar o uso com automação, a execução de `terraform apply <filename>` não exige confirmação.
- Se você decidir usar esse recurso, leia a [seção do aviso de segurança](https://www.terraform.io/docs/commands/plan.html#security-warning).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, exclua os recursos criados neste artigo.

1. Execute o [terraform destroy](https://www.terraform.io/docs/commands/destroy.html), que reverterá o plano de execução atual.

    ```powershell
    terraform destroy
    ```

1. O Terraform mostra o que acontecerá se você reverter o plano de execução e exige que você confirme. Confirme o comando inserindo `yes` e pressionando a tecla **Enter**.

1. Depois de confirmar a execução da reprodução, a saída é semelhante ao exemplo a seguir, verifique se o grupo de recursos foi excluído usando [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show).

    ```powershell
    Get-AzResourceGroup -Name QuickstartTerraformTest-rg
    ```

    Observações:
    - Se ele for bem-sucedido, `Get-AzResourceGroup` exibirá uma mensagem informando que o grupo de recursos não existe.

1. Altere os diretórios para o diretório pai e remova o diretório de demonstração. O parâmetro `-r` remove o conteúdo do diretório antes de remover o diretório. O conteúdo do diretório inclui o arquivo de configuração que você criou anteriormente e os arquivos de estado do Terraform.

    ```bash
    cd .. && rm -r QuickstartTerraformTest
    ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar uma VM do Azure com o Terraform](create-linux-virtual-machine-with-infrastructure.md)