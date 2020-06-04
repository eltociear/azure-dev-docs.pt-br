---
title: 'Etapa 4: Configurar um arquivo de inicialização personalizado para aplicativos Python no Serviço de Aplicativo do Azure no Linux'
description: Etapa 4 do tutorial, instruindo o Serviço de Aplicativo como iniciar o aplicativo Web, incluindo instruções específicas para Django, Flask e outras estruturas.
ms.topic: conceptual
ms.date: 05/19/2020
ms.custom: seo-python-october2019
ms.openlocfilehash: 1e724fa7d35c1b3488dbe6738a4840e20bbfbf96
ms.sourcegitcommit: efab6be74671ea4300162e0b30aa8ac134d3b0a9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84256441"
---
# <a name="4-configure-a-custom-startup-file-for-python-apps-on-azure-app-service"></a>4: Configurar um arquivo de inicialização personalizado para aplicativos Python no Serviço de Aplicativo do Azure

[Etapa anterior: criar o Serviço de Aplicativo](tutorial-deploy-app-service-on-linux-03.md)

Este artigo mostra que você precisa configurar um arquivo de inicialização personalizado para um aplicativo Python em um Serviço de Aplicativo do Azure.

Dependendo de como você estruturou seu aplicativo, talvez seja necessário criar um arquivo de comando de inicialização personalizado para ele, conforme descrito em [Configurar aplicativos Python para o Serviço de Aplicativo no Linux](https://docs.microsoft.com/azure/app-service/containers/how-to-configure-python) nos documentos do Azure.

Os casos de uso específicos de um comando de inicialização personalizado são os seguintes:

- Você tem um aplicativo Flask cujo arquivo de inicialização e o objeto do aplicativo têm nomes **diferentes** de *application.py* e `app`, respectivamente. Em outras palavras, a menos que você tenha *application.py* na pasta raiz de seu projeto *e* que o objeto do aplicativo Flask tenha o nome `app`, você precisará de um comando de inicialização personalizado.
- Você deseja iniciar o servidor Web Gunicorn com argumentos adicionais além dos padrões, que são `--bind=0.0.0.0 --timeout 600`.
- Seu aplicativo é criado com uma estrutura diferente de Flask ou Django, ou você deseja usar um servidor Web diferente. Nesse caso, você precisa personalizar o comando de inicialização.

## <a name="create-a-startup-file"></a>Criar um arquivo de inicialização

Se precisar de um arquivo de inicialização personalizado, use as seguintes etapas:

1. Crie em seu projeto um arquivo chamado *startup.txt*, *startup.sh* (ou outro nome de sua escolha) que contenha os comandos de inicialização. Consulte as seções adiante neste artigo para ver informações específicas sobre Django, Flask e outras estruturas.

    Um arquivo de inicialização pode incluir vários comandos, se necessário.

1. Confirme o arquivo em seu repositório de código para que ele possa ser implantado com o restante do aplicativo.

1. Na área **Azure: Serviço de Aplicativo**, no explorador, expanda o Serviço de Aplicativo, clique com o botão direito do mouse em **Configurações do Aplicativo** e selecione **Abrir no Portal**:

    ![Abrir Configurações de Aplicativo no Portal no gerenciador do Serviço de Aplicativo](media/deploy-azure/open-application-settings-in-portal-for-app-service.png)

1. No portal do Azure, entre se necessário. Em seguida, na página **Configuração**, selecione **Configurações gerais**, digite o nome do arquivo de inicialização (como *startup.txt* ou *startup.sh*) em **Configurações da pilha** > **Comando de inicialização** e depois selecione **Salvar**.

    ![Configurar o nome do arquivo do comando de inicialização no portal do Azure](media/deploy-azure/enter-startup-file-for-app-service-in-the-azure-portal.png)

    > [!NOTE]
    > Em vez de usar um arquivo de comando de inicialização, você também pode colocar o comando de inicialização diretamente no campo **Comando de Inicialização** no portal do Azure. No entanto, normalmente é preferível usar um arquivo, pois isso mantém essa parte da configuração em seu repositório, onde você pode auditar as alterações e reimplantar em uma instância do Serviço de Aplicativo diferente.

1. O Serviço de Aplicativo é reiniciado quando você salva as alterações. Entretanto, como você ainda não implantou o código do aplicativo, visitar o site neste ponto mostra "Erro do Aplicativo". Essa mensagem indica que o servidor Gunicorn foi iniciado, mas não localizou o aplicativo e, portanto, nada está respondendo às solicitações HTTP. Você implantará o código do aplicativo na próxima etapa.

Você também pode especificar um comando de inicialização com o comando [`az webapp create` da CLI do Azure](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) ao usar o argumento `--startup-file`.

## <a name="django-startup-commands"></a>Comandos de inicialização do Django

Por padrão, o Serviço de Aplicativo localiza automaticamente a pasta que contém o arquivo *wsgi.py* e inicia o Gunicorn com o seguinte comando:

```cmd
# <module> is the path to the folder that contains wsgi.py
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

Se quiser alterar algum dos argumentos do Gunicorn, por exemplo, usar `--timeout 1200`, crie um arquivo de comando com essas modificações.

## <a name="flask-startup-commands"></a>Comandos de inicialização do Flask

Por padrão, o contêiner do Serviço de Aplicativo do Linux pressupõe que um callable do WSGI do aplicativo Flask tenha o nome `app`, esteja contido em um arquivo com nome *application.py* ou *app.py* e esteja no diretório raiz do app.

Se seu aplicativo não estiver estruturado exatamente dessa forma, o comando de inicialização personalizado precisará identificar o local do objeto do aplicativo no formato *file:app_object*:

1. **Nome do arquivo e/ou nome de objeto de aplicativo diferente**: por exemplo, se o arquivo de inicialização do aplicativo for *hello.py* e o objeto de aplicativo tiver o nome `myapp`, o comando de inicialização será o seguinte:

    ```text
    gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
    ```

1. **O arquivo de inicialização está em uma subpasta**: por exemplo, se o arquivo de inicialização for *myapp/website.py* e o objeto de aplicativo for `app`, use o argumento `--chdir` do Gunicorn para especificar a pasta e, em seguida, nomeie o arquivo de inicialização e o objeto de aplicativo como de costume:

    ```text
    gunicorn --bind=0.0.0.0 --timeout 600 --chdir myapp website:app
    ```

1. **O arquivo de inicialização está dentro de um módulo**: no código [python-sample-vscode-flask-tutorial](https://github.com/Microsoft/python-sample-vscode-flask-tutorial), o arquivo de inicialização *webapp.py* está contido na pasta *hello_app*, que é um módulo com um arquivo *\_\_init\_\_.py*. O objeto de aplicativo tem o nome `app` e está definido em *\_\_init\_\_.py* e *webapp.py* usa uma importação relativa. Devido a essa disposição, apontar o Gunicorn para `webapp:app` produz o erro "Tentativa de importação relativa sem pacote" e o aplicativo não é iniciado.

    Nessa situação, crie um arquivo shim simples que importa o objeto de aplicativo do módulo e, em seguida, faça com que o Gunicorn inicie o aplicativo usando o shim. O código [python-sample-vscode-flask-tutorial](https://github.com/Microsoft/python-sample-vscode-flask-tutorial), por exemplo, contém *startup.py* com o seguinte conteúdo:

    ```python
    # startup.py shim
    from hello_app.webapp import app
    ```

    O comando de inicialização é o seguinte:

    ```text
    gunicorn --bind=0.0.0.0 --timeout 600 startup:app
    ```

## <a name="startup-commands-for-other-frameworks-and-web-servers"></a>Comandos de inicialização para outras estruturas e servidores Web

O contêiner do Serviço de Aplicativo que executa aplicativos Python tem o Django e o Flask instalados por padrão, juntamente com o servidor Web gunicorn.

Para usar uma estrutura diferente do Django ou Flask (como Falcon, FastAPI, etc.) ou para usar um servidor Web diferente, faça o seguinte:

- Inclua a estrutura e/ou o servidor Web no arquivo *requirements. txt*.
- No comando de inicialização, identifique o callable da WSGI, conforme descrito na [seção anterior para Flask](#flask-startup-commands).
- Para iniciar um servidor Web diferente do gunicorn, use um comando `python -m`, em vez de invocar o servidor diretamente. Por exemplo, o comando a seguir inicia o servidor uvicorn, supondo que o callable da WSGI tenha o nome `app` e esteja em *application.py*:

    ```sh
    python -m uvicorn application:app --host 0.0.0.0
    ```

    Usa-se `python -m` porque os servidores Web instalados via *requirements. txt* não são adicionados ao ambiente global do Python e, portanto, não podem ser invocados diretamente. O comando `python -m` invoca o servidor de dentro do ambiente virtual atual.

> [!div class="nextstepaction"]
> [Configurei meu arquivo de inicialização – prossiga para a etapa 5 >>>](tutorial-deploy-app-service-on-linux-05.md)

[Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=vscode-appservice-python&step=04-startup-command)
