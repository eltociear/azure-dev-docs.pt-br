---
title: Implantar no Azure usando o GitHub Actions
description: Crie fluxos de trabalho no repositório para criar, testar, empacotar, lançar e implantar no Azure.
ms.author: kaelli
author: KathrynEE
ms.topic: conceptual
ms.service: azure
ms.date: 05/05/2020
ms.openlocfilehash: 16ebc2d8013b2199911c0648f4bb8364c4d6de06
ms.sourcegitcommit: a631b36ec1277ee9397a860c597ffdd5495d88e7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83369920"
---
# <a name="deploy-to-azure-using-github-actions"></a>Implantar no Azure usando o GitHub Actions

Use o GitHub Actions para o Azure a fim de automatizar os fluxos de trabalho e implantá-los no Azure. Como introdução, confira estas GitHub Actions: 

- [Azure/webapps-deploy](https://github.com/Azure/webapps-deploy): implanta nos Aplicativos Web do Azure e no Aplicativo Web para Contêineres do Azure 
- [Azure/appservice-settings](https://github.com/Azure/appservice-settings): define configurações de aplicativos, cadeias de conexão e outras configurações gerais em massa usando a sintaxe JSON no aplicativo Web do Azure (Windows ou Linux) ou em qualquer um dos slots de implantação.

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
  
A fim de obter links para todas as GitHub Actions para o Azure, confira a seguinte página: 
   
- [Ações do Azure](https://github.com/marketplace?query=Azure&type=actions)  

## <a name="connect-to-azure"></a>Conectar-se ao Azure

Para obter fluxos de trabalho de exemplo para se conectar ao Azure, confira as seguintes GitHub Actions:  

- [Logon do Azure](https://github.com/Azure/login)  
- [CLI do Azure](https://github.com/Azure/CLI)  


## <a name="starter-templates-and-end-to-end-cicd-workflow-samples"></a>Modelos iniciais e exemplos de fluxo de trabalho de CI/CD de ponta a ponta 

Os exemplos a seguir fornecem fluxos de trabalho de ponta a ponta para implantar os aplicativos Web no Azure. 

- [Implantar um aplicativo Web com suporte ao ASP.NET](https://github.com/Azure-Samples/dotnet-sample)  
- [Implantar um aplicativo ASP.NET Core](https://github.com/Azure-Samples/dotnet_core_sample)  
- [Implantar um aplicativo Web Node.js](https://github.com/Azure-Samples/node_express_app)  
- [Implantar um aplicativo Web Java](https://github.com/Azure-Samples/java-spring-petclinic)  
- [Implantar um aplicativo Spring Java](https://github.com/Azure-Samples/Java-application-petstore-ee7)  
- [Implantar um aplicativo Web Python](https://github.com/Azure-Samples/pythonSample_thecatsaidno)  
- [Implantação usando o Docker](https://github.com/Azure-Samples/Node_express_container)  


## <a name="deploy-a-web-app"></a>Implantar um aplicativo Web

- [Aplicativo Web do Azure](https://github.com/Azure/webapps-deploy)  
- [Aplicativo Web para Contêineres do Azure](https://github.com/Azure/webapps-container-deploy)  
- [Configurações do Serviço de Aplicativo do Azure](https://github.com/Azure/appservice-settings)  

## <a name="deploy-a-serverless-app"></a>Implantar um aplicativo sem servidor

- [Azure Functions](https://github.com/Azure/functions-action)  
- [Azure Functions para contêineres](https://github.com/Azure/webapps-container-deploy)  
 
## <a name="build-and-deploy-containerized-apps"></a>Criar e implantar aplicativos em contêineres

- [Logon do Docker](https://github.com/Azure/docker-login)  

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

## <a name="trigger-a-run-in-azure-pipelines"></a>Disparar uma execução no Azure Pipelines

- [Azure Pipelines](https://github.com/Azure/pipelines)  
 
## <a name="utility-actions"></a>Ações de utilitário

- [Substituição de variáveis](https://github.com/Microsoft/variable-substitution) 


## <a name="additional-resources"></a>Recursos adicionais

Os recursos do GitHub a seguir estão disponíveis para dar suporte ao uso do GitHub para implantar os aplicativos no Azure.  

- [GitHub Actions para o Azure Marketplace](https://github.com/marketplace?query=Azure&type=actions)
- [Laboratório de aprendizado: entrega contínua com o Azure](https://lab.github.com/githubtraining/github-actions:-continuous-delivery-with-azure)
- [Fluxos de trabalho de ação inicial para implantar no Azure](https://github.com/Azure/actions-workflow-samples)
