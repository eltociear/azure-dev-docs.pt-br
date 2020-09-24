---
author: mnriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: abf3824dd83d0da27a411694e144a8e6bf7285ac
ms.sourcegitcommit: 850856d3fa2ddd8f96616ee6a1f092d8e0aedab3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90738437"
---
### <a name="deploy-to-aks"></a>Implantar no AKS

Criar e aplicar seus arquivos YAML do Kubernetes. Para saber mais, confira [Início Rápido: Implantar um cluster do Serviço de Kubernetes do Azure usando a CLI do Azure](/azure/aks/kubernetes-walkthrough#run-the-application). Se você estiver criando um balanceador de carga externo (seja para seu aplicativo ou para um controlador de entrada), será necessário fornecer o endereço IP provisionado na seção anterior como o `LoadBalancerIP`.

Inclua parâmetros externos como variáveis de ambiente. Para saber mais, confira [Definir variáveis de ambiente para um contêiner](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/). Não inclua segredos (como senhas, chaves de API e cadeias de conexão JDBC). Eles são abordados na seção a seguir.

Inclua as configurações de memória e CPU ao criar seu YAML de implantação para que os contêineres sejam dimensionados corretamente.
