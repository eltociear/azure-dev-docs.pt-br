---
title: 'Tutorial: Depurar localmente o código Python do Azure Functions com o VS Code'
description: 'Tutorial, etapa 4: execução do depurador do VS Code localmente para verificar seu código do Python.'
ms.topic: conceptual
ms.date: 09/02/2019
ms.custom: seo-python-october2019
ms.openlocfilehash: ffd5d433166c44edd8c021fd29cb7e43395df7ff
ms.sourcegitcommit: ac68fb174d606c7af2bfa79fe32b8ca7b73c86a1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75946690"
---
# <a name="tutorial-debug-the-azure-functions-python-code-locally"></a>Tutorial: Depurar localmente o código Python do Azure Functions

[Etapa anterior: examinar os arquivos de código](tutorial-vs-code-serverless-python-03.md)

É possível depurar localmente no Visual Studio Code o código do Python do Azure Functions.

1. Quando você cria o projeto do Functions, a extensão do Visual Studio Code também cria uma configuração de inicialização em `.vscode/launch.json` que contém uma única configuração chamada **Anexar ao Functions do Python**. Essa configuração significa que você pode apenas pressionar F5 ou usar o Gerenciador de Depuração para iniciar o projeto:

    ![Configuração para o gerenciador de depuração iniciar um projeto do Python](media/tutorial-vs-code-serverless-python/configuration-to-start-a-python-project-for-debugging.png)

1. Quando você inicia o depurador, um terminal é aberto mostrando a saída do Azure Functions, incluindo um resumo dos pontos de extremidade disponíveis. Sua URL poderá ser diferente se você tiver usado um nome diferente de "HttpExample":

    ```output
    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ```

1. Use **Ctrl+clique** ou **cmd+clique** sobre a URL na janela **Saída** do Visual Studio Code para abrir um navegador com esse endereço ou inicie um navegador e cole essa URL na barra de navegação. Em ambos os casos, o ponto de extremidade é `api/<function_name>`, nesse caso `api/HttpExample`. No entanto, como essa URL não inclui um parâmetro de nome, a janela do navegador deve mostrar apenas "Passe um nome na cadeia de caracteres de consulta ou no corpo da solicitação", conforme apropriado para esse caminho no código.

1. Agora, tente adicionar um parâmetro de nome – como `http://localhost:7071/api/HttpExample?name=VS%20Code`, por exemplo – e a janela do navegador deverá exibir a mensagem "Olá Visual Studio Code!", demonstrando que você executou esse caminho de código.

1. Para passar o valor do nome no corpo da solicitação JSON, você pode usar uma ferramenta como a cURL com o JSON embutido:

    ```bash
    # Mac OS/Linux: modify the URL if you're using a different function name
    curl --header "Content-Type: application/json" --request POST \
        --data '{"name":"Visual Studio Code"}' http://localhost:7071/api/HttpExample
    ```

    ```ps
    # Windows (escaping on the quotes is necessary; also modify the URL
    # if you're using a different function name)
    curl --header "Content-Type: application/json" --request POST \
        --data {"""name""":"""Visual Studio Code"""} http://localhost:7071/api/HttpExample
    ```

    No PowerShell, você também pode usar o [cmdlet Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-6).

    ---

    Como alternativa, crie um arquivo como *data.json* que contenha `{"name":"Visual Studio Code"}` e use o comando `curl --header "Content-Type: application/json" --request POST --data @data.json http://localhost:7071/api/HttpExample`.

1. Para testar a depuração da função, defina um ponto de interrupção na linha que lê `name = req.params.get('name')` e faça uma solicitação para a URL novamente. O depurador do Visual Studio Code deve parar nessa linha, permitindo que você examine as variáveis e percorra o código. (Para obter uma breve explicação sobre depuração básica, confira [Tutorial do Visual Studio Code tutorial – Configurar e executar o depurador](https://code.visualstudio.com/docs/python/python-tutorial#configure-and-run-the-debugger).)

1. Quando você estiver satisfeito por ter testado por completo a função localmente, pare o **depurador (com o comando de menu Depurar** > **Parar Depuração** ou com o comando **Desconectar**, na barra de ferramentas de depuração).

> [!div class="nextstepaction"]
> [Executei o depurador localmente](tutorial-vs-code-serverless-python-05.md)

[Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=vscode-functions-python&step=04-test-debug)
