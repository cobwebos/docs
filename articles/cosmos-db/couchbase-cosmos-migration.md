---
title: 从 CouchBase 迁移到 Azure 宇宙数据库 SQL API
description: 从 CouchBase 迁移到 Azure Cosmos DB SQL API 的分步指南
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: mansha
author: manishmsfte
ms.openlocfilehash: 9713d963978e34ad874dc032676a6e1f14e4657c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77210939"
---
# <a name="migrate-from-couchbase-to-azure-cosmos-db-sql-api"></a>从 CouchBase 迁移到 Azure 宇宙数据库 SQL API

Azure Cosmos DB 是一个可扩展的、全局分布的完全托管数据库。 它提供了对数据的保证低延迟访问。 要了解有关 Azure Cosmos DB 的详细信息，请参阅[概述](introduction.md)文章。 本文提供了将连接到 Couchbase 的 Java 应用程序迁移到 Azure Cosmos DB 中的 SQL API 帐户的说明。

## <a name="differences-in-nomenclature"></a>命名学的差异

与 Couchbase 相比，以下是 Azure Cosmos DB 中工作方式不同的关键功能：

|   Couchbase     |   Azure Cosmos DB   |
| ---------------|-------------------|
|沙发基础服务器| Account       |
|桶           | 数据库      |
|桶           | 容器/集合 |
|JSON 文档    | 项目/文档 |

## <a name="key-differences"></a>主要区别

* Azure Cosmos DB 在文档中有一个"ID"字段，而 Couchbase 的 ID 是存储桶的一部分。 "ID"字段在整个分区中是唯一的。

* Azure Cosmos DB 使用分区或分片技术进行缩放。 这意味着它将数据拆分为多个分片/分区。 这些分区/分片是根据您提供的分区键属性创建的。 您可以选择分区键来优化读取以及写入操作或读取/写入优化。 要了解更多信息，请参阅[分区](./partition-data.md)文章。

* 在 Azure Cosmos DB 中，顶级层次结构不需要指示集合，因为集合名称已存在。 此功能使 JSON 结构更简单。 下面是一个示例，该示例显示了 Couchbase 和 Azure Cosmos DB 之间的数据模型差异：

   **沙发底座**： 文档 ID = "99FF4444"

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

   **Azure 宇宙 DB**： 请参阅文档中的"ID"，如下所示

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

Azure Cosmos DB 具有以下 SDK 来支持不同的 Java 框架：

* 异步 SDK
* 弹簧启动 SDK

以下各节介绍何时使用这些 SDK。 考虑一个示例，其中我们有三种类型的工作负载：

## <a name="couchbase-as-document-repository--spring-data-based-custom-queries"></a>作为文档存储库的 Couchbase &基于数据的自定义查询

如果要迁移的工作负载基于基于 Spring Boot 的 SDK，则可以使用以下步骤：

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

1. 向 POM.xml 文件添加依赖项：

   ```java
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
       <version>2.1.6</version>
   </dependency>
   ```

1. 在资源下添加应用程序属性，并指定以下内容。 请确保替换 URL、键和数据库名称参数：

   ```java
      azure.cosmosdb.uri=<your-cosmosDB-URL>
      azure.cosmosdb.key=<your-cosmosDB-key>
      azure.cosmosdb.database=<your-cosmosDB-dbName>
   ```

1. 在模型中定义集合的名称。 您还可以指定进一步注释。 例如，ID，分区键以显式表示它们：

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

以下是 CRUD 操作的代码段：

### <a name="insert-and-update-operations"></a>插入和更新操作

*其中_repo*是存储库的对象，*文档*是 POJO 类的对象。 您可以使用`.save`插入或向上设置（如果找到具有指定 ID 的文档）。 以下代码段演示如何插入或更新文档对象：

```_repo.save(doc);```

### <a name="delete-operation"></a>删除操作

请考虑以下代码段，其中文档对象将具有标识和分区密钥，必须查找和删除该对象：

```_repo.delete(doc);```

### <a name="read-operation"></a>读取操作

您可以使用或不指定分区键来读取文档。 如果不指定分区键，则将其视为跨分区查询。 请考虑以下代码示例，首先将使用 ID 和分区键字段执行操作。 第二个示例使用常规字段&而不指定分区键字段。

* ```_repo.findByIdAndName(objDoc.getId(),objDoc.getName());```
* ```_repo.findAllByStatus(objDoc.getStatus());```

这就是它，您现在可以将应用程序与 Azure Cosmos DB 一起使用。 此文档中描述的示例的完整代码示例可在[CouchbasetoCosmosDB-SpringCosmos](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/SpringCosmos) GitHub 存储库中提供。

## <a name="couchbase-as-a-document-repository--using-n1ql-queries"></a>使用 N1QL 查询&作为文档存储库的 Couchbase

N1QL 查询是在 Couchbase 中定义查询的方法。

