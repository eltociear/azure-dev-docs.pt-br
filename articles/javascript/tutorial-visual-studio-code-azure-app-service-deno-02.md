---
title: Criar o Serviço de Aplicativo do Azure no Visual Studio Code
description: 'Parte 2 do tutorial: criar o aplicativo Deno e executá-lo localmente'
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: 32bd9e1f2b30d57ad573eb9be3fa22b9e246b0e9
ms.sourcegitcommit: 553da4e9aa988e5bb823364244ea81961cee5bc7
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85791159"
---
# <a name="test-local-deno-apps"></a>Testar aplicativos Deno locais

[Etapa anterior: Introdução e pré-requisitos](tutorial-visual-studio-code-azure-app-service-deno-01.md)

Nesta etapa, você criará uma API Deno simples usando o servidor Web interno do Deno. Você executará o aplicativo localmente.

## <a name="create-and-run-a-local-deno-app"></a>Criar e executar um aplicativo Deno local

1. Em um terminal ou prompt de comando, navegue até uma localização em que você deseja criar a pasta do aplicativo e crie uma pasta chamada `deno-demo`.

1. Crie um arquivo chamado `demo.ts`.
1. O Deno aceita o código em execução diretamente das URLs. Escreva um servidor HTTP que responda a todas as solicitações com "Olá, Mundo". Use o seguinte código:

    ```typescript
    import { serve } from "https://deno.land/std@0.54.0/http/server.ts"
    const handler = serve({ port: 80 })

    console.log("Serving at 80")

    for await (const req of handler) {
     req.respond({ body: "Hello World!\n" })
    }
    ```

1. Execute o aplicativo executando o seguinte script:

    ```bash
    deno run --allow-net ./demo.ts
    ```

1. Teste o aplicativo abrindo um navegador em `http://localhost:80`. O site deve aparecer da seguinte maneira:

    ![Executar o servidor de demonstração](media/deploy-azure/deno-hello-world.png)

    Você também pode executar esse código digitando `deno run --allow-net https://gist.githubusercontent.com/khaosdoctor/cd2bbb28e682feb8d20a7aba47fc1e17/raw/92de998fd11f2a24ae40bbcb84f5262cfe9389b2/deno-demo.ts`

1. Selecione **CTRL**+**C** no terminal para interromper o servidor.

> [!div class="nextstepaction"]
> [Eu criei o aplicativo Deno](tutorial-visual-studio-code-azure-app-service-deno-03.md) [Eu encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=deno-deployment-azureappservice&step=create-app)

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [tutorial-next-steps](includes/tutorial-next-steps.md)]

> [!div class="nextstepaction"]
> [Terminei](node-howto-deploy-web-app.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=deno-deployment-azureappservice&step=clean-up-resources)
