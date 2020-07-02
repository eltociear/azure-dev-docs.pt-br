---
title: Reimplantar um contêiner no Serviço de Aplicativo do Azure após fazer alterações no Visual Studio Code
description: Etapa 6 do tutorial, as etapas simples para recompilar e reimplantar uma imagem de contêiner.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 7920bc9ddb2b9b7cc06f936fb97400a5c1d9dd7d
ms.sourcegitcommit: 553da4e9aa988e5bb823364244ea81961cee5bc7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85791301"
---
# <a name="make-changes-and-redeploy"></a>Fazer alterações e implantar novamente

[Etapa anterior: Implantar a imagem do aplicativo](tutorial-vscode-docker-node-05.md)

Como inevitavelmente você faz alterações em seu aplicativo, você acaba recompilando e reimplantando o contêiner muitas vezes. Felizmente, o processo é simples:

1. Fazer alterações em seu aplicativo e testar localmente.

1. No Visual Studio Code, abra a **Paleta de Comandos** (**F1**) e execute **Imagens do Docker: Compilar Imagem** para recompilar a imagem. Se você alterar apenas o código do aplicativo, a compilação deverá levar apenas alguns segundos.

1. Para enviar a imagem por push para o registro, abra a **Paleta de Comandos** (**F1**) novamente e execute **Imagens do Docker: Push** escolhendo a imagem que você acabou de criar. Como antes, uma vez que uma alteração a seu código do aplicativo é pequena, somente tal camada precisará ser enviada por push e o processo costuma ser concluído em alguns segundos.

1. Na área **Azure: Serviço de Aplicativo**, clique com o botão direito do mouse no Serviço de Aplicativo correto e selecione **Reiniciar**. Reiniciar um serviço de aplicativo efetua pull automaticamente da imagem de contêiner mais recente do registro.

1. Após cerca de 15 a 20 segundos, visite a URL do Serviço de Aplicativo novamente para verificar as atualizações.

> [!div class="nextstepaction"]
> [Vejo as alterações](tutorial-vscode-docker-node-07.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-docker-extension&step=deploy-changes)
