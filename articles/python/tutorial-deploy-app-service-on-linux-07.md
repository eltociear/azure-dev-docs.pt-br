---
title: 'Etapa 7: Limpar recursos após implantar no Serviço de Aplicativo do Azure no Linux usando o Visual Studio Code'
description: Etapa 7 do tutorial, limpando os recursos do Azure
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: devx-track-python, seo-python-october2019
ms.openlocfilehash: cc1acdd316406d9269c97a4f5b25f465d0303509
ms.sourcegitcommit: 39f3f69e3be39e30df28421a30747f6711c37a7b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90831472"
---
# <a name="7-clean-up-resources-after-deploying-to-azure-app-service-on-linux-from-visual-studio-code"></a>7: Limpar recursos após implantar no Serviço de Aplicativo do Azure no Linux usando o Visual Studio Code

[Etapa anterior: stream de logs](tutorial-deploy-app-service-on-linux-06.md)

O Serviço de Aplicativo do Azure criado inclui um Plano de Serviço de Aplicativo que pode incorrer em custos. Para evitar esses custos, exclua o grupo de recursos que contém todos os recursos juntos.

[!INCLUDE [delete-resource-group](includes/delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Parabéns por concluir este passo a passo da implantação de código Python no Serviço de Aplicativo no Linux!

Conforme observado anteriormente, você pode aprender mais sobre a extensão do Serviço de Aplicativo visitando seu repositório do GitHub, [vscode-azureappservice](https://github.com/Microsoft/vscode-azureappservice). Relatos de problemas e contribuições também são bem-vindos.

Para saber mais sobre os serviços do Azure que você pode usar no Python, incluindo armazenamento de dados com IA e os serviços do Machine Learning, visite o [Centro de Desenvolvedores de Python do Azure](/python/azure/?view=azure-python).

Também há outras extensões do Azure para VS Code que podem ser úteis. Basta pesquisar "Azure" no explorador de Extensões:

![Extensões do Azure para Visual Studio Code](media/deploy-containers/azure-extensions-for-visual-studio-code.png)

Algumas extensões populares são:

- [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
- [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
- [Ferramentas da CLI do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
- [Ferramentas do ARM (Azure Resource Manager)](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

> [!div class="nextstepaction"]
> [Terminei!](/python/azure/?view=azure-python) 

[Está com problemas? Fale conosco.](https://aka.ms/FlaskVSCQuickstartHelp)