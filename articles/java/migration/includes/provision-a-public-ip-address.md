---
author: mriem
ms.author: manriem
ms.date: 2/28/2020
ms.openlocfilehash: 5527a38151afd531cbd256428bd729b7aba8fe45
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "81673162"
---
### <a name="provision-a-public-ip-address"></a>Provisionar um endereço IP público

Se quiser que o aplicativo esteja acessível de fora de suas redes internas ou virtuais, você precisará de um endereço IP estático público. Provisione esse endereço IP dentro do grupo de recursos do nó do cluster, conforme mostrado no exemplo a seguir:

```bash
nodeResourceGroup=$(az aks show -g $resourceGroup -n $aksName --query 'nodeResourceGroup' -o tsv)
publicIp=$(az network public-ip create -g $nodeResourceGroup -n applicationIp --sku Standard --allocation-method Static --query 'publicIp.ipAddress' -o tsv)
echo "Your public IP address is ${publicIp}."
```
