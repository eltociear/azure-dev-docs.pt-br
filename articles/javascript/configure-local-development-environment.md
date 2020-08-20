---
title: Configurar o ambiente do JavaScript local para desenvolvimento do Azure
description: Como configurar um ambiente de desenvolvimento do JavaScript local para trabalhar com o Azure, incluindo um editor, as bibliotecas do SDK do Azure, ferramentas opcionais e as credenciais necessárias para a autenticação da biblioteca.
ms.date: 07/01/2020
ms.topic: conceptual
ms.custom: devx-track-javascript
ms.openlocfilehash: 1071985de770d8c1d9e5e78a25e8556048857a3e
ms.sourcegitcommit: 0699b984b85782b1c441289fa756f285eae853c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88218931"
---
# <a name="configure-your-local-javascript-dev-environment-for-azure"></a>Configurar o ambiente de desenvolvimento do JavaScript local para o Azure

Ao criar aplicativos de nuvem, os desenvolvedores normalmente preferem testar o código em suas estações de trabalho locais antes de implantar esse código em um ambiente de nuvem como o Azure. O desenvolvimento local oferece a vantagem de uma variedade maior de ferramentas, juntamente com um ambiente de desenvolvimento familiar.

Este artigo fornece instruções de configuração para criar e validar um ambiente de desenvolvimento local adequado para o JavaScript com o Azure.

## <a name="one-time-subscription-creation"></a>Criação de assinatura única

Os recursos do Azure são criados em uma assinatura, que é a unidade de cobrança para o uso do Azure. Embora seja possível criar recursos gratuitos (cada assinatura oferece um recurso gratuito para a maioria dos serviços), você deverá criar recursos da camada paga quando planejar implantar o recurso na produção.

