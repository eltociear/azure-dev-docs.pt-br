---
title: Gerenciar contas de armazenamento do Azure com Java | Microsoft Docs
description: Exemplo de código para gerenciar as contas de armazenamento do Azure usando o SDK do Azure para Java
author: rloutlaw
ms.assetid: 49be8b66-3b56-4c10-8f14-9d326d815cb4
ms.topic: article
ms.date: 3/30/2017
ms.reviewer: asirveda
ms.openlocfilehash: 9084360a3cf4fa335bfded302461f4435b8452f7
ms.sourcegitcommit: 3c69d7c3e5c5a00a01ee18e63b0659830c7d4ec0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82104987"
---
# <a name="manage-azure-storage-accounts-from-your-java-applications"></a>Gerenciar contas de armazenamento do Azure a partir dos seus aplicativos Java

[Este exemplo](https://github.com/Azure-Samples/storage-java-manage-storage-accounts) cria uma conta de [Armazenamento do Azure](/azure/storage/common/storage-introduction) e funciona com as chaves de acesso da conta usando as [bibliotecas de gerenciamento de Java](https://github.com/Azure/azure-sdk-for-java). 

## <a name="run-the-sample"></a>Execute o exemplo

Criar um [arquivo de autenticação](https://docs.microsoft.com/azure/java/java-sdk-azure-authenticate#mgmt-file) e definir uma variável de ambiente `AZURE_AUTH_LOCATION` com o caminho completo para o arquivo em seu computador. Em seguida, execute:

```
git clone https://github.com/Azure-Samples/storage-java-manage-storage-accounts.git
cd storage-java-manage-storage-accounts
mvn clean compile exec:java
```

Exibição de [exemplo de código completo no GitHub](https://github.com/Azure-Samples/storage-java-manage-storage-accounts).

## <a name="authenticate-with-azure"></a>Autenticar com o Azure

[!INCLUDE [auth-include](includes/java-auth-include.md)] 

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

```java
// create a new storage account
StorageAccount storageAccount = azure.storageAccounts().define(storageAccountName)
                    .withRegion(Region.US_EAST)
                    .withNewResourceGroup(rgName)
                    .create();
```

O nome de armazenamento fornecido deve ser exclusivo entre todos os nomes no Azure e conter apenas letras minúsculas e números. O perfil de desempenho e replicação padrão usado para essa conta é [Standard_GRS](/azure/storage/common/storage-redundancy-grs).

## <a name="list-keys-in-a-storage-account"></a>Listar chaves em uma conta de armazenamento
```java
// list the name and value for each access key in the storage account
List<StorageAccountKey> storageAccountKeys = storageAccount.getKeys();
for(StorageAccountKey key : storageAccountKeys)    {
    System.out.println("Key name: " + key.keyName() + " with value "+ key.value());
}
```

Duas chaves são fornecidas em cada conta de armazenamento do Azure para que você possa regenerar uma chave e ainda permitir acesso ao armazenamento usando a outra chave.

## <a name="regenerate-a-key-in-a-storage-account"></a>Regenerar uma chave em uma conta de armazenamento

```java
// regenerate the first key in a storage account and return an updated list of keys 
List<StorageAccountKey> updatedStorageAccountKeys =
    storageAccount.regenerateKey(storageAccountKeys.get(0).keyName());
```

Depois de gerar um novo, você deve atualizar todos os aplicativos e recursos do Azure com a nova chave.

## <a name="list-all-storage-accounts-in-a-resource-group"></a>Listar todas as contas de armazenamento em um grupo de recursos
```java
// get a list of accounts in a resource group , log info about each one
List<StorageAccount> accounts = azure.storageAccounts().listByResourceGroup(rgName);
for (StorageAccount sa : accounts) {
    System.out.println("Storage Account " + sa.name() + " created @ " + sa.creationTime());
}
```

[com.microsoft.azure.management.storage.StorageAccount](/java/api/com.microsoft.azure.management.storage.storageaccount) fornece um conjunto de métodos úteis para inspecionar a configuração de uma conta de armazenamento.

## <a name="delete-a-storage-account"></a>Excluir uma conta de armazenamento
```java
// delete by ID when you already have a storage account object
azure.storageAccounts().deleteById(storageAccount.id());

// delete by resource group and account name if you don't have an account object
azure.storageAccounts().deleteByResourceGroup(rgName,accountName);
```

> [!NOTE]
> Contas de armazenamento com imagens de disco em uso conectadas a máquinas virtuais ou discos em uso por outros artefatos não podem remover esses métodos. Desanexe o armazenamento desses recursos antes de remover a conta.

## <a name="sample-explanation"></a>Explicação do exemplo

[Exemplo de código no GitHub](https://github.com/Azure-Samples/storage-java-manage-storage-accounts):

- cria uma conta de armazenamento
- lê e regenera as chaves de acesso
- lista todas as contas de armazenamento em um grupo de recursos
- elimina a conta de armazenamento 

| Classe usada no exemplo | Observações
|-------|-------|
| [StorageAccount](/java/api/com.microsoft.azure.management.storage.storageaccount)  | Representação de uma conta de armazenamento do Azure. Use os métodos na classe para obter informações sobre a conta de armazenamento.
| [StorageAccountKey](/java/api/com.microsoft.azure.management.storage.storageaccountkey) | `StorageAccount.getKeys()` retorna as chaves da conta de armazenamento. Use os métodos `regenerateKey` em `StorageAccount` para atualizar as chaves.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [next-steps](includes/java-next-steps.md)]