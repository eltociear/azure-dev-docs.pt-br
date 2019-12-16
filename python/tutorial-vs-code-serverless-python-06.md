---
title: 'Tutorial: Adicionar uma segunda função do Python ao Azure Functions com o VS Code'
description: Etapa 6 do tutorial, expandindo um projeto do Azure Functions adicionando uma segunda função.
ms.topic: conceptual
ms.date: 09/02/2019
ms.custom: seo-python-october2019
ms.openlocfilehash: 12cd3625d3eec63a2ba3de4d55dbd957fbf0fb34
ms.sourcegitcommit: 68a4044b9fa3291c9e7e2f68ae0049328f9c01bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74992432"
---
# <a name="tutorial-add-a-second-python-function-to-azure-functions"></a>Tutorial: Adicionar uma segunda função de Python ao Azure Functions

[Etapa anterior: implantar no Azure](tutorial-vs-code-serverless-python-05.md)

Após sua primeira implantação, você poderá fazer alterações em seu código, como adicionar outras funções do Python, e reimplantar no mesmo aplicativo do Azure Functions.

1. Na área **Azure: Funções**, no explorador, selecione o comando **Criar Função** ou use **Azure Functions: Criar Função** na Paleta de Comandos. Especifique os seguintes detalhes para a função:

    - Modelo: Gatilho HTTP
    - Nome: "DigitsOfPi"
    - Nível de autorização: Anônima

1. No explorador de arquivos do Visual Studio Code, é uma subpasta com o nome da função que, novamente, contém arquivos chamados *\_\_init\_\_.py*, *function.json* e *sample.dat*.

1. Substitua o conteúdo de *\_\_init\_\_.py* para corresponder ao código a seguir, que gera uma cadeia de caracteres contendo o valor de PI para um número de dígitos especificado na URL (este código usa apenas um parâmetro de URL)

    ```python
    import logging

    import azure.functions as func

    """ Adapted from the second, shorter solution at http://www.codecodex.com/wiki/Calculate_digits_of_pi#Python
    """

    def pi_digits_Python(digits):
        scale = 10000
        maxarr = int((digits / 4) * 14)
        arrinit = 2000
        carry = 0
        arr = [arrinit] * (maxarr + 1)
        output = ""

        for i in range(maxarr, 1, -14):
            total = 0
            for j in range(i, 0, -1):
                total = (total * j) + (scale * arr[j])
                arr[j] = total % ((j * 2) - 1)
                total = total / ((j * 2) - 1)

            output += "%04d" % (carry + (total / scale))
            carry = total % scale

        return output;

    def main(req: func.HttpRequest) -> func.HttpResponse:
        logging.info('DigitsOfPi HTTP trigger function processed a request.')

        digits_param = req.params.get('digits')

        if digits_param is not None:
            try:
                digits = int(digits_param)
            except ValueError:
                digits = 10   # A default

            if digits > 0:
                digit_string = pi_digits_Python(digits)

                # Insert a decimal point in the return value
                return func.HttpResponse(digit_string[:1] + '.' + digit_string[1:])

        return func.HttpResponse(
             "Please pass the URL parameter ?digits= to specify a positive number of digits.",
             status_code=400
        )
    ```

1. Como o código dá suporte apenas a HTTP GET, modifique *function.json* para que a coleção `"methods"` contenha apenas `"get"` (ou seja, remova `"post"`). O arquivo inteiro deve ter a seguinte aparência:

    ```json
    {
      "scriptFile": "__init__.py",
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
          ]
        },
        {
          "type": "http",
          "direction": "out",
          "name": "$return"
        }
      ]
    }
    ```

1. Inicie o depurador pressionando F5 ou selecionando o comando de menu **Depurar** > **Iniciar Depuração**. A janela **Saída** agora deve mostrar os dois pontos de extremidade em seu projeto:

    ```output
    Http Functions:

            DigitsOfPi: [GET] http://localhost:7071/api/DigitsOfPi

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ```

1. Em um navegador, ou na ondulação, faça uma solicitação para `http://localhost:7071/api/DigitsOfPi?digits=125` e observe a saída. (Você poderá observar que o algoritmo do código não é totalmente preciso, mas deixaremos os aprimoramentos para você!) Pare o depurador quando tiver terminado.

1. Reimplante o código usando **Implantar no Aplicativo de Funções** no explorador **Azure: Funções**. Se solicitado, selecione o Aplicativo de Funções criado anteriormente.

1. Após a conclusão da implantação (demora alguns minutos!), a janela **Saída** mostra os pontos de extremidade públicos com os quais você pode repetir seus testes.

> [!div class="nextstepaction"]
> [Adicionei uma segunda função](tutorial-vs-code-serverless-python-07.md)

[Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=vscode-functions-python&step=06-second-function)
