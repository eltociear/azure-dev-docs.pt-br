---
title: Tutorial – Criar um modelo de base do Terraform no Azure usando o Yeoman
description: Saiba como criar um modelo de base do Terraform no Azure usando o Yeoman.
ms.topic: tutorial
ms.date: 04/25/2020
ms.openlocfilehash: ed030d6685666e49a01b4a7d1dded1981012d778
ms.sourcegitcommit: be67ceba91727da014879d16bbbbc19756ee22e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82171412"
---
# <a name="tutorial-create-a-terraform-base-template-in-azure-using-yeoman"></a>Tutorial: Criar um modelo de base do Terraform no Azure usando o Yeoman

Neste tutorial, você aprenderá a usar a combinação de [Terraform](/azure/terraform/) e de [Yeoman](https://yeoman.io/). Terraform é uma ferramenta para a criação de infraestrutura no Azure. O Yeoman facilita a criação de módulos do Terraform.

Neste artigo, você aprende a realizar as seguintes tarefas:
> [!div class="checklist"]
> * Criar um modelo de base do Terraform usando o gerador de módulo do Yeoman.
> * Testar o modelo do Terraform usando dois métodos diferentes.
> * Executar o módulo do Terraform usando um arquivo do Docker.
> * Executar o módulo do Terraform nativamente no Azure Cloud Shell.

[!INCLUDE [hashicorp-support.md](includes/hashicorp-support.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../includes/open-source-devops-prereqs-azure-subscription.md)]
- **Visual Studio Code**: [Baixar o Visual Studio Code](https://code.visualstudio.com/download) para sua plataforma.
- **Terraform**: [Instalar o Terraform](install-configure.md) para executar o módulo criado pelo Yeoman.
- **Docker**: [Instalar o Docker](https://www.docker.com/get-started) para executar o módulo criado pelo gerador do Yeoman.
- **Linguagem de programação Go**: [Instale o Go](https://golang.org/) como casos de teste gerados pelo Yeoman que são o código que usa a linguagem Go.

>[!NOTE]
>A maioria dos procedimentos neste tutorial envolve interface de linha de comando. As etapas descritas se aplicam a todos os sistemas operacionais e as ferramentas de linha de comando. Nos exemplos, o PowerShell foi escolhido para o ambiente loca e Git Bash para o ambiente do cloud shell.

## <a name="prepare-your-environment"></a>Prepare o seu ambiente

### <a name="install-nodejs"></a>Instalar o Node. js

Para usar o Terraform no Cloud Shell, você precisará [instalar o Node.js](https://nodejs.org/en/download/) 6.0+.

>[!NOTE]
>Para verificar se o Node.js está instalado, abra uma janela do terminal e insira `node --version`.

### <a name="install-yeoman"></a>Instalar o Yeoman

Execute o comando a seguir:

```bash
npm install -g yo
```

![Instalar o Yeoman](media/create-a-base-template-using-yeoman/ymg-npm-install-yo.png)

### <a name="install-the-yeoman-template-for-terraform-module"></a>Instalar o modelo do Yeoman para o módulo do Terraform

Execute o comando a seguir:

```bash
npm install -g generator-az-terra-module
```

![Install generator-az-terra-module](media/create-a-base-template-using-yeoman/ymg-pm-install-generator-module.png)

Para verificar se o Yeoman está instalado, execute o seguinte comando:

```bash
yo --version
```

### <a name="create-a-directory-for-the-yeoman-generated-module"></a>Criar um diretório para o módulo gerado pelo Yeoman

O modelo do Yeoman gera arquivos no diretório atual. Por esse motivo, você precisará criar um diretório.

Esse diretório vazio precisa ser colocado em $GOPATH/src. Para obter mais informações sobre esse caminho, consulte o artigo [Setting GOPATH](https://github.com/golang/go/wiki/SettingGOPATH) (Configuração de GOPATH).

1. Navegue até o diretório pai do qual criar um diretório.

1. Execute o seguinte comando, substituindo o espaço reservado. Para este exemplo, é usado um nome de diretório de `GeneratorDocSample`.

    ```bash
    mkdir <new-directory-name>
    ```

    ![mkdir](media/create-a-base-template-using-yeoman/ymg-mkdir-GeneratorDocSample.png)

1. Navegue até o novo diretório:

    ```bash
    cd <new-directory-name>
    ```

    ![Navegue até o novo diretório](media/create-a-base-template-using-yeoman/ymg-cd-GeneratorDocSample.png)

## <a name="create-a-base-module-template"></a>Criar um modelo de base do módulo

1. Execute o comando a seguir:

    ```bash
    yo az-terra-module
    ```

1. Siga as instruções na tela para fornecer as seguintes informações:

    - **Nome do projeto de módulo do Terraform** – Um valor de `doc-sample-module` é usado para o exemplo.

        ![Nome do projeto](media/create-a-base-template-using-yeoman/ymg-project-name.png)


    - **Você gostaria de incluir o arquivo de imagem do Docker?** – Digite `y`. Se você inserir `n`, o código de módulo gerado dará suporte para execução somente no modo nativo.

        ![Incluir arquivo de imagem do Docker?](media/create-a-base-template-using-yeoman/ymg-include-docker-image-file.png) 

1. Insira o conteúdo do diretório para exibir os arquivos resultantes que forem criados:

    ```bash
    ls
    ```

    ![Listar arquivos criados](media/create-a-base-template-using-yeoman/ymg-ls-GeneratorDocSample-files.png)

## <a name="review-the-generated-module-code"></a>Rever o código do módulo gerado

1. Iniciar o Visual Studio Code

1. Na barra de menus, selecione **Arquivo > Abrir Pasta** e selecione a pasta criada.

    ![Visual Studio Code](media/create-a-base-template-using-yeoman/ymg-open-in-vscode.png)

Os seguintes arquivos foram criados pelo gerador de módulo do Yeoman:

- `main.tf` – Define um módulo chamado `random-shuffle`. A entrada é uma `string_list`. A saída é a contagem de permutações.
- `variables.tf` – Define as variáveis de entrada e saída usadas pelo módulo.
- `outputs.tf` – Define o que o módulo gera. Aqui, ele é o valor retornado por `random_shuffle`, que é um módulo interno do Terraform.
- `Rakefile` – Define as etapas de compilação. As etapas incluem:
    - `build` – Valida a formatação do arquivo main.tf.
    - `unit` – O esqueleto do módulo gerado não inclui o código para um teste de unidade. Se você quiser especificar um cenário de teste de unidade, adicione o código aqui.
    - `e2e` – Executa um teste de ponta a ponta no módulo.
- `test`
    - Os casos de teste são escritos em linguagem Go.
    - Todos os códigos de teste são testes de ponta a ponta.
    - Os testes de ponta a ponta tentam provisionar todos os itens definidos em `fixture`. Os resultados no arquivo `template_output.go` são comparados com os valores predefinidos esperados.
    - `Gopkg.lock` e `Gopkg.toml`: Define as dependências. 

Para obter mais informações sobre o gerador do Yeoman para o Azure https://github.com/Azure/generator-az-terra-module, confira a [documentação do Terratest](https://terratest.gruntwork.io/docs/).

## <a name="test-your-new-terraform-module-using-a-docker-file"></a>Testar o novo módulo do Terraform usando um arquivo do Docker

Esta seção mostra como testar um módulo do Terraform usando um arquivo do Docker.

>[!NOTE]
>Este exemplo executa o módulo localmente, não no Azure.

### <a name="confirm-docker-is-installed-and-running"></a>Confirmar se o Docker está instalado e em execução

Em um prompt de comando, insira `docker version`.

![Versão do Docker](media/create-a-base-template-using-yeoman/ymg-docker-version.png)

A saída resultante confirma que o Docker está instalado.

Para confirmar que o Docker está realmente em execução, digite `docker info`.

![Informações do Docker](media/create-a-base-template-using-yeoman/ymg-docker-info.png)

### <a name="set-up-a-docker-container"></a>Configurar um contêiner do Docker

1. Em um prompt de comando, insira

    `docker build --build-arg BUILD_ARM_SUBSCRIPTION_ID= --build-arg BUILD_ARM_CLIENT_ID= --build-arg BUILD_ARM_CLIENT_SECRET= --build-arg BUILD_ARM_TENANT_ID= -t terra-mod-example .`.

    A mensagem **Criado com êxito** será exibida.

    ![Mensagem indicando uma compilação bem-sucedida](media/create-a-base-template-using-yeoman/ymg-successfully-built.png)

1. No prompt de comando, digite `docker image ls` para ver o módulo criado `terra-mod-example` listado.

    ![Lista que contém o novo módulo](media/create-a-base-template-using-yeoman/ymg-repository-results.png)

1. Digite `docker run -it terra-mod-example /bin/sh`. Depois de executar o comando `docker run`, você estará no ambiente do Docker. Nesse ponto, você pode descobrir o arquivo usando o comando `ls`.

    ![Lista de arquivos no Docker](media/create-a-base-template-using-yeoman/ymg-list-docker-file.png)

### <a name="build-the-module"></a>Criar o módulo

1. Execute o comando a seguir:

    ```bash
    bundle install
    ```

1. Execute o comando a seguir:

    ```bash
    rake build
    ```

    ![Build do Rake](media/create-a-base-template-using-yeoman/ymg-rake-build.png)

### <a name="run-the-end-to-end-test"></a>Executar o teste de ponta a ponta

1. Execute o comando a seguir:

    ```bash
    rake e2e
    ```

1. Após alguns instantes, a mensagem **APROVADO** será exibida.

    ![PASS](media/create-a-base-template-using-yeoman/ymg-pass.png)

1. Insira `exit` para concluir o teste e sair do ambiente do Docker.

## <a name="use-yeoman-generator-to-create-and-test-a-module-in-cloud-shell"></a>Usar o gerador do Yeoman para criar e testar um módulo no Cloud Shell

Nesta seção, o gerador Yeoman é usado para criar e testar um módulo no Cloud Shell. Usar o Cloud Shell em vez de usar um arquivo do Docker simplifica bastante o processo. Usando o Cloud Shell, os seguintes produtos são pré-instalados:

- Node.js
- Yeoman
- Terraform

### <a name="start-a-cloud-shell-session"></a>Iniciar uma sessão do Cloud Shell

1. Inicie uma sessão do Azure Cloud Shell por meio do [portal do Azure](https://portal.azure.com/), de [shell.azure.com](https://shell.azure.com) ou do [aplicativo móvel do Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/).

1. **A página Boas-vindas ao Azure Cloud Shell** é aberta. Selecione **Bash (Linux)** .

    ![Bem-vindo ao Azure Cloud Shell](media/create-a-base-template-using-yeoman/ymg-welcome-to-azure-cloud-shell.png)

1. Se você ainda não configurou uma conta de armazenamento do Azure, a tela a seguir será exibida. Selecione **Criar armazenamento**.

    ![Você não tem nenhum armazenamento montado](media/create-a-base-template-using-yeoman/ymg-you-have-no-storage-mounted.png)

1. O Azure Cloud Shell é iniciado no shell que você selecionou anteriormente e exibe informações para a unidade de nuvem que acabou de ser criada.

    ![Sua unidade de nuvem foi criada](media/create-a-base-template-using-yeoman/ymg-your-cloud-drive-has-been-created-in.png)

### <a name="prepare-a-directory-to-hold-your-terraform-module"></a>Preparar um diretório para armazenar seu módulo do Terraform

1. Neste ponto, o Cloud Shell já terá configurado o GOPATH em suas variáveis de ambiente. Para ver o caminho, insira `go env`.

1. Crie o diretório $GOPATH, se ele ainda não existir: Digite `mkdir ~/go`.

1. Crie um diretório dentro do diretório $GOPATH. Esse diretório é usado para manter os diferentes diretórios de projeto criados neste exemplo. 

    ```bash
    mkdir ~/go/src
    ```

1. Crie um diretório para manter o seu módulo do Terraform substituindo o espaço reservado. Para este exemplo, é usado um nome de diretório de `my-module-name`.

    ```bash
    mkdir ~/go/src/<your-module-name>
    ```

1. Navegue até o diretório do módulo: 

    ```bash
    cd ~/go/src/<your-module-name>
    ```

### <a name="create-and-test-your-terraform-module"></a>Criar e testar seu módulo do Terraform

1. Execute o comando a seguir e siga as instruções. Quando perguntado se deseja criar os arquivos do Docker, insira `N`.

    ```bash
    yo az-terra-module
    ```

1. Execute o comando a seguir para instalar as dependências:

    ```bash
    bundle install
    ```

1. Execute o comando a seguir para compilar o módulo:

    ```bash
    rake build
    ```

    ![Build do Rake](media/create-a-base-template-using-yeoman/ymg-rake-build.png)

1. Execute o comando a seguir para executar o texto:

    ```bash
    rake e2e
    ```

    ![Resultados de aprovação de teste](media/create-a-base-template-using-yeoman/ymg-pass.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Instalar e usar a extensão Visual Studio Code do Azure Terraform](/azure/terraform/terraform-vscode-extension).