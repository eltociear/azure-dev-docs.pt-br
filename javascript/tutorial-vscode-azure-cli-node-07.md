---
title: Limpar recursos após implantar um aplicativo Node.js no Azure usando a CLI do Azure
description: Parte 7 do tutorial, limpar recursos
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 7998eb641090b252455613a46ae41e45e5cd1c1d
ms.sourcegitcommit: e77f8f652128b798dbf972078a7b460ed21fb5f8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74466753"
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
