---
title: Atribuir permissões de função a uma identidade de aplicativo ou a uma entidade de serviço
description: Como conceder permissões a uma entidade de serviço ou identidade de aplicativo usando a CLI do Azure
ms.date: 05/12/2020
ms.topic: conceptual
ms.openlocfilehash: 28aa6b41d84c31eb8b6e6332c08c3a464c6cb04e
ms.sourcegitcommit: 2cdf597e5368a870b0c51b598add91c129f4e0e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/14/2020
ms.locfileid: "83404899"
---
# <a name="how-to-assign-role-permissions-to-an-app-identity-or-service-principal"></a>Como atribuir permissões de função a uma identidade de aplicativo ou a uma entidade de serviço

O sistema RBAC (controle de acesso baseado em função) do Azure gerencia permissões específicas para uma ampla variedade de recursos. Uma *função* é essencialmente uma coleção de permissões relacionadas que normalmente são necessárias juntas. Para habilitar permissões, atribua uma função a uma *entidade de segurança* (usuário, grupo, entidade de serviço ou identidade de aplicativo) com um *escopo* específico ao qual essa função se aplica.

Na prática, sempre atribua apenas as funções que uma entidade de segurança realmente precisa no escopo mais específico. Evite atribuir funções mais amplas em escopos mais amplos, mesmo que, inicialmente, isso pareça mais prático. Ao limitar as funções e os escopos, você limitará os recursos que estão em risco se a entidade de segurança for comprometida (ou seja, se as credenciais dessa entidade forem expostas em uma violação de dados ou outro incidente de segurança).

