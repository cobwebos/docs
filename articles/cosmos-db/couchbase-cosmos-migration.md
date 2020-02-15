---
title: 从 CouchBase 迁移到 Azure Cosmos DB SQL API
description: 从 CouchBase 迁移到 Azure Cosmos DB SQL API 的循序渐进指南
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: mansha
author: manishmsfte
ms.openlocfilehash: 9713d963978e34ad874dc032676a6e1f14e4657c
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210939"
---
# <a name="migrate-from-couchbase-to-azure-cosmos-db-sql-api"></a>从 CouchBase 迁移到 Azure Cosmos DB SQL API

Azure Cosmos DB 是一种可扩展、全球分布式且完全托管的数据库。 它提供对数据的低延迟访问。 若要详细了解 Azure Cosmos DB，请参阅[概述](introduction.md)一文。 本文介绍如何将连接到 Couchbase 的 Java 应用程序迁移到 Azure Cosmos DB 中的 SQL API 帐户。

## <a name="differences-in-nomenclature"></a>命名法差异

与 Couchbase 相比，以下是 Azure Cosmos DB 中的主要功能：

|   Couchbase     |   Azure Cosmos DB   |
| ---------------|-------------------|
|Couchbase 服务器| 帐户       |
|地址           | 数据库      |
|地址           | 容器/集合 |
|JSON 文档    | 项/文档 |

## <a name="key-differences"></a>主要区别

* Azure Cosmos DB 在文档中具有 "ID" 字段，而 Couchbase 将 ID 作为 bucket 的一部分。 "ID" 字段在分区中是唯一的。

* Azure Cosmos DB 使用分区或分片技术进行缩放。 这意味着它将数据拆分为多个分片/分区。 这些分区/分片是基于你提供的分区键属性创建的。 您可以选择分区键，以便优化读取以及写入操作或读/写优化。 若要了解详细信息，请参阅[分区](./partition-data.md)一文。

* 在 Azure Cosmos DB 中，不需要顶级层次结构来表示集合，因为该集合名称已经存在。 此功能使 JSON 结构更简单。 下面是一个示例，该示例显示了 Couchbase 与 Azure Cosmos DB 之间的数据模型之间的差异：

   **Couchbase**：文档 ID = "99FF4444"

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

   **Azure Cosmos DB**：参阅文档中的 "ID"，如下所示

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

Azure Cosmos DB 提供了以下 Sdk 来支持不同的 Java 框架：

* 异步 SDK
* 春季 Boot SDK

以下各节介绍了何时使用这些 Sdk 中的每一种。 假设有一个示例，其中包含三种类型的工作负荷：

## <a name="couchbase-as-document-repository--spring-data-based-custom-queries"></a>Couchbase 作为文档存储库 & 基于弹簧数据的自定义查询

如果要迁移的工作负荷基于基于弹簧 Boot 的 SDK，则可以使用以下步骤：

1. 将父级添加到 POM .xml 文件：

   ```java
   <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.1.5.RELEASE</version>
      <relativePath/>
   </parent>
   ```

1. 将属性添加到 POM .xml 文件：

   ```java
   <azure.version>2.1.6</azure.version>
   ```

1. 将依赖项添加到 POM .xml 文件：

   ```java
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
       <version>2.1.6</version>
   </dependency>
   ```

1. 在 "资源" 下面添加应用程序属性并指定以下各项。 请确保替换 URL、key 和 database name 参数：

   ```java
      azure.cosmosdb.uri=<your-cosmosDB-URL>
      azure.cosmosdb.key=<your-cosmosDB-key>
      azure.cosmosdb.database=<your-cosmosDB-dbName>
   ```

1. 定义模型中的集合的名称。 还可以指定进一步的注释。 例如，ID、分区键显式表示它们：

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

其中 *_repo*是存储库的对象，而*doc*是 POJO 类的对象。 您可以使用 `.save` 插入或 upsert （如果找到具有指定 ID 的文档）。 下面的代码段演示如何插入或更新 doc 对象：

```_repo.save(doc);```

### <a name="delete-operation"></a>删除操作

请考虑以下代码片段，其中 doc 对象将需要 ID 和分区键来查找和删除对象：

```_repo.delete(doc);```

### <a name="read-operation"></a>读取操作

可以通过或不指定分区键来读取文档。 如果未指定分区键，则将其视为跨分区查询。 请考虑以下代码示例，第一个代码示例将使用 ID 和分区键字段执行操作。 第二个示例使用常规字段 & 未指定分区键字段。

* ```_repo.findByIdAndName(objDoc.getId(),objDoc.getName());```
* ```_repo.findAllByStatus(objDoc.getStatus());```

就是这样，你现在可以将应用程序用于 Azure Cosmos DB。 [CouchbaseToCosmosDB-SpringCosmos](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/SpringCosmos) GitHub 存储库中提供了本文档中所述示例的完整代码示例。

## <a name="couchbase-as-a-document-repository--using-n1ql-queries"></a>Couchbase 作为文档存储库使用 N1QL 查询 &

N1QL 查询是在 Couchbase 中定义查询的方式。

|N1QL 查询 | Azure CosmosDB 查询|
|-------------------|-------------------|
|选择 "META （`TravelDocument`）"。 id 为 id，`TravelDocument`. * FROM `TravelDocument`，其中 `_type` = "Visas" 和 "国家/地区"，国家/地区 = "印度"，而 "国家/地区" 中的任何 m 在 "印度"，不丹 "] ORDER BY ` Validity` DESC 限制 25 OFFSET 0   | 选择 c。 id，从 c 中的 c 联接 m （其中，c 为 "印度"），其中，c. _type = ".com. xx. xx. xx. .0... .0............... e. e。 |

