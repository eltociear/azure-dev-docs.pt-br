---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: e72cefaab3ccdbbaae01992cc11285944fb09a36
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "81673202"
---
### <a name="provision-azure-container-registry-and-azure-kubernetes-service"></a>Provisionar o Registro de Contêiner do Azure e o Serviço de Kubernetes do Azure

Use os seguintes comandos para criar um registro de contêiner e um cluster do Kubernetes do Azure cuja entidade de serviço tenha a função de Leitor no registro. Verifique se você [escolheu o modelo de rede apropriado](/azure/aks/operator-best-practices-network#choose-the-appropriate-network-model) para os requisitos de rede do seu cluster.

```bash
az group create -g $resourceGroup -l eastus
az acr create -g $resourceGroup -n $acrName --sku Standard
az aks create -g $resourceGroup -n $aksName --attach-acr $acrName --network-plugin azure
```
