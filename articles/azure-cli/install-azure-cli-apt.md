---
title: Instalar a CLI do Azure no Linux com apt
description: Como instalar a CLI do Azure com o gerenciador de pacotes apt
author: dbradish-microsoft
ms.author: dbradish
manager: barbkess
ms.date: 10/14/2019
ms.topic: conceptual
ms.service: azure-cli
ms.devlang: azurecli
ms.openlocfilehash: 302b98717ee422de9bd60a57b18d900bcf5fcaf9
ms.sourcegitcommit: 36e02e96b955ed0531f98b9c0f623f4acb508661
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030968"
---
# <a name="install-azure-cli-with-apt"></a>Instalar CLI do Azure com o apt

Se você estiver executando uma distribuição fornecida com `apt`, como Ubuntu ou Debian, há um pacote x86_64 disponível para a CLI do Azure. Este pacote foi testado e é compatível com:

* Ubuntu trusty, xenial, artful, bionic e disco
* Debian wheezy, jessie, stretch e buster

[!INCLUDE [current-version](includes/current-version.md)]

> [!NOTE]
>
> O pacote para a CLI do Azure instala seu próprio interpretador de Python e não usa o Python do sistema.

## <a name="install"></a>Instalar

Oferecemos duas maneiras de instalar a CLI do Azure com distribuições que dão suporte a `apt`: Como um script tudo-em-um que executa os comandos de instalação para você, e instruções que podem ser executadas como um processo passo a passo por conta própria.

### <a name="install-with-one-command"></a>Instalar com um comando

Oferecemos e mantemos um script que executa todos os comandos de instalação em uma única etapa. Executá-lo usando `curl` e redirecione diretamente para `bash`, ou baixe o script para um arquivo e inspecione-o antes da execução.

> [!IMPORTANT]
> Esse script é verificado somente para o Ubuntu 16.04+ e Debian 8+. Ele pode não funcionar em outras distribuições.
> Se você estiver usando uma distribuição derivada como Linux Mint, siga as instruções de instalação manual e execute qualquer solução de problemas necessária.

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

### <a name="manual-install-instructions"></a>Instruções para instalação manual

Se você não quiser executar um script como superusuário ou se o script tudo-em-um falhar, siga estas etapas para instalar a CLI do Azure.

1. Obtenha os pacotes necessários para o processo de instalação:

    ```bash
    sudo apt-get update
    sudo apt-get install ca-certificates curl apt-transport-https lsb-release gnupg
    ```

2. Baixe e instale a chave de autenticação da Microsoft:

    ```bash
    curl -sL https://packages.microsoft.com/keys/microsoft.asc |
        gpg --dearmor |
        sudo tee /etc/apt/trusted.gpg.d/microsoft.asc.gpg > /dev/null
    ```

3. <div id="set-release"/>Adicione o repositório de software da CLI do Azure:

    ```bash
    AZ_REPO=$(lsb_release -cs)
    echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ $AZ_REPO main" |
        sudo tee /etc/apt/sources.list.d/azure-cli.list
    ```

4. Atualize as informações do repositório e instale o pacote `azure-cli`:

    ```bash
    sudo apt-get update
    sudo apt-get install azure-cli
    ```

