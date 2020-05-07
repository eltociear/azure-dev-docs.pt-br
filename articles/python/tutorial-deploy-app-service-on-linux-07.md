---
title: 'Etapa 7: Limpar recursos após implantar no Serviço de Aplicativo do Azure no Linux usando o Visual Studio Code'
description: Etapa 7 do tutorial, limpando os recursos do Azure
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: seo-python-october2019
ms.openlocfilehash: 001e5d58311267221c5fafb3de89aff3859f4197
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "80441401"
---
# <a name="7-clean-up-resources-after-deploying-to-azure-app-service-on-linux-from-visual-studio-code"></a>7: Limpar recursos após implantar no Serviço de Aplicativo do Azure no Linux usando o Visual Studio Code

[Etapa anterior: stream de logs](tutorial-deploy-app-service-on-linux-06.md)

O Serviço de Aplicativo do Azure criado inclui um Plano de Serviço de Aplicativo que pode incorrer em custos. Para evitar esses custos, exclua o grupo de recursos que contém todos os recursos juntos.

[!INCLUDE [delete-resource-group](includes/delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Parabéns por concluir este passo a passo da implantação de código Python no Serviço de Aplicativo no Linux!

Conforme observado anteriormente, você pode aprender mais sobre a extensão do Serviço de Aplicativo visitando seu repositório do GitHub, [vscode-azureappservice](https://github.com/Microsoft/vscode-azureappservice). Relatos de problemas e contribuições também são bem-vindos.

Para saber mais sobre os serviços do Azure que você pode usar no Python, incluindo armazenamento de dados com IA e os serviços do Machine Learning, visite o [Centro de Desenvolvedores de Python do Azure](https://docs.microsoft.com/python/azure/?view=azure-python).

Também há outras extensões do Azure para VS Code que podem ser úteis. Basta pesquisar "Azure" no explorador de Extensões:

![Extensões do Azure para Visual Studio Code](media/deploy-containers/azure-extensions-for-visual-studio-code.png)

Algumas extensões populares são:

- [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
- [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
- [Ferramentas da CLI do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
- [Ferramentas do ARM (Azure Resource Manager)](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

> [!div class="nextstepaction"]
> [Terminei!](https://docs.microsoft.com/python/azure/?view=azure-python) 

[Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-python&step=07-clean-up-resources)
