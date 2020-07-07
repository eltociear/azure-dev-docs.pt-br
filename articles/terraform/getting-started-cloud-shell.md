---
title: Guia de Início Rápido – Introdução ao Terraform por meio do Azure Cloud Shell
description: Nesse início rápido, você aprende a como instalar e configurar o Terraform para criar recursos do Azure.
keywords: azure devops terraform install configure cloud shell init plan apply execution portal login rbac service principal automated script
ms.topic: quickstart
ms.date: 06/11/2020
ms.openlocfilehash: 4b0f6802673d886cecdc9523d99886c19fbad94a
ms.sourcegitcommit: 2d6c9687b39e33a6b5e980d9a375c9f8f1f2cab7
ms.contentlocale: pt-BR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84779658"
---
# <a name="quickstart-getting-started-with-terraform-using-azure-cloud-shell"></a>Início Rápido: Introdução ao Terraform por meio do Azure Cloud Shell
 
[!INCLUDE [terraform-intro.md](includes/terraform-intro.md)]

Este artigo descreve a introdução ao [Terraform no Azure](https://www.terraform.io/docs/providers/azurerm/index.html) no ambiente [Azure Cloud Shell](/azure/cloud-shell/overview).

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="configure-azure-cloud-shell"></a>Configurar o Azure Cloud Shell

1. Navegue até o [Portal do Azure](https://portal.azure.com).

1. Se você ainda não entrou, o portal do Azure exibe uma lista de contas Microsoft disponíveis. Selecione uma conta Microsoft associada a uma ou mais assinaturas ativas do Azure e insira suas credenciais para continuar.

1. Abra o Azure Cloud Shell.

    ![Accessar o Cloud Shell](media/install-configure/portal-cloud-shell.png)

1. Se você ainda não usou o Cloud Shell, defina as configurações de ambiente e armazenamento. Este artigo usa o ambiente de Bash.

## <a name="log-into-azure"></a>Fazer logon no Azure

O Cloud Shell é automaticamente autenticado sob a conta da Microsoft com a qual você entrou no portal do Azure.

Além disso, depois de conectado à sua conta Microsoft, você será automaticamente conectado à assinatura padrão do Azure dessa conta. Se a conta Microsoft atual estiver correta e você quiser alternar entre assinaturas, confira a seção [Especificar a assinatura atual do Azure](#specify-the-current-azure-subscription).

Se você tiver várias contas Microsoft com assinaturas do Azure, faça logon em uma dessas contas usando uma das seguintes opções:

- [Fazer logon na sua conta Microsoft](#log-into-your-microsoft-account)
- [Fazer logon usando uma entidade de serviço do Azure](#log-into-azure-using-an-azure-service-principal)

### <a name="log-into-your-microsoft-account"></a>Entre em sua conta Microsoft

A chamada a `az login` sem nenhum parâmetro exibe uma URL e um código. Navegue até a URL, insira o código e siga as instruções para entrar no Azure usando sua conta Microsoft. Quando você estiver conectado, volte ao portal.

```azurecli
az login
```

Observações:
- Após o logon bem-sucedido, `az login` exibe uma lista das assinaturas do Azure associadas à conta Microsoft conectada.
- Uma lista de propriedades é exibida para cada assinatura do Azure disponível. A propriedade `isDefault` identifica qual assinatura do Azure você está usando. Para saber como alternar para outra assinatura do Azure, consulte a seção [Especificar a assinatura do Azure atual](#specify-the-current-azure-subscription).

### <a name="log-into-azure-using-an-azure-service-principal"></a>Fazer logon no Azure usando uma entidade de serviço do Azure

**Crie uma entidade de serviço do Azure**: para fazer logon em uma assinatura do Azure usando uma entidade de serviço, primeiro você precisará ter acesso a uma entidade de serviço. Se você já tiver uma entidade de serviço, ignore esta parte da seção.

Ferramentas automatizadas que implantam ou usam os serviços do Azure, como o Terraform, sempre têm permissões restritas. Em vez de os aplicativos entrarem como um usuário com privilégio total, o Azure oferece as entidades de serviço. Mas e se você não tiver uma entidade de serviço com a qual entrar? Nesse cenário, você pode entrar usando suas credenciais de usuário e, em seguida, criar uma entidade de serviço. Depois que a entidade de serviço for criada, você poderá usar suas informações para futuras tentativas de logon.

Há muitas opções ao [criar uma entidade de serviço com a CLI do Azure](/cli/azure/create-an-azure-service-principal-azure-cli?). Para este artigo, usaremos [az ad sp create-for-rbac](/cli/azure/ad/sp?#az-ad-sp-create-for-rbac) para criar uma entidade de serviço com uma função de **Colaborador**. A função de **Colaborador** (a padrão) tem permissões completas de leitura e gravação em uma conta do Azure. Para obter mais informações sobre o Controle de Acesso Baseado em Função (RBAC) e funções, confira [RBAC: funções internas](/azure/active-directory/role-based-access-built-in-roles).

Insira o comando a seguir, substituindo `<subscription_id>` pela ID da conta de assinatura que deseja usar.
    
```azurecli
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscription_id>"
```

Observações:
- Após a conclusão bem-sucedida, `az ad sp create-for-rbac` exibe vários valores, incluindo a senha gerada automaticamente. Se for perdida, a senha não poderá ser recuperada. Por isso, você deve armazenar sua senha em um local seguro. Se você esquecer a senha, você precisará [redefinir as credenciais da entidade de serviço](/cli/azure/create-an-azure-service-principal-azure-cli#reset-credentials).
    
**Entre usando uma entidade de serviço Azure**: Na chamada a seguir a `az login`, substitua os espaços reservados por informações da entidade de serviço.

```azurecli
az login --service-principal -u <service_principal_name> -p "<service_principal_password>" --tenant "<service_principal_tenant>"
```

Observações:
- Após o logon bem-sucedido, `az login` exibe várias propriedades para a assinatura do Azure, como `id` e `name`.

## <a name="specify-the-current-azure-subscription"></a>Especifique a assinatura do Azure atual

Conforme explicado na seção anterior, duas das maneiras de entrar no Azure são os seguintes cenários:

- **Entrar usando uma conta Microsoft**: Uma conta Microsoft pode ser associada a várias assinaturas do Azure, como a assinatura padrão. A assinatura padrão é a que você usa se entrar e não mudar para outra.
- **Entre usando uma entidade de serviço Azure**: Uma entidade de serviço é específica para uma assinatura do Azure. Lembre-se de que as informações de assinatura são exibidas quando você entra.

As etapas a seguir abordam o primeiro cenário em que você realiza as seguintes tarefas:

- Exibir a assinatura atual do Azure
- Listar todas as assinaturas do Azure disponíveis para o conta Microsoft atual
- Alternar para outra assinatura do Azure

1. Para ver a assinatura atual do Azure, use [az account show](/cli/azure/account#az-account-show).

    ```azurecli
    az account show
    ```
    
1. Se você tiver acesso a várias assinaturas do Azure disponíveis, use [az account list](/cli/azure/account#az-account-list) para exibir uma lista de valores de ID de nome de assinatura:

    ```azurecli
    az account list --query "[].{name:name, subscriptionId:id}"
    ```

1. Para usar uma assinatura específica do Azure para a sessão atual do Cloud Shell, use [az account set](/cli/azure/account#az-account-set). Substitua o espaço reservado `<subscription_id>` com a ID (ou nome) da assinatura que deseja usar:

    ```azurecli
    az account set --subscription="<subscription_id>"
    ```

    Observações:
    - A chamada a `az account set` não exibe os resultados da alternância para a assinatura especificada do Azure. No entanto, você pode usar `az account show` para confirmar se a assinatura atual do Azure foi alterada.

## <a name="configure-terraform"></a>Configurar o Terraform

O Cloud Shell tem automaticamente a versão mais recente do Terraform instalada. Além disso, o Terraform usa automaticamente as informações da assinatura atual do Azure. Como resultado, não há nenhuma instalação ou configuração necessária.

## <a name="create-a-terraform-configuration-file"></a>Criar um arquivo de configuração Terraform

Nesta seção, você aprenderá a criar um arquivo de configuração do Terraform que cria um grupo de recursos do Azure.

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

1. Usando seu editor favorito, crie um arquivo de configuração do Terraform. Este artigo usa o [editor interno do Cloud Shell](/azure/cloud-shell/using-cloud-shell-editor).

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

    Observações:
    - O bloco `provider` especifica que o [provedor do Azure (`azurerm`)](https://www.terraform.io/docs/providers/azurerm/index.html) é usado.
    - No bloco do provedor `azurerm`, os atributos `version` e `features` são definidos. Como o comentário diz, seu uso é específico da versão. Para obter mais informações sobre como definir esses atributos para seu ambiente, consulte [v 2.0 do provedor AzureRM](https://www.terraform.io/docs/providers/azurerm/guides/2.0-upgrade-guide.html).
    - A única [declaração de recurso](https://www.terraform.io/docs/configuration/resources.html) é para um tipo de recurso de [azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html). Os dois argumentos necessários para `azure_resource_group` são `name` e `location`.

1. Salve o arquivo ( **&lt;Ctrl>S**).

1. Saia do editor ( **&lt;CTRL> Q**).

## <a name="create-and-apply-a-terraform-execution-plan"></a>Criar e aplicar um plano de execução Terraform

Depois que você criar os arquivos de configuração, esta seção explicará como criar um *plano de execução* e aplicá-lo à sua infraestrutura de nuvem.

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

1. Depois de confirmar a execução da reprodução, teste se o grupo de recursos foi criado com êxito usando [az group show](/cli/azure/group?#az-group-show).

    ```azurecli
    az group show -n "QuickstartTerraformTest-rg"
    ```

    Se ele for bem-sucedido, `az group show` exibirá várias propriedades do grupo de recursos recém-criado.

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

Observações:
- Para habilitar o uso com automação, a execução de `terraform apply <filename>` não exige confirmação.
- Se você decidir usar esse recurso, leia a [seção do aviso de segurança](https://www.terraform.io/docs/commands/plan.html#security-warning).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, exclua os recursos criados neste artigo.

1. Execute o [terraform destroy](https://www.terraform.io/docs/commands/destroy.html), que reverterá o plano de execução atual.

    ```bash
    terraform destroy
    ```

1. O Terraform mostra o que acontecerá se você reverter o plano de execução e exige que você confirme. Confirme sua escolha inserindo `yes` e selecionando a tecla **ENTER**.

1. Depois de confirmar a execução da reprodução, a saída é semelhante ao exemplo a seguir, verifique se o grupo de recursos foi excluído usando [az group show](/cli/azure/group?#az-group-show).

    ```azurecli
    az group show -n "QuickstartTerraformTest-rg"
    ```

    Observações:
    - Se ele for bem-sucedido, `az group show` exibirá uma mensagem informando que o grupo de recursos não existe.

1. Altere os diretórios para o diretório pai e remova o diretório de demonstração. O parâmetro `-r` remove o conteúdo do diretório antes de remover o diretório. O conteúdo do diretório inclui o arquivo de configuração que você criou anteriormente e os arquivos de estado do Terraform.

    ```bash
    cd .. && rm -r QuickstartTerraformTest
    ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar uma VM do Azure com o Terraform](create-linux-virtual-machine-with-infrastructure.md)