|N1QL 查询 | Azure 宇宙DB 查询|
|-------------------|-------------------|
|SELECT`TravelDocument`META（ .id `TravelDocument`AS ID， .* 从`TravelDocument`WHERE `_type` @ "com.xx.xx.xx.xxx.xxx" 和国家 = '印度' 和任何 m 在 签证 满足 m.type = '多入口'` Validity`和 m.国家在 ['印度'， 不丹 ' _ ORDER BY DESC LIMIT 25   | 选择 c.id，c 从 c JOIN m 在 c.国家_'印度' where c._type = "com.xx.xx.xx.xxx.xxx"和 c.国家 = '印度' 和 m.type = '多条目'和 m.国家 IN （'印度'， '不丹' ORDER BY c.有效性 DESC OFFSET 0 LIMIT 25 |

您可以在 N1QL 查询中注意到以下更改：

* 您无需使用 META 关键字或引用第一级文档。 相反，您可以创建自己对容器的引用。 在此示例中，我们将其视为"c"（可以是任何内容）。 此引用用作所有第一级字段的前缀。 Fr 示例，c.id，c.国家等。

* 现在，您可以在子文档中进行联接，并引用专用别名（如"m"）而不是"ANY"。 为子文档创建别名后，需要使用别名。 例如，m.国家/

* 在 Azure Cosmos DB 查询中，OFFSET 的顺序不同，首先需要指定 OFFSET，然后再指定"限制"。 如果使用最大自定义定义的查询，建议不要使用 Spring Data SDK，因为它在将查询传递到 Azure Cosmos DB 时在客户端可能有不必要的开销。 相反，我们有一个直接的AsyncJava SDK，在这种情况下可以非常有效地使用它。

### <a name="read-operation"></a>读取操作

将 Async Java SDK 用于以下步骤：

1. 将以下依赖项配置到 POM.xml 文件中：

   ```java
   <!-- https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb -->
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmos</artifactId>
       <version>3.0.0</version>
   </dependency>
   ```

1. 使用`ConnectionBuilder`如下示例所示的方法为 Azure Cosmos DB 创建连接对象。 请确保将此声明放入 Bean 中，以便以下代码只能执行一次：

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

1. 要执行查询，您需要运行以下代码段：

   ```java
   Flux<FeedResponse<CosmosItemProperties>> objFlux= container.queryItems(query, fo);
   ```

现在，在上述方法的帮助下，您可以传递多个查询并没有任何麻烦地执行。 如果需要执行一个大型查询（可以拆分为多个查询，请尝试以下代码段而不是前一个查询代码段）：

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

使用前面的代码，您可以并行运行查询，并增加分布式执行以进行优化。 此外，您还可以运行插入和更新操作：

### <a name="insert-operation"></a>插入操作

要插入文档，请运行以下代码：

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

然后订阅单声道作为：

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

Upsert 操作要求您指定需要更新的文档。 要获取完整文档，可以使用标题读取操作下提及的代码段，然后修改所需的字段。 以下代码段使文档向上：

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
然后订阅单声道。 请参阅插入操作中的单声道订阅代码段。

### <a name="delete-operation"></a>删除操作

以下代码段将执行删除操作：

```java     
CosmosItem objItem= container.getItem(doc.Id, doc.Tenant);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

然后订阅单声道，在插入操作中引用单声道订阅代码段。 完整的代码示例在[CouchbasetoCosmosDB-AsyncInSpring](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncInSpring) GitHub 存储库中提供。

## <a name="couchbase-as-a-keyvalue-pair"></a>沙发底座作为键/值对

这是一种简单的工作负载类型，您可以在其中执行查找而不是查询。 对键/值对使用以下步骤：

1. 请考虑将"/ID"作为主键，这将确保可以直接在特定分区中执行查找操作。 创建集合并将"/ID"指定为分区键。

1. 完全关闭索引。 由于您将执行查找操作，因此没有进行索引开销的点。 要关闭索引，请登录到 Azure 门户，转到 Azure Cosmos DB 帐户。 打开**数据资源管理器**，选择**您的数据库**和**容器**。 打开 **"缩放&设置"** 选项卡并选择**索引策略**。 当前索引策略如下所示：
    
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

   将上述索引策略替换为以下策略：

   ```json
   {
       "indexingMode": "none"
   }
   ```

1. 使用以下代码段创建连接对象。 连接对象（放置在其中@Bean或使其为静态对象）：

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

现在，您可以执行 CRUD 操作，如下所示：

### <a name="read-operation"></a>读取操作

要阅读该项目，请使用以下代码段：

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

要插入项目，可以执行以下代码：

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

然后订阅单声道作为：

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

要更新项的值，请参阅下面的代码段：

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
然后订阅单声道，在插入操作中引用单声道订阅代码段。

### <a name="delete-operation"></a>删除操作

使用以下代码段执行删除操作：

```java     
CosmosItem objItem= container.getItem(id, id);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

然后订阅单声道，在插入操作中引用单声道订阅代码段。 完整的代码示例在[CouchbasetoCosmosDB-AsyncKeyValue](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncKeyValue) GitHub 存储库中提供。

## <a name="data-migration"></a>数据迁移

有两种方法可以迁移数据。

* **使用 Azure 数据工厂：** 这是最推荐的方法来迁移数据。 将源配置为 Couchbase 并将接收器配置为 Azure Cosmos DB SQL API，有关详细步骤，请参阅 Azure [Cosmos DB 数据工厂连接器](../data-factory/connector-azure-cosmos-db.md)一文。

* **使用 Azure 宇宙数据库数据导入工具：** 建议使用数据量较少的 VM 迁移此选项。 有关详细步骤，请参阅[数据导入器](./import-data.md)一文。

## <a name="next-steps"></a>后续步骤

* 要执行性能测试，请参阅[使用 Azure Cosmos DB 一文进行性能和缩放测试](./performance-testing.md)。
* 要优化代码，请参阅[Azure Cosmos DB](./performance-tips-async-java.md)一文的性能提示。
* 探索 Java 同步 V3 [SDK，SDK 参考](https://github.com/Azure/azure-cosmosdb-java/tree/v3)GitHub 存储库。
