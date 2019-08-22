---
title: Implantar no Azure a partir da linha de comando com o .NET Core
description: Este artigo descreve como implantar um aplicativo ASP.NET Core a um Serviço de Aplicativo do Azure usando as ferramentas de linha de comando.
ms.date: 06/20/2017
author: CamSoper
ms.author: casoper
ms.topic: quickstart
ms.openlocfilehash: 1c2f9ae75d26238514b525c7730dbcbbf49820b5
ms.sourcegitcommit: 4eee7d9a484e35eb695248c011a63b27603d354b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69633112"
---
# <a name="deploy-to-azure-from-the-command-line-with-net-core"></a>Implantar no Azure a partir da linha de comando com o .NET Core

Este tutorial orientará você durante a criação e implantação de um aplicativo do Microsoft Azure usando o .NET Core.  Quando terminar, você terá um aplicativo de tarefas pendentes baseado na Web criado em ASP.NET MVC Core, hospedado como aplicativo Web do Azure e que usa o Azure Cosmos DB para armazenamento de dados.

## <a name="prerequisites"></a>Pré-requisitos

* Uma [assinatura do Microsoft Azure](https://azure.microsoft.com/free/)
* [.NET Core](https://www.microsoft.com/net/download/core) (opcional)
* [CLI do Azure 2.0 do Azure](/cli/azure/install-az-cli2) (opcional)
* Cliente de linha de comando [Git](https://www.git-scm.com/) (opcional)

O [Azure Cloud Shell](/azure/cloud-shell/) tem todos os pré-requisitos opcionais para este tutorial pré-instalados.  Você só precisa instalar os componentes opcionais acima se deseja executar o tutorial localmente.  Para iniciar rapidamente o Cloud Shell, clique no botão **Experimente** no canto superior direito de um dos blocos de código abaixo.

## <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

O Azure Cosmos DB é usado para o armazenamento de dados neste tutorial, portanto, você precisará criar uma conta.  Execute esse script localmente ou no Cloud Shell para criar uma conta da API do SQL do Azure Cosmos DB.

```azurecli-interactive
# Create the DotNetAzureTutorial resource group
az group create --name DotNetAzureTutorial --location EastUS

# Generate a unique name for the account
let randomNum=$RANDOM*$RANDOM
cosmosdbname=dotnettutorial$randomNum

# Create the Azure Cosmos DB account
az cosmosdb create --name $cosmosdbname --resource-group DotNetAzureTutorial

# Retrieve the endpoint and key (you'll need these later)
cosmosEndpoint=$(az cosmosdb show -n $cosmosdbname -g DotNetAzureTutorial --query [documentEndpoint] -o tsv)
cosmosAuthKey=$(az cosmosdb list-keys -n $cosmosdbname -g DotNetAzureTutorial --query [primaryMasterKey] -o tsv)

```

## <a name="download-and-configure-the-application"></a>Baixar e configurar o aplicativo

O aplicativo que você pretende implantar é um [aplicativo de tarefas pendentes simples](https://github.com/Azure-Samples/dotnet-cosmosdb-quickstart/) codificado usando ASP.NET MVC Core e as bibliotecas de cliente do Azure Cosmos DB.  Agora você vai obter o código para este tutorial e vai configurá-lo com suas informações do Azure Cosmos DB.

```azurecli-interactive
# Get the code from GitHub
git clone https://github.com/Azure-Samples/dotnet-cosmosdb-quickstart

# Change the working directory
cd dotnet-cosmosdb-quickstart

# Replace authKey and endpoint values in appsettings.json
sed -i "s|AUTHKEYVALUE|$cosmosAuthKey|g" appsettings.json
sed -i "s|ENDPOINTVALUE|$cosmosEndpoint|g" appsettings.json

# Now commit your changes to the local Git repository.
git commit -a -m "Modified settings"

```

> [!NOTE]
> Se você nunca executou `git commit` nesse ambiente antes, a definição da sua identidade poderá ser solicitada. Siga as instruções na tela e execute novamente o comando `git commit`.

Restaure os pacotes NuGet e compile o aplicativo.

```azurecli-interactive
dotnet restore
dotnet build
```

> [!TIP]
> Se você estiver usando as ferramentas em seu próprio computador, poderá testar o aplicativo executando `dotnet run` e navegando até o endereço `localhost` exibido.  No entanto, não é possível navegar até esse endereço no Cloud Shell.  

## <a name="configure-azure-app-service-and-deploy-the-web-app"></a>Configurar o Serviço de Aplicativo do Azure e implantar o aplicativo Web

Você baixou e compilou o aplicativo Web com êxito e está pronto para implantá-lo como um aplicativo Web do Azure.  Comece criando o recurso aplicativo Web.

```azurecli-interactive
# Generate a unique Web App name
let randomNum=$RANDOM*$RANDOM
webappname=todoApp$randomNum

# Create an App Service plan.
az appservice plan create --name $webappname --resource-group DotNetAzureTutorial --sku FREE

# Create the Web App
az webapp create --name $webappname --resource-group DotNetAzureTutorial --plan $webappname

```

Antes de implantá-lo, você precisa definir as credenciais de implantação no nível da conta.  Use o script abaixo incluindo seus próprios valores como nome de usuário e senha.

```azurecli-interactive
az webapp deployment user set --user-name <desired user name> --password <desired password>
```

Por fim, implante o aplicativo no Azure.  Será solicitada a senha que você criou acima.

```azurecli-interactive
# Get the Git deployment URL
giturl=$(az webapp deployment source config-local-git -n $webappname -g DotNetAzureTutorial --query [url] -o tsv)

# Add the URL as a Git remote repository
git remote add azure $giturl

# Push the local repository to the remote
git push azure master
```

O aplicativo será criado remotamente e implantado.  Teste o aplicativo navegando até `https://<web app name>.azurewebsites.net`.  Para exibir o endereço no console, use o seguinte:

```azurecli-interactive
az webapp show -n $webappname -g DotNetAzureTutorial --query defaultHostName -o tsv
```

Você pode adicionar novos itens à lista de tarefas pendentes clicando em **Criar Novo**.

![O aplicativo concluído](./media/dotnet-quickstart/todo.png)

## <a name="clean-up"></a>Limpar

Ao terminar de testar o aplicativo e inspecionar o código e os recursos, é possível excluir o aplicativo Web e a conta do Azure Cosmos DB por meio da exclusão do grupo de recursos.

```azurecli-interactive
az group delete -n DotNetAzureTutorial
```

## <a name="next-steps"></a>Próximas etapas

* [Usar o Azure Active Directory para autenticação em um aplicativo Web ASP.NET](/azure/active-directory/develop/active-directory-devquickstarts-webapp-dotnet)
* [Criar um aplicativo Web do Azure usando o Banco de Dados SQL do Azure](/azure/app-service-web/web-sites-dotnet-get-started)
* [Experimentar um aplicativo .NET de exemplo com o Armazenamento do Azure](/azure/storage/storage-samples-dotnet)


