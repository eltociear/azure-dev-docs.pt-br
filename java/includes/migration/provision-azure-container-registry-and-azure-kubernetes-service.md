---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: e72cefaab3ccdbbaae01992cc11285944fb09a36
ms.sourcegitcommit: 56e5f51daf6f671f7b6e84d4c6512473b35d31d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897412"
---
### <a name="provision-azure-container-registry-and-azure-kubernetes-service"></a>Provisionar o Registro de Contêiner do Azure e o Serviço de Kubernetes do Azure

Use os seguintes comandos para criar um registro de contêiner e um cluster do Kubernetes do Azure cuja entidade de serviço tenha a função de Leitor no registro. Verifique se você [escolheu o modelo de rede apropriado](/azure/aks/operator-best-practices-network#choose-the-appropriate-network-model) para os requisitos de rede do seu cluster.

```bash
az group create -g $resourceGroup -l eastus
az acr create -g $resourceGroup -n $acrName --sku Standard
az aks create -g $resourceGroup -n $aksName --attach-acr $acrName --network-plugin azure
```
