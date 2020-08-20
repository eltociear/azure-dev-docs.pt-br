---
title: Tutorial – Implantar o Serviço de Aplicativo do Azure com o Jenkins e a CLI do Azure
description: Saiba como usar a CLI do Azure para implantar um aplicativo Web do Java no Azure na pipeline do Jenkins
keywords: jenkins, azure, devops, serviço de aplicativo, cli
ms.topic: tutorial
ms.date: 08/08/2020
ms.custom: devx-track-jenkins, devx-track-azurecli
ms.openlocfilehash: 9c5f3b30507cda764dd29f0c133d4997dbc2bb53
ms.sourcegitcommit: 16ce1d00586dfa9c351b889ca7f469145a02fad6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88240948"
---
# <a name="tutorial-deploy-to-azure-app-service-with-jenkins-and-the-azure-cli"></a>Tutorial: Implantar o Serviço de Aplicativo do Azure com o Jenkins e a CLI do Azure

Para implantar um aplicativo Web Java no Azure, você pode usar a CLI do Azure em um [pipeline do Jenkins](https://jenkins.io/doc/book/pipeline/). Neste tutorial, você executará as seguintes tarefas:

> [!div class="checklist"]
> * Criar uma VM Jenkins
> * Configurar o Jenkins
> * Criar um aplicativo Web no Azure
> * Preparar um repositório GitHub
> * Criar pipeline do Jenkins
> * Executar o pipeline e verificar o aplicativo Web

## <a name="create-and-configure-jenkins-instance"></a>Criar e configurar uma instância do Jenkins

Se você ainda não tiver um mestre do Jenkins, [instale o Jenkins em uma VM do Linux](configure-on-linux-vm.md).

O plug-in Credencial do Azure permite que você armazene as credenciais de entidade de serviço do Microsoft Azure no Jenkins. Na versão 1.2, adicionamos suporte para que esse pipeline do Jenkins possa obter as credenciais do Azure. 

Verifique se você tem a versão 1.2 ou posterior:

* No painel do Jenkins, clique em **Gerenciar Jenkins -> Gerenciador de plug-ins ->** e pesquise pela **Credencial do Azure**. 
* Atualize o plug-in se a versão for anterior à 1.2.

O Java JDK e o Maven também são necessários no mestre do Jenkins. Para instalar, entre no Jenkins principal usando o SSH e execute os seguintes comandos:

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

## <a name="add-azure-service-principal-to-a-jenkins-credential"></a>Adicione uma entidade de serviço do Azure a uma credencial do Jenkins

Uma credencial do Azure é necessária para executar a CLI do Azure.

* No painel do Jenkins, clique em **Credenciais -> Sistema ->**. Clique em **Credenciais globais (irrestrito)**.
* Clique em **Adicionar credenciais** para adicionar uma [entidade de serviço do Microsoft Azure](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json), preenchendo a ID da assinatura, a ID do cliente, o segredo do cliente e o Ponto de Extremidade do Token OAuth 2.0. Forneça uma ID para uso na próxima etapa.

![Adicionar Credenciais](./media/deploy-to-azure-app-service-using-azure-cli/add-credentials.png)

## <a name="create-an-azure-app-service-for-deploying-the-java-web-app"></a>Criar um Serviço de Aplicativo do Azure para implantar o aplicativo Web Java

Crie um plano do Serviço de Aplicativo do Azure com o tipo de preço **GRÁTIS** usando a CLI de comando [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create). O plano do serviço de aplicativo define os recursos físicos usados para hospedar seus aplicativos. Todos os aplicativos atribuídos a um plano do serviço de aplicativo compartilham esses recursos, permitindo que você economize hospedando vários aplicativos. 

```azurecli-interactive
az appservice plan create \
    --name myAppServicePlan \ 
    --resource-group myResourceGroup \
    --sku FREE
```

Quando o plano estiver pronto, a CLI do Azure mostra uma saída semelhante ao exemplo a seguir:

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-an-azure-web-app"></a>Criar um aplicativo Web do Azure

 Use a CLI de comando [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) para criar uma definição de aplicativo Web no `myAppServicePlan` Plano do Serviço de Aplicativo. A definição de aplicativo Web fornece uma URL para acessar seu aplicativo e define várias opções para implantar seu código no Azure. 

```azurecli-interactive
az webapp create \
    --name <app_name> \ 
    --resource-group myResourceGroup \
    --plan myAppServicePlan
```

Substitua o espaço reservado `<app_name>` por seu próprio nome exclusivo do aplicativo. Esse nome exclusivo é parte do nome de domínio padrão para o aplicativo Web, portanto, o nome precisa ser exclusivo entre todos os aplicativos no Azure. Você poderá mapear uma entrada de nome de domínio personalizada para o aplicativo Web antes de expor para seus usuários.

Quando a definição do aplicativo Web tiver pronta, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir: 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
   ...
  < Output has been truncated for readability >
}
```

### <a name="configure-java"></a>Configurar o Java

Definir a configuração de runtime Java que seu aplicativo precisa com o comando [az appservice web config update](/cli/azure/webapp/config).

O comando a seguir configura o aplicativo Web para ser executado em um JDK do Java 8 e um [Apache Tomcat](https://tomcat.apache.org/) 8.0 recentes.

```azurecli
az webapp config set \ 
    --name <app_name> \
    --resource-group myResourceGroup \ 
    --java-version 1.8 \ 
    --java-container Tomcat \
    --java-container-version 8.0
