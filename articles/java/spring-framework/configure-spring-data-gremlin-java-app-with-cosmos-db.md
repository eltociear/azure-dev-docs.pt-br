---
title: Como usar o Iniciador do Spring Data Gremlin com a API SQL do Azure Cosmos DB
description: Saiba como configurar um aplicativo criado com o Inicializador do Spring Boot com a API SQL do Azure Cosmos DB.
services: cosmos-db
documentationcenter: java
ms.date: 01/10/2020
ms.service: cosmos-db
ms.tgt_pltfrm: multiple
ms.topic: article
ms.workload: data-services
ms.custom: devx-track-java
ms.openlocfilehash: 81a80d14e4cf371801cf75af1618048dda8775d7
ms.sourcegitcommit: b224b276a950b1d173812f16c0577f90ca2fbff4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87810619"
---
# <a name="how-to-use-the-spring-data-gremlin-starter-with-the-azure-cosmos-db-sql-api"></a>Como usar o Iniciador do Spring Data Gremlin com a API SQL do Azure Cosmos DB

## <a name="overview"></a>Visão geral

O Iniciador do Spring Data Gremlin fornece suporte ao Spring Data para a linguagem de consulta Gremlin do Apache, que os desenvolvedores podem usar com qualquer armazenamento de dados compatíveis com Gremlin.

Este artigo demonstra como criar um Azure Cosmos DB usando o portal do Azure para uso com a API do Gremlin. Depois, como usar o **[Spring Initializr]** para criar um aplicativo Java personalizado e, em seguida, como adicionar a funcionalidade de Iniciador do Spring Data Gremlin ao seu aplicativo personalizado para armazenar e recuperar dados de seu Azure Cosmos DB usando Gremlin.

## <a name="prerequisites"></a>Prerequisites

Os seguintes pré-requisitos são obrigatórios para que você siga as etapas neste artigo:

