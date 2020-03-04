---
title: Como usar a API do MongoDB do Spring Data com o Azure Cosmos DB
description: Saiba como usar a API do MongoDB do Spring Data com o Azure Cosmos DB.
services: cosmos-db
documentationcenter: java
ms.date: 12/19/2018
ms.service: cosmos-db
ms.tgt_pltfrm: multiple
ms.topic: article
ms.openlocfilehash: 0284f89f6a37497709947649fba3b1284416a95c
ms.sourcegitcommit: b3b7dc6332c0532f74d210b2a5cab137e38a6750
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74811931"
---
# <a name="how-to-use-spring-data-mongodb-api-with-azure-cosmos-db"></a>Como usar a API do MongoDB do Spring Data com o Azure Cosmos DB

## <a name="overview"></a>Visão geral

Este artigo demonstra a criação de um aplicativo de exemplo que usa o [Spring Data] para armazenar e recuperar informações usando a [API do MongoDB do Azure Cosmos DB](/azure/cosmos-db/mongodb-introduction).

## <a name="prerequisites"></a>Prerequisites

Os seguintes pré-requisitos são obrigatórios para você concluir as etapas neste artigo:

* Uma assinatura do Azure; se ainda não tiver uma assinatura do Azure, você poderá ativar o [benefício de assinante do MSDN] ou inscrever-se para uma [conta gratuita do Azure].
* Um JDK (Java Development Kit) com suporte. Para obter mais informações sobre os JDKs disponíveis para usar durante o desenvolvimento no Azure, confira <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/) versão 3.0 ou posterior.
* Um cliente [Git](https://git-scm.com/downloads).

## <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

### <a name="create-a-cosmos-db-account-using-the-azure-portal"></a>Criar uma conta do Cosmos DB usando o portal do Azure

> [!NOTE]
> 
> Veja informações mais detalhadas sobre como criar contas do Azure Cosmos DB na [documentação do Azure Cosmos DB](/azure/cosmos-db/).

1. Navegue até o portal do Azure em <https://portal.azure.com/> e entre.

1. Clique em **+Criar um recurso**, **Bancos de dados** e clique em **Azure Cosmos DB**.

   ![Criar uma conta do Azure Cosmos DB][COSMOSDB01]

1. Especifique as seguintes informações:

   - **Assinatura**: especifique a assinatura do Azure para usar.
   - **Grupo de recursos**: especifique se deseja criar um novo grupo de recursos ou escolher um grupo de recursos existente.
   - **Nome da conta**: Escolha um nome exclusivo para sua conta do Cosmos DB. Ele será usado para criar um nome de domínio totalmente qualificado, como *wingtiptoysmongodb.documents.azure.com*.
   - **API**: Especifique `Azure Cosmos DB for MongoDB API` para este tutorial.
   - **Localização**: especifique a região geográfica mais próxima do banco de dados.

   ![Especificar as configurações de conta do Cosmos DB][COSMOSDB02]
   
1. Após inserir todas as informações acima, clique em **Revisar + Criar**.

1. Se estiver tudo correto na página de revisão, clique em **Criar**.

   ![Revisar as configurações de conta do Cosmos DB][COSMOSDB03]

### <a name="retrieve-the-connection-string-for-your-azure-cosmos-db-account"></a>Recuperar a cadeia de conexão da conta do Azure Cosmos DB

1. Navegue até o portal do Azure em <https://portal.azure.com/> e entre.

1. Clique em **Todos os Recursos** e, em seguida, clique na conta do Azure Cosmos DB que você acabou de criar.

1. Clique em **Cadeias conexão** e copie o valor para o campo **Cadeia de conexão primária**; você usará esse valor para configurar seu aplicativo mais tarde.

   ![Recuperar a cadeia de conexão do Cosmos DB][COSMOSDB06]

## <a name="configure-the-sample-application"></a>Configurar o aplicativo de exemplo

1. Abra um shell de comando e clone o projeto de exemplo usando um comando git como no exemplo a seguir:

   ```shell
   git clone https://github.com/spring-guides/gs-accessing-data-mongodb.git
   ```

1. Crie um diretório *recursos* no diretório *&lt;raiz do projeto&gt;/complete/src/main* do exemplo de projeto e crie um arquivo *application.properties*  no diretório *recursos*.

1. Abra o arquivo *application.properties* em um editor de texto e adicione as seguintes linhas ao arquivo e substitua os valores de exemplo pelos valores adequados do início do artigo:

   ```yaml
   spring.data.mongodb.database=wingtiptoysmongodb
   spring.data.mongodb.uri=mongodb://wingtiptoysmongodb:AbCdEfGhIjKlMnOpQrStUvWxYz==@wingtiptoysmongodb.documents.azure.com:10255/?ssl=true&replicaSet=globaldb
   ```
   Em que:

   | Parâmetro | DESCRIÇÃO |
   |---|---|
   | `spring.data.mongodb.database` | Especifica o nome da sua conta do Cosmos DB referida anteriormente neste artigo. |
   | `spring.data.mongodb.uri` | Especifica a **Cadeia de conexão primária** referida anteriormente neste artigo. |

1. Salve e feche o arquivo *application.properties*.

## <a name="package-and-test-the-sample-application"></a>Empacotar e testar o aplicativo de exemplo

Para compilar o aplicativo, navegue até o diretório */gs-accessing-data-mongodb/complete*, que contém o arquivo pom.xml.

1. Compile o aplicativo de exemplo com o Maven e configure o Maven para ignorar os testes. Por exemplo:

   ```shell
   mvn clean package -DskipTests
   ```

1. Inicie o aplicativo de exemplo. Por exemplo:

   ```shell
   java -jar target/accessing-data-mongodb-0.0.1-SNAPSHOT.jar
   ```
    
   Seu aplicativo deve retornar valores como os seguintes:

   ```json
   Customers found with findAll():
   -------------------------------
   Customer[id=5c1b4ae4d0b5080ac105cc13, firstName='Alice', lastName='Smith']
   Customer[id=5c1b4ae4d0b5080ac105cc14, firstName='Bob', lastName='Smith']
   
   Customer found with findByFirstName('Alice'):
   --------------------------------
   Customer[id=5c1b4ae4d0b5080ac105cc13, firstName='Alice', lastName='Smith']
   Customers found with findByLastName('Smith'):
   --------------------------------
   Customer[id=5c1b4ae4d0b5080ac105cc13, firstName='Alice', lastName='Smith']
   Customer[id=5c1b4ae4d0b5080ac105cc14, firstName='Bob', lastName='Smith']
   ```

## <a name="summary"></a>Resumo

Neste tutorial, você criou um aplicativo Java de exemplo que usa o Spring Data para armazenar e recuperar informações usando a API do MongoDB do Azure Cosmos DB.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Spring e o Azure, continue no Spring no Centro de Documentação do Azure.

> [!div class="nextstepaction"]
> [Spring no Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações sobre como usar o Azure com Java, confira [Azure para Desenvolvedores Java] e [Como trabalhar com o Java e o Azure DevOps].

<!-- URL List -->

[Azure para desenvolvedores Java]: /azure/java/
[conta gratuita do Azure]: https://azure.microsoft.com/pricing/free-trial/
[Como trabalhar com o Java e o Azure DevOps]: /azure/devops/
[benefício de assinante do MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Data]: https://spring.io/projects/spring-data
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[COSMOSDB01]: media/configure-spring-data-mongodb-with-cosmos-db/create-cosmos-db-01.png
[COSMOSDB02]: media/configure-spring-data-mongodb-with-cosmos-db/create-cosmos-db-02.png
[COSMOSDB03]: media/configure-spring-data-mongodb-with-cosmos-db/create-cosmos-db-03.png
[COSMOSDB04]: media/configure-spring-data-mongodb-with-cosmos-db/create-cosmos-db-04.png
[COSMOSDB06]: media/configure-spring-data-mongodb-with-cosmos-db/create-cosmos-db-06.png
