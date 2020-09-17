---
title: Conectar o GitHub e o Azure
description: Recursos para se conectar ao GitHub por meio do Azure e de outros serviços
author: N-Usha
ms.author: ushan
ms.topic: reference
ms.service: azure
ms.date: 08/31/2020
ms.custom: github-actions-azure
ms.openlocfilehash: d7c791aec426e139592e8a32e7779b2f02832d5f
ms.sourcegitcommit: 5205d15c697bbfd4ecb3f45b5de093f709d11979
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90026407"
---
# <a name="use-github-actions-to-connect-to-azure"></a>Usar o GitHub Actions para conectar-se ao Azure

Saiba como usar o [logon do Azure](https://github.com/Azure/login) com o [Azure PowerShell](https://github.com/Azure/PowerShell) ou a [CLI do Azure](https://github.com/Azure/CLI) para interagir com seus recursos do Azure.

Para usar o Azure PowerShell ou a CLI do Azure, primeiro você precisa fazer logon com o [logon do Azure](https://github.com/marketplace/actions/azure-login). A ação de logon do Azure conecta sua assinatura do Azure ao GitHub usando uma entidade de serviço.

Após ter configurado a ação de logon, você pode usar a CLI do Azure ou o Azure PowerShell.  
A CLI do Azure configura o ambiente do executor de ação do GitHub para a CLI do Azure. O Azure PowerShell configura o ambiente do executor de ação do GitHub com o módulo do Azure PowerShell.


## <a name="create-a-service-principal-and-add-it-to-github-secret"></a>Criar uma entidade de serviço e adicioná-la ao segredo do GitHub

Para usar o [logon do Azure](https://github.com/marketplace/actions/azure-login), primeiro você precisa adicionar sua entidade de serviço do Azure como um segredo ao repositório GitHub.

Neste exemplo, você criará um segredo chamado `AZURE_CREDENTIALS` que pode ser usado para fazer a autenticação com o Azure.  

1. Se você não tiver um aplicativo, registre um [novo aplicativo do Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#register-an-application-with-azure-ad-and-create-a-service-principal&preserve-view=true) para usar com a entidade de serviço.

    ```azurecli-interactive
        appName="myApp"

        az ad app create \
        --display-name $appName \
        --homepage "http://localhost/$appName" \
        --identifier-uris http://localhost/$appName
    ```

1. [Crie uma entidade de serviço](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest) no portal do Azure para seu aplicativo. 

    ```azurecli-interactive
        az ad sp create-for-rbac --name "myApp" --role contributor \
                                    --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                                    --sdk-auth
    ```

1. Copie o objeto JSON da entidade de serviço.

    ```json
    {
        "clientId": "<GUID>",
        "clientSecret": "<GUID>",
        "subscriptionId": "<GUID>",
        "tenantId": "<GUID>",
        (...)
    }
    ```

1. Abra o repositório do GitHub e vá para **Configurações**.

    :::image type="content" source="media/github-repo-settings.png" alt-text="Selecionar Configurações na navegação":::

1. Selecione **Segredos** e escolha **Novo Segredo**.

    :::image type="content" source="media/select-secrets.png" alt-text="Escolher para adicionar um segredo":::

1. Cole o objeto JSON da entidade de serviço com o nome `AZURE_CREDENTIALS`. 

    :::image type="content" source="media/azure-secret-add.png" alt-text="Adicionar um segredo no GitHub":::

1. Salve selecionando **Adicionar segredo**.

## <a name="use-the-azure-login-action"></a>Usar a ação de logon do Azure

Use o segredo da entidade de serviço com a [ação de Logon do Azure](https://github.com/Azure/login) para autenticar no Azure.

Neste fluxo de trabalho, você faz a autenticação com `secrets.AZURE_CREDENTIALS` e executa uma ação da CLI do Azure.

Quando tiver um logon do Azure em funcionamento, você poderá usar as ações do Azure PowerShell ou da CLI do Azure. Você também pode usar outras ações do Azure, como [implantar aplicativo Web do Azure](https://github.com/Azure/webapps-deploy) e [funções do Azure](https://github.com/Azure/functions-action).

```yaml
on: [push]

name: AzureLoginSample

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Log in with Azure
        uses: azure/login@v1
        with:
          creds: '${{ secrets.AZURE_CREDENTIALS }}'
```

## <a name="use-the-azure-powershell-action"></a>Usar a ação do Azure PowerShell

Neste exemplo, você faz logon com a [ação de logon do Azure](https://github.com/Azure/login) e, em seguida, recupera um grupo de recursos com a [ação da CLI do Azure](https://github.com/azure/powershell).

```yaml
on: [push]

name: AzureLoginSample

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Log in with Azure
        uses: azure/login@v1
        with:
          creds: '${{ secrets.AZURE_CREDENTIALS }}'
          enable-AzPSSession: true
      - name: Azure PowerShell Action
        uses: Azure/powershell@v1
        with:
          inlineScript: Get-AzVM -ResourceGroupName "< YOUR RESOURCE GROUP >"
          azPSVersion: 3.1.0
```

## <a name="use-the-azure-cli-action"></a>Usar a ação da CLI do Azure

Neste exemplo, você faz logon com a [ação de Logon do Azure](https://github.com/Azure/login) e, em seguida, recupera um grupo de recursos com a [ação da CLI do Azure](https://github.com/Azure/CLI).


```yaml
on: [push]

name: AzureLoginSample

jobs:
build-and-deploy:
    runs-on: ubuntu-latest
    steps:

    - name: Log in with Azure
        uses: azure/login@v1
        with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Azure CLI script
        uses: azure/CLI@v1
        with:
        azcliversion: 2.0.72
        inlineScript: |
            az account show
            az storage -h
```

## <a name="connect-with-other-azure-services"></a>Conectar-se com outros serviços do Azure

Os artigos a seguir fornecem detalhes sobre como se conectar ao GitHub por meio do Azure e de outros serviços.  

### <a name="azure-active-directory"></a>Azure Active Directory 

- [Entrar no GitHub Enterprise com o Azure AD (logon único)](https://docs.microsoft.com/azure/active-directory/saas-apps/github-tutorial)   

### <a name="power-bi"></a>Power BI

- [Conectar o Power BI com o GitHub](https://docs.microsoft.com/power-bi/service-connect-to-github)   

### <a name="connectors"></a>Conectores

- [Conector GitHub para os Aplicativos Lógicos do Azure, o Power Automate e o Power Apps](https://docs.microsoft.com/connectors/github/)   

### <a name="azure-databricks"></a>Azure Databricks

- [Usar o GitHub como controle de versão para notebooks](https://docs.microsoft.com/azure/databricks/notebooks/github-version-control) 

> [!div class="nextstepaction"]
> [Implantar aplicativos do GitHub no Azure](deploy-to-azure.md)
