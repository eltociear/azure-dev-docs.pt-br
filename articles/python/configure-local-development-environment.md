---
title: Configurar o ambiente do Python local para o Azure
description: Como configurar um ambiente de desenvolvimento Python local para trabalhar com o Azure, incluindo Visual Studio Code, as bibliotecas do SDK do Azure e as credenciais necessárias para a autenticação de biblioteca.
ms.date: 05/29/2020
ms.topic: conceptual
ms.openlocfilehash: cf87c90bd36594ffa4e1f3837133238f89a77836
ms.sourcegitcommit: 43e4b50f6f6f5806b2f162ca39367face0779ff6
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2020
ms.locfileid: "84421492"
---
# <a name="configure-your-local-python-dev-environment-for-azure"></a>Configurar o ambiente de desenvolvimento do Python local para o Azure

Ao criar aplicativos de nuvem, os desenvolvedores normalmente preferem testar o código em suas estações de trabalho locais antes de implantar esse código em um ambiente de nuvem como o Azure. O desenvolvimento local oferece a vantagem da velocidade e uma variedade maior de ferramentas de depuração.

Este artigo fornece as instruções de configuração única para criar e validar um ambiente de desenvolvimento local adequado para o Python no Azure:

- [Instale os componentes necessários](#required-components), ou seja, uma conta do Azure, Python e a CLI do Azure.
- [Configure a autenticação](#configure-authentication) para quando você usar bibliotecas do Azure para provisionar, gerenciar e acessar recursos do Azure.
- Examine o processo de [uso de ambientes virtuais Python](#use-python-virtual-environments) para cada um de seus projetos.

Depois de configurar sua estação de trabalho, você precisará apenas da configuração mínima adicionada para concluir vários guias de início rápido e tutoriais em outro lugar neste centro de desenvolvedores e na documentação do Azure.

Essa configuração para o desenvolvimento local é uma questão separada dos [recursos de provisionamento](cloud-development-flow.md) que compõem o *ambiente de nuvem* do seu aplicativo no Azure. No seu processo de desenvolvimento, você executa o código no ambiente de desenvolvimento local que pode acessar esses recursos de nuvem, mas o código ainda não foi implantado em um [serviço de hospedagem adequado](quickstarts-app-hosting.md) na nuvem. Essa etapa de implantação vem depois, conforme descrito no artigo [Fluxo de desenvolvimento do Azure](cloud-development-flow.md).

## <a name="install-components"></a>Instalar componentes

### <a name="required-components"></a>Componentes necessários

| Nome/instalador | Descrição |
| --- | --- |
| [Conta do Azure com uma assinatura ativa](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=python-dev-center&mktingSource=environment-setup) | Contas/assinaturas são gratuitas e incluem muitos serviços gratuitos para uso. |
| [Python 2.7+ ou 3.5.3+](https://www.python.org/downloads) | A linguagem comum do Python Recomendamos a versão mais recente do Python 3.x, a menos que você tenha requisitos de versão específicos. |
| [CLI (interface de linha de comando) do Azure](/cli/azure/install-azure-cli) | Fornece um pacote completo de comandos da CLI para provisionar e gerenciar recursos do Azure. Os desenvolvedores do Python normalmente usam a CLI do Azure com scripts Python personalizados que usam as bibliotecas de gerenciamento do Azure. |

Observações:

- Você instala pacotes de bibliotecas individuais do Azure em uma base por projeto, dependendo de suas necessidades. Recomendamos [usar ambientes virtuais Python](#use-python-virtual-environments) para cada projeto. Não há nenhum instalador "SDK" independente para Python.
- Embora Azure PowerShell geralmente seja equivalente à CLI do Azure, recomendamos a CLI do Azure ao trabalhar com o Python.

### <a name="recommended-components"></a>Componentes recomendados

| Nome/instalador | Descrição |
| --- | --- |
| [Visual Studio Code](https://code.visualstudio.com) | Embora você possa trabalhar com qualquer editor ou IDE adequado, o IDE leve e gratuito da Microsoft é muito popular entre os desenvolvedores do Python. Para obter uma introdução, consulte [Python no VS Code](https://code.visualstudio.com/docs/python/python-tutorial). |
| [Extensão do Python para VS Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python) | Adiciona o suporte do Python ao VS Code. |
| [Extensão do Azure para VS Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) | Adiciona suporte para uma variedade de serviços do Azure ao VS Code. O suporte para serviços específicos também pode ser instalado individualmente. |
| [git](https://git-scm.com/downloads) | Ferramentas de linha de comando para controle do código-fonte. Você pode usar diferentes ferramentas de controle do código-fonte se preferir. |

### <a name="optional-components"></a>Componentes opcionais

| Nome/instalador | Descrição |
| --- | --- |
| [Extensão do Docker para VS Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python) | Adiciona suporte do Docker ao VS Code, o que é útil se você trabalha regularmente com contêineres. |

### <a name="verify-components"></a>Verificar componentes

1. Abra um terminal ou prompt de comando.
1. Verifique sua versão do Python ao executar o comando `python --version`.
1. Execute `az --version` para verificar a versão da CLI do Azure.
1. Verifique a instalação do VS Code:
    1. Execute `code .` para abrir o VS Code na pasta atual.
    1. No VS Code, selecione o comando **Exibir** > **Extensões** para abrir a exibição de extensões e, em seguida, verifique se você vê "Python" e "Conta do Azure" na lista (entre outras extensões do "Azure", e o "Docker" se você instalou essa extensão também).

## <a name="sign-in-to-azure-from-the-cli"></a>Entrar no Azure por com a CLI

Em um terminal ou prompt de comando, entre na sua assinatura do Azure:

```azurecli
az login
```

O comando `az` é o comando raiz da CLI do Azure. Após `az`, está um ou mais comandos específicos, como `login`. Consulte a referência do comando [az login](/cli/azure/authenticate-azure-cli).

A CLI do Azure normalmente mantém suas credenciais entre as sessões, mas é uma boa prática executar `az login` sempre que você abrir um novo terminal ou um prompt de comando.

## <a name="configure-authentication"></a>Configurar autenticação

Conforme descrito em [Como gerenciar entidades de serviço - Noções básicas de autorização](how-to-manage-service-principals.md#basics-of-azure-authorization), cada desenvolvedor precisa de uma entidade de serviço para usar como a identidade do aplicativo ao testar o código do aplicativo localmente.

As seções a seguir descrevem como criar uma entidade de serviço e as variáveis de ambiente que fornecem as propriedades da entidade de serviço para as bibliotecas do Azure quando necessário.

Cada desenvolvedor em sua organização deve executar essas etapas individualmente.

### <a name="create-a-service-principal-and-environment-variables-for-development"></a>Criar uma entidade de serviço e variáveis de ambiente para desenvolvimento

1. Abra um terminal ou prompt de comando no qual você entrou na CLI do Azure (`az login`).

1. Criar a entidade de serviço:

    ```azurecli
    az ad sp create-for-rbac --name localtest-sp-rbac --skip-assignment --sdk-auth > local-sp.json
    ```

    Esse comando salva a saída no *local-sp.json*. Para obter mais detalhes sobre o comando e seus argumentos, consulte [O que o comando create-for-rbac faz](#what-the-create-for-rbac-command-does).

    Se você estiver em uma organização, talvez não tenha permissão na assinatura para executar esse comando. Nesse caso, entre em contato com os proprietários da assinatura para que eles criem a entidade de serviço para você.

1. Crie variáveis de ambiente que as bibliotecas do Azure exigem. (O objeto `DefaultAzureCredential` da biblioteca do azure-identity procura essas variáveis).

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```cmd
    set AZURE_SUBSCRIPTION_ID="aa11bb33-cc77-dd88-ee99-0918273645aa"
    set AZURE_TENANT_ID=00112233-7777-8888-9999-aabbccddeeff
    set AZURE_CLIENT_ID=12345678-1111-2222-3333-1234567890ab
    set AZURE_CLIENT_SECRET=abcdef00-4444-5555-6666-1234567890ab
    ```

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    AZURE_SUBSCRIPTION_ID="aa11bb33-cc77-dd88-ee99-0918273645aa"
    AZURE_TENANT_ID="00112233-7777-8888-9999-aabbccddeeff"
    AZURE_CLIENT_ID="12345678-1111-2222-3333-1234567890ab"
    AZURE_CLIENT_SECRET="abcdef00-4444-5555-6666-1234567890ab"
    ```

    ---

    Substitua os valores mostrados nesses comandos pelos de sua entidade de serviço específica.

    Para recuperar sua ID de assinatura, execute o comando [`az account show`](/cli/azure/account?view=azure-cli-latest#az-account-show) e procure a propriedade `id` na saída.

    Para sua conveniência, crie um arquivo *.sh* ou *.cmd* com estes comandos que podem ser executados sempre que você abrir um terminal ou um prompt de comando para testes locais. Novamente, não adicione o arquivo ao controle do código-fonte para que ele permaneça somente dentro de sua conta de usuário.

1. Proteja a ID do cliente e o segredo do cliente (e os arquivos que os armazenam) para que eles sempre permaneçam dentro de uma conta de usuário específica em uma estação de trabalho. Nunca salve essas propriedades no controle do código-fonte ou compartilhe-as com outros desenvolvedores. Se necessário, você pode excluir a entidade de serviço e criar uma nova.

    Para obter uma camada adicional de segurança, você pode fazer uma política para excluir e recriar entidades de serviço em um agendamento regular, invalidando, assim, IDs e segredos anteriores.

    Além disso, uma entidade de serviço de desenvolvimento é idealmente autorizada apenas para recursos de não produção ou é criada dentro de uma assinatura do Azure usada somente para fins de desenvolvimento. O aplicativo de produção usaria uma assinatura separada e recursos de produção separados que são autorizados apenas para o aplicativo de nuvem implantado.

1. Para modificar ou excluir as entidades de serviço posteriormente, consulte [Como gerenciar entidades de serviço](how-to-manage-service-principals.md).

#### <a name="what-the-create-for-rbac-command-does"></a>O que o comando create-for-rbac faz

O comando `az ad create-for-rbac` cria uma entidade de serviço para "autenticação baseada em função" (RBAC).

- `ad` significa Azure Active Directory; `sp` significa "entidade de serviço", e `create-for-rbac` significa "criar para controle de acesso baseado em função", a forma principal de autorização do Azure. Consulte a referência de comando [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac).

- O argumento `--name` deve ser exclusivo na sua organização e geralmente usa o nome do desenvolvedor que usa a entidade de serviço. Se você omitir esse argumento, a CLI do Azure usará um nome genérico do formulário `azure-cli-<timestamp>`. Você pode renomear a entidade de serviço no portal do Azure, se desejar.

- O argumento `--skip-assignment` cria uma entidade de serviço sem permissões padrão. Em seguida, atribua permissões específicas à entidade de serviço para permitir que o código de execução local acesse os recursos. Diferentes guias de início rápido e tutoriais fornecem detalhes para autorizar uma entidade de serviço nos recursos envolvidos.

- O comando fornece a saída JSON, a qual, no exemplo, é salva em um arquivo chamado *local-sp.json*.

- O argumento `--sdk-auth` gera uma saída JSON semelhante aos valores a seguir. Seus valores de ID e o segredo serão diferentes):

    <pre>
    {
      "clientId": "12345678-1111-2222-3333-1234567890ab",
      "clientSecret": "abcdef00-4444-5555-6666-1234567890ab",
      "subscriptionId": "00000000-0000-0000-0000-000000000000",
      "tenantId": "00112233-7777-8888-9999-aabbccddeeff",
      "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
      "resourceManagerEndpointUrl": "https://management.azure.com/",
      "activeDirectoryGraphResourceId": "https://graph.windows.net/",
      "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
      "galleryEndpointUrl": "https://gallery.azure.com/",
      "managementEndpointUrl": "https://management.core.windows.net/"
    }
    </pre>

    Sem o argumento `--sdk-auth`, o comando gera uma saída mais simples:

    <pre>
    {
      "appId": "12345678-1111-2222-3333-1234567890ab",
      "displayName": "localtest-sp-rbac",
      "name": "http://localtest-sp-rbac",
      "password": "abcdef00-4444-5555-6666-1234567890ab",
      "tenant": "00112233-7777-8888-9999-aabbccddeeff"
    }
    </pre>

    Nesse caso, `tenant` é a ID do locatário, `appId` é a ID do cliente e `password` é o segredo do cliente.

    > [!IMPORTANT]
    > A saída desse comando é o único local em que você verá o segredo do cliente/senha. Você não poderá recuperar o segredo/senha posteriormente. No entanto, você poderá adicionar um novo segredo, se necessário, sem invalidar a entidade de serviço ou os segredos existentes.

## <a name="use-python-virtual-environments"></a>Usar ambientes virtuais do Python

Para cada projeto, recomendamos que você sempre crie e ative um *ambiente virtual* usando as seguintes etapas:

1. Abra um terminal ou prompt de comando.

1. Crie uma pasta para seu projeto.

1. Crie o ambiente virtual:

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```bash
    python -m venv .venv
    ```

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    python -m venv .venv
    ```

    ---

    Esse comando executa o módulo `venv` do Python e cria um ambiente virtual em uma pasta chamada `.venv`.

1. Ative o ambiente virtual:

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```bash
    .venv\scripts\activate
    ```

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    source .venv/scripts/activate
    ```

    ---

Um ambiente virtual é uma pasta dentro de um projeto que isola uma cópia de um interpretador específico do Python. Depois de ativar esse ambiente (que Visual Studio Code faz automaticamente), executar `pip install` instala uma biblioteca somente nesse ambiente. Quando você executa o código Python, ele é executado no contexto exato do ambiente com versões específicas de cada biblioteca. Ao executar `pip freeze`, você obtém a lista exata dessas bibliotecas. (Em muitos dos exemplos nesta documentação, você cria um arquivo *requirements.txt* para as bibliotecas de que precisa e, em seguida, usa `pip install -r requirements.txt`. Um arquivo de requisitos geralmente é necessário quando você implanta o código no Azure.)

Se você não usar um ambiente virtual, o Python será executado em seu *ambiente global*. Embora o uso do ambiente global seja rápido e prático, ele tende a encher ao longo do tempo com todas as bibliotecas que você instala para qualquer projeto ou experimento. Além disso, se você atualizar uma biblioteca para um projeto, poderá interromper outros projetos que dependem de versões diferentes dessa biblioteca. Como o ambiente é compartilhado por qualquer número de projetos, você não pode usar `pip freeze` para recuperar de uma lista de dependências de um projeto.

O ambiente global é onde você deseja instalar pacotes de ferramentas que deseja usar em vários projetos. Por exemplo, você pode executar `pip install gunicorn` no ambiente global para disponibilizar o servidor Web em todos os lugares.

## <a name="use-source-control"></a>Usar o controle do código-fonte

É recomendável que você tenha o hábito de criar um repositório de controle do código-fonte, sempre que iniciar um projeto. Se o Git estiver instalado, basta executar o seguinte comando:

```cmd
git init
```

A partir daí, você pode executar comandos como `git add` e `git commit` para confirmar as alterações. Ao confirmar as alterações regularmente, você cria um histórico de confirmação com o qual pode reverter para qualquer estado anterior.

Para fazer um backup online do seu projeto, também recomendamos carregar seu repositório para [GitHub](https://github.com) ou [Azure DevOps](/azure/devops/user-guide/code-with-git?view=azure-devops). Se você tiver inicializado primeiro um repositório local, use `git remote add` para anexar o repositório local ao GitHub ou ao Azure DevOps.

A documentação do git encontra-se em [git-scm.com/docs](https://git-scm.com/docs) e na Internet.

O Visual Studio Code inclui vários recursos internos do git. Para saber mais, confira [Usar o controle de versão no VS Code](https://code.visualstudio.com/docs/editor/versioncontrol).

Você também pode usar qualquer outra ferramenta de controle do código-fonte de sua escolha; o Git é somente um dos mais amplamente usados e com suporte.

## <a name="next-step"></a>Próxima etapa

Com o ambiente de desenvolvimento local em vigor, agora vamos examinar os padrões de uso comum das bibliotecas do Azure:

> [!div class="nextstepaction"]
> [Analisar padrões de uso comum >>>](azure-sdk-library-usage-patterns.md)