```

## <a name="prepare-a-github-repository"></a>Preparar um repositório GitHub

1. Abra o repositório [Aplicativo Web Java simples para o Azure](https://github.com/azure-devops/javawebappsample). Para bifurcar o repositório para sua própria conta do GitHub, clique no botão **Bifurcação** no canto superior direito.

1. Na interface do usuário da Web do GitHub, abra o arquivo **Jenkinsfile**. Clique no ícone de lápis para editar esse arquivo para atualizar o grupo de recursos e o nome do seu aplicativo Web nas linhas 20 e 21, respectivamente.

    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<app_name>'
    ```
    
1. Altere a linha 23 para atualizar a ID de credencial na sua instância do Jenkins

    ```java
    withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
    ```
    
## <a name="create-jenkins-pipeline"></a>Criar pipeline do Jenkins

Abra o Jenkins em um navegador da Web, clique em **Novo Item**.

1. Insira um nome para o trabalho.
1. Selecione **Pipeline**. 
1. Selecione **OK**.
1. Selecione **Pipeline**.
1. Para **Definição**, selecione **Script de pipeline do SCM**.
1. Para **SCM**, selecione **Git**.
1. Insira a URL do GitHub para o repositório bifurcado: `https:\<your forked repo\>.git`
1. Selecione **Salvar**

## <a name="test-your-pipeline"></a>Testar o pipeline

1. Acesse o pipeline que você criou
1. Clique em **Compilar Agora**
1. Após a conclusão do build, selecione **Saída do Console** para ver os detalhes dele.

## <a name="verify-your-web-app"></a>Verifique seu aplicativo Web

Faça o seguinte para verificar se o arquivo WAR foi implantado com êxito no aplicativo Web. 

1. abra um navegador da Web:

1. Navegue para `http://&lt;app_name>.azurewebsites.net/api/calculator/ping`

1. Você deverá ver texto similar ao seguinte:

    ```output
    Welcome to Java Web App!!! This is updated!
    Today's date
    ```

1. Acesse http://&lt;app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (substitua &lt;x > e &lt;y > por qualquer número) para obter a soma de x e y

    ![Calculadora: adicionar](./media/deploy-to-azure-app-service-using-azure-cli/calculator-add.png)

## <a name="deploy-to-azure-web-app-on-linux"></a>Implantar no Aplicativo Web do Azure no Linux

Após usar a CLI do Azure no pipeline do Jenkins, altere o script para a implantação em um aplicativo Web do Azure no Linux. Os aplicativos Web no Linux dão suporte ao Docker. Assim sendo, você fornece um Dockerfile que empacota o aplicativo Web com runtime de serviço em uma imagem do Docker. O plug-in cria a imagem, envia-a por push para um registro do Docker e a implanta no aplicativo Web.

1. [Crie um aplicativo Web do Azure que seja executado no Linux](/azure/app-service/containers/quickstart-nodejs).

1. [Instale o Docker no Jenkins](https://docs.docker.com/engine/installation/linux/ubuntu/).

1. [Crie um registro de contêiner no portal do Azure](/azure/container-registry/container-registry-get-started-azure-cli).

1. No mesmo repositório [aplicativo Web Java simples para o Azure](https://github.com/azure-devops/javawebappsample) que você bifurcou, edite o arquivo **Jenkinsfile2** da seguinte maneira:

    1. Atualize para os nomes do grupo de recursos, do aplicativo Web e do ACR (substituindo os espaços reservados pelos respectivos valores).

        ```bash
        def webAppResourceGroup = '<myResourceGroup>'
        def webAppName = '<app_name>'
        def acrName = '<myRegistry>'
        ```

    1. Atualize `<azsrvprincipal\>` para sua ID de credencial

        ```bash
        withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
        ```

1. Crie um pipeline do Jenkins como fez durante a implantação no aplicativo Web do Azure no Windows usando `Jenkinsfile2`.

1. Execute seu novo trabalho.

1. Para a verificação na CLI do Azure, execute o seguinte comando:

    ```azurecli
    az acr repository list -n <myRegistry> -o json
    ```

    Você deverá ver resultados semelhantes ao seguinte:
    
    ```output
    [
    "calculator"
    ]
    ```
    
1. Navegue até `http://<app_name>.azurewebsites.net/api/calculator/ping` (substituindo o espaço reservado). Você deverá ver resultados semelhantes ao seguinte: 

    ```output
    Welcome to Java Web App!!! This is updated!
    Today's date
    ```

1. Navegue até `http://<app_name>.azurewebsites.net/api/calculator/add?x=<x>&y=<y>` (substituindo os espaços reservados). Os valores que você especificou para `x` e `y` são somados e exibidos.
    
## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Jenkins no Azure](/azure/jenkins)
