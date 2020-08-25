---
title: Limpar recursos após implantar um aplicativo Node.js no Azure usando a CLI do Azure
description: Parte 7 do tutorial, limpar recursos
ms.topic: conceptual
ms.date: 09/24/2019
ms.custom: devx-track-javascript
ms.openlocfilehash: fb78f369d80ff40fef1d4a21df68ea79fafa2dbc
ms.sourcegitcommit: 0699b984b85782b1c441289fa756f285eae853c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88218527"
---
# <a name="part-7-clean-up-resources"></a>Parte 7: Limpar os recursos

[Etapa anterior: Fazer alterações e reimplantar](tutorial-vscode-docker-node-06.md)

O Serviço de Aplicativo que você criou inclui um plano do Serviço de Aplicativo de backup que pode incorrer em custos. Para limpar os recursos, execute o seguinte comando em um terminal ou prompt de comando:

```azurecli
az group delete --name myResourceGroup
```

Você também pode visitar o [portal do Azure](https://portal.azure.com), selecionar **Grupos de recursos** no painel de navegação esquerdo, selecionar o grupo de recursos criado no processo deste tutorial e, em seguida, usar o comando **Excluir grupo de recursos**.

> [!div class="nextstepaction"]
> [Terminei](node-howto-deploy-web-app.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=clean-up-resources)
