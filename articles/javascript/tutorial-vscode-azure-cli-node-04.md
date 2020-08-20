---
title: Implantar o código de aplicativo no Serviço de Aplicativo do Azure usando a CLI do Azure
description: Parte 4 do tutorial, implantar o site
ms.topic: conceptual
ms.date: 09/24/2019
ms.custom: devx-track-javascript
ms.openlocfilehash: e794388280c537261e428195ac3943379f04866b
ms.sourcegitcommit: 0699b984b85782b1c441289fa756f285eae853c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88217940"
---
# <a name="deploy-the-app-to-app-service"></a>Implantar o aplicativo no Serviço de Aplicativo

[Etapa anterior: Criar o Serviço de Aplicativo](tutorial-vscode-azure-cli-node-03.md)

Nesta etapa, você implanta o código do aplicativo Node.js para o Serviço de Aplicativo do Azure usando um processo básico de enviar seu repositório Git local para o Azure.

1. Em um terminal ou prompt de comando, execute os comandos a seguir para inicializar um repositório Git local e fazer uma confirmação inicial. (A pasta *node_modules* é ignorada porque é especificada no arquivo *.gitignore* criado quando você executou o Gerador Expresso anteriormente.)

    ```bash
    git init
    git add -A
    git commit -m "Initial Commit"
    ```

1. Execute os comandos a seguir para configurar as credenciais de implantação no Azure, substituindo `username` e `pPassword` por suas credenciais. O comando exibe a saída JSON quando há sucesso.

    ```azurecli
    az webapp deployment user set --user-name <username> --password <password>
    ```

1. Execute o comando a seguir para recuperar o ponto de extremidade Git para o qual desejamos enviar por push o código do aplicativo, substituindo `<your_app_name>` pelo nome usado ao criar o Serviço de Aplicativo na etapa anterior:

    ```azurecli
    az webapp deployment source config-local-git --name <your_app_name>
    ```

    A saída do comando é semelhante à seguinte:

    <pre>
    {
      "url": "https://username@msdocs-node-cli.scm.azurewebsites.net/msdocs-node-cli.git"
    }
    </pre>

1. Execute o comando a seguir para definir um novo remoto no git chamado `azure`, usando a URL da etapa anterior *omitindo o nome de usuário*. Usando o exemplo na etapa anterior, o comando seria o seguinte:

    ```bash
    git remote add azure https://msdocs-node-cli.scm.azurewebsites.net/msdocs-node-cli.git
    ```

1. Execute o comando a seguir para implantar o código do aplicativo do repositório Git para o Serviço de Aplicativo. O comando solicita suas credenciais:

    ```bash
    git push azure master
    ```

1. À medida que o comando é executado, ele exibe uma série de mensagens do host remoto. Quando o processo estiver concluído, atualize o navegador no qual você tem a URL do aplicativo aberta para ver o código em execução:

    ![Código do aplicativo em execução no Azure](media/azure-cli/remote-app.png)

> [!TIP]
> Se você encontrar o erro `Object #<eventemitter> has no method 'hrtime'`, provavelmente precisará definir a versão de runtime do nó no site. O comando a seguir diz para o site usar a versão do nó `6.9.1`. Se seu site exigir uma versão diferente ou posterior do nó, especifique a versão semântica completa `major.minor.patch`.
>
> ```azurecli
> az webapp config appsettings set --name <your_app_name> --settings
> ```

> [!div class="nextstepaction"]
> [Implantei o aplicativo](tutorial-vscode-azure-cli-node-05.md) [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment&step=deploy-website)
