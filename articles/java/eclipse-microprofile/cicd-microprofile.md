---
title: CI/CD para aplicativos MicroProfile usando o Azure Pipelines
description: Saiba como configurar um ciclo de versão de CI/CD para implantar um aplicativo MicroProfile em uma instância do Aplicativo Web para Contêineres do Azure com o Azure Pipelines.
services: devops
documentationcenter: MicroProfile
author: ruyakubu
manager: brunoborges
ms.author: ruyakubu
ms.date: 07/31/2019
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: web
ms.openlocfilehash: cdd704626b51105f93c19378511f4a267cb56649
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "81670292"
---
# <a name="cicd-for-microprofile-apps-using-azure-pipelines"></a>CI/CD para aplicativos MicroProfile usando o Azure Pipelines

Este tutorial mostra como configurar com facilidade um ciclo de versão de CI/CD (integração contínua e implantação contínua) do Azure Pipelines para implantar seu aplicativo do Java EE [MicroProfile](http://microprofile.io) em um Aplicativo Web para Contêineres do Azure. O aplicativo MicroProfile neste tutorial usa uma imagem básica [Payara Micro](https://www.payara.fish/payara_micro) para criar um arquivo WAR. 

```Dockerfile
FROM payara/micro:5.182
COPY target/*.war $DEPLOY_DIR/ROOT.war
EXPOSE 8080
```
Comece o processo de transporte em contêineres do Azure Pipelines criando uma imagem do Docker e enviando por push a imagem de contêiner para um ACR (Registro de Contêiner do Azure). Conclua o processo criando um pipeline de lançamento do Azure Pipelines e implantando a imagem de contêiner em um aplicativo Web.

## <a name="prerequisites"></a>Prerequisites

1. No [portal do Azure](https://portal.azure.com), crie um [Registro de Contêiner do Azure](https://azure.microsoft.com/services/container-registry).
   
1. No portal do Azure, crie um [Aplicativo Web para Contêineres do Azure](https://azure.microsoft.com/services/app-service/containers/). Selecione **Linux** para o **Sistema Operacional** e para **Configurar contêiner**, selecione **Início Rápido** como a **Origem da imagem**.  
   
1. Copie e salve a URL do clone do repositório GitHub de exemplo em [https://github.com/Azure-Samples/microprofile-hello-azure](https://github.com/Azure-Samples/microprofile-hello-azure).
   
1. Registre ou faça logon na organização do [Azure DevOps](https://dev.azure.com) e crie um [projeto](/vsts/organizations/projects/create-project). 
   
1. Importe o repositório GitHub de exemplo para o Azure Repos:
   
   1. Na página de projeto do Azure DevOps, selecione **Repositórios** na navegação esquerda.
   1. Em **ou importar um repositório**, selecione **Importar**. 
   1. Em **Clonar URL**, insira a URL de clone do Git salva e selecione **Importar**.
  
## <a name="create-a-build-pipeline"></a>Criar um pipeline de build

O Pipeline de build de integração contínua no Azure Pipelines executa automaticamente todas as tarefas de build sempre que há uma confirmação no aplicativo de origem do Java EE. Neste exemplo, o Azure Pipelines usa o Maven para criar o projeto MicroProfile em Java.

1. Na página de projeto do Azure DevOps, selecione **Pipelines** > **Builds** na navegação esquerda. 
   
1. Selecione **Novo Pipeline**.
   
1. Selecione **Usar o editor clássico para criar um pipeline sem YAML**. 
   
1. Verifique se o nome do projeto e o repositório GitHub importado aparecem nos campos e selecione **Continuar**.
   
1. Selecione **Maven** na lista de modelos e, em seguida, **Aplicar**.
   
1. No painel direito, verifique se **Ubuntu 1604 hospedado** é exibido na lista suspensa **Pool de agentes**.
   
   > [!NOTE]
   > Essa configuração informa ao Azure Pipelines qual servidor de build usar.  Você também pode usar seu servidor de build personalizado privado.
   
1. Para configurar o pipeline para integração contínua, selecione a guia **Gatilhos** no painel esquerdo e marque a caixa de seleção ao lado de **Habilitar integração contínua**.  
   
1. Na parte superior da página, selecione a lista suspensa ao lado de **Salvar e enfileirar** e selecione **Salvar**. 

   ![Ativar integração contínua](media/cicd-microprofile/continuous-integration.png)

## <a name="create-a-docker-build-image"></a>Criar uma imagem de build do Docker

O Azure Pipelines usa um Dockerfile com uma imagem básica do Payara Micro para criar uma imagem do Docker.  

1. Selecione a guia **Tarefas** e, em seguida, selecione **+** ao lado de **Trabalho do agente 1** para adicionar uma tarefa.
   
   ![Adicionar uma nova tarefa](media/cicd-microprofile/add-task.png)
   
1. No painel direito, selecione **Docker** na lista de modelos e, em seguida, **Adicionar**. 
   
1. Selecione **buildAndPush** no painel esquerdo e, no painel direito, insira uma descrição no campo **Nome de exibição**.
   
1. Em **Repositório de Contêineres**, selecione **Novo** ao lado do campo **Registro de Contêiner**. 
   
1. Preencha a caixa de diálogo **Adicionar uma conexão do serviço de Registro do Docker** da seguinte maneira:
   
   |Campo|Valor|
   |---|---|
   |**Tipo de Registro**|Selecione **Registro de Contêiner do Azure**.|
   |**Nome da conexão**|Insira um nome para a conexão.|
   |**Assinatura do Azure**|Selecione sua assinatura do Azure na lista suspensa e, se necessário, selecione **Autorizar**.|
   |**Registro de contêiner do Azure**|Selecione o nome do Registro de Contêiner do Azure na lista suspensa.| 
   
1. Selecione **OK**.
   
   ![Adicionar uma conexão de serviço do Registro do Docker](media/cicd-microprofile/dockerconnection.png)
   
   > [!NOTE]
   > Se estiver usando o Docker Hub ou outro Registro, selecione **Docker Hub** ou **Outros** em vez de **Registro de Contêiner do Azure** ao lado de **Tipo de Registro**. Em seguida, forneça as informações de entrada e de conexão para seu registro de contêiner.
   
1. Em **Comandos**, selecione **build** na lista suspensa **Comando**.
   
1. Selecione as reticências **...** ao lado do campo **Dockerfile**, navegue até **Dockerfile** e selecione-o em seu repositório GitHub e, em seguida, selecione **OK**. 
   
   ![Selecionar o Dockerfile](media/cicd-microprofile/selectdockerfile.png)
   
1. Em **Marcas**, insira *mais recente* em uma nova linha. 
   
1. Na parte superior da página, selecione a lista suspensa ao lado de **Salvar e enfileirar** e selecione **Salvar**. 

## <a name="push-the-docker-image-to-acr"></a>Enviar por push a imagem do Docker para o ACR

O Azure Pipelines envia a imagem do Docker por push para seu Registro de Contêiner do Azure e a usa para executar o aplicativo de API MicroProfile como um aplicativo Web em Java em contêineres.

1. Como você está usando o Docker no Azure Pipelines, crie outro modelo do Docker repetindo as etapas em [Criar uma imagem de build do Docker](#create-a-docker-build-image). Dessa vez, selecione **push** na lista suspensa **Comando**.
   
1. Selecione a lista suspensa ao lado de **Salvar e enfileirar** e selecione **Salvar e enfileirar**. 
   
1. Na pop-up **Executar pipeline**, verifique se **Ubuntu 1604 Hospedado** está selecionado em **Pool de agentes** e selecione **Salvar e executar**. 
   
1. Após a conclusão do build, selecione o hiperlink na página **Build** para verificar o êxito do build e ver outros detalhes.
   
   ![Selecionar o hiperlink do build](media/cicd-microprofile/checkbuild.png)

## <a name="create-a-release-pipeline"></a>Criar um pipeline de lançamento

O Pipeline de lançamento contínuo do Azure Pipelines dispara automaticamente a implantação para um ambiente de destino como o Azure assim que o build é concluído com êxito. É possível criar pipelines de lançamento para ambientes como de desenvolvimento, de teste, de preparo ou de produção.

1. Na página de projeto do Azure DevOps, selecione **Pipelines** > **Lançamentos** na navegação esquerda. 
   
1. Selecione **Novo Pipeline**.
   
1. Selecione **Implantar um aplicativo Java no Serviço de Aplicativo do Azure** na lista de modelos e selecione **Aplicar**. 
   
   ![Selecionar o modelo Implantar um aplicativo Java no Serviço de Aplicativo do Azure](media/cicd-microprofile/selectreleasetemplate.png)
   
1. Na janela pop-up, altere **Estágio 1** para um nome de estágio como *Desenvolvimento*, *Teste*, *Preparo* ou *Produção* e feche a janela. 
   
1. Em **Artefatos** no painel esquerdo, selecione **adicionar** para vincular artefatos do pipeline de build ao pipeline de lançamento. 
   
1. No painel direito, selecione o pipeline de build na lista suspensa em **Origem (pipeline de build)** e selecione **Adicionar**.
   
   ![Adicionar um artefato de build](media/cicd-microprofile/addbuildartifact.png)
   
1. Selecione o hiperlink no estágio de **Produção** para **Exibir tarefas do estágio**.
   
   ![Selecionar o nome do estágio](media/cicd-microprofile/viewstagetasks.png)
   
1. No painel direito, preencha o formulário da seguinte maneira:
   
   |Campo|Valor|
   |---|---|
   |**Assinatura do Azure**|Selecione a assinatura do Azure na lista suspensa.|
   |**Tipo de aplicativo**|Selecione **Aplicativo Web para Contêineres (Linux)** na lista suspensa.|
   |**Nome do serviço de aplicativo**|Selecione sua instância do ACR na lista suspensa.|
   |**Registro ou Namespaces**|Insira o nome do ACR no campo. Por exemplo, insira *mymicroprofileregistry.azure.io*.
   |**Repositório**|Insira o repositório que contém a imagem do Docker.| 
   
   ![Configurar tarefas de estágio](media/cicd-microprofile/configurestage.png)
   
1. No painel esquerdo, selecione **Implantar War no Serviço de Aplicativo do Azure** e, no painel direito, insira a marca *mais recente* no campo **Marca**. 
   
1. No painel esquerdo, selecione **Executar no agente** e, no painel direito, selecione **Ubuntu 1604 Hospedado** na lista suspensa **Pool de agentes**. 

## <a name="set-up-environment-variables"></a>Configurar variáveis de ambiente

Adicione e defina variáveis de ambiente para conectar-se ao registro de contêiner durante a implantação.

1. Selecione a guia **Variáveis** e, em seguida, **Adicionar** para adicionar as seguintes variáveis para a URL, o nome de usuário e a senha do registro de contêiner. 
   
   |Nome|Valor|
   |---|---|
   |*registry.url*|Insira a URL do registro de contêiner. Por exemplo: *https:\//mymicroprofileregistry.azure.io*|
   |*registry.username*|Insira o nome de usuário do Registro.|
   |*registry.password*|Insira a senha do Registro. Para maior segurança, selecione o ícone de cadeado para manter o valor da senha oculto.|
   
   ![Adicionar variáveis](media/cicd-microprofile/addvariables.png)
   
1. Na guia **Tarefas**, selecione **Implantar War no Serviço de Aplicativo do Azure** no painel esquerdo. 
   
1. No painel direito, expanda **Aplicativo e Configurações** e selecione as reticências **...** ao lado do campo **Configurações do Aplicativo**.
   
1. Na pop-up **Configurações do aplicativo**, selecione **Adicionar** para definir e atribuir as variáveis de configuração do aplicativo:
   
   |Nome|Valor|
   |---|---|
   |*DOCKER_REGISTRY_SERVER_URL*|*$(registry.url)*|
   |*DOCKER_REGISTRY_SERVER_USERNAME*|*$(registry.username)*|
   |*DOCKER_REGISTRY_SERVER_PASSWORD*|*$(registry.password)*|
   
1. Selecione **OK**.
   
   ![Adicionar e definir variáveis](media/cicd-microprofile/appsettings.png)
   
## <a name="set-up-continuous-deployment"></a>Configurar a implantação contínua 

Para habilitar a implantação contínua: 

1. Na guia **Pipeline**, em **Artefatos**, selecione o ícone de raio no artefato de compilação. 
   
1. No painel direito, defina o **Gatilho de implantação contínua** como **Habilitado**.
   
1. Selecione **Salvar** no canto superior direito e selecione **Salvar** novamente. 
   
   ![Habilitar gatilho de implantação contínua](media/cicd-microprofile/setcontinuousdeployment.png)
   
## <a name="deploy-the-java-app"></a>Implantar aplicativo Java

Agora que você habilitou a CI/CD, modificar o código-fonte cria e executa builds e versões automaticamente. Também é possível criar e executar versões manualmente, da seguinte maneira:

1. No canto superior direito da página de pipeline de lançamento, selecione **Criar versão**.
   
1. Na página **Criar uma versão**, selecione o nome do estágio em **Estágios para um gatilho mudar de automatizado para manual**. 
   
1. Selecione **Criar**. 
   
1. Selecione o nome da versão, passe o mouse sobre o estágio ou selecione-o e, em seguida, selecione **Implantar**. 
   
## <a name="test-the-java-web-app"></a>Testar aplicativo Web Java

Após a conclusão com êxito da implantação, teste o aplicativo Web. 

1. Copie a URL do aplicativo Web do portal do Azure.
   
   ![Aplicativo do Serviço de Aplicativo no portal do Azure](media/cicd-microprofile/portalurl.png)
   
1. Insira a URL em seu navegador da Web para executar seu aplicativo. A página da Web deverá mostrar **Olá Azure!**
   
   ![Página do aplicativo Web Java](media/cicd-microprofile/webapp.png)