* Se você já tiver uma assinatura, não precisará criar uma. Use o [portal do Azure](https://portal.azure.com) para acessar sua assinatura existente.
* [Iniciar uma assinatura de avaliação gratuita]()

## <a name="one-time-installation"></a>Instalação única

Para desenvolver usando um recurso do Azure com JavaScript em sua estação de trabalho local, você precisará instalar o seguinte:

|Nome/instalador|Descrição|
|--|--|
|[Node.js]()|Instale o ambiente de runtime LTS (suporte a longo prazo) mais recente para desenvolvimento na estação de trabalho local. |
| NPM (instalado com versões modernas do Node.js) ou [Yarn]()|Gerenciador de pacotes para instalar bibliotecas do SDK do Azure.|
|[VSCode](https://aka.ms/vscode-deploy)| O VSCode fornecerá uma ótima experiência de integração e codificação do JavaScript, mas ele não é necessário. Você pode usar qualquer editor de código. Para este documento, se você estiver usando um editor diferente, verifique a integração com o Azure ou use a CLI do Azure.|
|[CLI do Azure]()|Você pode usar a CLI do Azure para recriar e gerenciar recursos do Azure em uma linha de comando, um terminal ou um shell do bash.|

> [!CAUTION]
> Se você planeja usar um recurso do Azure como o ambiente de runtime para seu código, como um aplicativo Web do Azure ou uma Instância de Contêiner do Azure, verifique se o ambiente de desenvolvimento local do Node.js corresponde ao runtime do recurso do Azure que você planeja usar.

### <a name="optional-local-installations"></a>Instalações locais opcionais

As instalações comuns de estação de trabalho local a seguir são opcionais para ajudar com suas tarefas de desenvolvimento local.

|Nome/instalador|Descrição|
|--|--|
| [git](https://git-scm.com/downloads) | Ferramentas de linha de comando para controle do código-fonte. Você poderá usar uma ferramenta de controle do código-fonte diferente se preferir. |

## <a name="one-time-configuration-of-service-principal"></a>Configuração única da entidade de serviço

Cada serviço do Azure tem um mecanismo de autenticação. Isso inclui chaves e pontos de extremidade, cadeias de conexão ou outros mecanismos. Para estar em conformidade com as melhores práticas, crie recursos e faça autenticação com esses recursos usando uma entidade de serviço. Uma entidade de serviço permite que você defina concretamente o escopo de acesso para a necessidade de desenvolvimento imediata.

Conceitualmente, as etapas para criar e usar uma entidade de serviço incluem:

* Fazer logon no Azure com sua conta de usuário individual, como joe@microsoft.com.
* Criar uma entidade de serviço nomeada com escopo específico. Como a maioria dos guias de início rápido solicita que você crie um recurso do Azure, a entidade de serviço precisa ter a capacidade de criar recursos.
* Fazer logoff da sua conta de usuário do Azure.
* Autenticar-se no Azure de maneira programática com a entidade de serviço.
* A entidade de serviço cria um recurso do Azure e usa o serviço associado ao serviço.

### <a name="create-service-principal"></a>Criar uma entidade de serviço

Para facilitar a criação da entidade de serviço, use as etapas a seguir e o script fornecido para criar a entidade de serviço a ser usada com os guias de início rápido do Azure. As etapas a seguir usam o nome `JOE` como um nome de usuário de exemplo. Substitua-o pelo seu próprio nome ou alias de email.

1. Abra o VSCode e instale a extensão [ferramentas da CLI do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli). Essa extensão permite que você execute comandos da CLI do Azure no arquivo de script, linha por linha. Quando você executa cada comando, um documento vizinho é aberto no VSCode para exibir os resultados.

1. Crie um arquivo chamado `create-service-principal.sh` e copie os seguintes comandos do Azure para esse arquivo:

    ```azurecli
    # Replace ALL-CAPS variables with your own values

    ####################################
    # Login as you
    ####################################

    # Login - command opens browser, select your account to finish authentication, then close browser
    az login

    ####################################
    # Optional, set default subscription
    ####################################

    # If you have more than 1 subscription, use the `list` command to find the subscription, then use the `set` command to set the default by name or id
    az account list
    az account set --subscription MYCOMPANYSUBSCRIPTION

    ####################################
    # Create service principal
    ####################################

    # Create a service principal with a name that indicates its purpose and owner - the response includes the `appId` which is necessary in some of the remaining commands
    az ad sp create-for-rbac --name JOE-SERVICEPRINCIPAL-DOCUMENT-QUICKSTARTS --skip-assignment

    ####################################
    # Add role of contributor
    ####################################

    # Add contributor role to service principal so it can create Azure resources
    az role assignment create --assignee APP-ID --role CONTRIBUTOR

    ####################################
    # Optional, verify role assignment
    ####################################

    # Verify role assignment for service principal
    az role assignment list --assignee APP-ID

    ####################################
    # Logout
    ####################################

    # Logout off Azure CLI
    az logout
    ```

    Nas etapas restantes deste procedimento, em cada linha do arquivo que **não** começar com `#`, coloque o cursor do VSCode na linha e **clique com o botão direito do mouse** para selecionar **Executar Linha no Editor**.

    :::image type="content" source="media/development-setup/vscode-rightclick-run-line-in-editor.png" alt-text="Nas etapas restantes deste procedimento, em cada linha do arquivo que não começar com `#`, coloque o cursor do VSCode na linha e clique com o botão direito do mouse para selecionar Executar Linha no Editor.":::

1. Clique com o botão direito do mouse/Executar Linha no Editor na linha a seguir para se autenticar no Azure com sua própria conta de usuário usando a CLI do Azure. Esse comando abre um navegador da Internet. Selecione sua conta do Azure. Depois que sua conta for autenticada, feche a janela do navegador; você não precisará dela nas tarefas restantes.

    ```azurecli
    az login
    ```

    A resposta inclui todas as assinaturas às quais você tem acesso, exibidas em outra janela de documento do VSCode como uma matriz JSON. Localize a propriedade `name` ou `id`. Você precisará de um desses valores para os comandos restantes.

    ```json
    [  {
    "cloudName": "AzureCloud",
    "id": "320d9379-aaaa-bbbb-cccc-52f2b0fc40ac",
    "isDefault": false,
    "name": "contoso-development-team",
    "state": "Enabled",
    "tenantId": "72f988bf-aaaa-bbbb-cccc-2d7cd011db47",
    "user": {
      "name": "joe@contoso.com",
      "type": "user"
    }
    }]
    ```

    A assinatura marcada com `isDefault: true` é a assinatura que recebe os comandos restantes. Se você precisar alterar a assinatura padrão, use o comando `az account set --subscription <name or id>`.


<a name='create-service-principal-command'></a>

1. Clique com o botão direito do mouse/Executar Linha no Editor na linha a seguir para criar a entidade de serviço vinculada à sua conta de usuário. Essa entidade de serviço ainda não tem nenhuma permissão no escopo, devido ao parâmetro `--skip-assignment`.


    ```azurecli
    az ad sp create-for-rbac --name JOE-SERVICEPRINCIPAL-DOCUMENT-QUICKSTARTS --skip-assignment
    ```

    O nome da entidade de serviço é `JOE-SERVICEPRINCIPAL-DOCUMENT-QUICKSTARTS`. Veja uma lista de todas as entidades de serviço associadas à sua conta de usuário do Azure no portal do Azure, na lista de aplicativos do serviço Active Directory.

    O resultado inclui as informações necessárias: `appId` e `password`. Salve o arquivo com o nome `create-service-principal.json`

    ```json
    {
      "appId": "93453d56-aaaa-bbbb-cccc-db600ecc4f6a",
      "displayName": "JOE-SERVICEPRINCIPAL-DOCUMENT-QUICKSTARTS",
      "name": "http://JOE-SERVICEPRINCIPAL-DOCUMENT-QUICKSTARTS",
      "password": "d88b21e0-aaaa-bbbb-cccc-e1e9b06d50f6",
      "tenant": "72f988bf-aaaa-bbbb-cccc-2d7cd011db47"
    }
    ```

1. Clique com o botão direito do mouse/Executar Linha no Editor na linha a seguir para atribuir a permissão com escopo para criar recursos do Azure. O escopo `CONTRIBUTOR` permite que a entidade de serviço crie recursos do Azure.

    ```azurecli
    az role assignment create --assignee APP-ID --role CONTRIBUTOR
    ```

    O resultado se parece com o seguinte:

    ```json
    {
      "canDelegate": null,
      "id": "/subscriptions/a5b1ca8b-aaaa-bbbb-cccc-4cf7ec4791a0/providers/Microsoft.Authorization/roleAssignments/3a155db5-aaaa-bbbb-cccc-0cbfebf75464",
      "name": "3a155db5-aaaa-bbbb-cccc-0cbfebf75464",
      "principalId": "c05d56c9-aaaa-bbbb-cccc-0535d6167ed4",
      "principalType": "ServicePrincipal",
      "roleDefinitionId": "/subscriptions/a5b1ca8b-aaaa-bbbb-cccc-4cf7ec4791a0/providers/Microsoft.Authorization/roleDefinitions/b24988ac-aaaa-bbbb-cccc-20f7382dd24c",
      "scope": "/subscriptions/a5b1ca8b-aaaa-bbbb-cccc-4cf7ec4791a0",
      "type": "Microsoft.Authorization/roleAssignments"
    }
    ```

    Agora sua entidade de serviço está pronta para uso.

1. Clique com o botão direito do mouse/Executar Linha no Editor na linha a seguir para sair da CLI do Azure com o seguinte comando:

    ```azurecli
    az logout
    ```

## <a name="steps-for-each-new-development-project-setup"></a>Etapas para cada nova configuração de projeto de desenvolvimento

Como as bibliotecas do SDK do Azure são fornecidas individualmente para cada serviço, não há um único pacote para download para acessar todos os recursos do Azure. Você instala cada biblioteca com base no serviço do Azure que deseja usar.

Cada novo projeto que usa o Azure deve:
- Criar recursos do Azure ou localizar informações de autenticação para recursos existentes do Azure
- Instalar as bibliotecas do SDK do Azure do NPM ou Yarn. Saiba mais sobre as [versões de biblioteca](#library-versions).
- Gerenciar informações de autenticação dentro do projeto com segurança. Um método comum é o uso de **[Dotenv](https://www.npmjs.com/package/dotenv)** para ler variáveis de ambiente em um arquivo `.env`. Adicione o arquivo `.env` ao arquivo `.gitignore` para não fazer check-in do arquivo `.env` no controle do código-fonte.

### <a name="library-versions"></a>Versões de biblioteca

Todas as bibliotecas do Azure estão migrando para o escopo `@azure`.

| Tipo de biblioteca | Descrição|
|--|--|
|Moderna|Têm escopo definido para `@azure`, por exemplo [@azure/storage-blob](https://www.npmjs.com/package/@azure/storage-blob) e [@azure/cosmos](https://www.npmjs.com/package/@azure/cosmos), e incluem tipos TypeScript.|
|Pacotes mais antigos|Normalmente começam com `azure-`. Muitos pacotes, que não são produzidos pela Microsoft, começam com esse nome. Verifique se o proprietário do pacote é a Microsoft ou o Azure.|

### <a name="create-resource-using-service-principal"></a>Criar recurso usando a entidade de serviço

A seção a seguir fornece um exemplo de como criar um recurso de serviço do Azure com uma entidade de serviço. Para entrar com uma entidade de serviço, você precisará de `appId`, `tenant` e `password` salvos no procedimento [Criar entidade de serviço](#create-service-principal) no `create-service-principal.json`.

1. Abra o VSCode e use a extensão [ferramentas da CLI do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli) instalada anteriormente. Essa extensão permite que você execute comandos da CLI do Azure no arquivo de script, linha por linha. Quando você executa cada comando, um documento vizinho é aberto no VSCode para exibir os resultados.

1. Crie um arquivo chamado `create-service-resource.sh` e copie os seguintes comandos do Azure para esse arquivo:

    ```azurecli
    ####################################
    # Login as service principal
    ####################################
    # User name for command is the app id
    az login --service-principal --username APP_ID --password PASSWORD --tenant TENANT_ID

    ####################################
    # Create resource group
    ####################################

    # Create resource group in westus region - check your quickstart if it requires a specific region, then change this value to the appropriate region
    # Common naming convention for resource group is `USERNAME-REGION-PURPOSE`
    az group create --location WESTUS --name JOE-WESTUS-QUICKSTARTS-RESOURCEGROUP

    ####################################
    # Create specific service resource
    ####################################

    # Create resource in westus
    # This is an example of creating a Cognitive Services LUIS resource
    # Review your quickstart to find the exact command
    az SERVICENAME account create --name JOE-WESTUS-COGNITIVESERVICES-LUIS --resource-group JOE-WESTUS-QUICKSTARTS-RESOURCEGROUP --kind LUIS --sku F0 --location WESTUS --yes

    ####################################
    # Get resource keys
    ####################################

    # Get resource keys
    az cognitiveservices account keys list --name JOE-WESTUS-COGNITIVESERVICES-LUIS --resource-group JOE-WESTUS-QUICKSTARTS-RESOURCEGROUP
    ```

1. Clique com o botão direito do mouse/Executar Linha no Editor na linha a seguir para fazer logon com a entidade de serviço. As variáveis em todos os limites foram retornadas na resposta do [comando anterior para criar a entidade de serviço](#create-service-principal-command).

    ```azurecli
    az login --service-principal --username APP_ID --password PASSWORD --tenant TENANT_ID
    ```

1. Clique com o botão direito do mouse/Executar Linha no Editor na linha a seguir para criar um grupo de recursos para todos os recursos que você precisa criar para o guia de início rápido. A região do grupo de recursos pode conter apenas recursos dessa região.

    ```azurecli
    az group create --location WESTUS --name JOE-WESTUS-QUICKSTARTS-RESOURCEGROUP
    ```

    Quando terminar de usar os recursos do início rápido, você poderá excluir o grupo de recursos, excluindo os recursos em uma única ação.

1. Clique com o botão direito do mouse/Executar Linha no Editor na linha a seguir para criar um recurso do LUIS dos Serviços Cognitivos. Este é um exemplo; o seu recurso terá um comando diferente.

    ```azurecli
    az SERVICENAME account create --name JOE-WESTUS-COGNITIVESERVICES-LUIS --resource-group JOE-WESTUS-QUICKSTARTS-RESOURCEGROUP --kind LUIS --sku F0 --location WESTUS --yes
    ```

    O recurso LUIS usa uma chave e um ponto de extremidade, que você precisa usar os guias de início rápido para o LUIS.

1. Clique com o botão direito do mouse/Executar Linha no Editor na linha a seguir para obter a chave e o ponto de extremidade do LUIS. A autenticação no serviço LUIS usa a chave e o ponto de extremidade.

    ```azurecli
    az cognitiveservices account keys list --name JOE-WESTUS-COGNITIVESERVICES-LUIS --resource-group JOE-WESTUS-QUICKSTARTS-RESOURCEGROUP
    ```

### <a name="create-environment-variables-for-the-azure-libraries"></a>Criar variáveis de ambiente para as bibliotecas do Azure

Para usar as configurações do Azure necessárias para as bibliotecas de SDK do Azure para acessar a nuvem do Azure, defina os valores mais comuns para variáveis de ambiente. Os comandos a seguir definem as variáveis de ambiente para a estação de trabalho local. Outro mecanismo comum é usar o pacote NPM `DOTENV` para criar um arquivo `.env` para essas configurações. Se você planeja usar um `.env`, não faça check-in do arquivo no controle do código-fonte. A adição do arquivo `.env` ao arquivo `.ignore` do git é a maneira padrão de fazer check-in dessas configurações no controle do código-fonte.

Nos exemplos a seguir, a ID do cliente é a ID da entidade de serviço e o segredo da entidade de serviço.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
AZURE_SUBSCRIPTION_ID="aa11bb33-cc77-dd88-ee99-0918273645aa"
AZURE_TENANT_ID="00112233-7777-8888-9999-aabbccddeeff"
AZURE_CLIENT_ID="12345678-1111-2222-3333-1234567890ab"
AZURE_CLIENT_SECRET="abcdef00-4444-5555-6666-1234567890ab"
```

# <a name="cmd"></a>[cmd](#tab/cmd)

```cmd
set AZURE_SUBSCRIPTION_ID="aa11bb33-cc77-dd88-ee99-0918273645aa"
set AZURE_TENANT_ID=00112233-7777-8888-9999-aabbccddeeff
set AZURE_CLIENT_ID=12345678-1111-2222-3333-1234567890ab
set AZURE_CLIENT_SECRET=abcdef00-4444-5555-6666-1234567890ab
```

---

Substitua os valores mostrados nesses comandos pelos de sua entidade de serviço específica.

### <a name="install-npm-packages"></a>Instalar pacotes NPM

Para cada projeto, é recomendável que você sempre crie uma pasta separada e o próprio arquivo `package.json` dela usando as seguintes etapas:

1. Abra um terminal, um prompt de comando ou um shell de bash e crie uma pasta para o projeto. Em seguida, acesse a nova pasta.

    ```console
    mkdir MY-NEW-PROJECT && cd MY-NEW-PROJECT
    ```

1. Inicialize o arquivo de pacote:

    ```console
    npm init -y
    ```

    Esse comando executa o comando NPM para criar o arquivo package.json e inicializar as propriedades mínimas. À medida que você instala pacotes da biblioteca do SDK do Azure com NPM ou Yarn, o arquivo package.json captura essas informações de instalação.

1. Instale as bibliotecas do SDK do Azure necessárias para o guia de início rápido. O comando a seguir é um exemplo.

    ```console
    npm install @azure/cognitiveservices-luis-runtime
    ```

## <a name="use-source-control"></a>Usar o controle do código-fonte

É recomendável que você tenha o hábito de criar um repositório de controle do código-fonte, sempre que iniciar um projeto. Se o Git estiver instalado, execute o seguinte comando:

```bash
git init
```

Agora você pode executar comandos como `git add` e `git commit` para confirmar as alterações. Ao confirmar as alterações regularmente, você cria um histórico de confirmação com o qual pode reverter para qualquer estado anterior.

Para fazer um backup online do seu projeto, também recomendamos carregar seu repositório para [GitHub](https://github.com) ou [Azure DevOps](/azure/devops/user-guide/code-with-git?view=azure-devops). Se você tiver inicializado primeiro um repositório local, use `git remote add` para anexar o repositório local ao GitHub ou ao Azure DevOps.

A documentação do git encontra-se em [git-scm.com/docs](https://git-scm.com/docs) e na Internet.

O Visual Studio Code inclui vários recursos internos do git. Para saber mais, confira [Usar o controle de versão no VS Code](https://code.visualstudio.com/docs/editor/versioncontrol).

Você também pode usar qualquer outra ferramenta de controle do código-fonte de sua escolha; o Git é somente um dos mais amplamente usados e com suporte.

## <a name="next-steps"></a>Próximas etapas

* [Implantar um site estático no Azure usando o Visual Studio Code](tutorial-vscode-static-website-node-01.md)