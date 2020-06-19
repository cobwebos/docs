---
title: 从 CouchBase 迁移到 Azure Cosmos DB SQL API
description: 从 CouchBase 迁移到 Azure Cosmos DB SQL API 的分步指南
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: mansha
author: manishmsfte
ms.openlocfilehash: 248860ad6963fcd04526f0d94e52d6a6181463c5
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83657344"
---
# <a name="migrate-from-couchbase-to-azure-cosmos-db-sql-api"></a>从 CouchBase 迁移到 Azure Cosmos DB SQL API

Azure Cosmos DB 是一种可扩展、全球分布式、完全托管的数据库。 它提供对数据的有保证低延迟访问。 若要详细了解 Azure Cosmos DB，请参阅[概述](introduction.md)一文。 本文提供有关将连接到 Couchbase 的 Java 应用程序迁移到 Azure Cosmos DB 中的 SQL API 帐户的说明。

## <a name="differences-in-nomenclature"></a>命名法的区别

与 Couchbase 相比，以下是 Azure Cosmos DB 中工作方式有所不同的主要功能：

|   Couchbase     |   Azure Cosmos DB   |
| ---------------|-------------------|
|Couchbase 服务器| 帐户       |
|Bucket           | 数据库      |
|Bucket           | 容器/集合 |
|JSON 文档    | 项/文档 |

## <a name="key-differences"></a>主要区别

* Azure Cosmos DB 在文档中具有“ID”字段，而 Couchbase 将 ID 作为 bucket 的一部分。 “ID”字段在分区中是唯一的。

* Azure Cosmos DB 使用分区或分片技术进行扩展。 这意味着它将数据拆分为多个分片/分区。 这些分区/分片是基于你提供的分区键属性创建的。 你可以选择分区键，以便优化读取以及写入操作，或读/写已优化。 有关详细信息，请参阅[分区](./partition-data.md)一文。

* 在 Azure Cosmos DB 中，顶级层次结构无需表示集合，因为该集合名称已经存在。 此功能使 JSON 结构更简单。 下面是显示 Couchbase 与 Azure Cosmos DB 之间的数据模型差异的示例：

   Couchbase：Document ID =  "99FF4444"

    ```json
    {
      "TravelDocument":
      {
       "Country":"India",
      "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    }
   ```

   **Azure Cosmos DB**：请参阅文档中的“ID”，如下所示

    ```json
    {
      "id" : "99FF4444",
    
      "Country":"India",
       "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    
    ```
         
## <a name="java-sdk-support"></a>Java SDK 支持

Azure Cosmos DB 提供了以下 SDK 以支持不同的 Java 框架：

* Async SDK
* Spring Boot SDK

下列各部分描述了使用上述每个 SDK 的时间。 假设有一个示例，其中包含三种类型的工作负荷：

## <a name="couchbase-as-document-repository--spring-data-based-custom-queries"></a>作为文档存储库和基于 spring 数据的自定义查询的 Couchbase

如果要迁移的工作负荷基于 Spring Boot 的 SDK，则可以使用以下步骤：

1. 将父级添加到 POM.xml 文件：

   ```java
   <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.1.5.RELEASE</version>
      <relativePath/>
   </parent>
   ```

1. 将属性添加到 POM.xml 文件：

   ```java
   <azure.version>2.1.6</azure.version>
   ```

1. 将依赖项添加到 POM.xml 文件：

   ```java
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
       <version>2.1.6</version>
   </dependency>
   ```

1. 在资源下面添加应用程序属性并指定以下各项。 请确保替换 URL、键和数据库名称参数：

   ```java
      azure.cosmosdb.uri=<your-cosmosDB-URL>
      azure.cosmosdb.key=<your-cosmosDB-key>
      azure.cosmosdb.database=<your-cosmosDB-dbName>
   ```

1. 在模型中定义集合的名称。 还可以指定更多注释。 例如，显式表示它们的 ID、分区键：

   ```java
   @Document(collection = "mycollection")
       public class User {
           @id
           private String id;
           private String firstName;
           @PartitionKey
           private String lastName;
       }
   ```

下面是 CRUD 操作的代码片段：

### <a name="insert-and-update-operations"></a>插入和更新操作

其中 _repo 是存储库的对象，而 doc 是 POJO 类的对象。 你可以使用 `.save` 插入或更新插入（如果找到具有指定 ID 的文档）。 以下代码片段演示如何插入或更新文档对象：

```_repo.save(doc);```

### <a name="delete-operation"></a>删除操作

请考虑以下代码片段，其中文档对象必须使用 ID 和分区键来查找和删除对象：

```_repo.delete(doc);```

