---
title: 'Etapa 8: Limpar os recursos usados com o código Python no Azure Functions'
description: Etapa 8 do tutorial, limpando os recursos do Azure para evitar incorrer em encargos contínuos.
ms.topic: conceptual
ms.date: 09/17/2020
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: 885ff8b79ac219d4adaf4760e31fe439d010f9d5
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90832002"
---
# <a name="8-clean-up-azure-resources-for-azure-functions"></a>8: Limpar recursos do Azure para o Azure Functions

[Etapa anterior: adicionar uma associação de armazenamento](tutorial-vs-code-serverless-python-07.md)

Este artigo mostra como remover recursos do Azure criados neste tutorial. O Aplicativo de Funções do Azure que você criou com o Visual Studio Core inclui recursos que podem incorrer em custos mínimos. (Para obter mais informações, confira [Preços de Funções](https://azure.microsoft.com/pricing/details/functions/).)

A melhor maneira de limpar os recursos é excluir o grupo de recursos que contém todos os recursos individuais usados neste tutorial. Os recursos incluem o aplicativo de funções, a conta de armazenamento e o Plano do Serviço de Aplicativo de backup.

[!INCLUDE [delete-resource-group](includes/delete-resource-group.md)]

No Visual Studio Code, você pode observar que o menu de contexto no aplicativo de funções no gerenciador **Azure: Functions** tem um comando **Excluir Aplicativo de Funções**. No entanto, o comando exclui apenas o aplicativo de funções, deixando outros recursos em funcionamento, o que pode incorrer em custos contínuos.

## <a name="next-steps"></a>Próximas etapas

Parabéns por concluir este passo a passo da implantação de código Python no Azure Functions! Agora, você está pronto para criar muitas funções sem servidor.

Conforme observado anteriormente, você pode aprender mais sobre a extensão do Functions visitando seu repositório do GitHub, [vscode-azurefunctions](https://github.com/Microsoft/vscode-azurefunctions). Relatos de problemas e contribuições também são bem-vindos.

Leia a [Visão geral do Azure Functions](/azure/azure-functions/functions-overview) para explorar os diferentes gatilhos que você pode usar.

Para saber mais sobre os serviços do Azure que você pode usar no Python, incluindo armazenamento de dados com IA e os serviços do Machine Learning, visite o [Centro de Desenvolvedores de Python do Azure](./index.yml).

Também há outras extensões do Azure para Visual Studio Code que podem ser úteis. Basta pesquisar "Azure" no explorador de Extensões:

![Extensões do Azure para Visual Studio Code](media/tutorial-vs-code-serverless-python/azure-extensions-for-visual-studio-code.png)

Algumas extensões populares são:

- [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
- [Serviço de Aplicativo do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Confira o tutorial [Implantar no Serviço de Aplicativo](tutorial-deploy-app-service-on-linux-01.md)
- [Ferramentas da CLI do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
- [Ferramentas do Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

> [!div class="nextstepaction"]
> [Terminei!](/python/azure/?preserve-view=true&view=azure-python)