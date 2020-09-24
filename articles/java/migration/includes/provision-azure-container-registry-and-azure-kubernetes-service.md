---
author: mnriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: c8cd0672bfedf640077e9ae3b9272b12d8ce0b61
ms.sourcegitcommit: 850856d3fa2ddd8f96616ee6a1f092d8e0aedab3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2020
ms.locfileid: "90738108"
---
### <a name="provision-azure-container-registry-and-azure-kubernetes-service"></a>Provisionar o Registro de Contêiner do Azure e o Serviço de Kubernetes do Azure

Use os seguintes comandos para criar um registro de contêiner e um cluster do Kubernetes do Azure cuja entidade de serviço tenha a função de Leitor no registro. Verifique se você [escolheu o modelo de rede apropriado](/azure/aks/operator-best-practices-network#choose-the-appropriate-network-model) para os requisitos de rede do seu cluster.

```bash
az group create -g $resourceGroup -l eastus
az acr create -g $resourceGroup -n $acrName --sku Standard
az aks create -g $resourceGroup -n $aksName --attach-acr $acrName --network-plugin azure
```
