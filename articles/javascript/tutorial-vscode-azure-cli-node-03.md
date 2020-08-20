---
title: Criar o Serviço de Aplicativo do Azure usando a CLI do Azure para hospedar o aplicativo
description: Parte 3 do tutorial, criar o Serviço de Aplicativo
ms.topic: conceptual
ms.date: 09/24/2019
ms.custom: devx-track-javascript
ms.openlocfilehash: d48ee9ba1b1ddb813b0dd7f0f48da52e0cf96e15
ms.sourcegitcommit: 0699b984b85782b1c441289fa756f285eae853c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88217932"
---
# <a name="create-the-app-service"></a>Criar o Serviço de Aplicativo

[Etapa anterior: Criar o aplicativo](tutorial-vscode-azure-cli-node-02.md)

Nesta etapa, você usa a CLI do Azure para criar o Serviço de Aplicativo do Azure para hospedar o código do aplicativo.

1. Em um terminal ou prompt de comando, use o seguinte comando para criar um **grupo de recursos** para o Serviço de Aplicativo. Um grupo de recursos é essencialmente uma coleção nomeada de recursos de um aplicativo no Azure, como um site, um banco de dados, Azure Functions etc.

    ```azurecli
    az group create --name myResourceGroup --location westus
    ```

    O comando acima cria um grupo de recursos chamado `myResourceGroup` no data center do `westus`. Você pode alterar esses valores conforme desejar.

    Depois que o comando for executado com êxito, ele exibirá a saída JSON com os detalhes do grupo de recursos.

1. Execute o comando a seguir para definir o grupo de recursos e a região padrão para comandos subsequentes. Com isso, não é preciso especificar esses valores a cada vez. (Esse comando não tem nenhuma saída em caso de sucesso.)

    ```azurecli
    az configure --defaults group=myResourceGroup location=westus
    ```

1. Execute o comando a seguir para criar um **plano de serviço de aplicativo** que defina a máquina virtual subjacente usada pelo Serviço de Aplicativo:

    ```azurecli
    az appservice plan create --name myPlan --sku F1
    ```

    O comando acima especifica um plano de hospedagem gratuita (`--sku F1`), que usa uma máquina virtual compartilhada e dá ao plano o nome de `myPlan`. Novamente, o comando mostra a saída JSON em caso de êxito.

1. Execute o comando a seguir para criar o Serviço de Aplicativo, substituindo `<your_app_name>` por um nome exclusivo que se torna a URL, `http://<your_app_name>.azurewebsites.net`. Observe que o comando do PowerShell é levemente diferente. O argumento `--runtime "node|6.9"` informa ao Azure para usar o Node versão 6.9.x no servidor.

    ```azurecli
    az webapp create --name <your_app_name> --plan myPlan --runtime "node|6.9"
    ```

    > [!TIP]
    > Você também pode declarar a versão do Node desejada em seu `package.json`. O Azure aplica essa configuração durante a implantação. Por exemplo, a seguinte entrada `package.json` informa ao Azure para usar pelo menos o Node 7.0.0:
    >
    > ``` json
    > "engines": {
    >     "node": ">7.0.0"
    > },
    > ```

1. Execute o seguinte comando para abrir um navegador para o Serviço de Aplicativo que acaba de ser criado, novamente substituindo `<your_app_name>` pelo nome usado:

    ```azurecli
    az webapp browse --name <your_app_name>
    ```

1. Como você não implantou código personalizado para o aplicativo, o navegador deve mostrar uma página padrão:

    ![Página padrão do Serviço de Aplicativo](media/azure-cli/azure-default-page.png)

> [!div class="nextstepaction"]
> [Criei o Serviço de Aplicativo](tutorial-vscode-azure-cli-node-04.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=create-website)