Execute a CLI do Azure com o comando `az`. Para entrar, use o comando [az login](/cli/azure/reference-index#az-login).

[!INCLUDE [interactive-login](includes/interactive-login.md)]

Para saber mais sobre os diferentes métodos de autenticação, confira [Entrar com a CLI do Azure](authenticate-azure-cli.md).

## <a name="troubleshooting"></a>Solução de problemas

Aqui estão alguns problemas comuns vistos durante a instalação com `apt`. Se você tiver um problema não abordado aqui, [arquive um problema no github](https://github.com/Azure/azure-cli/issues).

### <a name="lsb_release-does-not-return-the-correct-base-distribution-version"></a>lsb_release não retorna a versão correta da distribuição de base

Algumas distribuições derivadas do Ubuntu ou do Debian, como o Linux Mint, podem não retornar o nome correto da versão de `lsb_release`. Esse valor é usado no processo de instalação para determinar o pacote de instalação. Se você souber o nome do código da versão do Ubuntu ou do Debian da qual sua distribuição é derivada, poderá definir o valor de `AZ_REPO` manualmente ao [adicionar o repositório](#set-release). Caso contrário, procure informações para sua distribuição sobre como determinar o nome do código da distribuição de base e defina `AZ_REPO` com o valor correto.

### <a name="no-package-for-your-distribution"></a>Nenhum pacote para distribuição

Às vezes, um pacote da CLI do Azure pode ser disponibilizado somente um tempo depois do lançamento da distribuição. A CLI do Azure é projetada para ser resiliente em relação a versões futuras das dependências e precisam da menor quantidade possível delas. Se não houver pacotes disponíveis para sua distribuição de base, tente um pacote de uma distribuição anterior.

Para isso, defina o valor de `AZ_REPO` manualmente ao [adicionar o repositório](#set-release). Para as distribuições do Ubuntu, use o repositório `bionic`; para as distribuições do Debian, use `stretch`. Não há suporte para as distribuições lançadas antes do Ubuntu Trusty e do Debian Wheezy.

### <a name="elementary-os-eos-fails-to-install-the-azure-cli"></a>O EOS (SO elementar) falha ao instalar a CLI do Azure

O EOS não consegue instalar a CLI do Azure porque `lsb_release` retorna `HERA`, que é o nome da versão de EOS.  A solução é corrigir o arquivo `/etc/apt/sources.list.d/azure-cli.list` e alterar `hera main` para `bionic main`.

Conteúdo do arquivo original:

```
deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ hera main
```

Conteúdo do arquivo modificado

```
deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ bionic main
```

### <a name="proxy-blocks-connection"></a>Conexão de blocos de proxy

[!INCLUDE[configure-proxy](includes/configure-proxy.md)]

Talvez você queira configurar explicitamente `apt` para usar esse proxy sempre. Verifique se as linhas a seguir aparecem em um arquivo de configuração `apt` em `/etc/apt/apt.conf.d/`. É recomendável usar o arquivo de configuração global existente, um arquivo de configuração de proxy existente, `40proxies` ou `99local`. No entanto, siga os requisitos de administração do sistema.

```apt.conf
Acquire {
    http::proxy "http://[username]:[password]@[proxy]:[port]";
    https::proxy "https://[username]:[password]@[proxy]:[port]";
}
```

Se o proxy não usar autenticação básica, __remova__ a parte `[username]:[password]@` do URI do proxy. Se você precisar de mais informações sobre a configuração do proxy, confira a documentação oficial do Ubuntu:

* [apt.conf manpage](http://manpages.ubuntu.com/manpages/bionic/en/man5/apt.conf.5.html)
* [Wiki do Ubuntu – apt-get howto](https://help.ubuntu.com/community/AptGet/Howto#Setting_up_apt-get_to_use_a_http-proxy)

Para obter a chave de assinatura da Microsoft e o pacote do nosso repositório, o proxy precisa permitir conexões HTTPS com o seguinte endereço:

* `https://packages.microsoft.com`

[!INCLUDE[troubleshoot-wsl.md](includes/troubleshoot-wsl.md)]

## <a name="update"></a>Atualizar

Use `apt-get upgrade` para atualizar o pacote da CLI.

   ```bash
   sudo apt-get update && sudo apt-get upgrade
   ```

> [!NOTE]
> Esse comando atualiza todos os pacotes instalados no sistema que não tiveram uma alteração de dependência.
> Para atualizar apenas a CLI, use `apt-get install`.
>
> ```bash
> sudo apt-get update && sudo apt-get install --only-upgrade -y azure-cli
> ```

## <a name="uninstall"></a>Desinstalar

[!INCLUDE [uninstall-boilerplate.md](includes/uninstall-boilerplate.md)]

1. Desinstalar com `apt-get remove`:

    ```bash
    sudo apt-get remove -y azure-cli
    ```

2. Se você não pretender reinstalar a CLI, remova as informações do repositório da CLI do Azure:

   ```bash
   sudo rm /etc/apt/sources.list.d/azure-cli.list
   ```

3. Se você não usa nenhum outro pacote da Microsoft, remova a chave de assinatura:

    ```bash
    sudo rm /etc/apt/trusted.gpg.d/microsoft.asc.gpg
    ```

4. Remova quaisquer pacotes desnecessários:

   ```bash
   sudo apt autoremove
   ```

## <a name="next-steps"></a>Próximas etapas

Agora que você instalou a CLI do Azure, faça um tour breve de seus recursos e comandos comuns.

> [!div class="nextstepaction"]
> [Introdução à CLI do Azure](get-started-with-azure-cli.md)
