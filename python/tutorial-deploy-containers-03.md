---
title: 'Etapa 3: Reimplantar um contêiner no Serviço de Aplicativo do Azure após fazer alterações no Visual Studio Code'
description: Etapa 3 do tutorial, as etapas simples para recompilar e reimplantar uma imagem de contêiner.
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: seo-python-october2019
ms.openlocfilehash: f0e6eb808e25f3bb48eb2c849310747945e0d7f9
ms.sourcegitcommit: 44d1abfb836f90b8731d7ea5d5a5af09245b2b89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2020
ms.locfileid: "77422405"
---
# <a name="2-redeploy-a-container-to-azure-app-service-after-making-changes"></a>2: Reimplantar um contêiner no Serviço de Aplicativo do Azure após fazer alterações

[Etapa anterior: implantar a imagem no Azure](tutorial-deploy-containers-02.md)

Este artigo explica como reimplantar um contêiner no Serviço de Aplicativo do Azure após fazer alterações no Visual Studio Code.

Como inevitavelmente você faz alterações em seu aplicativo, você acaba recompilando e reimplantando o contêiner muitas vezes. Felizmente, o processo é simples:

1. Fazer alterações em seu aplicativo e testar localmente. (Essa etapa e as duas que seguem são explicadas no tutorial [Criar um contêiner do Python no VS Code](https://code.visualstudio.com/docs/python/tutorial-create-containers).)

1. Recompilar a imagem do Docker. Se você alterar apenas o código do aplicativo, a compilação deverá levar apenas alguns segundos.

1. Enviar a imagem por push para o registro. Se você alterar apenas o código do aplicativo, apenas essa camada pequena precisará ser enviada por push e o processo normalmente será concluído em alguns segundos.

1. Na área **Azure: Serviço de Aplicativo**, clique com o botão direito do mouse no Serviço de Aplicativo correto e selecione **Reiniciar**. Reiniciar um serviço de aplicativo efetua pull automaticamente da imagem de contêiner mais recente do registro.

1. Após cerca de 15 a 20 segundos, visite a URL do Serviço de Aplicativo novamente para verificar as atualizações.

> [!div class="nextstepaction"]
> [Fiz alterações e reimplantei – prossiga para a etapa 4 >>>](tutorial-deploy-containers-04.md)

[Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-containers&step=03-make-changes-redeploy)