你可以在 N1QL 查询中注意到以下更改：

* 不需要使用 META 关键字或引用第一级文档。 相反，你可以创建自己的容器引用。 在此示例中，我们将其视为 "c" （它可以是任何内容）。 此引用用作所有第一级字段的前缀。 Fr 示例、c.id、c. 国家/地区等。

* 现在你可以在子文档上执行联接，而不是 "任何"，而是使用专用的别名（如 "m"）来引用它。 为子文档创建别名后，需要使用别名。 例如，m. Country。

* Azure Cosmos DB 查询中偏移量的序列不同，首先需要指定 OFFSET，然后限制。 如果使用最大的自定义查询，则建议不要使用弹簧数据 SDK，因为在客户端传递查询到 Azure Cosmos DB 时，它可能会在客户端产生不必要的开销。 相反，我们有了一个直接的异步 Java SDK，在这种情况下，可以更有效地利用该 SDK。

### <a name="read-operation"></a>读取操作

通过以下步骤使用 Async Java SDK：

1. 将以下依赖项配置到 POM .xml 文件：

   ```java
   <!-- https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb -->
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmos</artifactId>
       <version>3.0.0</version>
   </dependency>
   ```

1. 使用 `ConnectionBuilder` 方法为 Azure Cosmos DB 创建一个连接对象，如下面的示例中所示。 请确保将此声明放入 bean，以便仅执行以下代码一次：

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

现在，在上面的方法的帮助下，可以传递多个查询并执行而无需任何麻烦。 如果您要求执行一个可拆分为多个查询的大型查询，请尝试以下代码片段，而不是上一个查询：

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

对于前面的代码，你可以并行运行查询并增加分布式执行以进行优化。 此外，还可以运行 insert 和 update 操作：

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

### <a name="upsert-operation"></a>Upsert 操作

Upsert 操作要求指定需要更新的文档。 若要获取完整文档，你可以使用标题为 "读取操作" 下所述的代码段，然后修改必填字段。 以下代码片段 upsert 文档：

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
然后订阅 mono。 请参阅插入操作中的 mono 订阅片段。

### <a name="delete-operation"></a>删除操作

以下代码片段将执行删除操作：

```java     
CosmosItem objItem= container.getItem(doc.Id, doc.Tenant);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

然后订阅 mono，在插入操作中引用 mono 订阅片段。 完整的代码示例在[CouchbaseToCosmosDB-AsyncInSpring](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncInSpring) GitHub 存储库中提供。

## <a name="couchbase-as-a-keyvalue-pair"></a>作为键/值对的 Couchbase

这是一个简单的工作负荷类型，您可以在其中执行查找而不是查询。 对于键/值对，请使用以下步骤：

1. 考虑将 "/ID" 作为主键，这将确保你可以直接在特定分区中执行查找操作。 创建一个集合，并指定 "/ID" 作为分区键。

1. 完全关闭索引。 由于你将执行查找操作，因此没有任何位置索引开销。 若要禁用索引，请登录 Azure 门户，转到 Azure Cosmos DB 帐户。 打开 "**数据资源管理器**"，选择**数据库**和**容器**。 打开 "**缩放 & 设置**" 选项卡，然后选择 "**索引策略**"。 当前的索引策略如下所示：
    
   ```json
   {
       "indexingMode": "consistent",
       "includedPaths": 
       [
           {
            "path": "/*",
            "indexes": 
             [
                {
                  "kind": "Range",
                  "dataType": "Number"
                },
                {
                  "kind": "Range",
                  "dataType": "String"
                },
                {
                   "kind": "Spatial",
                   "dataType": "Point"
                }
             ]
          }
       ],
       "excludedPaths": 
       [
         {
             "path": "/path/to/single/excluded/property/?"
         },
         {
             "path": "/path/to/root/of/multiple/excluded/properties/*"
         }
      ]
   }
   ````

   将以上索引策略替换为以下策略：

   ```json
   {
       "indexingMode": "none"
   }
   ```

1. 使用以下代码段创建连接对象。 要放置在 @Bean 或使其成为静态对象的连接对象）：

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

若要读取此项，请使用以下代码片段：

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

### <a name="upsert-operation"></a>Upsert 操作

若要更新项的值，请参阅下面的代码片段：

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
然后订阅 mono，在插入操作中引用 mono 订阅片段。

### <a name="delete-operation"></a>删除操作

使用以下代码片段执行删除操作：

```java     
CosmosItem objItem= container.getItem(id, id);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

然后订阅 mono，在插入操作中引用 mono 订阅片段。 完整的代码示例在[CouchbaseToCosmosDB-AsyncKeyValue](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncKeyValue) GitHub 存储库中提供。

## <a name="data-migration"></a>数据迁移

可以通过两种方式迁移数据。

* **使用 Azure 数据工厂：** 这是迁移数据最建议使用的方法。 将源配置为 Couchbase 和 sink Azure Cosmos DB SQL API，有关详细步骤，请参阅 Azure [Cosmos DB 数据工厂连接器](../data-factory/connector-azure-cosmos-db.md)一文。

* **使用 Azure Cosmos DB 数据导入工具：** 建议使用此选项，使用少量数据的 Vm 进行迁移。 有关详细步骤，请参阅[数据导入](./import-data.md)程序一文。

## <a name="next-steps"></a>后续步骤

* 若要执行性能测试，请参阅[Azure Cosmos DB 文章的性能和规模测试](./performance-testing.md)。
* 若要优化代码，请参阅[Azure Cosmos DB 文章的性能提示](./performance-tips-async-java.md)。
* 探索 Java Async V3 SDK SDK[引用](https://github.com/Azure/azure-cosmosdb-java/tree/v3)GitHub 存储库。
