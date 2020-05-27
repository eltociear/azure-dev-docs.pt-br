---
title: 'Etapa 5: Implantar o Azure Functions no Python com o VS Code'
description: 'Tutorial, etapa 5: implantação do código de função do Python no Azure e aprendizado sobre como transmitir logs e sincronizar configurações entre um projeto local e o Azure.'
ms.topic: conceptual
ms.date: 05/19/2020
ms.custom: seo-python-october2019
ms.openlocfilehash: 3e51760f6f779ef244d5788a3df852bc98924946
ms.sourcegitcommit: 089b87e1631a9db145583eb274edac6f80d16367
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83708610"
---
# <a name="5-deploy-azure-functions-in-python"></a>5: Implantar o Azure Functions no Python

[Etapa anterior: depurar localmente](tutorial-vs-code-serverless-python-04.md)

Neste artigo, você usará a extensão do Azure Functions para criar um aplicativo de funções no Azure, juntamente com outros recursos obrigatórios do Azure. Um aplicativo de funções permite a você agrupar funções como uma unidade lógica para facilitar o gerenciamento, implantação e compartilhamento de recursos.

Um aplicativo de funções requer uma Conta de Armazenamento do Azure para os dados e um [plano de hospedagem](/azure/azure-functions/functions-scale#hosting-plan-support). Todos esses recursos são organizados dentro de um único grupo de recursos.

1. Na área **Azure: Functions**, selecione o comando **Implantar no Aplicativo de Funções** ou abra a Paleta de Comandos (**F1**) e selecione o comando **Azure Functions: Implantar no Aplicativo de Funções**. Novamente, o aplicativo de funções é onde seu projeto do Python é executado no Azure.

    ![Implantar sua função do Python em um Aplicativo de Funções do Azure](media/tutorial-vs-code-serverless-python/deploy-a-python-fuction-to-azure-function-app.png)

1. Quando solicitado, selecione **Criar Aplicativo de Funções no Azure** e forneça um nome que seja exclusivo em todo o Azure (por exemplo, usando seu nome ou o nome da empresa, juntamente com outros identificadores exclusivos; você pode usar letras, números e hifens). Se você já criou um Aplicativo de Funções anteriormente, o nome dele aparecerá nessa lista de opções.

1. Quando solicitado, selecione uma versão do Python e o local do Azure.

1. A extensão executa as seguintes ações, que você pode observar nas mensagens pop-up e na janela **Saída** do Visual Studio Code (o processo leva alguns minutos):

    - Crie um grupo de recursos usando o nome que você deu (removendo os hifens) e o local que você selecionou.
    - Nesse grupo de recursos, crie a conta de armazenamento, o plano de hospedagem e o aplicativo de funções. Por padrão, um [Plano de Consumo](/azure/azure-functions/functions-scale#consumption-plan) será criado. Para executar suas funções em um plano dedicado, você precisa [habilitar a publicação com opções de criação avançadas](/azure/azure-functions/functions-develop-vs-code).
    - Implante seu código no aplicativo de funções.

    O gerenciador do **Azure: Functions** também mostra o progresso:

    ![Indicador de progresso de implantação no gerenciador do Azure: Functions](media/tutorial-vs-code-serverless-python/deployment-progress-indicator-in-azure-function-explorer.png)

1. Depois que a implantação for concluída, a extensão do Azure Functions exibirá uma mensagem com botões para três ações adicionais:

    ![Mensagem indicando uma implantação bem-sucedida com ações adicionais](media/tutorial-vs-code-serverless-python/azure-functions-deployment-success-with-additional-actions.png)

    Para **Transmitir Logs** e **Carregar Configurações**, confira as próximas seções.

1. Selecione **Exibir saída** para alternar para a janela de **Saída**. A saída mostra o ponto de extremidade público no Azure (a URL do seu ponto de extremidade específico corresponderá ao nome especificado para o aplicativo de funções):

    <pre>
    HTTP Trigger Urls:

          HttpExample: https://vscode-azure-functions.azurewebsites.net/api/HttpExample
    </pre>

    Use esse ponto de extremidade para executar os mesmos testes que fez localmente, usando parâmetros de URL e/ou solicitações com dados JSON no corpo da solicitação. Os resultados do ponto de extremidade público devem corresponder aos resultados do ponto de extremidade local, testado anteriormente na [parte 4](tutorial-vs-code-serverless-python-04.md).

## <a name="stream-logs"></a>Transmitir logs

O suporte para streaming de log está atualmente em desenvolvimento, conforme descrito no [Problema 589](https://github.com/microsoft/vscode-azurefunctions/issues/589) para a extensão do Azure Functions. O botão **Transmitir logs** do pop-up da mensagem de implantação eventualmente conectará a saída do log no Azure ao Visual Studio Code. Você também poderá iniciar e interromper o streaming de logs no gerenciador do **Azure Functions** clicando com o botão direito do mouse no projeto do Functions e selecionando **Iniciar streaming de logs** ou **Interromper streaming de logs**.

No presente, no entanto, esses comandos ainda não estão operacionais. O streaming de logs está disponível em um navegador executando o comando a seguir, substituindo `<app_name>` pelo nome do seu aplicativo do Functions no Azure:

```
# Replace <app_name> with the name of your Functions app on Azure
func azure functionapp logstream <app_name> --browser
```

## <a name="sync-local-settings-to-azure"></a>Sincronizar configurações locais ao Azure

O botão **Carregar Configurações**, no pop-up da mensagem de implantação, aplica todas as alterações feitas no arquivo *local.settings.json* ao Azure. Você também pode invocar o comando no gerenciador do **Azure Functions** expandindo o nó do projeto no Functions, clicando com o botão direito do mouse em **Configurações do aplicativo** e selecionando **Carregar configurações locais**. Você também pode usar a Paleta de Comandos para selecionar o comando **Azure Functions: Carregar Configurações Locais**.

O carregamento das configurações atualiza todas as configurações existentes e adiciona as novas configurações definidas no arquivo *local.settings.json*. O carregamento não remove nenhuma configuração do Azure que não esteja listada no arquivo local. Para remover essas configurações, expanda o nó **Configurações de Aplicativos**, no gerenciador do **Azure Functions**, clique com o botão direito do mouse na configuração e selecione **Excluir configuração**. Você também pode editar as configurações diretamente no portal do Azure.

Para aplicar as alterações feitas por meio do portal ou do **Azure Explorer** ao arquivo *local.settings.json*, clique com o botão direito do mouse sobre o nó **Configurações de Aplicativos** e selecione o comando **Baixar Configurações Remotas**. Você também pode usar a Paleta de Comandos para selecionar o comando **Azure Functions: Baixar Configurações Remotas**.

> [!div class="nextstepaction"]
> [Implantei as funções – prossiga para a etapa 6 >>>](tutorial-vs-code-serverless-python-06.md)

[Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=vscode-functions-python&step=05-deploy)
