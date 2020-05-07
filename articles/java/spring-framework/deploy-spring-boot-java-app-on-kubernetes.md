---
title: Implantar um aplicativo Spring Boot no Serviço de Kubernetes do Azure
titleSuffix: Azure Kubernetes Service
description: Este tutorial orienta você pelas etapas de implantação de um aplicativo Spring Boot em um cluster Kubernetes no Microsoft Azure.
services: container-service
documentationcenter: java
ms.date: 11/12/2019
ms.service: multiple
ms.tgt_pltfrm: multiple
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: 783197c2a98ef76d1a30126144cb44ebdf474fdc
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82166688"
---
# <a name="deploy-spring-boot-application-to-the-azure-kubernetes-service"></a>Implantar um aplicativo Spring Boot no Serviço de Kubernetes do Azure

O **[Kubernetes]** e o **[Docker]** são soluções de software livre que ajudam os desenvolvedores a automatizar a implantação, o dimensionamento e o gerenciamento de seus aplicativos em execução em contêineres.

Este tutorial fornece uma orientação sobre como combinar essas duas tecnologias populares de software livre para desenvolver e implantar um aplicativo Spring Boot no Microsoft Azure. Mais especificamente, você usa o *[Spring Boot]* para o desenvolvimento de aplicativos, o *[Kubernetes]* para a implantação de contêineres e o [AKS (Serviço de Kubernetes do Azure)] para hospedar seu aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure; se ainda não tiver uma assinatura do Azure, você poderá ativar o [benefício de assinante do MSDN] ou inscrever-se para uma [conta gratuita do Azure].
* A[CLI (interface de linha de comando) do Azure].
* Um JDK (Java Development Kit) com suporte. Para obter mais informações sobre os JDKs disponíveis para usar durante o desenvolvimento no Azure, confira <https://aka.ms/azure-jdks>.
* A ferramenta de compilação [Maven] do Apache (Versão 3).
* Um cliente [Git].
* Um cliente do [Docker].
* O [auxiliar de credencial do Docker do ACR](https://github.com/Azure/acr-docker-credential-helper).

> [!NOTE]
>
> Devido aos requisitos de virtualização deste tutorial, você não pode seguir as etapas neste artigo em uma máquina virtual. Você deve usar um computador físico com recursos de virtualização habilitados.
>

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Criar o aplicativo Web de Introdução ao Spring Boot no Docker

As etapas a seguir mostram como compilar um aplicativo Web Spring Boot e testá-lo localmente.

1. Abra um prompt de comando, crie um diretório local para conter o aplicativo e altere para o diretório. Por exemplo:
   ```
   md C:\SpringBoot
   cd C:\SpringBoot
   ```
   -- ou --
   ```
   md /users/robert/SpringBoot
   cd /users/robert/SpringBoot
   ```

1. Clone o exemplo de projeto [Introdução ao Spring Boot no Docker] para o diretório.
   ```
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Altere o diretório para o projeto concluído.
   ```
   cd gs-spring-boot-docker
   cd complete
   ```

1. Use o Maven para compilar e executar o aplicativo de exemplo.
   ```
   mvn package spring-boot:run
   ```

1. Teste o aplicativo Web navegando até `http://localhost:8080` ou com o seguinte comando `curl`:
   ```
   curl http://localhost:8080
   ```

1. Você verá a seguinte mensagem exibida: **Olá, mundo do Docker**

   ![Procurar aplicativo de exemplo localmente][SB01]

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>Criar um Registro de Contêiner do Azure usando a CLI do Azure

1. Abra um prompt de comando.

1. Faça logon na sua Conta do Azure:
   ```azurecli
   az login
   ```

1. Escolha a sua Assinatura do Azure:
   ```azurecli
   az account set -s <YourSubscriptionID>
   ```

1. Crie um grupo de recursos para os recursos do Azure usados neste tutorial.
   ```azurecli
   az group create --name=wingtiptoys-kubernetes --location=eastus
   ```

1. Crie um registro de contêiner do Azure privado no grupo de recursos. O tutorial envia o aplicativo de exemplo para esse registro como uma imagem de Docker em etapas posteriores. Substitua `wingtiptoysregistry` por um nome exclusivo para o registro.
   ```azurecli
   az acr create --resource-group wingtiptoys-kubernetes --location eastus \
    --name wingtiptoysregistry --sku Basic
   ```

## <a name="push-your-app-to-the-container-registry-via-jib"></a>Enviar por push seu aplicativo para o registro de contêiner via Jib

1. Faça logon no Registro de Contêiner do Azure a partir da CLI do Azure.
   ```azurecli
   # set the default name for Azure Container Registry, otherwise you will need to specify the name in "az acr login"
   az configure --defaults acr=wingtiptoysregistry
   az acr login
   ```

1. Navegue até o diretório do projeto completo de seu aplicativo Spring Boot (por exemplo, "*C:\SpringBoot\gs-spring-boot-docker\complete*" ou " */users/robert/SpringBoot/gs-spring-boot-docker/complete*"), e abra o arquivo *pom.xml* com um editor de texto.

1. Atualize a coleção `<properties>` no arquivo *pom.xml* com o nome de registro do Registro de Contêiner do Azure e a versão mais recente do [jib-maven-plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin).

   ```xml
   <properties>
      <docker.image.prefix>wingtiptoysregistry.azurecr.io</docker.image.prefix>
      <jib-maven-plugin.version>2.2.0</jib-maven-plugin.version>
      <java.version>1.8</java.version>
   </properties>
   ```
1. Atualize a coleção `<plugins>` no arquivo de *pom.xml* para que o elemento `<plugin>` contenha uma entrada para o `jib-maven-plugin`, conforme mostrado no exemplo a seguir. Observe que estamos usando uma imagem base do MCR (Registro de Contêiner da Microsoft): `mcr.microsoft.com/java/jdk:8-zulu-alpine`, que contém um JDK oficialmente compatível para o Azure. Para outras imagens base do MCR com JDKs oficialmente compatíveis, confira [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk), [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre), [Java SE Headless JRE](https://hub.docker.com/_/microsoft-java-jre-headless) e [Java SE JDK e Maven](https://hub.docker.com/_/microsoft-java-maven).

   ```xml
   <plugin>
     <artifactId>jib-maven-plugin</artifactId>
     <groupId>com.google.cloud.tools</groupId>
     <version>${jib-maven-plugin.version}</version>
     <configuration>
        <from>
            <image>mcr.microsoft.com/java/jdk:8-zulu-alpine</image>
        </from>
        <to>
            <image>${docker.image.prefix}/${project.artifactId}</image>
        </to>
     </configuration>
   </plugin>
   ```

1. Navegue para o diretório do projeto completo de seu aplicativo Spring Boot, execute o seguinte comando para compilar a imagem e envie por push a imagem para o registro:

   ```cmd
   az acr login && mvn compile jib:build
   ```

> [!NOTE]
>
> Devido à preocupação com a segurança da CLI do Azure e do Registro de Contêiner do Azure, a credencial criada pelo `az acr login` é válida por 1 hora; se você encontrar o erro *401 Não Autorizado*, poderá executar o comando `az acr login -n <your registry name>` novamente para autenticar-se outra vez.
>

## <a name="create-a-kubernetes-cluster-on-aks-using-the-azure-cli"></a>Criar um Cluster Kubernetes no AKS usando a CLI do Azure

1. Criar um cluster Kubernetes no Serviço de Kubernetes do Azure. O seguinte comando cria um cluster *kubernetes* no grupo de recursos *wingtiptoys-kubernetes*, com *wingtiptoys-akscluster* como o nome do cluster, com o ACR (Registro de Contêiner do Azure) `wingtiptoysregistry` anexado e com *wingtiptoys-kubernetes* como o prefixo DNS:
   ```azurecli
   az aks create --resource-group=wingtiptoys-kubernetes --name=wingtiptoys-akscluster \ 
    --attach-acr wingtiptoysregistry \
    --dns-name-prefix=wingtiptoys-kubernetes --generate-ssh-keys
   ```
   Esse comando pode demorar algum tempo para ser concluído.

1. Instalar `kubectl` usando a CLI do Azure. Os usuários de Linux podem ter que prefixar esse comando com `sudo`, já que ele implanta a CLI do Kubernetes em `/usr/local/bin`.
   ```azurecli
   az aks install-cli
   ```

1. Baixe as informações de configuração do cluster para que possa gerenciá-lo na interface da Web do Kubernetes e `kubectl`. 
   ```azurecli
   az aks get-credentials --resource-group=wingtiptoys-kubernetes --name=wingtiptoys-akscluster
   ```

## <a name="deploy-the-image-to-your-kubernetes-cluster"></a>Implantar a imagem em seu cluster Kubernetes

Este tutorial implanta o aplicativo usando `kubectl` e depois permite que você explore a implantação por meio da interface da Web do Kubernetes.

### <a name="deploy-with-kubectl"></a>Implantar com kubectl

1. Abra um prompt de comando.

1. Execute seu contêiner no cluster Kubernetes usando o comando `kubectl run`. Forneça um nome de serviço para seu aplicativo no Kubernetes e o nome de imagem completo. Por exemplo: 
   ```
   kubectl run gs-spring-boot-docker --image=wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest
   ```
   Neste comando:

   * O nome do contêiner `gs-spring-boot-docker` é especificado imediatamente após o comando `run`

   * O parâmetro `--image` especifica a combinação de servidor de logon e nome da imagem como `wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest`

1. Exponha seu cluster Kubernetes externamente usando o comando `kubectl expose`. Especifique o nome do serviço, a porta TCP voltada para o público usada para acessar o aplicativo e a porta de destino interna na qual seu aplicativo escuta. Por exemplo: 
   ```
   kubectl expose deployment gs-spring-boot-docker --type=LoadBalancer --port=80 --target-port=8080
   ```
   Neste comando:

   * O nome do contêiner `gs-spring-boot-docker` é especificado imediatamente após o comando `expose deployment`.

   * O parâmetro `--type` especifica que o cluster usa o balanceador de carga.

   * O parâmetro `--port` especifica a porta TCP 80 voltada para o público. Você acessa o aplicativo nessa porta.

   * O parâmetro `--target-port` especifica a porta TCP interna 8080. O balanceador de carga encaminha solicitações ao seu aplicativo nesta porta.

1. Após a implantação do aplicativo no cluster, consulte o endereço IP externo e abra-o em seu navegador da Web:

   ```
   kubectl get services -o jsonpath={.items[*].status.loadBalancer.ingress[0].ip} --namespace=default
   ```

   ![Procurar aplicativo de exemplo no Azure][SB02]



### <a name="deploy-with-the-kubernetes-web-interface"></a>Implantar com a interface da Web do Kubernetes

1. Abra um prompt de comando.

1. Abra o site de configuração do cluster Kubernetes em seu navegador padrão:
   ```
   az aks browse --resource-group=wingtiptoys-kubernetes --name=wingtiptoys-akscluster
   ```
   

> [!IMPORTANT]
> Se o cluster do AKS usa RBAC, um *ClusterRoleBinding* deve ser criado antes de poder acessar o painel corretamente. Por padrão, o painel do Kubernetes é implantado com um mínimo de acesso de leitura e exibe os erros de acesso do RBAC. O painel do Kubernetes não oferece suporte a credenciais fornecidas pelo usuário para determinar o nível de acesso, em vez disso, ele usa as funções concedidas à conta de serviço. Um administrador de cluster pode optar por conceder acesso adicional para o *painel do kubernetes* conta de serviço, no entanto, isso pode ser um vetor para elevação de privilégios. Você também pode integrar a autenticação do Active Directory para fornecer um nível mais granular de acesso.
> 
> Para criar uma associação, use o comando [kubectl create clusterrolebinding]. O exemplo a seguir mostra como criar uma associação de exemplo. No entanto, ela não aplica nenhum componente de autenticação adicional e pode levar a um uso inseguro. O painel do Kubernetes está aberto para qualquer pessoa com acesso à URL. Não expor publicamente o painel do Kubernetes.
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> Para obter mais informações sobre como usar os métodos de autenticação diferentes, confira o wiki do painel do Kubernetes em [autenticação de painel].

1. Quando o site de configuração de Kubernetes abrir no navegador, selecione o link para **implantar um aplicativo em contêiner**:

   ![Site de configuração do Kubernetes][KB01]

1. Quando a página **Criação de Recursos** for exibida, especifique as seguintes opções:

   a. Selecione **CRIAR UM APLICATIVO**.

   b. Insira o nome do aplicativo Spring Boot para o **Nome do aplicativo**; por exemplo: "*gs-spring-boot-docker*".

   c. Insira o servidor de logon e imagem do contêiner de antes na **Imagem do contêiner**; por exemplo: "*wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest*".

   d. Escolha **Externo** para o **Serviço**.

   e. Especifique as portas internas e externas nas caixas de texto **Porta** e **Porta de destino**.

   ![Site de configuração do Kubernetes][KB02]


1. Selecione **Implantar** para implantar o contêiner.

   ![Implantar Kubernetes][KB05]

1. Após a implantação de seu aplicativo, você verá o aplicativo Spring Boot listado em **Serviços**.

   ![Serviços Kubernetes][KB06]

1. Se você selecionar o link para **Pontos de extremidade externos**, poderá ver o aplicativo Spring Boot em execução no Azure.

   ![Serviços Kubernetes][KB07]

   ![Procurar aplicativo de exemplo no Azure][SB02]

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Spring e o Azure, continue no Spring no Centro de Documentação do Azure.

> [!div class="nextstepaction"]
> [Spring no Azure](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações sobre como usar o Spring Boot no Azure, confira o seguinte artigo:

* [Implantar um aplicativo Spring Boot no Serviço de Aplicativo do Azure](deploy-spring-boot-java-app-from-container-registry-using-maven-plugin.md)

Para obter mais informações sobre como usar o Azure com Java, confira [Azure para Desenvolvedores Java] e [Como trabalhar com o Java e o Azure DevOps].

Para saber mais sobre como implantar um aplicativo Java para Kubernetes com o Visual Studio Code, confira [Tutoriais de Java do Visual Studio Code].

Para saber mais sobre o Spring Boot no projeto de exemplo do Docker, veja [Introdução ao Spring Boot no Docker].

Os links a seguir fornecem mais informações sobre como criar aplicativos Spring Boot:

* Para saber mais sobre como começar a criar um aplicativo Spring Boot simples, confira o Spring Initializr em https://start.spring.io/.

Os links a seguir fornecem mais informações sobre como usar kubernetes com o Azure:

* [Comece com um cluster Kubernetes no Serviço de Kubernetes do Azure](/azure/aks/intro-kubernetes)

Saiba mais sobre como usar a interface de linha de comando Kubernetes no guia do usuário **kubectl** em <https://kubernetes.io/docs/user-guide/kubectl/>.

O site do Kubernetes tem vários artigos que abordam o uso de imagens em registros privados:

* [Configurar contas de serviço para Pods]
* [Namespaces]
* [Extrair uma imagem de um registro privado]

Para obter mais exemplos sobre como usar imagens personalizadas do Docker com o Azure, veja [Usando uma imagem personalizada do Docker para o Aplicativo Web do Azure no Linux].

Para obter mais informações sobre como executar iterativamente e depurar os contêineres direto no Serviço de Kubernetes do Azure (AKS) com o Azure Dev Spaces, confira [Introdução ao Azure Dev Spaces com Java]

<!-- URL List -->
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[autenticação de painel]: https://github.com/kubernetes/dashboard/wiki/Access-control
[CLI (interface de linha de comando) do Azure]: /cli/azure/overview
[AKS (Serviço de Kubernetes do Azure)]: https://azure.microsoft.com/services/kubernetes-service/
[Azure para desenvolvedores Java]: /azure/developer/java/
[Azure portal]: https://portal.azure.com/
[Create a private Docker container registry using the Azure portal]: /azure/container-registry/container-registry-get-started-portal
[Usando uma imagem personalizada do Docker para o Aplicativo Web do Azure no Linux]: /azure/app-service-web/app-service-linux-using-custom-docker-image
[Docker]: https://www.docker.com/
[conta gratuita do Azure]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Como trabalhar com o Java e o Azure DevOps]: /azure/devops/java/
[Kubernetes]: https://kubernetes.io/
[Kubernetes Command-Line Interface (kubectl)]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[Maven]: http://maven.apache.org/
[benefício de assinante do MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Introdução ao Spring Boot no Docker]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/
[Configurar contas de serviço para Pods]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/
[Namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[Extrair uma imagem de um registro privado]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

[Java Development Kit (JDK)]: https://aka.ms/azure-jdks
<!-- http://www.oracle.com/technetwork/java/javase/downloads/ -->

<!-- Newly added -->
[Authenticate with Azure Container Registry from Azure Kubernetes Service]: /azure/container-registry/container-registry-auth-aks/
[Tutoriais de Java do Visual Studio Code]: https://code.visualstudio.com/docs/java/java-kubernetes/
[Introdução ao Azure Dev Spaces com Java]: /azure/dev-spaces/get-started-java
<!-- IMG List -->

[SB01]: media/deploy-spring-boot-java-app-on-kubernetes/SB01.png
[SB02]: media/deploy-spring-boot-java-app-on-kubernetes/SB02.png

[AR01]: media/deploy-spring-boot-java-app-on-kubernetes/AR01.png
[AR02]: media/deploy-spring-boot-java-app-on-kubernetes/AR02.png
[AR03]: media/deploy-spring-boot-java-app-on-kubernetes/AR03.png
[AR04]: media/deploy-spring-boot-java-app-on-kubernetes/AR04.png

[KB01]: media/deploy-spring-boot-java-app-on-kubernetes/KB01.png
[KB02]: media/deploy-spring-boot-java-app-on-kubernetes/KB02.png
[KB03]: media/deploy-spring-boot-java-app-on-kubernetes/KB03.png
[KB04]: media/deploy-spring-boot-java-app-on-kubernetes/KB04.png
[KB05]: media/deploy-spring-boot-java-app-on-kubernetes/KB05.png
[KB06]: media/deploy-spring-boot-java-app-on-kubernetes/KB06.png
[KB07]: media/deploy-spring-boot-java-app-on-kubernetes/KB07.png
