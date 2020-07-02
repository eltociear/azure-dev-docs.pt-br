---
title: Limpar recursos após implantar um aplicativo Node.js no Azure usando a CLI do Azure
description: Parte 7 do tutorial, limpar recursos
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 183539b8e2f0246bd812e5fa364a885b75626819
ms.sourcegitcommit: 553da4e9aa988e5bb823364244ea81961cee5bc7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85791076"
---
# <a name="clean-up-resources"></a>Limpar os recursos

[Etapa anterior: Fazer alterações e reimplantar](tutorial-vscode-docker-node-06.md)

O Serviço de Aplicativo que você criou inclui um plano do Serviço de Aplicativo de backup que pode incorrer em custos. Para limpar os recursos, execute o seguinte comando em um terminal ou prompt de comando:

```azurecli
az group delete --name myResourceGroup
```

Você também pode visitar o [portal do Azure](https://portal.azure.com), selecionar **Grupos de recursos** no painel de navegação esquerdo, selecionar o grupo de recursos criado no processo deste tutorial e, em seguida, usar o comando **Excluir grupo de recursos**.

> [!div class="nextstepaction"]
> [Terminei](node-howto-deploy-web-app.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=clean-up-resources)
