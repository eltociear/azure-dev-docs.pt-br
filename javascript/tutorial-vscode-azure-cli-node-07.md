---
title: Limpar recursos após implantar um aplicativo Node.js no Azure usando a CLI do Azure
description: Parte 7 do tutorial, limpar recursos
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: kraigb
ms.openlocfilehash: 6d0b9c671c4ffd0fb5d521e6247e9964d6d93980
ms.sourcegitcommit: c04984b6367e922dbc5973af44f8cd0ca81ce157
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71686111"
---
# <a name="clean-up-resources"></a>Limpar recursos

[Etapa anterior: Fazer alterações e reimplantar](tutorial-vscode-docker-node-06.md)

O Serviço de Aplicativo que você criou inclui um plano do Serviço de Aplicativo de backup que pode incorrer em custos. Para limpar os recursos, execute o seguinte comando em um terminal ou prompt de comando:

```bash
az group delete --name myResourceGroup
```

Você também pode visitar o [portal do Azure](https://portal.azure.com), selecionar **Grupos de recursos** no painel de navegação esquerdo, selecionar o grupo de recursos criado no processo deste tutorial e, em seguida, usar o comando **Excluir grupo de recursos**.

> [!div class="nextstepaction"]
> [Terminei](node-howto-deploy-web-app.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=clean-up-resources)