* Uma assinatura do Azure; se ainda não tiver uma assinatura do Azure, você poderá ativar o [benefício de assinante do MSDN] ou inscrever-se para uma [conta gratuita do Azure].
* Um JDK (Java Development Kit) com suporte. Para obter mais informações sobre os JDKs disponíveis para usar durante o desenvolvimento no Azure, confira <https://aka.ms/azure-jdks>.
* [Apache Maven](http://maven.apache.org/) versão 3.0 ou posterior.


## <a name="create-resource"></a>Criar recurso

### <a name="create-azure-cosmos-db"></a>Criar Azure Cosmos DB

1. Navegue até o portal do Azure em <https://portal.azure.com/> e clique em `+Create a resource`.

   >[!div class="mx-imgBorder"]
   >![create-a-resource][create-a-resource-01]

1. Clique em `Databases` e em `Azure Cosmos DB`.

   >[!div class="mx-imgBorder"]
   >![create-azure-cosmos-db][create-a-resource-02]

1. Na página `Azure Cosmos DB`, insira as seguintes informações:

   * Escolha a `Subscription` que você deseja usar para o seu banco de dados.
   * Especifique se deseja criar um novo `Resource Group` para seu banco de dados ou escolher um grupo de recursos existente.
   * Insira um `Account Name` exclusivo para usar como parte do URI do Gremlin para o seu banco de dados. Por exemplo: se você inseriu `account-sample` para o `Account Name`, o URI Gremlin seria `account-samplewingtiptoysdata.gremlin.cosmosdb.azure.com`.
   * Escolha `Gremlin (Graph)` para a API.
   * Especifique o `Location` para seu banco de dados.
   
1. Após especificar essas opções, clique em `Review + create`.

   >[!div class="mx-imgBorder"]
   >![create-azure-cosmos-db-account][create-a-resource-03]

1. Examine a especificação e clique em `Create` para criar seu banco de dados.

### <a name="add-a-graph-to-your-azure-cosmos-database"></a>Adicionar um grafo ao seu Azure Cosmos DB

1. Na página Cosmos DB, clique em `Data Explorer` e em `New Graph`.

   >[!div class="mx-imgBorder"]
   >![new-graph][create-a-graph-01]

1. Quando `Add Graph` for exibida, insira as seguintes informações:

   * Especifique uma `Database id` exclusiva para o seu banco de dados.
   * Você pode optar por especificar sua `Storage capacity` ou pode aceitar o padrão.
   * Especifique uma `Graph id` exclusiva para o seu grafo.
   * Especifique um `Partition key`. Para obter mais informações, confira a [partição do grafo].
Clique em `OK`.
   
   Quando você tiver especificado essas opções, clique em `OK` para criar o grafo.

   >[!div class="mx-imgBorder"]
   >![add-graph][create-a-graph-02]

1. Após a criação do grafo, você pode usar o `Data Explorer` para exibi-lo.

   >[!div class="mx-imgBorder"]
   >![graph-detail][create-a-graph-03]
   
   

## <a name="create-simple-spring-boot-application-with-the-spring-initializr"></a>Criar um aplicativo Spring Boot simples com o Spring Initializr

1. Navegue até <https://start.spring.io/>.

1. Preencha os metadados do projeto e clique em `GENERATE`:

   >[!div class="mx-imgBorder"]
   >![spring-initializr][spring-initializr-01]

1. Descompacte o arquivo e importe-o para o IDE.


## <a name="update-code-according-to-the-sample-project"></a>Atualizar o código de acordo com o projeto de exemplo

Modifique o projeto como o projeto de exemplo: [azure-spring-data-sample-gremlin].

1. Adicionar dependência de `azure-spring-data-gremlin`

1. Excluir todos os conteúdos em `src/test/`

1. Adicione todos os arquivos Java em `src/main/java`, assim como faz esse exemplo.

1. Atualize a configuração em `src/main/resorces/application.properties`, em que:

   | Campo              | Descrição                                                                                                                                                                                                             |
   |--------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
   | `endpoint`         | Especifica o URI do Gremlin para o seu banco de dados, que é derivado da **ID** exclusiva que você especificou ao criar seu Azure Cosmos DB anteriormente no tutorial.                                                 |
   | `port`             | Especifica a porta TCP/IP, que deve ser **443** para HTTPS.                                                                                                                                                           |
   | `username`         | Especifica a **ID de banco de dados** e a **ID do Graph** exclusivas usadas ao adicionar o grafo anteriormente. Essa opção deve ser inserida usando a seguinte sintaxe: “/dbs/**{ID do banco de dados}**/colls/**{Id do Graph}**”. |
   | `password`         | Especifica a **Chave de acesso** primária ou secundária copiada anteriormente neste tutorial.                                                                                                                      |
   | `sslEnabled`       | Especifica se deseja habilitar SSL.                                                                                                                                                                                           |
   | `telemetryAllowed` | Especifique **true** se você quiser habilitar a telemetria; caso contrário, **false**.
   | `maxContentLength` | Especifica o tamanho máximo do conteúdo.                                                                                                                                                                                           |

1. Sobre como obter a senha:

   >[!div class="mx-imgBorder"]
   >![get-password][get-password-01]

## <a name="build-and-run-the-project"></a>Compilar e executar o projeto

1. Crie seu aplicativo Spring Boot com Maven e execute-o; por exemplo:

   ```shell
   mvn clean package
   mvn spring-boot:run
   ```

1. Se o aplicativo for iniciado com sucesso, você poderá verificar o grafo no portal do Azure:

   >[!div class="mx-imgBorder"]
   >![execute-result][execute-result-01]


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Spring e o Azure, continue no Spring no Centro de Documentação do Azure.

> [!div class="nextstepaction"]
> [Spring no Azure](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>Recursos adicionais

Confira estes artigos para obter mais informações sobre o suporte do Azure para Gremlin e API do Graph:

* [Introdução ao Azure Cosmos DB: API do Graph](/azure/cosmos-db/graph-introduction)

* [Suporte do Azure Cosmos DB para grafo do Gremlin](/azure/cosmos-db/gremlin-support)

* [Azure Cosmos DB: Criar um banco de dados de grafo usando o Java e o portal do Azure](/azure/cosmos-db/create-graph-java)

* [Tutorial: Consultar a API do Graph do Azure Cosmos DB usando Gremlin](/azure/cosmos-db/tutorial-query-graph)

* [Iniciador do Spring Data Gremlin]

Para obter mais informações sobre como usar o Azure Cosmos DB e Java, consulte os seguintes artigos:

* [Documentação do Azure Cosmos DB].

* [Banco de dados do Azure Cosmos DB: criar um banco de dados de documentos usando o Java e o portal do Microsoft Azure][Build a SQL API app with Java]

* [Spring Data para a API do SQL do Azure Cosmos DB]

Para obter mais informações sobre como usar aplicativos Spring Boot no Azure, confira os seguintes artigos:

* [Implantar um aplicativo Spring Boot no Serviço de Aplicativo do Azure](deploy-spring-boot-java-app-from-container-registry-using-maven-plugin.md)

* [Executando um Aplicativo Spring Boot em um Cluster Kubernetes no Serviço de Contêiner do Azure](deploy-spring-boot-java-app-on-kubernetes.md)

Para obter mais informações sobre como usar o Azure com Java, confira [Azure para Desenvolvedores Java] e [Como trabalhar com o Java e o Azure DevOps].

O **[Spring Framework]** é uma solução de software livre que ajuda os desenvolvedores Java criar aplicativos de nível empresarial. Um dos projetos mais populares que é criado com base nessa plataforma é o [Spring Boot], que fornece uma abordagem simplificada para a criação de aplicativos Java autônomos. Para ajudar os desenvolvedores a começarem a usar o Spring Boot, vários exemplos de pacotes do Spring Boot estão disponíveis em <https://github.com/spring-guides/>. Além de escolher na lista de projetos básicos do Spring Boot, o **[Spring Initializr]** ajuda os desenvolvedores a começarem a criar aplicativos personalizados do Spring Boot.

<!-- URL List -->

[Documentação do Azure Cosmos DB]: /azure/cosmos-db/
[Azure para desenvolvedores Java]: /azure/developer/java/
[Build a SQL API app with Java]: /azure/cosmos-db/create-sql-api-java 
[Spring Data para a API do SQL do Azure Cosmos DB]: https://azure.microsoft.com/blog/spring-data-azure-cosmos-db-nosql-data-access-on-azure/
[Iniciador do Spring Data Gremlin]: https://github.com/Microsoft/spring-data-gremlin
[conta gratuita do Azure]: https://azure.microsoft.com/pricing/free-trial/
[Como trabalhar com o Java e o Azure DevOps]: /azure/devops/
[benefício de assinante do MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/
[partição do grafo]: https://docs.microsoft.com/azure/cosmos-db/graph-partitioning
[azure-spring-data-sample-gremlin]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-samples/azure-spring-data-sample-gremlin

<!-- IMG List -->

[create-a-resource-01]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/create-a-resource-01.png
[create-a-resource-02]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/create-a-resource-02.png
[create-a-resource-03]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/create-a-resource-03.png

[create-a-graph-01]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/create-a-graph-01.png
[create-a-graph-02]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/create-a-graph-02.png
[create-a-graph-03]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/create-a-graph-03.png

[spring-initializr-01]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/spring-initializr-01.png

[get-password-01]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/get-password-01.png

[execute-result-01]: media/configure-spring-data-gremlin-java-app-with-cosmos-db/execute-result-01.png