### <a name="read-operation"></a>读取操作

可以通过指定或不指定分区键来读取文档。 如果未指定分区键，则将其视为跨分区查询。 请考虑以下代码示例，第一个代码示例将使用 ID 和分区键字段执行操作。 第二个示例使用常规字段且未指定分区键字段。

* ```_repo.findByIdAndName(objDoc.getId(),objDoc.getName());```
* ```_repo.findAllByStatus(objDoc.getStatus());```

就这么简单，你现在可以将应用程序用于 Azure Cosmos DB。 [CouchbaseToCosmosDB-SpringCosmos](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/SpringCosmos) GitHub 存储库中提供了本文档中所述示例的完整代码示例。

## <a name="couchbase-as-a-document-repository--using-n1ql-queries"></a>作为文档存储库和使用 N1QL 查询的 Couchbase

N1QL 查询是在 Couchbase 中定义查询的方式。

|N1QL 查询 | Azure CosmosDB 查询|
|-------------------|-------------------|
|SELECT META(`TravelDocument`).id AS id, `TravelDocument`.* FROM `TravelDocument` WHERE `_type` = "com.xx.xx.xx.xxx.xxx.xxxx " and country = 'India’ and ANY m in Visas SATISFIES m.type == 'Multi-Entry' and m.Country IN ['India', Bhutan’] ORDER BY ` Validity` DESC LIMIT 25 OFFSET 0   | SELECT c.id,c FROM c JOIN m in  c.country=’India’ WHERE c._type = " com.xx.xx.xx.xxx.xxx.xxxx" and c.country = 'India' and m.type = 'Multi-Entry' and m.Country IN ('India', 'Bhutan') ORDER BY c.Validity DESC OFFSET 0 LIMIT 25 |

你可能会注意到 N1QL 查询中的以下更改：

* 不需要使用 META 关键字或引用第一级文档。 你可以转为创建自己的对容器的引用。 在此示例中，我们将其视为“c”（它可以是任何内容）。 此引用用作所有第一级字段的前缀。 例如，c.id、c.country 等。

* 现在你可以在子文档上执行联接，并使用专用别名（如“m”）来引用它，而非使用“ANY”。 为子文档创建别名后，需要使用别名。 例如，m.Country。

* Azure Cosmos DB 查询中 OFFSET 的顺序不同，首先需要指定 OFFSET，然后指定 LIMIT。 如果使用最大数量的自定义查询，则不建议使用 Spring Data SDK，因为在将查询传递到 Azure Cosmos DB 时，它可能会在客户端产生不必要的开销。 相反，我们有一个直接 Async Java SDK，在此案例中，可以更有效地利用该 SDK。

### <a name="read-operation"></a>读取操作

通过以下步骤使用 Async Java SDK：

1. 将以下依赖项配置到 POM.xml 文件中：

   ```java
   <!-- https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb -->
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmos</artifactId>
       <version>3.0.0</version>
   </dependency>
   ```

1. 使用 `ConnectionBuilder` 方法为 Azure Cosmos DB 创建一个连接对象，如下面的示例中所示。 请确保将此声明放入 bean，以便以下代码仅执行一次：

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
    
   if(client==null)
    client= CosmosClient.builder()
        .endpoint(Host)//(Host, MasterKey, dbName, collName).Builder()
        .connectionPolicy(cp)
        .key(MasterKey)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();   
   
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

1. 若要执行查询，需要运行以下代码片段：

   ```java
   Flux<FeedResponse<CosmosItemProperties>> objFlux= container.queryItems(query, fo);
   ```

现在，在上述方法的帮助下，可以传递多个查询并执行，而不会有任何麻烦。 如果你要求执行一个可拆分为多个查询的大型查询，请尝试以下代码片段，而不是上一个代码片段：

```java
for(SqlQuerySpec query:queries)
{
    objFlux= container.queryItems(query, fo);
    objFlux .publishOn(Schedulers.elastic())
            .subscribe(feedResponse->
                {
                    if(feedResponse.results().size()>0)
                    {
                        _docs.addAll(feedResponse.results());
                    }
                
                },
                Throwable::printStackTrace,latch::countDown);
    lstFlux.add(objFlux);
}
                        
        Flux.merge(lstFlux);
        latch.await();
}
```

对于前面的代码，你可以并行运行查询并增加分布式执行以进行优化。 此外，还可以运行插入和更新操作：

### <a name="insert-operation"></a>插入操作

若要插入文档，请运行以下代码：

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

然后以以下方式订阅 Mono：

```java
CountDownLatch latch=new CountDownLatch(1);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.statusCode()!=successStatus)
                {
                    throw new RuntimeException(resourceResponse.toString());
                }
            },
        Throwable::printStackTrace,latch::countDown);
latch.await();              
```

