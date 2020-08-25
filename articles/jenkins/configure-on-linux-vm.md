---
title: Guia de Início Rápido – Configurar o Jenkins usando a CLI do Azure
description: Saiba como instalar o Jenkins em uma máquina virtual do Linux no Azure e crie um aplicativo Java de exemplo.
keywords: jenkins, azure, devops, portal, linux, máquina virtual
ms.topic: quickstart
ms.date: 08/19/2020
ms.custom: devx-track-jenkins
ms.openlocfilehash: b5be59dc1ed3fab69051a8ddd23576e27c966a7b
ms.sourcegitcommit: 800c5e05ad3c0b899295d381964dd3d47436ff90
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88614564"
---
# <a name="quickstart-configure-jenkins-using-azure-cli"></a>Início Rápido: Configurar o Jenkins usando a CLI do Azure

Este guia de início rápido mostra como instalar o [Jenkins](https://jenkins.io) em uma VM Linux Ubuntu com as ferramentas e os plug-ins configurados para funcionar com o Azure.

Neste início rápido, você concluiu estas tarefas:

> [!div class="checklist"]
> * Criar um arquivo de instalação que baixe e instale o Jenkins
> * Criar um grupo de recursos
> * Criar uma máquina virtual com o arquivo de instalação
> * Abrir a porta 8080 para que você possa conectar-se à máquina virtual via SSH
> * Conectar-se à máquina virtual via SSH
> * Configurar um trabalho de exemplo do Jenkins com base em um aplicativo Java de exemplo no GitHub
> * Criar o trabalho de exemplo do Jenkins

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="troubleshooting"></a>Solução de problemas

Se você encontrar problemas ao configurar o Jenkins, consulte a [página de instalação do Cloudbees Jenkins](https://www.jenkins.io/doc/book/installing/) para obter as instruções mais recentes e informações sobre problemas conhecidos.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Entre no [portal do Azure](https://portal.azure.com).

1. Abra o [Azure Cloud Shell](/azure/cloud-shell/overview) e, se não tiver feito isso ainda, mude para **Bash**.

1. Crie um arquivo chamado `cloud-init-jenkins.txt`.

    ```bash
    code cloud-init-jenkins.txt
    ```

1. Cole o seguinte código no novo arquivo:

    ```json
    #cloud-config
    package_upgrade: true
    runcmd:
      - apt install openjdk-8-jdk -y
      - wget -qO - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
      - sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
      - apt-get update && apt-get install jenkins -y
      - service jenkins restart
    ```

1. Salve o arquivo ( **&lt;Ctrl>S**) e saia do editor ( **&lt;Ctrl>Q**).

1. Criar um grupo de recursos usando [az group create](/cli/azure/group#az-group-create). Talvez seja necessário substituir o parâmetro `--location` pelo valor apropriado para o seu ambiente.

    ```azurecli
    az group create \
    --name QuickstartJenkins-rg \
    --location eastus
    ```

1. Crie uma máquina virtual com [az vm create](/cli/azure/vm#az-vm-create).

    ```azurecli
    az vm create \
    --resource-group QuickstartJenkins-rg \
    --name QuickstartJenkins-vm \
    --image UbuntuLTS \
    --admin-username "azureuser" \
    --generate-ssh-keys \
    --custom-data cloud-init-jenkins.txt
    ```

1. Confirme a criação (e o estado) da nova máquina virtual usando [az vm list](/cli/azure/vm#az-vm-list).

    ```azurecli
    az vm list -d -o table --query "[?name=='QuickstartJenkins-vm']"
    ```

1. Abra a porta 8080 na nova máquina virtual usando [az vm open](/cli/azure/vm#az-vm-open-port).

    ```azurecli
    az vm open-port \
    --resource-group QuickstartJenkins-rg \
    --name QuickstartJenkins-vm  \
    --port 8080 --priority 1010
    ```

## <a name="configure-jenkins"></a>Configurar o Jenkins

1. Obtenha o endereço IP público para a máquina virtual de exemplo usando [az vm show](/cli/azure/vm#az-vm-show).

    ```azurecli
    az vm show \
    --resource-group QuickstartJenkins-rg \
    --name QuickstartJenkins-vm -d \
    --query [publicIps] \
    --output tsv
    ```

    **Observações**:

    - O parâmetro `--query` limita a saída para os endereços IP públicos para a máquina virtual.

1. Usando o endereço IP recuperado na etapa anterior, conecte-se por SSH à máquina virtual. Você precisará confirmar a solicitação de conexão.

    ```azurecli
    ssh azureuser@<ip_address>
    ```

    **Observações**:

    - Após a conexão bem-sucedida, o prompt do Cloud Shell inclui o nome de usuário e o nome da máquina virtual: `azureuser@QuickstartJenkins-vm`.

1. Verifique se o Jenkins está em execução obtendo o status do serviço Jenkins.

    ```bash
    service jenkins status
    ```

1. Obtenha a senha do Jenkins gerada automaticamente.

    ```bash
    sudo cat /var/lib/jenkins/secrets/initialAdminPassword
    ```

1. Usando o endereço IP, abra a seguinte URL em um navegador: `http://<ip_address>:8080`

1. Insira a senha que você recuperou anteriormente e selecione **Continuar**.

    ![Página inicial para desbloquear o Jenkins](./media/configure-on-linux-vm/unlock-jenkins.png)

1. Selecione **Selecionar plug-ins para instalar**.

    ![Selecione a opção para instalar os plug-ins selecionados](./media/configure-on-linux-vm/select-plugins.png)

1. Na caixa de diálogo de filtro na parte superior da página, insira `github`. Selecione o plug-in do GitHub e selecione **Instalar**.

    ![Instalar os plug-ins do GitHub](./media/configure-on-linux-vm/install-github-plugin.png)

1. Insira as informações do primeiro usuário administrador e selecione **Salvar e Continuar**.

    ![Inserir informações para o primeiro usuário administrador](./media/configure-on-linux-vm/create-first-user.png)

1. Na página **Configuração da Instância**, selecione **Salvar e Concluir**.

    ![Página de confirmação para configuração de instância](./media/configure-on-linux-vm/instance-configuration.png)

1. Selecione **Começar a usar o Jenkins**.

    ![O Jenkins está pronto!](./media/configure-on-linux-vm/start-using-jenkins.png)

## <a name="create-your-first-job"></a>Criar seu primeiro trabalho

1. Na home page do Jenkins, selecione **Criar um trabalho**.

    ![Home page do console do Jenkins](./media/configure-on-linux-vm/jenkins-home-page.png)

1. Insira um nome de trabalho de `mySampleApp`, selecione **Projeto Freestyle** e selecione **OK**.

    ![Criação de trabalho](./media/configure-on-linux-vm/new-job.png)

1. Selecione a guia **Gerenciamento de Código-Fonte**. Habilite o **Git** e insira a seguinte URL como o valor de **URL do Repositório**: `https://github.com/spring-guides/gs-spring-boot.git`

    ![Definir o repositório Git](./media/configure-on-linux-vm/source-code-management.png)

1. Selecione a guia **Build** e depois selecione **Adicionar etapa de build**

    ![Adicionar uma nova etapa de build](./media/configure-on-linux-vm/add-build-step.png)

1. No menu suspenso, selecione **Invocar script Gradle**.

    ![Selecione a opção de script Gradle](./media/configure-on-linux-vm/invoke-gradle-script-option.png)

1. Selecione **Usar Wrapper Gradle**, digite `complete` na **localização do Wrapper** e `build` como **Tarefas**.

    ![Opções de script Gradle](./media/configure-on-linux-vm/gradle-script-options.png)

1. Selecione **Avançado** e insira `complete` no campo **Script de Build Raiz**.

    ![Opções avançadas de script Gradle](./media/configure-on-linux-vm/root-build-script.png)

1. Role até a parte inferior da página e selecione **Salvar**.

## <a name="build-the-sample-java-app"></a>Criar o aplicativo Java de exemplo

1. Quando a home page do projeto for exibida, selecione **Criar agora** para compilar o código e empacotar o aplicativo de exemplo.

    ![Home page do projeto](./media/configure-on-linux-vm/project-home-page.png)

1. Um gráfico abaixo do título **Histórico de Build** indica que o trabalho está sendo compilado.

    ![Build do trabalho em andamento](./media/configure-on-linux-vm/job-currently-building.png)

1. Quando o build for concluído, selecione o link **Workspace**.

    ![Selecionar o link do workspace](./media/configure-on-linux-vm/job-workspace.png)

1. Navegue até `complete/build/libs` para ver se o arquivo `.jar` foi criado com êxito.

    ![A biblioteca de destino verifica se o build foi bem-sucedido](./media/configure-on-linux-vm/successful-build.png)

1. O servidor Jenkins agora está pronto para criar seus próprios projetos no Azure.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Jenkins no Azure](/azure/developer/jenkins)