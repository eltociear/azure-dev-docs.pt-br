---
title: Implantar aplicativos no Azure Spring Cloud usando o Jenkins e a CLI do Azure
description: Saiba como usar a CLI do Azure em um pipeline de implantação e integração contínua para implantar microsserviços no serviço Azure Spring Cloud
keywords: jenkins, azure, devops, azure spring cloud, cli do azure
ms.topic: tutorial
ms.date: 08/10/2020
ms.custom: devx-track-jenkins,devx-track-azurecli
ms.openlocfilehash: 707af403bf789ccd85e52f7bb5880389f61e8f15
ms.sourcegitcommit: 2f832baf90c208a8a69e66badef5f126d23bbaaf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88725160"
---
# <a name="tutorial-deploy-apps-to-azure-spring-cloud-using-jenkins-and-the-azure-cli"></a>Tutorial: Implantar aplicativos no Azure Spring Cloud usando o Jenkins e a CLI do Azure

O [Azure Spring Cloud](/azure/spring-cloud/spring-cloud-overview) é um desenvolvimento de microsserviço totalmente gerenciado com descoberta de serviço e gerenciamento de configuração internos. O serviço facilita a implantação de aplicativos de microsserviço baseados no Spring Boot no Azure. Este tutorial demonstra como você pode usar a CLI do Azure no Jenkins para automatizar a CI/CD (integração e entrega contínuas) no Azure Spring Cloud.

Neste tutorial, você concluirá estas tarefas:

> [!div class="checklist"]
> * Provisionar uma instância de serviço e iniciar um aplicativo Java Spring
> * Preparar seu servidor Jenkins
> * Usar a CLI do Azure em um pipeline do Jenkins para compilar e implantar os aplicativos de microsserviço 

