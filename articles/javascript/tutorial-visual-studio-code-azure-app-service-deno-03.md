---
title: Implantar aplicativos Deno para o Serviço de Aplicativo do Azure do Visual Studio Code
description: Parte 3 do tutorial, implantar o site
ms.topic: conceptual
ms.date: 06/01/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: fe7106976f16cbd80de0d3e0692b76287dbecec9
ms.sourcegitcommit: 0699b984b85782b1c441289fa756f285eae853c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2020
ms.locfileid: "90772909"
---
# <a name="deploy-deno-apps-to-azure"></a>Implantar aplicativos Deno no Azure

[Etapa anterior: Criar o aplicativo](tutorial-visual-studio-code-azure-app-service-deno-02.md)

Nesta etapa, você implantará seu aplicativo Deno no Azure usando a CLI do Azure.

## <a name="deploy-the-app-to-azure"></a>Implantar o aplicativo no Azure

1. Crie um grupo de recursos chamado `deno-quickstart` com o seguinte comando:

    ```bash
    az groups create -n deno-quickstart -l eastus
    ```

    Se você decidir alterar o nome do grupo de recursos, lembre-se de atualizar todos os sinalizadores `-g` nas etapas a seguir

1. Crie um Plano do AppService chamado `deno-plan` que armazenará seu site usando este comando:

    ```bash
    az appservice plan create -g deno-quickstart -n deno-plan --is-linux
    ```

1. Em seguida, você criará o próprio webapp. Esse comando criará um AppService e o associará ao plano criado anteriormente. Altere a marca `<your-app-name>` para o nome que você deseja dar ao seu Webapp. Lembre-se de que ele precisa ser exclusivo!

    ```bash
    az webapp create -n <your-app-name> -g deno-quickstart -p deno-plan -i anthonychu/azure-webapps-deno:1.0.2
    ```

    Esse AppService executa uma imagem do Docker, que fornece a funcionalidade básica para executar qualquer código Deno. Esse processo pode levar alguns segundos para ser concluído.

1. Após a criação, você precisará configurar algumas variáveis. Você pode fazer isso emitindo este comando:

    ```bash
    az webapp config container set -n <your-app-name> -g deno-quickstart -i anthonychu/azure-webapps-deno:1.0.2 -r 'https://index.docker.io' -u '' -p  '' -t true && \
    az webapp config set -n <your-app-name> -g deno-quickstart --startup-file '' && \
    az webapp config appsettings set -n <your-app-name> -g deno-quickstart --settings WEBSITE_RUN_FROM_PACKAGE=1 WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
    ```

Agora o AppService está configurado e está aguardando para receber o aplicativo da etapa anterior. Mas, para executá-lo, o aplicativo precisa ser empacotado em um pacote `.zip`. Você pode fazer isso com as seguintes etapas:

1. Acessar a pasta `deno-demo`

    ```bash
    cd deno-demo
    ```

1. Executar o comando `zip`:

    ```bash
    zip demo demo.ts
    ```

    O resultado desse comando será um arquivo chamado `demo.zip` na mesma pasta que o arquivo `demo.ts`.

1. Após o empacotamento, você poderá carregar o arquivo para o AppService a ser executado:

    ```bash
    az webapp deployment source config-zip -n <your-app-name> -g deno-quickstart --src ./demo.zip && \
    az webapp config set -n <your-app-name> -g deno-quickstart --startup-file 'deno run --allow-net demo.ts'
    ```

1. Teste o aplicativo acessando `https://<your-app-name>.azurewebsites.net`

> [!div class="nextstepaction"]
> [Meu site está no Azure](tutorial-visual-studio-code-azure-app-service-deno-04.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=deno-deployment-azureappservice&step=deploy-app)

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [tutorial-next-steps](includes/tutorial-next-steps.md)]

> [!div class="nextstepaction"]
> [Terminei](node-howto-deploy-web-app.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=deno-deployment-azureappservice&step=clean-up-resources)
