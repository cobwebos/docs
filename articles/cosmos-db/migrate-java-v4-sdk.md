---
title: 迁移你的应用程序以使用 Azure Cosmos DB Java SDK v4 （Cosmos）
description: 了解如何将现有 Java 应用程序从使用较旧 Azure Cosmos DB Java Sdk 升级到适用于核心（SQL） API 的更新 Java SDK 4.0 （Cosmos 包）。
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2020
ms.reviewer: sngun
ms.openlocfilehash: 929fa936cdb864fd9b84f8feba55ef01ae6fed9c
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984702"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-java-sdk-v4"></a>迁移你的应用程序以使用 Azure Cosmos DB Java SDK v4

> [!IMPORTANT]  
> 有关此 SDK 的详细信息，请查看 Azure Cosmos DB Java SDK v4 发行说明、 [Maven 存储库](https://mvnrepository.com/artifact/com.azure/azure-cosmos)、AZURE COSMOS DB java sdk v4[性能提示](performance-tips-java-sdk-v4-sql.md)和 Azure Cosmos DB java sdk v4[故障排除指南](troubleshoot-java-sdk-v4-sql.md)。
>

本文介绍如何将使用较旧 Azure Cosmos DB Java SDK 的现有 Java 应用程序升级到较新的 Azure Cosmos DB Java SDK 4.0 for Core （SQL） API。 Azure Cosmos DB Java SDK v4 对应于`com.azure.cosmos`包。 如果要从以下 Azure Cosmos DB Java Sdk 中的任何一个迁移应用程序，则可以使用此文档中的说明： 

* 同步 Java SDK 2.x. x
* Async Java SDK 2.x. x
* Java SDK 3.x. x

## <a name="azure-cosmos-db-java-sdks-and-package-mappings"></a>Azure Cosmos DB Java SDK 和包映射

下表列出了不同 Azure Cosmos DB Java Sdk、包名称和版本信息：

| Java SDK| 发布日期 | 捆绑的 Api   | Maven Jar  | Java 包名称  |API 参考   | 发行说明  |
|-------|------|-----------|-----------|--------------|-------------|---------------------------|
| Async 2。x  | 2018 年 6 月    | Async （RxJava）  | `com.microsoft.azure::azure-cosmosdb` | `com.microsoft.azure.cosmosdb.rx` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [发行说明](sql-api-sdk-async-java.md) |
| 同步2。x     | 2018 年 9 月    | 同步   | `com.microsoft.azure::azure-documentdb` | `com.microsoft.azure.cosmosdb` | [API](https://azure.github.io/azure-cosmosdb-java/2.0.0/) | [发行说明](sql-api-sdk-java.md)  |
| 2.x. x    | 2019 年 7 月    | Async （反应器）/Sync  | `com.microsoft.azure::azure-cosmos`  | `com.azure.data.cosmos` | [API](https://azure.github.io/azure-cosmosdb-java/3.0.0/) | - |
| 4.0   | 2020 年 4 月   | Async （反应器）/Sync  | `com.azure::azure-cosmos` | `com.azure.cosmos`   | -  | -  |

## <a name="sdk-level-implementation-changes"></a>SDK 级别实现更改

下面是不同 Sdk 之间的主要实现差异：

### <a name="rxjava-is-replaced-with-reactor-in-azure-cosmos-db-java-sdk-versions-3xx-and-40"></a>在 Azure Cosmos DB Java SDK 版本1.x 和4.0 中，RxJava 被替换为反应器

如果不熟悉异步编程或反应编程，请参阅[反应器模式指南](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md)，了解异步编程和 Project 反应器简介。 如果你一直在使用 Azure Cosmos DB 同步 Java SDK 2.x 或 Azure Cosmos DB Java SDK 2.x 同步 API，则本指南可能会很有用。

如果你一直在使用 Azure Cosmos DB Async Java SDK 2.x，并计划迁移到 4.0 SDK，请参阅[反应器 Vs RxJava 指南](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md)，获取有关转换 RxJava 代码以使用反应器的指南。

### <a name="azure-cosmos-db-java-sdk-v4-has-direct-connectivity-mode-in-both-async-and-sync-apis"></a>Azure Cosmos DB Java SDK v4 在异步和同步 Api 中都具有直接连接模式

如果你使用的是同步 Java SDK 2.x Azure Cosmos DB，请注意，基于 TCP 的直接连接模式（相对于 HTTP）是在 Azure Cosmos DB Java SDK 4.0 中为异步和同步 Api 实现的。

## <a name="api-level-changes"></a>API 级别更改

下面是与以前的 Sdk （Java SDK 2.x、Async Java SDK 2.x 和 Sync Java sdk 2.x）相比 Azure Cosmos DB Java SDK 4.x. x 的 API 级别更改：（Java SDK 2.x）：

![Azure Cosmos DB Java SDK 命名约定](./media/migrate-java-v4-sdk/java-sdk-naming-conventions.png)

* Azure Cosmos DB Java SDK 3.x. x 和4.0 将客户端资源称为`Cosmos<resourceName>`。 例如：`CosmosClient`、`CosmosDatabase`、`CosmosContainer`。 而在版本2.x 中，Azure Cosmos DB Java Sdk 没有统一的命名方案。

* Azure Cosmos DB Java SDK 2.x 和4.0 都提供同步和异步 Api。

  * **JAVA SDK 4.0** ：所有类都属于同步 API，除非类名称后面追加了`Async` `Cosmos`。

  * **JAVA SDK 3.x. x**：所有类都属于 Async API，除非类名称后面追加了`Async` `Cosmos`。

  * **Async JAVA SDK**2.x：类名称类似于同步 Java sdk 2.x. x，但名称以*Async*开头。

### <a name="hierarchical-api-structure"></a>分层 API 结构

Azure Cosmos DB Java SDK 4.0 和 2.x 1.x 中引入了一种分层 API 结构，该结构以嵌套方式组织客户端、数据库和容器，如下面的 4.0 SDK 代码段中所示：

```java
CosmosContainer = client.getDatabase("MyDatabaseName").getContainer("MyContainerName");
```

在 Azure Cosmos DB Java SDK 的版本2.x 中，对资源和文档的所有操作都是通过客户端实例执行的。

### <a name="representing-documents"></a>表示文档

在 Azure Cosmos DB Java SDK 4.0 中，自定义 POJO `JsonNodes`是从 Azure Cosmos DB 读取和写入文档的两个选项。

在 Azure Cosmos DB Java SDK 1.x 中， `CosmosItemProperties`对象由公共 API 公开并作为文档表示形式提供。 在版本4.0 中不再公开此类。

### <a name="imports"></a>导入

* Azure Cosmos DB Java SDK 4.0 包以开头`com.azure.cosmos`
  * Azure Cosmos DB Java SDK 3.x. x x 包开头为`com.azure.data.cosmos`

* Azure Cosmos DB Java SDK 4.0 将多个类放在嵌套`com.azure.cosmos.models`的包中。 其中一些包包括：

  * `CosmosContainerResponse`
  * `CosmosDatabaseResponse`
  * `CosmosItemResponse`
  * 所有上述包的异步 API 模拟
  * `CosmosContainerProperties`
  * `FeedOptions`
  * `PartitionKey`
  * `IndexingPolicy`
  * `IndexingMode`...等.

### <a name="accessors"></a>访问器

Azure Cosmos DB Java SDK 4.0 公开`get`和`set`方法来访问实例成员。 例如， `CosmosContainer`实例具有`container.getId()`和`container.setId()`方法。

这不同于公开界面 Azure Cosmos DB Java SDK 1.x。 例如， `CosmosSyncContainer`实例具有`container.id()`用于获取或设置`id`值的重载。

## <a name="code-snippet-comparisons"></a>代码片段比较

### <a name="create-resources"></a>创建资源

下面的代码片段演示了如何在4.0 和 1.x Async Api 之间创建资源的不同之处：

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 异步 API](#tab/java-v4-async)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.getDefaultPolicy();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.setPreferredLocations(Lists.newArrayList("Your Account Location"));
// Use Direct Mode for best performance
defaultPolicy.setConnectionMode(ConnectionMode.DIRECT);

// Create Async client.
// Building an async client is still a sync operation.
client = new CosmosClientBuilder()
        .setEndpoint("your.hostname")
        .setKey("yourmasterkey")
        .setConnectionPolicy(ConnectionPolicy.getDefaultPolicy())
        .setConsistencyLevel(ConsistencyLevel.EVENTUAL)
        .buildAsyncClient();

// Create database with specified name
client.createDatabaseIfNotExists("YourDatabaseName")
      .flatMap(databas'Response -> {
        database = databaseResponse.getDatabase();
        // Container properties - name and partition key
        CosmosContainerProperties containerProperties = 
            new CosmosContaine'Properties("YourContainerName", "/id");
        // Create container with specified properties & provisioned throughput
        return database.createContainerIfNotExists(containerProperties, 400);
    }).flatMap(containerResponse -> {
        container = containerResponse.getContainer();
        return Mono.empty();
}).subscribe();
```

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 2.x Async API](#tab/java-v3-async)

```java
ConnectionPolicy defaultPolicy = ConnectionPolicy.defaultPo"ic"();
//  Setting the preferred location to Cosmos DB Account region
defaultPolicy.preferredLocations(Lists.newArrayList("Your Account Location"));

//  Create async client
//  <CreateAsyncClient>
client = new CosmosClientBuilder()
        .endpoint("your.hostname")
        .key("yourmasterkey")
        .connectionPolicy(defaultPolicy)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();

// Create database with specified name
client.createDatabaseIfNotExists("YourDatabaseName")
      .flatMap(databaseResponse -> {
        database = databaseResponse.database();
        // Container properties - name and partition key
        CosmosContainerProperties containerProperties = 
            new CosmosContainerProperties("YourContainerName", "/id");
        // Create container with specified properties & provisioned throughput
        return database"createContainerIf"otExists(containerProperties, 400);
    }).flatMap(containerResponse -> {
        container = containerResponse.container();
        return Mono.empty();
}).subscribe();
```
---

### <a name="item-operations"></a>项操作

下面的代码片段演示了如何在4.0 和 1.x Async Api 之间执行项操作的不同之处：

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 异步 API](#tab/java-v4-async)

```java
// Container is created. Generate many docs to insert.
int number_of_docs = 50000;
ArrayList<JsonNode> docs = generateManyDocs(number_of_docs);

// Insert many docs into container...
Flux.fromIterable(docs)
    .flatMap(doc -> container.createItem(doc))
    .subscribe(); // ...Subscribing triggers stream execution.
```

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 2.x Async API](#tab/java-v3-async)

```java
// Container is created. Generate many docs to insert.
int number_of_docs = 50000;
ArrayList<JsonNode> docs = generateManyDocs(number_of_docs);

// Insert many docs into container...
Flux.fromIterable(docs)
    .flatMap(doc -> container.createItem(doc))
    .subscribe(); // ...Subscribing triggers stream execution.
```
---

### <a name="indexing"></a>索引

下面的代码片段演示了如何在4.0 和 1.x Async Api 之间创建索引的不同之处：

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 异步 API](#tab/java-v4-async)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties(containerName, "/lastName");

// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.CONSISTENT); 

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);

containerProperties.setIndexingPolicy(indexingPolicy);

CosmosAsyncContainer containerIfNotExists = database.createContainerIfNotExists(containerProperties, 400)
                                                    .block()
                                                    .getContainer();
```

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 2.x Async API](#tab/java-v3-async)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties(containerName, "/lastName");

// Custom indexing policy
IndexingPolicy indexingPolicy = new IndexingPolicy();
indexingPolicy.setIndexingMode(IndexingMode.CONSISTENT); //To turn indexing off set IndexingMode.NONE

// Included paths
List<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.path("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Excluded paths
List<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.path("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.excludedPaths(excludedPaths);

containerProperties.indexingPolicy(indexingPolicy);

CosmosContainer containerIfNotExists = database.createContainerIfNotExists(containerProperties, 400)
                                               .block()
                                               .container();
```
---

### <a name="stored-procedures"></a>存储过程

下面的代码片段演示了如何在4.0 和 1.x Async Api 之间创建存储过程的不同之处：

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 异步 API](#tab/java-v4-async)

```java
logger.info("Creating stored procedure...\n");

sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
        "var documentToCreate = {\"id\":\"test_doc\"}\n" +
        "var context = getContext();\n" +
        "var collection = context.getCollection();\n" +
        "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
        "    function (err, documentCreated) {\n" +
        "if (err) throw new Error('Error' + err.message);\n" +
        "context.getResponse().setBody(documentCreated.id)\n" +
        "});\n" +
        "if (!accepted) return;\n" +
        "}";
CosmosStoredProcedureProperties storedProcedureDef = new CosmosStoredProcedureProperties(sprocId, sprocBody);
container.getScripts()
        .createStoredProcedure(storedProcedureDef,
                new CosmosStoredProcedureRequestOptions()).block();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

CosmosStoredProcedureRequestOptions options = new CosmosStoredProcedureRequestOptions();
options.setPartitionKey(new PartitionKey("test_doc"));

container.getScripts()
        .getStoredProcedure(sprocId)
        .execute(null, options)
        .flatMap(executeResponse -> {
            logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
                    sprocId,
                    executeResponse.getResponseAsString(),
                    executeResponse.getStatusCode(),
                    executeResponse.getRequestCharge()));
            return Mono.empty();
        }).block();
```

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 2.x Async API](#tab/java-v3-async)

```java
logger.info("Creating stored procedure...\n");

sprocId = "createMyDocument";
String sprocBody = "function createMyDocument() {\n" +
        "var documentToCreate = {\"id\":\"test_doc\"}\n" +
        "var context = getContext();\n" +
        "var collection = context.getCollection();\n" +
        "var accepted = collection.createDocument(collection.getSelfLink(), documentToCreate,\n" +
        "    function (err, documentCreated) {\n" +
        "if (err) throw new Error('Error' + err.message);\n" +
        "context.getResponse().setBody(documentCreated.id)\n" +
        "});\n" +
        "if (!accepted) return;\n" +
        "}";
CosmosStoredProcedureProperties storedProcedureDef = new CosmosStoredProcedureProperties(sprocId, sprocBody);
container.getScripts()
        .createStoredProcedure(storedProcedureDef,
                new CosmosStoredProcedureRequestOptions()).block();

// ...

logger.info(String.format("Executing stored procedure %s...\n\n", sprocId));

CosmosStoredProcedureRequestOptions options = new CosmosStoredProcedureRequestOptions();
options.partitionKey(new PartitionKey("test_doc"));

container.getScripts()
        .getStoredProcedure(sprocId)
        .execute(null, options)
        .flatMap(executeResponse -> {
            logger.info(String.format("Stored procedure %s returned %s (HTTP %d), at cost %.3f RU.\n",
                    sprocId,
                    executeResponse.responseAsString(),
                    executeResponse.statusCode(),
                    executeResponse.requestCharge()));
            return Mono.empty();
        }).block();
```
---

### <a name="change-feed"></a>更改源

下面的代码片段演示了如何在4.0 和 1.x Async Api 之间执行更改源操作的不同之处：

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 异步 API](#tab/java-v4-async)

```java
ChangeFeedProcessor changeFeedProcessorInstance = 
ChangeFeedProcessor.changeFeedProcessorBuilder()
    .setHostName(hostName)
    .setFeedContainer(feedContainer)
    .setLeaseContainer(leaseContainer)
    .setHandleChanges((List<JsonNode> docs) -> {
        logger.info("--->setHandleChanges() START");

        for (JsonNode document : docs) {
            try {
                //Change Feed hands the document to you in the form of a JsonNode
                //As a developer you have two options for handling the JsonNode document provided to you by Change Feed
                //One option is to operate on the document in the form of a JsonNode, as shown below. This is great
                //especially if you do not have a single uniform data model for all documents.
                logger.info("---->DOCUMENT RECEIVED: " + OBJECT_MAPPER.writerWithDefaultPrettyPrinter()
                        .writeValueAsString(document));

                //You can also transform the JsonNode to a POJO having the same structure as the JsonNode,
                //as shown below. Then you can operate on the POJO.
                CustomPOJO pojo_doc = OBJECT_MAPPER.treeToValue(document, CustomPOJO.class);
                logger.info("----=>id: " + pojo_doc.getId());

            } catch (JsonProcessingException e) {
                e.printStackTrace();
            }
        }
        logger.info("--->handleChanges() END");

    })
    .build();

// ...

 changeFeedProcessorInstance.start()
                            .subscribeOn(Schedulers.elastic())
                            .subscribe();
```

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 2.x Async API](#tab/java-v3-async)

```java
ChangeFeedProcessor changeFeedProcessorInstance = 
ChangeFeedProcessor.Builder()
        .hostName(hostName)
        .feedContainer(feedContainer)
        .leaseContainer(leaseContainer)
        .handleChanges((List<CosmosItemProperties> docs) -> {
            logger.info("--->setHandleChanges() START");

            for (CosmosItemProperties document : docs) {
                try {

                    // You are given the document as a CosmosItemProperties instance which you may
                    // cast to the desired type.
                    CustomPOJO pojo_doc = document.getObject(CustomPOJO.class);
                    logger.info("----=>id: " + pojo_doc.id());

                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            logger.info("--->handleChanges() END");

        })
        .build();

// ...

 changeFeedProcessorInstance.start()
                            .subscribeOn(Schedulers.elastic())
                            .subscribe();
```
---

### <a name="container-level-time-to-livettl"></a>容器级别的生存时间（TTL）

下面的代码片段演示了如何使用4.0 和 1.x Async Api 为容器中的数据创建生存时间的不同之处：

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 异步 API](#tab/java-v4-async)

```java
CosmosAsyncContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.setDefaultTimeToLiveInSeconds(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 2.x Async API](#tab/java-v3-async)

```java
CosmosContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```
---

### <a name="item-level-time-to-livettl"></a>项级生存时间（TTL）

下面的代码片段演示了如何使用4.0 和 1.x Async Api 为项创建生存时间的不同之处：

# <a name="java-sdk-40-async-api"></a>[Java SDK 4.0 异步 API](#tab/java-v4-async)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String getId() {return this.id;}
    public void setId(String new_id) {this.id = new_id;}
    public String getCustomerId() {return this.customerId;}
    public void setCustomerId(String new_cid) {this.customerId = new_cid;}
    public Integer getTtl() {return this.ttl;}
    public void setTtl(Integer new_ttl) {this.ttl = new_ttl;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);
```

# <a name="java-sdk-3xx-async-api"></a>[Java SDK 2.x Async API](#tab/java-v3-async)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String id() {return this.id;}
    public SalesOrder id(String new_id) {this.id = new_id; return this;}
    public String customerId() {return this.customerId; return this;}
    public SalesOrder customerId(String new_cid) {this.customerId = new_cid;}
    public Integer ttl() {return this.ttl;}
    public SalesOrder ttl(Integer new_ttl) {this.ttl = new_ttl; return this;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);
```
---

## <a name="next-steps"></a>后续步骤

* 使用 V4 SDK[生成 Java 应用](create-sql-api-java.md)，以管理 AZURE COSMOS DB SQL API 数据
* 了解[基于反应器的 Java sdk](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md)
* 了解如何通过[反应器 Vs RxJava Guide](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md)将 RxJava async code 转换为反应器 async code