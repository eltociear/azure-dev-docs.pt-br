---
title: Limpar recursos após implantar no Serviço de Aplicativo do Azure
description: Parte 4 do tutorial, limpar recursos
ms.topic: conceptual
ms.date: 06/01/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: 6573d7b33d4e18d541def9ca742809a6741f821a
ms.sourcegitcommit: 0699b984b85782b1c441289fa756f285eae853c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2020
ms.locfileid: "90772899"
---
# <a name="clean-up"></a>Limpar

[Etapa anterior: Implantar o aplicativo](tutorial-visual-studio-code-azure-app-service-deno-03.md)

Nesta seção, removeremos e limparemos todos os recursos criados.

## <a name="remove-your-resources"></a>Remover seus recursos

O Serviço de Aplicativo criado inclui um Plano do Serviço de Aplicativo de backup em execução em um tipo de preço gratuito; portanto, você não incorrerá em nenhum custo contínuo.

Quando desejar limpar os recursos, acesse o [portal do Azure](https://portal.azure.com), selecione **Grupos de recursos**, localize e selecione o grupo de recursos criado no processo deste tutorial (como `deno-quickstart`) e use o comando **Excluir grupo de recursos**.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [tutorial-next-steps](includes/tutorial-next-steps.md)]

> [!div class="nextstepaction"]
> [Terminei](node-howto-deploy-web-app.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=deno-deployment-azureappservice&step=clean-up-resources)