Como você usa entidades de segurança diferentes em desenvolvimento e produção, repete as atribuições de função em cada ambiente. Ou seja, durante o desenvolvimento, você normalmente atribui funções à entidade de serviço local criada em sua estação de trabalho (consulte [Configurar seu ambiente de desenvolvimento Python local - Autenticação](configure-local-development-environment.md#configure-authentication)). Em produção, você atribui funções à identidade do aplicativo ou à entidade de serviço antes da implantação para garantir que o aplicativo tenha acesso na inicialização.

Para obter mais informações sobre o RBAC em geral, consulte [O que é controle de acesso baseado em função do Azure?](/azure/role-based-access-control/overview).

## <a name="role-assignment-process"></a>Processo de atribuição de função

A atribuição de uma função tem três etapas:

1. [Localize a função apropriada](#find-the-roles-you-need) para o tipo de recurso envolvido e as operações que você deseja autorizar.

1. Identifique o escopo necessário para a função em questão, que descreve a extensão dos recursos para os quais as operações são autorizadas.

1. Atribua a função a uma entidade de segurança.

A etapa 1 é a mesma para o portal do Azure e a CLI do Azure. As etapas 2 e 3 são diferentes entre o portal e a CLI e, portanto, são combinadas nas seções a seguir: [Identificar o escopo e atribuir uma função no portal do Azure](#azure-portal) e [Identificar o escopo e atribuir uma função por meio da CLI do Azure](#azure-cli).

> [!NOTE]
> Se a sua conta de usuário não tiver permissão para atribuir uma função na sua assinatura, você verá uma mensagem de erro informando que sua conta "não tem autorização para executar a ação 'Microsoft.Authorization/roleAssignments/write'." Nesse caso, entre em contato com os administradores da sua assinatura, pois eles podem atribuir as permissões em seu nome.

## <a name="find-the-roles-you-need"></a>Localizar as funções necessárias

1. Comece com o artigo abrangente, [Funções internas do Azure](/azure/role-based-access-control/built-in-roles). A tabela na parte superior do artigo é um índice dos detalhes presentes mais adiante neste artigo.

1. Nesse artigo, navegue até a categoria de serviço (computação, armazenamento, bancos de dados etc.) para o recurso ao qual você deseja conceder permissões. Normalmente, a maneira mais fácil de encontrar o que você está procurando é pesquisar a página em busca de uma palavra-chave relevante, como "blob", "máquina virtual" e assim por diante.

1. Examine as funções listadas para a categoria de serviço e identifique as operações específicas de que você precisa. Novamente, sempre comece com a função mais restritiva.

    Por exemplo, se uma entidade de segurança precisar ler blobs em uma conta de armazenamento do Azure, mas não precisar de acesso de gravação, escolha "Leitor de dados de blob de armazenamento" em vez de "Colaborador de dados de blob de armazenamento" (e, definitivamente, não escolha a função de "Proprietário de dados de blob de armazenamento" do nível de administrador). Você sempre pode atualizar as atribuições de função posteriormente, conforme necessário.

    Se a entidade de segurança também precisar de acesso ao armazenamento de filas, você poderá usar funções como "leitor de dados de fila de armazenamento" e "colaborador de dados da fila de armazenamento". Novamente, seja o mais específico possível, em vez de atribuir uma função ampla, como "leitor e acesso a dados", que fornece acesso a chaves de conta por meio das quais a entidade de segurança pode acessar o que for necessário em toda a conta de armazenamento.

1. Se você não encontrar uma função adequada, poderá criar [funções personalizadas](/azure/role-based-access-control/custom-roles).

## <a name="identify-scope-and-assign-a-role-on-the-azure-portal"></a><a name="azure-portal"></a>Identificar o escopo e atribuir uma função no portal do Azure

1. No [portal do Azure](https://portal.azure.com), navegue até o recurso ao qual você deseja atribuir uma função. O escopo do recurso determina o escopo da atribuição.

    Por exemplo, se você navegar para uma conta de armazenamento, qualquer atribuição de função se aplicará a toda a conta de armazenamento. Se você navegar para um contêiner de blob específico dentro dessa conta de armazenamento, a atribuição de função se aplicará somente a esse contêiner.

1. Selecione a folha **Controle de Acesso (IAM)** , onde IAM significa "Gerenciamento de identidades e acesso".

1. Nessa folha, há uma seção de **Atribuições de função**, na qual você pode adicionar e remover funções atribuídas a quaisquer entidades de segurança.

Para obter detalhes completos e instruções de interface do usuário, consulte [Adicionar ou remover atribuições de função do Azure](/azure/role-based-access-control/role-assignments-portal) na documentação do RBAC do Azure.

## <a name="identify-scope-and-assign-a-role-through-the-azure-cli"></a><a name="azure-cli"></a>Identificar o escopo e atribuir uma função no portal da CLI do Azure

A atribuição de função com a CLI do Azure usa o comando [`az role assignment`](/cli/azure/role/assignment?view=azure-cli-latest). Use `az role assignment create` para adicionar uma atribuição e `az role assignment delete` para remover uma atribuição. 

Embora o processo completo seja descrito em [Adicionar ou remover atribuições de função do Azure usando a CLI do Azure](/azure/role-based-access-control/role-assignments-cli), o resumo a seguir fornece exemplos específicos que são relevantes para outros artigos neste centro de desenvolvedores.

O comando `az role assignment create` tem a seguinte sintaxe:

```azurecli
az role assignment create --assignee <assignee> --role <role> --scope <scope>
```

- `<assignee>` identifica a entidade de segurança; para entidades de serviço, como a que você usa durante o desenvolvimento local, o destinatário é a ID do cliente da entidade de segurança. Para aplicativos implantados na nuvem, o destinatário é o nome do aplicativo.
- `<role>` é o nome da função a ser atribuída, como "Colaborador de dados de blob de armazenamento", ou seu GUID, como "ba92f5b4-2D11-453d-A403-e96b0029c9fe".
- `<scope>` é uma cadeia de caracteres potencialmente longa que identifica o escopo exato da atribuição.

O escopo consiste em uma série de identificadores separados pelo caractere /. Você pode considerar essa cadeia de caracteres como a hierarquia a seguir, em que o texto sem espaços reservados (`<>`) são identificadores fixos:

<pre>
/subscriptions
  /&lt;subscription_id&gt;
    /resourcegroups
      /&lt;resource_group_name&gt;
        /providers
          /&lt;provider_name&gt;
            /&lt;resource_type&gt;
              /&lt;resource_sub_type_1&gt;
                /&lt;resource_sub_type_2&gt;
                  /&lt;resource_name&gt;
</pre>

- `<subscription_id>` é a ID da assinatura a ser usada (um GUID).
- `<resources_group_name>` é o nome do grupo de recursos contentor.
- `<provider_name>` é o nome do serviço que manipula o recurso e, em seguida, `<resource_type>` e `<resource_sub_type_*>` identificam mais níveis dentro desse serviço.
  
    Para encontrar esses nomes e tipos, consulte a referência [Funções internas do Azure](/azure/role-based-access-control/built-in-roles). Localize e selecione a função na tabela superior para saltar para a descrição específica da função. Nesse local, você encontrará cadeias de caracteres que contêm o nome do provedor, o tipo de recurso e os tipos de sub-recurso. Por exemplo, com o "Colaborador de dados de blob de armazenamento", você vê "Microsoft.Storage/storageAccounts/blobservices/containers/". Para a "Função de leitor de conta Cosmos DB", você vê "Microsoft.DocumentDB/databaseAccounts/readonlykeys", que tem apenas um subtipo.

- `<resource_name>` é a última parte da cadeia de caracteres que identifica um recurso específico.

O escopo mais amplo (menos específico) é `/subscriptions/<subscription_id>`, que aplica uma atribuição em toda a assinatura. Cada nível adicional de hierarquia torna o escopo mais específico.

### <a name="examples"></a>Exemplos

Os exemplos a seguir pressupõem as seguintes condições (consulte o [Exemplo: Usar o SDK do Azure com o Armazenamento do Microsoft Azure](azure-sdk-example-storage.md)):

- Sua ID de assinatura do Azure está contida em uma variável de ambiente chamada `AZURE_SUBSCRIPTION_ID`.
- A ID do cliente da entidade de serviço à qual você deseja atribuir uma função está contida em uma variável de ambiente chamada `AZURE_CLIENT_ID`.
- Na assinatura, você tem um grupo de recursos denominado "PythonSDKExample-Storage-RG".
- O grupo de recursos contém uma conta de armazenamento do Azure denominada "pythonsdkstorage-12345".
- Você tem um contêiner de blobs na conta de armazenamento denominada "blob-container-01".
- Você deseja atribuir o "Colaborador de dados de blob de armazenamento" à entidade de serviço.

> [!TIP]
> Pode levar um minuto para que as alterações nas atribuições de função sejam propagadas no Azure. Como resultado, você pode descobrir que o código ainda funciona por um curto período após a remoção de uma permissão. Se você vir um comportamento inesperado, aguarde um minuto ou dois e tente novamente.

#### <a name="grant-permissions-for-the-specific-container-only"></a>Conceder permissões somente para o contêiner específico

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az role assignment create --assignee $AZURE_CLIENT_ID \
    --role "Storage Blob Data Contributor" \
    --scope "/subscriptions/$AZURE_SUBSCRIPTION_ID/resourceGroups/PythonSDKExample-Storage-rg/providers/Microsoft.Storage/storageAccounts/pythonsdkstorage12345/blobServices/default/containers/blob-container-01"
```

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az role assignment create --assignee %AZURE_CLIENT_ID% ^
    --role "Storage Blob Data Contributor" ^
    --scope "/subscriptions/%AZURE_SUBSCRIPTION_ID%/resourceGroups/PythonSDKExample-Storage-rg/providers/Microsoft.Storage/storageAccounts/pythonsdkstorage12345/blobServices/default/containers/blob-container-01"
```

---

#### <a name="grant-permissions-for-all-blob-containers-in-the-storage-account"></a>Conceder permissões para todos os contêineres de blob na conta de armazenamento

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az role assignment create --assignee $AZURE_CLIENT_ID \
    --role "Storage Blob Data Contributor" \
    --scope "/subscriptions/$AZURE_SUBSCRIPTION_ID/resourceGroups/PythonSDKExample-Storage-rg/providers/Microsoft.Storage/storageAccounts/pythonsdkstorage12345"
```

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az role assignment create --assignee %AZURE_CLIENT_ID% ^
    --role "Storage Blob Data Contributor" ^
    --scope "/subscriptions/%AZURE_SUBSCRIPTION_ID%/resourceGroups/PythonSDKExample-Storage-rg/providers/Microsoft.Storage/storageAccounts/pythonsdkstorage12345"
```

---

#### <a name="grant-permissions-for-all-blob-containers-in-the-resource-group"></a>Conceder permissões para todos os contêineres de blob no grupo de recursos

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az role assignment create --assignee $AZURE_CLIENT_ID \
    --role "Storage Blob Data Contributor" \
    --scope "/subscriptions/$AZURE_SUBSCRIPTION_ID/resourceGroups/PythonSDKExample-Storage-rg"
```

Como alternativa, você pode apenas especificar o grupo de recursos com o parâmetro `--resource-group`:

```azurecli
az role assignment create --assignee $AZURE_CLIENT_ID \
    --role "Storage Blob Data Contributor" \
    --resource-group "PythonSDKExample-Storage-rg"
```

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az role assignment create --assignee %AZURE_CLIENT_ID% ^
    --role "Storage Blob Data Contributor" ^
    --scope "/subscriptions/%AZURE_SUBSCRIPTION_ID%/resourceGroups/PythonSDKExample-Storage-rg"
```

Como alternativa, você pode apenas especificar o grupo de recursos com o parâmetro `--resource-group`:

```azurecli
az role assignment create --assignee %AZURE_CLIENT_ID% ^
    --role "Storage Blob Data Contributor" ^
    --resource-group "PythonSDKExample-Storage-rg"
```

---

#### <a name="grant-permissions-to-all-blob-containers-in-the-subscription"></a>Conceder permissões para todos os contêineres de blob na assinatura

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az role assignment create --assignee $AZURE_CLIENT_ID \
    --role "Storage Blob Data Contributor" \
    --scope "/subscriptions/$AZURE_SUBSCRIPTION_ID"
```

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az role assignment create --assignee %AZURE_CLIENT_ID% ^
    --role "Storage Blob Data Contributor" ^
    --scope "/subscriptions/%AZURE_SUBSCRIPTION_ID%"
```

---