### <a name="upsert-operation"></a>更新插入操作

更新插入操作要求指定需要更新的文档。 若要获取完整文档，你可以使用标题为“读取操作”下所述的片段，然后修改必填字段。 以下代码片段更新插入文档：

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
然后订阅 mono。 请参阅插入操作中的 mono 订阅片段。

### <a name="delete-operation"></a>删除操作

以下片段将执行删除操作：

```java     
CosmosItem objItem= container.getItem(doc.Id, doc.Tenant);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

然后订阅 mono，在插入操作中引用 mono 订阅片段。 [CouchbaseToCosmosDB-AsyncInSpring](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncInSpring) GitHub 存储库中提供了完整的代码示例。

## <a name="couchbase-as-a-keyvalue-pair"></a>作为键/值对的 Couchbase

这是一个简单的工作负荷类型，你可以在其中执行查找而不是查询。 对于键/值对，请使用以下步骤：

1. 考虑将“/ID”作为主键，这将确保你可以直接在特定分区中执行查找操作。 创建一个集合，并指定“/ID”作为分区键。

1. 完全关闭索引。 由于你将执行查找操作，因此没有任何位置可执行索引开销。 若要禁用索引，请登录 Azure 门户，转到 Azure Cosmos DB 帐户。 打开“数据资源管理器”，选择“数据库”和“容器”。 打开“扩展和设置”选项卡，然后选择“索引策略”。 当前的索引策略如下所示：
    
   ```json
   {
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/\"_etag\"/?"
        }
    ]
    }
   ````

   将以上索引策略替换为以下策略：

   ```json
   {
    "indexingMode": "none",
    "automatic": false,
    "includedPaths": [],
    "excludedPaths": []
    }
   ```

1. 使用以下代码片段创建连接对象。 要放置在 @Bean 或使其成为静态对象的连接对象：

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
   
   if(client==null)
    client= CosmosClient.builder()
        .endpoint(Host)//(Host, MasterKey, dbName, collName).Builder()
        .connectionPolicy(cp)
        .key(MasterKey)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();
    
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

现在，可以执行 CRUD 操作，如下所示：

### <a name="read-operation"></a>读取操作

若要读取此项，请使用以下片段：

```java        
CosmosItemRequestOptions ro=new CosmosItemRequestOptions();
ro.partitionKey(new PartitionKey(documentId));
CountDownLatch latch=new CountDownLatch(1);
        
var objCosmosItem= container.getItem(documentId, documentId);
Mono<CosmosItemResponse> objMono = objCosmosItem.read(ro);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.item()!=null)
            {
                doc= resourceResponse.properties().toObject(UserModel.class);
            }
        },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="insert-operation"></a>插入操作

若要插入项，可以执行以下代码：

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

然后以以下方式订阅 mono：

```java
CountDownLatch latch=new CountDownLatch(1);
objMono.subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.statusCode()!=successStatus)
                {
                    throw new RuntimeException(resourceResponse.toString());
                }
            },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="upsert-operation"></a>更新插入操作

若要更新项的值，请参阅下面的代码片段：

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
然后订阅 mono，在插入操作中引用 mono 订阅片段。

### <a name="delete-operation"></a>删除操作

使用以下片段执行删除操作：

```java     
CosmosItem objItem= container.getItem(id, id);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

然后订阅 mono，在插入操作中引用 mono 订阅片段。 [CouchbaseToCosmosDB-AsyncKeyValue](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncKeyValue) GitHub 存储库中提供了完整的代码示例。

## <a name="data-migration"></a>数据迁移

有两种迁移数据的方式。

* 使用 Azure 数据工厂：这是最常推荐使用的数据迁移方法。 将源配置为 Couchbase，将接收器配置为 Azure Cosmos DB SQL API，有关详细步骤，请参阅 Azure [Cosmos DB 数据工厂连接器](../data-factory/connector-azure-cosmos-db.md)一文。

* 使用 Azure Cosmos DB 数据导入工具：建议使用此选项，使用具有少量数据的 VM 进行迁移。 有关详细步骤，请参阅[数据导入程序](./import-data.md)一文。

## <a name="next-steps"></a>后续步骤

* 若要进行性能测试，请参阅[执行 Azure Cosmos DB 性能和扩展测试](./performance-testing.md)一文。
* 若要优化代码，请参阅 [Azure Cosmos DB 性能提示](./performance-tips-async-java.md)一文。
* 浏览 Java Async V3 SDK，[SDK 引用](https://github.com/Azure/azure-cosmosdb-java/tree/v3) GitHub 存储库。
