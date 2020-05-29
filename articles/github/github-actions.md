---
title: Implantar no Azure usando o GitHub Actions
description: Crie fluxos de trabalho no repositório para criar, testar, empacotar, lançar e implantar no Azure.
author: N-Usha
ms.author: ushan
ms.topic: conceptual
ms.service: azure
ms.date: 05/05/2020
ms.openlocfilehash: 3e6f6d447da93873a1278eaa42882a9d08a94d57
ms.sourcegitcommit: 9330d5af796b4b114466bbe75b8e18a9206f218e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83862799"
---
# <a name="deploy-to-azure-using-github-actions"></a>Implantar no Azure usando o GitHub Actions

[O GitHub Actions](https://help.github.com/articles/about-github-actions) permite que os desenvolvedores criem fluxos de trabalho automatizados de ciclo de vida de desenvolvimento de software.  

Com o GitHub Actions para Azure, você pode criar fluxos de trabalho que podem ser configurados em seu repositório para compilar, testar, empacotar, liberar e **implantar** no Azure. [Saiba mais sobre todas as outras integrações com o Azure](https://aka.ms/GitHubonAzure).

Comece hoje mesmo com uma [conta gratuita do Azure](https://azure.com/free/open-source)!

> [!NOTE]   
> Os links fornecidos neste artigo estão vinculados a um artigo do GitHub ou a um repositório do GitHub. 

## <a name="key-concepts"></a>Principais conceitos

O GitHub Actions permite que você crie fluxos de trabalho de SDLC (ciclo de vida de desenvolvimento de software) personalizados diretamente no repositório GitHub. Para obter uma visão geral do GitHub Actions e dos conceitos básicos, examine os seguintes artigos: 

- [Sobre as GitHub Actions](https://help.github.com/actions/getting-started-with-github-actions/about-github-actions)
- [Conceitos básicos ](https://help.github.com/actions/getting-started-with-github-actions/core-concepts-for-github-actions)
- [Sobre o empacotamento com o GitHub Actions](https://help.github.com/en/actions/publishing-packages-with-github-actions/about-packaging-with-github-actions)

## <a name="get-started"></a>Introdução 

O GitHub Actions inclui modelos pré-configurados e ações do Marketplace. 

- [Usar modelos pré-configurados](https://help.github.com/actions/getting-started-with-github-actions/starting-with-preconfigured-workflow-templates)  
- [Usar ações do GitHub Marketplace](https://help.github.com/en/actions/getting-started-with-github-actions/using-actions-from-github-marketplace)  
- [Ações do GitHub Marketplace: implantação no Azure](https://github.com/marketplace?type=actions&query=Azure)  
  
Para o GitHub Actions para Azure, confira as seguintes páginas: 
   
- [Ações do Azure](https://github.com/marketplace?query=Azure&type=actions)  
- [Fluxos de trabalho de ação inicial para implantar no Azure](https://github.com/Azure/actions-workflow-samples)


## <a name="connect-to-azure"></a>Conectar-se ao Azure

Para que os fluxos de trabalho de exemplo se conectem ao Azure e executem scripts baseados na CLI do Az ou no Az PowerShell, use as seguintes ações do GitHub:  

- [Logon do Azure](https://github.com/Azure/login)  
- [CLI do Azure](https://github.com/Azure/CLI)
- [PowerShell do Azure](https://github.com/Azure/powershell)


## <a name="sample-apps-with-cicd-workflow-samples"></a>Exemplos de aplicativos com amostras de fluxo de trabalho de CI/CD 

Os exemplos a seguir fornecem fluxos de trabalho de ponta a ponta para criar e implantar aplicativos Web de qualquer linguagem e qualquer ecossistema no Azure. 

- [Implantar um aplicativo Web com suporte ao ASP.NET](https://github.com/Azure-Samples/dotnet-sample)  
- [Implantar um aplicativo ASP.NET Core](https://github.com/Azure-Samples/dotnet_core_sample)  
- [Implantar um aplicativo Web Node.js](https://github.com/Azure-Samples/node_express_app)  
- [Implantar um aplicativo Web Java](https://github.com/Azure-Samples/java-spring-petclinic)  
- [Implantar um aplicativo Spring Java](https://github.com/Azure-Samples/Java-application-petstore-ee7)  
- [Implantar um aplicativo Web Python](https://github.com/Azure-Samples/pythonSample_thecatsaidno)  
- [Implantar um aplicativo Web em contêineres usando o Docker](https://github.com/Azure-Samples/Node_express_container)


## <a name="deploy-a-web-app"></a>Implantar um aplicativo Web

Implantar em Aplicativos Web do Azure e no Aplicativo Web para Contêineres do Azure:

- [Azure/webapps-deploy action](https://github.com/Azure/webapps-deploy)

Implantar um aplicativo Web estático:
- [Azure/static-web-apps-deploy](https://docs.microsoft.com/azure/static-web-apps/getting-started?tabs=angular)


Defina as configurações e as cadeias de conexão do aplicativo usando as ações:

- [Azure/appservice-settings](https://github.com/Azure/appservice-settings) 
- [Configurações do Serviço de Aplicativo do Azure](https://github.com/Azure/appservice-settings)  

## <a name="deploy-a-serverless-app"></a>Implantar um aplicativo sem servidor

- [Azure Functions](https://github.com/Azure/functions-action)  
- [Azure Functions para contêineres](https://github.com/Azure/webapps-container-deploy)  
 
## <a name="build-and-deploy-containerized-apps"></a>Criar e implantar aplicativos em contêineres

- [Logon do Docker](https://github.com/Azure/docker-login)  
- [Implantar em Instâncias de Contêiner do Azure](https://github.com/Azure/aci-deploy)

## <a name="deploy-to-kubernetes"></a>Implantar para o Kubernetes

- [Instalador da ferramenta kubectl](https://github.com/Azure/setup-kubectl)  
- [Definição de contexto do Kubernetes](https://github.com/Azure/k8s-set-context)  
- [Definição de contexto do AKS](https://github.com/Azure/aks-set-context)  
- [Criação de segredo do Kubernetes](https://github.com/Azure/k8s-create-secret)  
- [Implantação no Kubernetes](https://github.com/Azure/k8s-deploy)  
- [Configuração do Helm](https://github.com/Azure/setup-helm)  
- [Fazer o bake no Kubernetes](https://github.com/Azure/k8s-bake)  

## <a name="train-and-deploy-a-machine-learning-model"></a>Treinar e implantar um modelo de machine learning 

- [Logon](https://github.com/Azure/aml-workspace) 
- [Treinamento](https://github.com/Azure/aml-run)
- [Implantar modelo](https://github.com/Azure/aml-deploy)

## <a name="deploy-to-databases"></a>Implantar no banco de dados

- [Banco de Dados SQL do Azure](https://github.com/Azure/sql-action)  
- [Ação do MySQL do Azure](https://github.com/Azure/mysql-action)  

## <a name="deploy-machine-learning-models"></a>Implantar modelos do Azure Machine Learning

- [Implantar o Azure Machine Learning](https://github.com/Azure/aml-deploy)  

## <a name="trigger-a-run-in-azure-pipelines"></a>Disparar uma execução no Azure Pipelines

- [Azure Pipelines](https://github.com/Azure/pipelines)  
 
## <a name="utility-actions"></a>Ações de utilitário

- [Substituição de variáveis](https://github.com/Microsoft/variable-substitution) 


## <a name="additional-resources"></a>Recursos adicionais

Os recursos do GitHub a seguir estão disponíveis para dar suporte ao uso do GitHub para implantar os aplicativos no Azure.  

- [Marketplace para o GitHub Actions para Azure](https://github.com/marketplace?query=Azure&type=actions)
- [Laboratório de aprendizado: entrega contínua com o Azure](https://lab.github.com/githubtraining/github-actions:-continuous-delivery-with-azure)
- [Fluxos de trabalho de ação inicial para implantar no Azure](https://github.com/Azure/actions-workflow-samples)
