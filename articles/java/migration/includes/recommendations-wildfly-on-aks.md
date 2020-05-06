---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: 1ffc12fa2a99e2abdbbc43e0024afd8e5e98c4b9
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "81673252"
---
### <a name="recommendations"></a>Recomendações

* Considere adicionar um nome DNS ao endereço IP alocado ao controlador de entrada ou ao balanceador de carga do aplicativo. Para obter mais informações, confira [Criar um controlador de entrada com um endereço IP público estático no AKS](/azure/aks/ingress-static-ip).

* Considere adicionar [gráficos HELM](https://helm.sh/docs/topics/charts/) ao aplicativo. Um gráfico do Helm permite que você parametrize a implantação do aplicativo para uso e personalização por um conjunto mais diversificado de clientes.

* Crie e implemente uma estratégia de DevOps. Para manter a confiabilidade, aumentando simultaneamente a velocidade de desenvolvimento, considere automatizar implantações e testar com Azure Pipelines. Para obter mais informações, confira [Criar e implantar no AKS](/azure/devops/pipelines/ecosystems/kubernetes/aks-template).

* Habilite o Monitoramento do Azure para o cluster. Para obter mais informações, confira [Habilitar o monitoramento do cluster do AKS já implantado](/azure/azure-monitor/insights/container-insights-enable-existing-clusters). Isso permite que o Azure Monitor colete logs do contêiner, acompanhe a utilização e assim por diante.

* Considere a possibilidade de expor métricas específicas do aplicativo por meio do Prometheus. O Prometheus é uma estrutura de métricas open-source amplamente adotada na comunidade do Kubernetes. Você pode configurar o recorte de Métricas do Prometheus no Azure Monitor, em vez de hospedar seu próprio servidor do Prometheus para habilitar a agregação de métricas de seus aplicativos e resposta automatizada para condições anormais ou escalonamento dessas condições. Para obter mais informações, confira [Configurar o recorte de Métricas do Prometheus com o Azure Monitor para contêineres](/azure/azure-monitor/insights/container-insights-prometheus-integration).

* Criar a implementar uma estratégia de continuidade dos negócios e recuperação de desastre. Para aplicativos críticos, considere a possibilidade de usar uma arquitetura de implantação em várias regiões. Para obter mais informações, confira [Melhores práticas para a continuidade dos negócios e recuperação de desastres no AKS](/azure/aks/operator-best-practices-multi-region).

* Examine a [Política de suporte à versão do Kubernetes](/azure/aks/supported-kubernetes-versions#kubernetes-version-support-policy). É sua responsabilidade continuar atualizando seu cluster do AKS para garantir que você esteja sempre executando uma versão compatível. Para obter mais informações, confira [Atualizar um cluster do AKS](/azure/aks/upgrade-cluster).

* Faça com que todos os membros da equipe responsáveis pela administração de clusters e pelo desenvolvimento de aplicativos examinem as melhores práticas do AKS. Para obter mais informações, confira [Melhores práticas para o operador e o desenvolvedor de clusters para criar e gerenciar aplicativos no AKS](/azure/aks/best-practices).

* Verifique se o arquivo de implantação especifica como as atualizações sem interrupção são feitas. Para obter mais informações, confira [Implantação de atualização sem interrupção](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#rolling-update-deployment) na documentação do Kubernetes.

* Configure o dimensionamento automático para lidar com as cargas em horário de pico. Para obter mais informações, confira [Dimensionar automaticamente um cluster para atender às demandas de aplicativo no AKS](/azure/aks/cluster-autoscaler).

* Considere a possibilidade de monitorar o tamanho do cache de código e adicionar os parâmetros de JVM `-XX:InitialCodeCacheSize` e `-XX:ReservedCodeCacheSize` no Dockerfile para otimizar ainda mais o desempenho. Para obter mais informações, confira [Ajuste do Codecache](https://docs.oracle.com/javase/8/embedded/develop-apps-platforms/codecache.htm) na documentação Oracle.
