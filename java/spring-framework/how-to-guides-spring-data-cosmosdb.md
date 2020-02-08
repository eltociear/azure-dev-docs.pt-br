---
title: Guia do desenvolvedor do Azure Cosmos DB do Spring Data
description: Este guia descreve o que você deve saber ao usar o SDK do Azure Cosmos DB do Spring Data.
author: kushagraThapar
ms.author: kuthapar
ms.topic: conceptual
ms.date: 1/9/2019
ms.openlocfilehash: 6721a85ca00d277776545c5d717ccdb2948da207
ms.sourcegitcommit: d9f585bea70b01ba6657a75ea245d8519d4a5aad
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2020
ms.locfileid: "76972690"
---
# <a name="spring-data-azure-cosmos-db-developers-guide"></a>Guia do desenvolvedor do Azure Cosmos DB do Spring Data

Este tópico descreve os recursos do [Cosmos DB do Spring Data](https://github.com/microsoft/spring-data-cosmosdb) usando a API do SQL. Este tópico também inclui diretrizes sobre problemas comuns, soluções alternativas e etapas de diagnóstico.

O [Azure Cosmos DB](/azure/cosmos-db/introduction) é um serviço de banco de dados distribuído globalmente que permite aos desenvolvedores trabalhar com os dados usando uma variedade de APIs padrão. O SDK do Cosmos DB do Spring Data é baseado na estrutura [Spring Data](https://spring.io/projects/spring-data) e oferece integração ao Azure Cosmos DB usando a API do SQL. Você pode encontrar informações sobre o suporte para outras APIs nos seguintes tópicos:

- [Como usar a API do MongoDB do Spring Data com o Azure Cosmos DB](/azure/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
- [Como usar a API do Apache Cassandra do Spring Data com o Azure Cosmos DB](/azure/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
- [Como usar o Iniciador do Spring Data Gremlin com a API do SQL do Azure Cosmos DB](/azure/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)

O SDK do Cosmos DB do Spring Data está disponível como software livre no GitHub no repositório [spring-data-cosmosdb](https://github.com/microsoft/spring-data-cosmosdb). Esse repositório tem uma lista [Problemas](https://github.com/microsoft/spring-data-cosmosdb/issues) ativa em que você pode arquivar bugs ou verificar se há soluções sobre problemas que já foram arquivados. Você também pode verificar a lista [Versões](https://github.com/microsoft/spring-data-cosmosdb/releases) para ver se um problema foi corrigido em uma versão mais recente. A versão de treinamento do SDK do Cosmos DB do Spring Data versão 2.2.x dá suporte à versão 2.2.0.RELEASE de spring-data-commons, enquanto a versão de treinamento da versão 2.1.x do SDK dá suporte à versão 2.1.0.RELEASE de spring-data-common.

## <a name="available-features"></a>Recursos disponíveis

As seções a seguir descrevem os recursos disponíveis no momento.

### <a name="crudrepository-and-reactivecrudrepository-support"></a>Suporte a CrudRepository e a ReactiveCrudRepository

O SDK do Cosmos DB do Spring Data fornece as interfaces `CosmosRepository` e `ReactiveCosmosRepository`, que estendem as interfaces `CrudRepository` e `ReactiveCrudRepository` do Spring Data.

O exemplo a seguir mostra como estender essas interfaces.

```java
@Repository
public interface SampleRepository extends CosmosRepository<SampleEntity, String> {
    List<SampleEntity> findByName(String name);
}

@Repository
public interface ReactiveSampleRepository extends ReactiveCosmosRepository<SampleEntity, String> {
    Flux<SampleEntity> findByName(String name);
}
```

Dependendo do uso, você precisa habilitar ambos os repositórios separadamente na classe `Configuration`. Por exemplo:

```java
@Configuration
@PropertySource(value = {"classpath:application.properties"})
@EnableCosmosRepositories
@EnableReactiveCosmosRepositories
public class TestRepositoryConfig extends AbstractCosmosConfiguration {
    ...
}
```

### <a name="define-a-simple-entity"></a>Definir uma entidade simples

Você pode definir entidades adicionando a anotação `@Document` e especificando propriedades relacionadas à coleção, como o nome da coleção, as RUs (unidades de solicitação), a vida útil e o sinalizador da coleção de criação automática.

Por padrão, o nome da coleção será o nome de classe da classe de domínio do usuário. Para personalizá-lo, adicione a anotação `@Document(collection="myCustomCollectionName")` à classe de domínio. O campo de coleção também dá suporte a expressões [SpEL](https://docs.spring.io/spring/docs/3.0.x/reference/expressions.html) (Spring Expression Language); dessa forma, você pode fornecer nomes de coleção de maneira programática por meio das propriedades de configuração. Por exemplo, você pode usar expressões como `collection = "${dynamic.collection.name}"` e `collection = "#{@someBean.getCollectionName()}"`.

Há duas maneiras de mapear um campo em uma classe de domínio para o campo `id` de um documento do Azure Cosmos DB:

- Anote o campo com `@Id`.
- Defina o nome do campo como `id`.

O exemplo a seguir mostra o uso das anotações `@Document` e `@Id`.

```java
@Document(collection = "myCollection")
class MyDocument {

    @Id
    private String myId;

    @PartitionKey
    private String data;

    @Version
    private String _etag;
}
```

Por padrão, `IndexingPolicy` é definido pelo serviço do Azure. Para personalizá-lo, adicione a anotação `@DocumentIndexingPolicy` à classe de domínio. Essa anotação tem quatro atributos:

```java
boolean automatic;     // Indicates whether the indexing policy is automatic.
IndexingMode mode;     // The indexing policy mode; the options are Consistent, Lazy, or None.
String[] includePaths; // Included paths for indexing.
String[] excludePaths; // Excluded paths for indexing.
```

O SDK também dá suporte ao particionamento. Para obter mais informações, confira [Particionamento e dimensionamento horizontal no Azure Cosmos DB](/azure/cosmos-db/partition-data). Para especificar um campo de uma classe de domínio como a chave da partição, anote-o com `@PartitionKey`. Em seguida, ao executar operações CRUD, especifique o valor da partição.

O exemplo a seguir mostra como usar a anotação `@PartitionKey` ao executar operações CRUD.

```java
@Document(ru = "400")
public class Address {
    @Id
    String postalCode;

    @PartitionKey
    String city;

    String street;
    String country;
    String phoneNumber;

    ...
}

class AddressService {

    @Autowired
    AddressRepository repository;

    final Address newAddress = new Address("12345", "city");

    // There's no need to specify a partition key in the save operation.
    repository.save(updatedAddress);

    // Provide a partition key when performing a find-by-id operation.
    final Optional<Address> addressById = repository.findById("12345", new PartitionKey("city"));

    final Address foundAddress = addressById.get();

    // Provide a partition key when performing a delete-by-id operation.
    repository.deleteById(foundAddress.getPostalCode(), new PartitionKey(foundAddress.getCity())); 
}
```

O SDK também dá suporte a operações personalizadas de localização de consulta do Spring Data, como `findByAFieldAndBField`. Para obter mais informações, confira [Definir Métodos de Consulta](https://docs.spring.io/spring-data/commons/docs/current/reference/html/#repositories.query-methods.details) na documentação do Spring.

## <a name="best-practices"></a>Práticas recomendadas

As seções a seguir descrevem as melhores práticas ao usar o SDK.

### <a name="configuring-the-application"></a>Configurando o aplicativo

Use as seguintes etapas para configurar o aplicativo:

1. Estenda a classe `AbstractCosmosConfiguration` para definir a configuração do aplicativo (chave do Cosmos DB, URL, nome do banco de dados e assim por diante).
1. Adicione a anotação `@Configuration`.
1. Dependendo do uso do repositório, adicione uma ou as duas anotações `@EnableCosmosRepositories` e `@EnableReactiveCosmosRepositories`.

O recurso `CosmosKeyCredential` permite girar chaves de forma dinâmica. Você pode alternar chaves usando o método `switchToSecondaryKey`.

O código de exemplo a seguir mostra uma configuração do aplicativo e demonstra o uso de `switchToSecondaryKey`.

```java
@Configuration
@EnableCosmosRepositories
public class AppConfiguration extends AbstractCosmosConfiguration {

    @Value("${azure.cosmosdb.uri}")
    private String uri;

    @Value("${azure.cosmosdb.key}")
    private String key;

    @Value("${azure.cosmosdb.secondaryKey}")
    private String secondaryKey;

    @Value("${azure.cosmosdb.database}")
    private String dbName;

    @Value("${azure.cosmosdb.populateQueryMetrics}")
    private boolean populateQueryMetrics;

    private CosmosKeyCredential cosmosKeyCredential;

    @Bean
    public CosmosDBConfig getConfig() {
        this.cosmosKeyCredential = new CosmosKeyCredential(key);
        CosmosDbConfig cosmosdbConfig = CosmosDBConfig.builder(uri,
            this.cosmosKeyCredential, dbName).build();
        cosmosdbConfig.setPopulateQueryMetrics(populateQueryMetrics);
        cosmosdbConfig.setResponseDiagnosticsProcessor(new ResponseDiagnosticsProcessorImplementation());
        return cosmosdbConfig;
    }

    public void switchToSecondaryKey() {
        this.cosmosKeyCredential.key(secondaryKey);
    }
}
```

Você também pode personalizar a configuração para alterar o modo de conexão, o tamanho máximo do pool de conexão, o tempo limite da solicitação e assim por diante, conforme mostrado no exemplo a seguir.

```java
public CosmosDBConfig getConfig() {

    this.cosmosKeyCredential = new CosmosKeyCredential(key);
    ConnectionPolicy customizedConnectionPolicy = new ConnectionPolicy();

    // Set the connection mode to Direct (TCP).
    customizedConnectionPolicy.setConnectionMode(ConnectionMode.DIRECT);

    // Set the maximum number of HTTP/TCP connections to 1000 per application.
    customizedConnectionPolicy.setMaxPoolSize(1000);

    // Set the request timeout to 10 seconds.
    customizedConnectionPolicy.requestTimeoutInMillis(10000);

    // Set the idle connection timeout to two minutes.
    customizedConnectionPolicy.idleConnectionTimeoutInMillis(120000);
    CosmosDBConfig cosmosDbConfig = CosmosDBConfig.builder(uri,   this.cosmosKeyCredential, dbName)
                                                  .connectionPolicy  (customizedConnectionPolic  y)
                                                  .build();
    return cosmosDbConfig;
}
```

### <a name="response-diagnostics-and-query-metrics"></a>Diagnóstico de resposta e métricas de consulta

A versão 2.2.x do SDK do Cosmos DB do Spring Data é compatível com a cadeia de caracteres de diagnóstico de resposta e com as métricas de consulta.

Para habilitar as métricas de consulta, defina o sinalizador `populateQueryMetrics` como **true** no arquivo `application.properties`. Em seguida, estenda a interface `ResponseDiagnosticsProcessor` e implemente o método `processResponseDiagnostics` para registrar as informações de diagnóstico em log. Por fim, passe uma instância da sua implementação para o método `CosmosDbConfig.setResponseDiagnosticsProcessor`. O código a seguir mostra um exemplo de implementação.

```java
@Configuration
@EnableCosmosRepositories
public class AppConfiguration extends AbstractCosmosConfiguration {

    ...

    @Value("${azure.cosmosdb.populateQueryMetrics}")
    private boolean populateQueryMetrics;

    private static class ResponseDiagnosticsProcessorImplementation implements ResponseDiagnosticsProcessor {

        @Override
        public void processResponseDiagnostics(@Nullable ResponseDiagnostics responseDiagnostics) {
            log.info("Response Diagnostics {}", responseDiagnostics);
        }
    }

    @Bean
    public CosmosDBConfig getConfig() {
        this.cosmosKeyCredential = new CosmosKeyCredential(key);
        CosmosDbConfig cosmosdbConfig = CosmosDBConfig.builder(uri, this.cosmosKeyCredential, dbName).build();
        cosmosdbConfig.setPopulateQueryMetrics(populateQueryMetrics);
        cosmosdbConfig.setResponseDiagnosticsProcessor(new ResponseDiagnosticsProcessorImplementation());
        return cosmosdbConfig;
    }
}
```

### <a name="pagination-and-sorting"></a>Paginação e classificação

O SDK do Cosmos DB do Spring Data é compatível com a paginação e a classificação do Spring Data. Para obter informações, confira [Tratamento de parâmetro especial](https://docs.spring.io/spring-data/commons/docs/current/reference/html/#repositories.special-parameters) na documentação do Spring.

Com base nas RUs (unidades de solicitação) disponíveis na conta do banco de dados, o Cosmos DB pode retornar documentos menores ou iguais ao tamanho solicitado. Para obter mais informações, consulte [Unidades de Solicitação no Azure Cosmos DB](/azure/cosmos-db/request-units).

Você não deve depender do valor `totalPageSize`, porque o número de documentos retornados em cada iteração é variável. Em vez disso, você deve iterar um objeto `Pageable`, conforme mostrado no exemplo a seguir.

```java
final Sort sort = Sort.by(Sort.Direction.DESC, "name");
final CosmosPageRequest pageRequest = new CosmosPageRequest(0, pageSize,   null, sort);
Page<T> page = tRepository.findAll(pageRequest);
List<T> pageContent = page.getContent();
while(page.hasNext()) {
    Pageable nextPageable = page.nextPageable();
    page = repository.findAll(nextPageable);
    pageContent = page.getContent();
}
```

## <a name="common-issues-and-workarounds"></a>Problemas comuns e soluções alternativas

As seções a seguir descrevem os problemas que você deve conhecer ao usar o SDK do Cosmos DB do Spring Data.

### <a name="getting-the-correct-cosmos-db-configuration"></a>Obter a configuração correta do Cosmos DB

Estender a interface `AbstractCosmosConfiguration` pode ser complicado devido a várias anotações e configurações presentes na classe. O problema mais comum é com a anotação `Enable Repositories`.

Se os repositórios estenderem `CosmosRepository`, adicione a anotação `@EnableCosmosRepositories`. Se os repositórios estenderem `ReactiveCosmosRepository`, adicione a anotação `@EnableReactiveCosmosRepositories`. O exemplo a seguir demonstra o uso dessas anotações.

```java
@Configuration
@PropertySource(value = {"classpath:application.properties"})
@EnableCosmosRepositories
@EnableReactiveCosmosRepositories
public class TestRepositoryConfig extends AbstractCosmosConfiguration {
    ...
}
```

Ao criar ou personalizar um bean `CosmosDBConfig`, use o objeto `CosmosKeyCredential` em vez de usar a chave diretamente.

O recurso `CosmosKeyCredential` permite girar chaves de forma dinâmica. Você pode alternar chaves usando o método `switchToSecondaryKey`.

O `CosmosKeyCredential` deve ser um objeto singleton porque o SDK do Cosmos DB usa o mesmo objeto internamente para detectar alterações no valor da chave dentro desse objeto.

### <a name="custom-query-execution"></a>Execução de consulta personalizada

O recurso de anotação de consulta ainda não é compatível com o SDK do Cosmos DB do Spring Data. Até lá, você pode executar consultas personalizadas e complexas diretamente no bean `cosmosClient` exposto pelo contexto do aplicativo Spring.

O código a seguir mostra um exemplo simples de como executar as consultas de deslocamento e de limite usando o bean `cosmosClient`.

```java
final FeedOptions feedOptions = new FeedOptions();

// Enable cross-partition queries.
feedOptions.enableCrossPartitionQuery(true);

// Set the page size.
feedOptions.maxItemCount(20);

// Set the number of parallel operations on the client-side SDK when executing parallel queries.
feedOptions.maxDegreeOfParallelism(2);

// Populate query metrics from Cosmos DB.
feedOptions.populateQueryMetrics(true);

final String query = "SELECT * from c OFFSET " + skipCount + " LIMIT " + takeCount;

final CosmosClient cosmosClient = applicationContext.getBean(CosmosClient.class);

Flux<FeedResponse<CosmosItemProperties>> feedResponseFlux =
    cosmosClient.getDatabase(databaseId)
                .getContainer(collectionId)
                .queryItems(query, feedOptions);
    feedResponseFlux.subscribeOn(Schedulers.parallel())
                    .flatMap(feedResponse -> {
                        List<CosmosItemProperties> results =
                        feedResponseFlux.results();
                        log.info("Results are {}", results);
                        return feedResponse;
                    })
                    .subscribe();
```

### <a name="enable-diagnostics-and-query-metrics"></a>Habilitar diagnóstico e métricas de consulta

Ao depurar, é útil ter a cadeia de caracteres de diagnóstico de resposta e as métricas de consulta do SDK do Cosmos DB. O SDK do Cosmos DB registra a cadeia de caracteres de diagnóstico de resposta em log no lado do cliente. O back-end registra as métricas de consulta em log e as fornece ao SDK do Cosmos DB.

O método `ResponseDiagnosticsProcessor.processResponseDiagnostics` é chamado após cada chamada à API no SDK do Cosmos DB do Spring Data. Portanto, é importante que sua implementação tenha alto desempenho, sendo livre de bugs e evitando a complexidade. Por exemplo, você não deve registrar o conjunto completo de informações de diagnóstico em log nesse método, pois a quantidade de informações envolvidas criaria um custo de desempenho significativo. Você também deve usar o nível de registros em log `Debug` para evitar afetar o desempenho do aplicativo.

O código a seguir mostra um exemplo de como implementar a interface `ResponseDiagnosticsProcessor`.

```java
private static class ResponseDiagnosticsProcessorImplementation implements ResponseDiagnosticsProcessor {

    @Override
    public void processResponseDiagnostics(@Nullable ResponseDiagnostics responseDiagnostics) {

        // To log everything:
        if (log.isDebugEnabled()) {
            log.debug("Response diagnostics {}", responseDiagnostics);
        }

        // To log Cosmos DB response diagnostics:
        if (responseDiagnostics != null && log.isDebugEnabled()) {
            CosmosResponseDiagnostics cosmosResponseDiagnostics = responseDiagnostics.getCosmosResponseDiagnostics();
            log.debug("Cosmos DB response diagnostics {}", cosmosResponseDiagnostics);
        }

        // To log just the request latency:
        if (responseDiagnostics != null && log.isDebugEnabled()) {
            CosmosResponseDiagnostics cosmosResponseDiagnostics = responseDiagnostics.getCosmosResponseDiagnostics();
            log.debug("Request latency {}", cosmosResponseDiagnostics.requestLatency());
        }

        // To log query metrics:
        if (responseDiagnostics != null && log.isDebugEnabled()) {
            FeedResponseDiagnostics feedResponseDiagnostics =
                responseDiagnostics.getFeedResponseDiagnostics();
            log.debug("Query metrics {}", feedResponseDiagnostics);
        }
    }
}
```

## <a name="how-to-troubleshoot"></a>Como solucionar os problemas

As seções a seguir descrevem maneiras de solucionar problemas comuns.

### <a name="connection-issues"></a>Problemas de conexão

Se você tiver problemas de conexão, verifique se todas as anotações necessárias na classe de configuração estão presentes e corretas, conforme descrito na seção [Obter a configuração correta do Cosmos DB](#getting-the-correct-cosmos-db-configuration).

### <a name="api-exceptions"></a>Exceções de API

A versão 2.2.1 do SDK do Cosmos DB do Spring Data fornece os seguintes aprimoramentos ao tratamento de exceções:

- Todas as APIs geram `CosmosDBAccessException`, que expõe um campo `cosmosClientException` por meio de um getter.
- O SDK do Cosmos DB gera `CosmosClientException`, que você pode usar para implementar qualquer lógica de repetição no lado do cliente.
- As exceções comuns à repetição são aquelas com as mensagens `Resource already exists`, `Request rate too large`, `Request timeout exception` e assim por diante.

### <a name="api-or-query-slowness"></a>Lentidão da API ou da consulta

Se você tiver altas latências em APIs ou em execuções de consulta, experimente registrar as cadeias de caracteres de diagnóstico e as métricas de consulta em log conforme descrito na seção [Habilitar Diagnóstico e Métricas de Consulta](#enable-diagnostics-and-query-metrics). Verifique o uso da CPU, a largura de banda da rede e o espaço em disco de E/S, que podem ser as causas raiz da lentidão do lado do cliente.
