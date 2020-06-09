---
title: Início Rápido – Introdução ao Terraform – Azure Cloud Shell
description: Nesse início rápido, você aprende a como instalar e configurar o Terraform para criar recursos do Azure.
keywords: azure devops terraform install configure cloud shell init plan apply execution portal login rbac service principal automated script
ms.topic: quickstart
ms.date: 06/01/2020
ms.openlocfilehash: 184d2720e3e2259a6c909d0775ffee20c0f30419
ms.sourcegitcommit: db56786f046a3bde1bd9b0169b4f62f0c1970899
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84329904"
---
# <a name="quickstart-getting-started-with-terraform---azure-cloud-shell"></a>Início Rápido: Introdução ao Terraform – Azure Cloud Shell
 
[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

Este artigo descreve a introdução ao Terraform do ambiente do [Azure Cloud Shell](/azure/cloud-shell/overview).

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="configure-your-environment"></a>Configurar seu ambiente

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="open-cloud-shell"></a>Abrir Cloud Shell

1. Navegue até o [Portal do Azure](https://portal.azure.com).

1. Se você ainda não entrou, o portal do Azure exibe uma lista de contas Microsoft disponíveis. Selecione uma conta Microsoft associada a uma ou mais assinaturas ativas do Azure e insira suas credenciais para continuar.

1. Abra o Azure Cloud Shell.

    ![Accessar o Cloud Shell](media/install-configure/portal-cloud-shell.png)

1. Se você ainda não usou o Cloud Shell, defina as configurações de ambiente e armazenamento. Este artigo usa o ambiente de Bash.

## <a name="log-into-your-microsoft-account"></a>Entre em sua conta Microsoft

O Cloud Shell é automaticamente autenticado sob a conta da Microsoft com a qual você entrou no portal do Azure. Entretanto, se você tiver várias contas Microsoft com assinaturas do Azure, você poderá entrar em uma dessas contas usando [az login](/cli/azure/reference-index?view=azure-cli-latest#az-login). Aqui estão dois exemplos de como usar o `az login` comando:

De acordo com o seu cenário, selecione um dos seguintes caminhos:
    
- **Você quer entrar como um usuário**: Executar o comando `az login` sem quaisquer parâmetros exibe uma URL e um código. Navegue até a URL, insira o código e siga as instruções para entrar no Azure usando sua conta Microsoft. Depois que o comando te fizer entrar, retorne ao portal.

    ```azurecli-interactive
    az login
    ```

    **Observações**:
    - Após entrar com sucesso, o comando `az login` exibe uma lista das assinaturas do Azure associadas à conta Microsoft conectada.
    - Uma lista de propriedades é exibida para cada assinatura do Azure disponível. A propriedade `isDefault` identifica qual assinatura do Azure você está usando. Para saber como alternar para outra assinatura do Azure, consulte a seção [Especificar a assinatura do Azure atual](#specify-the-current-azure-subscription).

- **Você deseja usar uma entidade de serviço, mas ainda não tem uma**: Ferramentas automatizadas que implantam ou usam os serviços do Azure, como o Terraform, sempre têm permissões restritas. Em vez de os aplicativos entrarem como um usuário com privilégio total, o Azure oferece as entidades de serviço. Mas e se você não tiver uma entidade de serviço com a qual entrar? Nesse cenário, você pode entrar usando suas credenciais de usuário e, em seguida, criar uma entidade de serviço. Depois que a entidade de serviço for criada, você poderá usar suas informações para futuras tentativas de logon.

    Há muitas opções ao [criar uma entidade de serviço](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Para este artigo, criaremos uma entidade de serviço com uma função de **Colaborador** (a função padrão). A função de **Colaborador** tem permissões completas para ler e gravar em uma conta do Azure. Para obter mais informações sobre o Controle de Acesso Baseado em Função (RBAC) e funções, confira [RBAC: funções internas](/azure/active-directory/role-based-access-built-in-roles). 
    
    Usando o comando [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac), substitua `<subscription_id>` pela ID da conta de assinatura que você deseja usar.
    
    ```azurecli-interactive
    az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscription_id>"
    ```

    **Observações**:
    - Após a conclusão bem-sucedida, o comando `az ad sp create-for-rbac` exibe vários valores, incluindo a senha gerada automaticamente. Se for perdida, a senha não poderá ser recuperada. Por isso, você deve armazenar sua senha em um local seguro. Se você esquecer a senha, você precisará [redefinir as credenciais da entidade de serviço](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#reset-credentials).
    
- **Entre usando uma entidade de serviço Azure**: Substitua os espaços reservados no seguinte `az login` comando por informações de sua entidade de serviço.

    ```azurecli-interactive
    az login --service-principal -u <service_principal_name> -p "<service_principal_password>" --tenant "<service_principal_tenant>"
    ```

    **Observações**:
    - Após entrar com sucesso, o comando `az login` exibe várias propriedades para a assinatura do Azure, como `id` e `name`.

## <a name="specify-the-current-azure-subscription"></a>Especifique a assinatura do Azure atual

Conforme explicado na seção anterior, duas das maneiras de entrar no Azure são os seguintes cenários:

- **Entrar usando uma conta Microsoft**: Uma conta Microsoft pode ser associada a várias assinaturas do Azure, como a assinatura padrão. A assinatura padrão é a que você usa se entrar e não mudar para outra.
- **Entre usando uma entidade de serviço Azure**: Uma entidade de serviço é específica para uma assinatura do Azure. Lembre-se de que as informações de assinatura são exibidas quando você entra.

As etapas a seguir abordam o primeiro cenário em que você realiza as seguintes tarefas:

- Verificar a assinatura do Azure atual
- Listar todas as assinaturas do Azure disponíveis para o conta Microsoft atual
- Alternar para outra assinatura do Azure

1. Para verificar a assinatura atual do Azure, use o comando [az account show](/cli/azure/account#az-account-show).

    ```azurecli-interactive
    az account show
    ```
    
1. Se você tiver acesso a várias assinaturas do Azure disponíveis, use [az account list](/cli/azure/account#az-account-list) para exibir uma lista de valores de ID de nome de assinatura:

    ```azurecli-interactive
    az account list --query "[].{name:name, subscriptionId:id}"
    ```

1. Para usar uma assinatura específica do Azure para a sessão de Cloud Shell atual, use o comando [az account set](/cli/azure/account#az-account-set). Substitua o espaço reservado `<subscription_id>` com a ID (ou nome) da assinatura que deseja usar:

    ```azurecli-interactive
    az account set --subscription="<subscription_id>"
    ```

    **Observações**:
    - O comando `az account set` não exibe os resultados de mudança para a assinatura do Azure especificada. No entanto, você pode usar o comando `az account show` para confirmar que a assinatura atual do Azure foi alterada.

## <a name="create-a-terraform-configuration-file"></a>Criar um arquivo de configuração Terraform

Nesta seção, você usa o [editor de Code Shell](/azure/cloud-shell/using-cloud-shell-editor) para definir um arquivo de configuração do Terraform.

1. Altere os diretórios para o compartilhamento de arquivos montado em que seu trabalho no Cloud Shell é mantido. Para obter mais informações sobre como o Cloud Shell mantém seus arquivos, consulte [Conectar seu armazenamento de Arquivos do Microsoft Azure](/azure/cloud-shell/overview#connect-your-microsoft-azure-files-storage)
    
    ```bash
    cd clouddrive
    ```

1. Crie um diretório para manter os arquivos Terraform para esta demonstração.

    ```bash
    mkdir QuickstartTerraformTest
    ```

1. Altere os diretórios para o diretório de demonstração.

    ```bash
    cd QuickstartTerraformTest
    ```

1. Usando seu editor favorito, crie um arquivo de configuração do Terraform. Este artigo usa o editor de Cloud Shell interno.

    ```bash
    code QuickstartTerraformTest.tf
    ```
 
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

    **Observações**:
    - O bloco `provider` especifica que o [provedor do Azure (`azurerm`)](https://www.terraform.io/docs/providers/azurerm/index.html) é usado.
    - No bloco do provedor `azurerm`, os atributos `version` e `features` são definidos. Como o comentário diz, seu uso é específico da versão. Para obter mais informações sobre como definir esses atributos para seu ambiente, consulte [v 2.0 do provedor AzureRM](https://www.terraform.io/docs/providers/azurerm/guides/2.0-upgrade-guide.html).
    - A única [declaração de recurso](https://www.terraform.io/docs/configuration/resources.html) é para um tipo de recurso de [azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html). Os dois argumentos necessários para `azure_resource_group` são `name` e `location`.

1. Salve o arquivo ( **&lt;Ctrl>S**).

1. Saia do editor ( **&lt;CTRL> Q**).

## <a name="create-and-apply-a-terraform-execution-plan"></a>Criar e aplicar um plano de execução Terraform

O Cloud Shell tem automaticamente a versão mais recente do Terraform instalada. Além disso, o Terraform usa automaticamente as informações da assinatura atual do Azure. Como resultado, não há nenhuma instalação ou configuração necessária. Depois de criar seus arquivos de configuração, você precisa executar apenas alguns comandos Terraform para criar um plano de execução. Depois de criar o plano de execução, você pode verificá-lo e implantá-lo.

1. Inicialize a implantação do Terraform com [terraform init](https://www.terraform.io/docs/commands/init.html). Esta etapa faz o download dos módulos do Azure necessários para criar um grupo de recursos do Azure.

    ```bash
    terraform init
    ```
    
1. O Terraform permite que você visualize as ações a serem concluídas com [terraform plan](https://www.terraform.io/docs/commands/plan.html).

    ```bash
    terraform plan
    ```

    **Observações:**
    - O comando `terraform plan` cria um plano de execução, mas não o executa. Em vez disso, ele determina quais ações são necessárias para criar a configuração especificada em seus arquivos de configuração.
    - O comando `terraform plan` permite que você verifique se o plano de execução corresponde às suas expectativas antes de fazer qualquer alteração nos recursos reais.
    - O parâmetro opcional `-out` permite que você especifique um arquivo de saída para o plano. Para obter mais informações sobre como usar o parâmetro `-out`, consulte a seção [Manter planos de execução para implantação posterior](#persist-an-execution-plan-for-later-deployment).

1. Aplique o plano de execução com [terraform apply](https://www.terraform.io/docs/commands/apply.html).

    ```bash
    terraform apply
    ```
    
1. O Terraform mostra o que acontecerá se você aplicar o plano de execução e exige que você confirme executá-lo. Confirme o comando inserindo `yes` e pressionando a tecla **Enter**.

1. Depois de confirmar a execução da reprodução, teste se o grupo de recursos foi criado com êxito usando [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show).

    ```azurecli-interactive
    az group show -n "QuickstartTerraformTest-rg"
    ```

    Se for bem-sucedido, o comando exibirá várias propriedades do grupo de recursos recém-criado.

## <a name="persist-an-execution-plan-for-later-deployment"></a>Manter um plano de execução para implantação posterior

Na seção anterior, você viu como executar o [terraform plan](https://www.terraform.io/docs/commands/plan.html) para criar um plano de execução. Em seguida, você viu que usar o [terraform apply](https://www.terraform.io/docs/commands/apply.html) aplica esse plano. Esse padrão funciona bem quando as etapas são interativas e sequenciais.

Para cenários mais complexos, você pode manter o plano de execução para um arquivo. Mais tarde, ou até de um computador diferente, você poderá aplicar esse plano de execução.

Se você usa esse recurso, é recomendável que você leia o artigo [Executar Terraform em automação](https://learn.hashicorp.com/terraform/development/running-terraform-in-automation).

As etapas a seguir ilustram o padrão básico para usar esse recurso:

1. Execute [terraform init](https://www.terraform.io/docs/commands/init.html).

    ```bash
    terraform init
    ```

1. Execute `terraform plan` com o parâmetro `-out`.

    ```bash
    terraform plan -out QuickstartTerraformTest.tfplan
    ```

1. Execute `terraform apply`, especificando o nome do arquivo da etapa anterior.

    ```bash
    terraform apply QuickstartTerraformTest.tfplan
    ```

**Observações**:
- Para habilitar o uso com automação, a execução de `terraform apply <filename>` não exige confirmação.
- Se você decidir usar esse recurso, leia a [seção do aviso de segurança](https://www.terraform.io/docs/commands/plan.html#security-warning).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, exclua os recursos criados neste artigo.

1. Execute o [terraform destroy](https://www.terraform.io/docs/commands/destroy.html), que reverterá o plano de execução atual.

    ```bash
    terraform destroy
    ```

1. O Terraform mostra o que acontecerá se você reverter o plano de execução e exige que você confirme. Confirme o comando inserindo `yes` e pressionando a tecla **Enter**.

1. Depois de confirmar a execução da reprodução, a saída é semelhante ao exemplo a seguir, verifique se o grupo de recursos foi excluído usando [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show).

    ```azurecli-interactive
    az group show -n "QuickstartTerraformTest-rg"
    ```

    **Observações**:
    - Se for bem-sucedido, o comando `az group show` exibirá o fato de que o grupo de recursos não existe.

1. Altere os diretórios para o diretório pai e remova o diretório de demonstração. O parâmetro `-r` remove o conteúdo do diretório antes de remover o diretório. O conteúdo do diretório inclui o arquivo de configuração que você criou anteriormente e os arquivos de estado do Terraform.

    ```bash
    cd .. && rm -r QuickstartTerraformTest
    ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar uma VM do Azure com o Terraform](create-linux-virtual-machine-with-infrastructure.md)