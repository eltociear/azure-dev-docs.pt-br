---
title: Implantar o Azure Functions no Python com o Visual Studio Code
description: 'Tutorial, etapa 5: implantação do código de função do Python no Azure e aprendizado sobre como transmitir logs e sincronizar configurações entre um projeto local e o Azure.'
services: functions
author: kraigb
manager: barbkess
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: kraigb
ms.openlocfilehash: 0bf24a900eb0c501443b8ff353c8b07529f840be
ms.sourcegitcommit: 74e28a479c87a3a53592646420b78e69852dd86a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/16/2019
ms.locfileid: "71019954"
---
# <a name="deploy-to-azure-functions"></a>Implantar no Azure Functions

[Etapa anterior: depurar localmente](tutorial-vs-code-serverless-python-04.md)

Nessas etapas, você usa a extensão do Functions para criar um aplicativo de funções no Azure, juntamente com outros recursos obrigatórios do Azure. Um aplicativo de funções permite a você agrupar funções como uma unidade lógica para facilitar o gerenciamento, implantação e compartilhamento de recursos. Também é necessário ter uma conta de armazenamento do Azure para os dados e um [plano de hospedagem](/azure/azure-functions/functions-scale.md#hosting-plan-support). Todos esses recursos são organizados dentro de um único grupo de recursos.

1. Na área **Azure: Functions**, selecione o comando **Implantar no Aplicativo de Funções** ou abra a Paleta de Comandos (**F1**) e selecione o comando **Azure Functions: Implantar no Aplicativo de Funções**. Novamente, o aplicativo de funções é onde seu projeto do Python é executado no Azure.

    ![Comando Implantar no Aplicativo de Funções](media/tutorial-vs-code-serverless-python/deploy-command.png)

1. Quando solicitado, selecione **Criar Aplicativo de Funções no Azure** e forneça um nome que seja exclusivo em todo o Azure (por exemplo, usando seu nome ou o nome da empresa, juntamente com outros identificadores exclusivos; você pode usar letras, números e hifens). Se você já criou um Aplicativo de Funções anteriormente, o nome dele aparecerá nessa lista de opções.

1. A extensão executa as seguintes ações, que você pode observar nas mensagens pop-up e na janela **Saída** do Visual Studio Code (o processo leva alguns minutos):

    - Crie um grupo de recursos usando o nome que você deu (removendo os hifens).
    - Nesse grupo de recursos, crie a conta de armazenamento, o plano de hospedagem e o aplicativo de funções. Por padrão, um [Plano de Consumo](/azure/azure-functions/functions-scale.md#consumption-plan) será criado. Para executar suas funções em um plano dedicado, você precisa [habilitar a publicação com opções de criação avançadas](/azure/azure-functions/functions-develop-vs-code.md).
    - Implante seu código no aplicativo de funções.

    O gerenciador do **Azure: Functions** também mostra o progresso:

    ![Indicador de progresso de implantação no gerenciador do Azure: Functions](media/tutorial-vs-code-serverless-python/deploy-progress.png)

1. Depois que a implantação for concluída, a extensão do Azure Functions exibirá uma mensagem com botões para três ações adicionais:

    ![Mensagem indicando uma implantação bem-sucedida com ações adicionais](media/tutorial-vs-code-serverless-python/deployment-popup.png)

    Para **Transmitir Logs** e **Carregar Configurações**, confira as próximas seções. Para **Exibir a saída**, confira a etapa 5 a seguir.

1. Após a implantação, a janela **Saída** também mostrará o ponto de extremidade público no Azure:

    ```output
    HTTP Trigger Urls:
      HttpExample: https://vscode-azure-functions.azurewebsites.net/api/HttpExample
    ```

    Use esse ponto de extremidade para executar os mesmos testes que fez localmente, usando parâmetros de URL e/ou solicitações com dados JSON no corpo da solicitação. Os resultados do ponto de extremidade público devem corresponder aos resultados do ponto de extremidade local, testado anteriormente na [parte 4](tutorial-vs-code-serverless-python-04.md).

## <a name="stream-logs"></a>Transmitir logs

O suporte para streaming de log está atualmente em desenvolvimento, conforme descrito no [Problema 589](https://github.com/microsoft/vscode-azurefunctions/issues/589) para a extensão do Azure Functions. O botão **Transmitir logs** do pop-up da mensagem de implantação eventualmente conectará a saída do log no Azure ao Visual Studio Code. Você também poderá iniciar e interromper o streaming de logs no gerenciador do **Azure Functions** clicando com o botão direito do mouse no projeto do Functions e selecionando **Iniciar streaming de logs** ou **Interromper streaming de logs**.

No presente, no entanto, esses comandos ainda não estão operacionais. O streaming de logs está disponível em um navegador executando o comando a seguir, substituindo `<app_name>` pelo nome do seu aplicativo do Functions no Azure:

```bash
# Replace <app_name> with the name of your Functions app on Azure
func azure functionapp logstream <app_name> --browser
```

## <a name="sync-local-settings-to-azure"></a>Sincronizar configurações locais ao Azure

O botão **Carregar Configurações**, no pop-up da mensagem de implantação, aplica todas as alterações feitas no arquivo *local.settings.json* ao Azure. Você também pode invocar o comando no gerenciador do **Azure Functions** expandindo o nó do projeto no Functions, clicando com o botão direito do mouse em **Configurações do aplicativo** e selecionando **Carregar configurações locais**. Você também pode usar a Paleta de Comandos para selecionar o comando **Azure Functions: Carregar Configurações Locais**.

O carregamento das configurações atualiza todas as configurações existentes e adiciona as novas configurações definidas no arquivo *local.settings.json*. O carregamento não remove nenhuma configuração do Azure que não esteja listada no arquivo local. Para remover essas configurações, expanda o nó **Configurações de Aplicativos**, no gerenciador do **Azure Functions**, clique com o botão direito do mouse na configuração e selecione **Excluir configuração**. Você também pode editar as configurações diretamente no portal do Azure.

Para aplicar as alterações feitas por meio do portal ou do **Azure Explorer** ao arquivo *local.settings.json*, clique com o botão direito do mouse sobre o nó **Configurações de Aplicativos** e selecione o comando **Baixar Configurações Remotas**. Você também pode usar a Paleta de Comandos para selecionar o comando **Azure Functions: Baixar Configurações Remotas**.

> [!div class="nextstepaction"]
> [Implantei as funções](tutorial-vs-code-serverless-python-06.md)

[Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=vscode-functions-python&step=05-deploy)
