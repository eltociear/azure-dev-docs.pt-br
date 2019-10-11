---
title: 'Tutorial: Limpar recursos do Azure – Azure Functions no Python'
description: Etapa 8 do tutorial, limpando os recursos do Azure para evitar incorrer em encargos contínuos.
services: functions
author: kraigb
manager: barbkess
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: kraigb
ms.custom: seo-python-october2019
ms.openlocfilehash: aac220a57c83b274c46886f4548e41b41ec8327e
ms.sourcegitcommit: bed07b313eeab51281d1a6d4eba67a75524b2f57
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72172417"
---
# <a name="tutorial-clean-up-azure-resources-for-azure-functions"></a>Tutorial: Limpar recursos do Azure para o Azure Functions

[Etapa anterior: adicionar uma associação de armazenamento](tutorial-vs-code-serverless-python-07.md)

A Aplicativo de Funções que você criou inclui recursos que podem incorrer em custos mínimos (consulte o [Preço das funções](https://azure.microsoft.com/pricing/details/functions/)). Para limpar os recursos, clique com o botão direito do mouse no Aplicativo de Funções no explorador **Azure: Funções** e selecione **Excluir Aplicativo de Funções**.

Você também pode visitar o [portal do Azure](https://portal.azure.com), selecionar **Grupos de recursos** no painel de navegação esquerdo, selecionar o grupo de recursos criado no processo deste tutorial e, em seguida, usar o comando **Excluir grupo de recursos**.

## <a name="next-steps"></a>Próximas etapas

Parabéns por concluir este passo a passo da implantação de código Python no Azure Functions! Agora, você está pronto para criar muitas funções sem servidor.

Conforme observado anteriormente, você pode aprender mais sobre a extensão do Functions visitando seu repositório do GitHub, [vscode-azurefunctions](https://github.com/Microsoft/vscode-azurefunctions). Relatos de problemas e contribuições também são bem-vindos.

Leia a [Visão geral do Azure Functions](/azure/azure-functions/functions-overview) para explorar os diferentes gatilhos que você pode usar.

Para saber mais sobre os serviços do Azure que você pode usar no Python, incluindo armazenamento de dados com IA e os serviços do Machine Learning, visite o [Centro de Desenvolvedores de Python do Azure](/azure/python/?view=azure-python).

Também há outras extensões do Azure para Visual Studio Code que podem ser úteis. Basta pesquisar "Azure" no explorador de Extensões:

![Extensões do Azure para Visual Studio Code](media/tutorial-vs-code-serverless-python/azure-extensions.png)

Algumas extensões populares são:

- [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
- [Serviço de Aplicativo do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Confira o tutorial [Implantar no Serviço de Aplicativo](tutorial-deploy-app-service-on-linux-01.md)
- [Ferramentas da CLI do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
- [Ferramentas do Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

> [!div class="nextstepaction"]
> [Terminei](https://docs.microsoft.com/python/azure/?view=azure-python)

[Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=vscode-functions-python&step=08-clean-up-resources)
