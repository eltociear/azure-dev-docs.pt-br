---
title: Implantar aplicativos Node.js para o Serviço de Aplicativo do Azure do Visual Studio Code
description: Parte 3 do tutorial, implantar o site
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 937eb54e9885e3b5b9fa7be54f551945a54572cd
ms.sourcegitcommit: e77f8f652128b798dbf972078a7b460ed21fb5f8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74467205"
---
# <a name="deploy-the-website"></a>Implantar o site

[Etapa anterior: Criar o aplicativo](tutorial-vscode-azure-app-service-node-02.md)

Nesta etapa, você implanta seu site do Node.js para usar o Visual Studio Code e a extensão do Serviço de Aplicativo do Azure. Este tutorial usa o modelo de implantação mais básico em que seu aplicativo é compactado e implantado em um Serviço de Aplicativo do Azure no Linux.

1. Antes de implantar o aplicativo, verifique se ele está escutando na porta fornecida pela variável de ambiente `PORT`: `process.env.PORT`.

1. Na pasta do aplicativo (como `myExpressApp` da etapa anterior), inicie o VS Code nessa pasta usando o seguinte comando:

    ```bash
    code .
    ```

1. No VS Code, selecione o ícone do Azure para abrir o gerenciador de **Serviço de Aplicativo do Azure** e, em seguida, selecione o ícone de seta azul para cima para implantar o aplicativo no Azure:

    ![Implantar no aplicativo Web](media/deploy-azure/deploy.png)

    > [!TIP]
    > Como alternativa, abra a **Paleta de Comandos** (**F1**), digite "implantar no aplicativo Web" e selecione **Serviço de Aplicativo do Azure: Implantar no comando** do aplicativo Web.

1. Nos prompts, insira as seguintes informações:

    a. Selecione sua pasta atual para o aplicativo (`myExpressApp` conforme usado neste tutorial).

    a. Selecione **Criar Aplicativo Web**.

    a. Insira um nome globalmente exclusivo para seu aplicativo e pressione **Enter**. Os caracteres válidos para um nome de aplicativo são “a-z”, “0-9” e “-”.

    a. Escolha um local em uma região do Azure (conforme explicado em [Regiões](https://azure.microsoft.com/regions/)) perto de você ou perto de outros serviços que você possa precisar acessar.

    a. Escolha sua **versão do Node.js**; LTS é recomendado.

1. Como a extensão cria o aplicativo, o progresso aparece na janela **Saída** no VS Code (talvez seja necessário selecionar a saída **Serviço de Aplicativo do Azure**).

    ![Janela de saída do Visual Studio Code para o Serviço de Aplicativo do Azure](media/deploy-azure/output-window.png)

1. Selecione **Sim** quando solicitado a atualizar sua configuração para ser executar `npm install` no servidor de destino. Seu aplicativo será implantado.

    ![Implantação configurada](media/deploy-azure/server-build.png)

1. Quando a implantação começar, você deverá atualizar o workspace para que todas as implantações subsequentes automaticamente tenham como destino o mesmo Aplicativo Web do Serviço de Aplicativo. Escolha **Sim** para que suas alterações sejam implantadas no aplicativo correto.

    ![Implantação configurada](media/deploy-azure/save-configuration.png)

1. Quando a implantação for concluída, selecione **Procurar no Site** no prompt para exibir seu site implantado recentemente.

## <a name="troubleshooting"></a>solução de problemas

O erro "Você não tem permissão para exibir este diretório ou página" indica que o aplicativo provavelmente falhou em iniciar corretamente. A exibição de logs, conforme descrito na [próxima etapa](tutorial-vscode-azure-app-service-node-04.md) pode ajudar a diagnosticar e a corrigir o problema. Se você não conseguir corrigi-lo, contate-nos clicando no botão **Encontrei um problema** abaixo. Temos o prazer de ajudar!

## <a name="updating-the-website"></a>Atualizar o site

Você pode implantar alterações nesse aplicativo usando o mesmo processo e escolhendo o aplicativo existente em vez de criar um.

----

> [!div class="nextstepaction"]
> [Meu site está no Azure](tutorial-vscode-azure-app-service-node-04.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azureappservice&step=deploy-app)
