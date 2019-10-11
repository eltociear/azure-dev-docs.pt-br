---
title: 'Tutorial: Reimplantar um contêiner no Serviço de Aplicativo do Azure após fazer alterações no Visual Studio Code'
description: Etapa 3 do tutorial, as etapas simples para recompilar e reimplantar uma imagem de contêiner.
services: app-service
author: kraigb
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: kraigb
ms.custom: seo-python-october2019
ms.openlocfilehash: 0c57729fd3ab1f671b2533f3e15056aec99cb6c9
ms.sourcegitcommit: bed07b313eeab51281d1a6d4eba67a75524b2f57
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72172454"
---
# <a name="tutorial-redeploy-a-container-to-azure-app-service-after-making-changes"></a>Tutorial: Reimplantar um contêiner no Serviço de Aplicativo do Azure após fazer alterações

[Etapa anterior: implantar a imagem no Azure](tutorial-deploy-containers-02.md)

Como inevitavelmente você faz alterações em seu aplicativo, você acaba recompilando e reimplantando o contêiner muitas vezes. Felizmente, o processo é simples:

1. Fazer alterações em seu aplicativo e testar localmente. (Essa etapa e as duas que seguem são explicadas no tutorial [Criar um contêiner do Python no VS Code](https://code.visualstudio.com/docs/python/tutorial-create-container).)

1. Recompilar a imagem do Docker. Se você alterar apenas o código do aplicativo, a compilação deverá levar apenas alguns segundos.

1. Enviar a imagem por push para o registro. Se você alterar apenas o código do aplicativo, apenas essa camada pequena precisará ser enviada por push e o processo normalmente será concluído em alguns segundos.

1. Na área **Azure: Serviço de Aplicativo**, clique com o botão direito do mouse no Serviço de Aplicativo correto e selecione **Reiniciar**. Reiniciar um serviço de aplicativo efetua pull automaticamente da imagem de contêiner mais recente do registro.

1. Após cerca de 15 a 20 segundos, visite a URL do Serviço de Aplicativo novamente para verificar as atualizações.

> [!div class="nextstepaction"]
> [Fiz alterações e reimplantei](tutorial-deploy-containers-04.md)

[Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-containers&step=03-make-changes-redeploy)
