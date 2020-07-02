---
title: Provisionar e implantar um aplicativo Web usando as bibliotecas do SDK do Azure
description: Use as bibliotecas de gerenciamento nas bibliotecas do SDK do Azure para Python para provisionar um aplicativo Web e, em seguida, implantar o código do aplicativo de um repositório do GitHub.
ms.date: 05/29/2020
ms.topic: conceptual
ms.openlocfilehash: f09775fbd5eda59e9e4a687d63acca052429b8e1
ms.sourcegitcommit: 5ebbc1c06f98e29a146764661efbf34957020fe8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2020
ms.locfileid: "84716074"
---
# <a name="example-use-the-azure-libraries-to-provision-and-deploy-a-web-app"></a>Exemplo: Usar as bibliotecas do Azure para provisionar e implantar um aplicativo Web

Este exemplo demonstra como usar as bibliotecas de gerenciamento do SDK do Azure em um script Python para provisionar um aplicativo Web no Serviço de Aplicativo do Azure e implantar o código do aplicativo de um repositório do GitHub. ([Comandos de CLI do Azure equivalentes](#for-reference-equivalent-azure-cli-commands) são fornecidos posteriormente neste artigo.)

Todos os comandos neste artigo funcionam da mesma forma no bash do Linux/Mac OS e nos shells de comando do Windows, a menos que haja uma observação.

## <a name="1-set-up-your-local-development-environment"></a>1: Configurar seu ambiente de desenvolvimento

Se ele ainda não foi configurado, siga todas as instruções em [Configurar o ambiente de desenvolvimento do Python local para o Azure](configure-local-development-environment.md).

Não se esqueça de criar uma entidade de serviço para o desenvolvimento local e de criar e ativar um ambiente virtual para este projeto.

## <a name="2-install-the-needed-azure-library-packages"></a>2: Instalar os pacotes de biblioteca do Azure necessários

Crie um arquivo chamado *requirements.txt* com o conteúdo abaixo:

```text
azure-mgmt-resource
azure-mgmt-web
azure-cli-core
```

Em um terminal ou prompt de comando com o ambiente virtual ativado, instale os requisitos:

```cmd
pip install -r requirements.txt
```

## <a name="3-fork-the-sample-repository"></a>3: Bifurcar a amostra de repositório

Acesse [https://github.com/Azure-Samples/python-docs-hello-world](https://github.com/Azure-Samples/python-docs-hello-world) e bifuque o repositório na sua conta do GitHub. Use uma bifurcação para garantir que você tenha permissões para implantar o repositório no Azure.

![Bifurcação de amostra de repositório no GitHub](media/azure-sdk-example-web-app/fork-github-repository.png)

Em seguida, crie uma variável de ambiente com nome `REPO_URL` na URL da sua bifurcação. O código de exemplo na próxima seção depende desta variável de ambiente:

# <a name="cmd"></a>[cmd](#tab/cmd)

```cmd
set REPO_URL=<url_of_your_fork>
```

# <a name="bash"></a>[Bash](#tab/bash)

```bash
REPO_URL=<url_of_your_fork>
```

---

## <a name="4-write-code-to-provision-and-deploy-a-web-app"></a>4: Escrever o código para provisionar e implantar um aplicativo Web

Crie um arquivo Python com o nome *provision_deploy_webapp.py* e o código a seguir. Os comentários explicam os detalhes:

```python
import random, os
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.web import WebSiteManagementClient

# Retrieve subscription ID from environment variable
subscription_id = os.environ["AZURE_SUBSCRIPTION_ID"]

# Constants we need in multiple places: the resource group name and the region
# in which we provision resources. You can change these values however you want.
RESOURCE_GROUP_NAME = 'PythonAzureExample-WebApp-rg'
LOCATION = "centralus"

# Step 1: Provision the resource group.
resource_client = get_client_from_cli_profile(ResourceManagementClient)

rg_result = resource_client.resource_groups.create_or_update(RESOURCE_GROUP_NAME,
    { "location": LOCATION })

print(f"Provisioned resource group {rg_result.name}")

# For details on the previous code, see Example: Provision a resource group
# at https://docs.microsoft.com/azure/developer/python/azure-sdk-example-resource-group


#Step 2: Provision the App Service plan, which defines the underlying VM for the web app.

# Names for the App Service plan and App Service. We use a random number with the
# latter to create a reasonably unique name. If you've already provisioned a
# web app and need to re-run the script, set the WEB_APP_NAME environment 
# variable to that name instead.
SERVICE_PLAN_NAME = 'PythonAzureExample-WebApp-plan'
WEB_APP_NAME = os.environ.get("WEB_APP_NAME", f"PythonAzureExample-WebApp-{random.randint(1,100000):05}")

# Obtain the client object
app_service_client = get_client_from_cli_profile(WebSiteManagementClient)

# Provision the plan; Linux is the default
poller = app_service_client.app_service_plans.create_or_update(RESOURCE_GROUP_NAME,
    SERVICE_PLAN_NAME,
    {
        "location": LOCATION,
        "reserved": True,
        "sku" : {"name" : "B1"}
    }
)

plan_result = poller.result()

print(f"Provisioned App Service plan {plan_result.name}")


# Step 3: With the plan in place, provision the web app itself, which is the process that can host
# whatever code we want to deploy to it.

poller = app_service_client.web_apps.create_or_update(RESOURCE_GROUP_NAME,
    WEB_APP_NAME,
    {
        "location": LOCATION,
        "server_farm_id": plan_result.id,
        "site_config": {
            "linux_fx_version": "python|3.8"
        }
    }
)

web_app_result = poller.result()

print(f"Provisioned web app {web_app_result.name} at {web_app_result.default_host_name}")

# Step 4: deploy code from a GitHub repository. For Python code, App Service on Linux runs
# the code inside a container that makes certain assumptions about the structure of the code.
# For more information, see How to configure Python apps,
# https://docs.microsoft.com/azure/app-service/containers/how-to-configure-python.
#
# The create_or_update_source_control method doesn't provision a web app. It only sets the
# source control configuration for the app. In this case we're simply pointing to
# a GitHub repository.
#
# You can call this method again to change the repo.

REPO_URL = 'https://github.com/kraigb/python-docs-hello-world'

poller = app_service_client.web_apps.create_or_update_source_control(RESOURCE_GROUP_NAME,
    WEB_APP_NAME,
    {
        "location": "GitHub",
        "repo_url": REPO_URL,
        "branch": "master"
    }
)

sc_result = poller.result()

print(f"Set source control on web app to {sc_result.branch} branch of {sc_result.repo_url}")
```

Esse código usa os métodos de autenticação baseados em CLI (`get_client_from_cli_profile`), pois demonstra ações que você pode efetuar diretamente com a CLI do Azure. Em ambos os casos, você usará a mesma identidade para autenticação.

Para colocar esse código em um script de produção, você deve usar `DefaultAzureCredential` (recomendado) ou um método baseado na entidade de serviço, como descrito em [Como autenticar aplicativos Python com os serviços do Azure](azure-sdk-authenticate.md).

### <a name="reference-links-for-classes-used-in-the-code"></a>Links de referência para classes usadas no código

- [ResourceManagementClient (azure.mgmt.resource)](/python/api/azure-mgmt-resource/azure.mgmt.resource.resourcemanagementclient?view=azure-python)
- [WebSiteManagementClient (azure.mgmt.web import)](/python/api/azure-mgmt-web/azure.mgmt.web.websitemanagementclient?view=azure-python)

## <a name="5-run-the-script"></a>5: Executar o script

```cmd
python provision_deploy_web_app.py
```

## <a name="6-verify-the-web-app-deployment"></a>6: Verificar a implantação do aplicativo Web

1. Para acessar o site da Web implantado, execute o seguinte comando:

    ```azurecli
    az webapp browse -n PythonAzureExample-WebApp-12345
    ```

    Substitua "PythonSDKExample-WebApp-12345" pelo nome específico do seu aplicativo Web.

    Você deve ver o "Hello World!" no navegador.

1. Acesse o [portal do Azure](https://portal.azure.com), selecione **Grupos de recursos**e verifique se "PythonSDKExample-WebApp-rg" está na lista. Em seguida, navegue até essa lista para verificar se os recursos esperados existem, ou seja, o plano do Serviço de Aplicativo e o Serviço de Aplicativo.

## <a name="7-clean-up-resources"></a>7: Limpar os recursos

```azurecli
az group delete -n PythonAzureExample-WebApp-rg
```

Execute esse comando se você não precisar manter os recursos provisionados neste exemplo e quiser evitar encargos contínuos na sua assinatura.

Você também pode usar o método [`ResourceManagementClient.resource_groups.delete`](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.resourcegroupsoperations?view=azure-python#delete-resource-group-name--custom-headers-none--raw-false--polling-true----operation-config-) para excluir um grupo de recursos do código.

### <a name="for-reference-equivalent-azure-cli-commands"></a>Para referência: comandos equivalentes da CLI do Azure

Os comandos a seguir da CLI do Azure executam as mesmas etapas de provisionamento do script Python:

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az group create -l centralus -n PythonAzureExample-WebApp-rg

az appservice plan create -n PythonAzureExample-WebApp-plan --is-linux --sku F1

az webapp create -g PythonAzureExample-WebApp-rg -n PythonAzureExample-WebApp-12345 ^
    --plan PythonAzureExample-WebApp-plan --runtime "python|3.8"

rem You can use --deployment-source-url with the first create command. It's shown here
rem to match the sequence of the Python code.

az webapp create -n PythonAzureExample-WebApp-12345 --plan PythonAzureExample-WebApp-plan ^
    --deployment-source-url https://github.com/<your_fork>/python-docs-hello-world

rem Replace <your_fork> with the specific URL of your forked repository.
```

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az group create -l centralus -n PythonAzureExample-WebApp-rg

az appservice plan create -n PythonAzureExample-WebApp-plan --is-linux --sku F1

az webapp create -g PythonAzureExample-WebApp-rg -n PythonAzureExample-WebApp-12345 \
    --plan PythonAzureExample-WebApp-plan --runtime "python|3.8"

# You can use --deployment-source-url with the first create command. It's shown here
# to match the sequence of the Python code.

az webapp create -n PythonAzureExample-WebApp-12345 --plan PythonAzureExample-WebApp-plan \
    --deployment-source-url https://github.com/<your_fork>/python-docs-hello-world

# Replace <your_fork> with the specific URL of your forked repository.
```

---

## <a name="see-also"></a>Confira também

- [Exemplo: Provisionar um grupo de recursos](azure-sdk-example-resource-group.md)
- [Exemplo: Provisionar o Armazenamento do Microsoft Azure](azure-sdk-example-storage.md)
- [Exemplo: Usar o Armazenamento do Microsoft Azure](azure-sdk-example-storage-use.md)
- [Exemplo: Provisionar e usar um banco de dados MySQL](azure-sdk-example-database.md)
- [Exemplo: Provisionar uma máquina virtual >>>](azure-sdk-example-virtual-machines.md)