>[!Note]
> Atualmente, o Azure Spring Cloud é oferecido como visualização pública. As ofertas de visualização pública permitem que os clientes experimentem os novos recursos antes do lançamento oficial.  Os serviços e recursos de visualização pública não são destinados ao uso em produção.  Para saber mais sobre o suporte durante as visualizações, confira as [Perguntas frequentes](https://azure.microsoft.com/support/faq/) ou envie uma [Solicitação de suporte](/azure/azure-portal/supportability/how-to-create-azure-support-request) para saber mais.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

**Jenkins**: [Instalar o Jenkins em uma VM do Linux](configure-on-linux-vm.md)

**Conta do GitHub**: Se você ainda não tem uma conta do GitHub, crie uma [conta gratuita](https://github.com/) antes de começar.

## <a name="provision-a-service-instance-and-launch-a-java-spring-application"></a>Provisionar uma instância de serviço e iniciar um aplicativo Java Spring

Usamos o [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics) como o aplicativo de serviço da Microsoft de exemplo e seguimos as mesmas etapas descritas no [Início Rápido: Iniciar um aplicativo Java Spring usando a CLI do Azure](/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) para provisionar a instância de serviço e configurar os aplicativos. Caso já tenha passado pelo mesmo processo, vá para a próxima seção. Caso contrário, os comandos da CLI do Azure estão incluídos a seguir. Veja [Início Rápido: Iniciar um aplicativo Java Spring usando a CLI do Azure](/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) para obter mais informações sobre o contexto.

Seu computador local precisa atender ao mesmo pré-requisito do servidor de build do Jenkins. Verifique se os seguintes itens estão instalados para compilar e implantar os aplicativos de microsserviço:
    * [Git](https://git-scm.com/)
    * [JDK 8](/java/azure/jdk/?view=azure-java-stable)
    * [Maven 3.0 ou posterior](https://maven.apache.org/download.cgi)
    * [CLI do Azure instalada](/cli/azure/install-azure-cli?view=azure-cli-latest), versão 2.0.67 ou superior

1. Instale a extensão do Azure Spring Cloud:

    ```Azure CLI
    az extension add --name spring-cloud
    ```

1. Crie um grupo de recursos para conter o serviço do Azure Spring Cloud:

    ```Azure CLI
    az group create --location eastus --name <resource group name>
    ```

1. Provisione uma instância do Azure Spring Cloud:

    ```Azure CLI
    az spring-cloud create -n <service name> -g <resource group name>
    ```

1. Crie fork do repositório do [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics) para a própria conta do GitHub. No computador local, clone seu repositório em um diretório chamado `source-code`:

    ```bash
    mkdir source-code
    git clone https://github.com/<your GitHub id>/piggymetrics
    ```

1. Configure o servidor de configuração. Lembre-se de substituir &lt;sua ID do GitHub&gt; pelo valor correto.

    ```Azure CLI
    az spring-cloud config-server git set -n <your-service-name> --uri https://github.com/<your GitHub id>/piggymetrics --label config
    ```

1. Compile o projeto:

    ```bash
    cd piggymetrics
    mvn clean package -D skipTests
    ```

1. Crie os três microsserviços: **gateway**, **auth-service** e **account-service**:

    ```Azure CLI
    az spring-cloud app create --n gateway -s <service name> -g <resource group name>
    az spring-cloud app create --n auth-service -s <service name> -g <resource group name>
    az spring-cloud app create --n account-service -s <service name> -g <resource group name>
    ```

1. Implante os aplicativos:

    ```Azure CLI
    az spring-cloud app deploy -n gateway -s <service name> -g <resource group name> --jar-path ./gateway/target/gateway.jar
    az spring-cloud app deploy -n account-service -s <service name> -g <resource group name> --jar-path ./account-service/target/account-service.jar
    az spring-cloud app deploy -n auth-service -s <service name> -g <resource group name> --jar-path ./auth-service/target/auth-service.jar
    ```

1. Atribua um ponto de extremidade público ao gateway:

    ```Azure CLI
    az spring-cloud app update -n gateway -s <service name> -g <resource group name> --is-public true
    ```

1. Consulte o aplicativo de gateway para obter a URL, de modo que você possa verificar se o aplicativo está em execução.

    ```Azure CLI
    az spring-cloud app show --name gateway | grep url
    ```
    
 1. Navegue até a URL fornecida pelo comando anterior para executar o aplicativo PiggyMetrics.

## <a name="prepare-jenkins-server"></a>Preparar o servidor Jenkins

Nesta seção, você preparará o servidor do Jenkins para executar um build, que é adequado para o teste. No entanto, devido a implicações de segurança, você deverá usar um [agente de VM do Azure](https://plugins.jenkins.io/azure-vm-agents) ou um [agente de Contêiner do Azure](https://plugins.jenkins.io/azure-container-agents) para criar um agente no Azure e executar os builds. Para obter mais informações, consulte o artigo de Jenkins sobre o [implicações de segurança de compilação no mestre](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master).

### <a name="install-plug-ins"></a>Instalar plug-ins

1. Entre no seu servidor Jenkins. Escolha **Gerenciar Jenkins > Gerenciar Plug-ins**.

1. Na guia **Disponível**, selecione os seguintes plug-ins:

    * [Integração com o GitHub](https://plugins.jenkins.io/github-pullrequest)
    * [Credencial do Azure](https://plugins.jenkins.io/azure-credentials)

    Se esses plug-ins não aparecerem na lista, verifique a guia **Instalado** para ver se já estão instalados.

1. Para instalar os plug-ins, escolha **Baixar agora e instalar após a reinicialização**.

1. Reinicie o servidor do Jenkins para concluir a instalação.

### <a name="add-your-azure-service-principal-credential-in-jenkins-credential-store"></a>Adicionar suas credenciais de entidade de serviço do Azure ao repositório de credenciais do Jenkins

1. Você precisará de uma entidade de serviço do Azure para implantação no Azure. Para obter mais informações, confira a seção [Criar uma entidade de serviço](deploy-from-github-to-azure-app-service.md#create-service-principal) no tutorial Implantação no Serviço de Aplicativo do Azure. A saída de `az ad sp create-for-rbac` é semelhante a esta:

    ```
    {
        "appId": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "displayName": "xxxxxxxjenkinssp",
        "name": "http://xxxxxxxjenkinssp",
        "password": "xxxxxx-xxx-xxxx-xxx-xxxxxxxxxxxx",
        "tenant": "xxxxxx--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

1. No painel do Jenkins, selecione **Credenciais** > **Sistema**. Em seguida, selecione **Credenciais globais (irrestrito)** .

1. Selecione **Adicionar Credenciais**.

1. Selecione **Entidade de Serviço do Microsoft Azure** como o tipo.

1. Forneça valores para os seguintes campos:

    - **ID da assinatura**: ID de assinatura do Azure
    - **ID do cliente**: Appid da entidade de serviço
    - **Segredo do cliente**: Senha da entidade de serviço
    - **ID do locatário**: ID do locatário de conta Microsoft
    - **Ambiente Azure**: Selecione o valor apropriado para o ambiente. Por exemplo, use **Azure** para o Azure Global
    - **ID**: Defina como `azure_service_principal`. Usaremos essa ID em uma etapa posterior neste artigo
    - **Descrição**: Isso é opcional, mas recomendado.
    
### <a name="install-maven-and-az-cli-spring-cloud-extension"></a>Instalar a extensão spring-cloud do Maven e da CLI do Az

O pipeline de exemplo usa o Maven para build e a CLI do Az para implantação na instância de serviço. Quando o Jenkins está instalado, ele cria uma conta do administrador chamada *jenkins*. Verifique se o usuário *jenkins* tem permissão para executar a extensão spring-cloud.

1. Conecte-se ao mestre do Jenkins via SSH.

1. Instale o Maven.

    ```bash
    sudo apt-get install maven
    ```

1. Verifique se a CLI do Azure está instalada inserindo `az version`. Se a CLI do Azure não estiver instalada, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

1. Alterne para o usuário `jenkins`:

    ```bash
    sudo su jenkins
    ```

1. Adicione a extensão **spring-cloud**:

    ```bash
    az extension add --name spring-cloud
    ```

## <a name="create-a-jenkinsfile"></a>Criar um Jenkinsfile

1. No próprio repositório (https://github.com/&lt ;sua ID do GitHub&gt; /piggymetrics), crie um **Jenkinsfile** na raiz.

1. Atualize o arquivo conforme mostrado a seguir. Verifique se você substituiu os valores de **\<resource group name>** e **\<service name>** . Substitua **azure_service_principal** pela ID correta se você usar outro valor ao adicionar a credencial no Jenkins.

   ```groovy
       node {
         stage('init') {
           checkout scm
         }
         stage('build') {
           sh 'mvn clean package'
         }
         stage('deploy') {
           withCredentials([azureServicePrincipal('azure_service_principal')]) {
             // login to Azure
             sh '''
               az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
               az account set -s $AZURE_SUBSCRIPTION_ID
             '''  
             // Set default resource group name and service name. Replace <resource group name> and <service name> with the right values
             sh 'az configure --defaults group=<resource group name>'
             sh 'az configure --defaults spring-cloud=<service name>'
             // Deploy applications
             sh 'az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar'
             sh 'az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar'
             sh 'az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar'
             sh 'az logout'
           }
         }
       }
   ```

1. Salve e confirme a alteração.

## <a name="create-the-job"></a>Criar o trabalho

1. No painel do Jenkins, clique em **Novo Item**.

1. Forneça um nome, *Deploy-PiggyMetrics*, para o trabalho e selecione **Pipeline**. Clique em OK.

1. Clique na próxima guia **Pipeline**.

1. Para **Definição**, selecione **Script de pipeline do SCM**.

1. Para **SCM**, selecione **Git**.

1. Insira a URL do GitHub para o repositório bifurcado: **https://github.com/&lt ;sua ID do GitHub&gt; /piggymetrics.git**

1. Verifique se o **Especificador de Branch (preto para 'qualquer um')** é * **/Azure**

1. Mantenha o **Caminho do script** como **Jenkinsfile**

1. Clique em **Salvar**

## <a name="validate-and-run-the-job"></a>Validar e executar o trabalho

Antes de executar o trabalho, vamos atualizar o texto na caixa de entrada de logon para **inserir a ID de logon**.

1. No próprio repositório, abra `index.html` em **/gateway/src/main/resources/static/**

1. Pesquise "inserir seu logon" e atualize para "inserir a ID de logon"

    ```HTML
    <input class="frontforms" id="frontloginform" name="username" placeholder="enter login ID" type="text" autocomplete="off"/>
    ```

1. Confirmar as alterações

1. Execute o trabalho no Jenkins manualmente. No painel do Jenkins, clique no trabalho *Deploy-PiggyMetrics* e, em seguida, **Compilar Agora**.

Depois que o trabalho for concluído, navegue até o IP público do aplicativo de **gateway** e verifique se o aplicativo foi atualizado. 

![Piggy Metrics atualizado](./media/deploy-to-azure-spring-cloud-using-azure-cli/piggymetrics.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Exclua os recursos criados neste artigo quando não forem mais necessários:

```bash
az group delete -y --no-wait -n <resource group name>
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Jenkins no Azure](/azure/jenkins/)