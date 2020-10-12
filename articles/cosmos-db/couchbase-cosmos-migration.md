---
title: 从 CouchBase 迁移到 Azure Cosmos DB SQL API
description: 有关从 CouchBase 迁移到 Azure Cosmos DB SQL API 的分步指南
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/11/2020
ms.author: mansha
author: manishmsfte
ms.custom: devx-track-java
ms.openlocfilehash: b0c9ef99e4cbb0683273d613d3a85e7f6455a40d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87366715"
---
# <a name="migrate-from-couchbase-to-azure-cosmos-db-sql-api"></a>从 CouchBase 迁移到 Azure Cosmos DB SQL API

Azure Cosmos DB 是一种可扩展、全球分布式、完全托管的数据库。 它能够确保数据访问延迟较低。 若要详细了解 Azure Cosmos DB，请参阅[概述](introduction.md)文章。 本文说明如何将连接到 Couchbase 的 Java 应用程序迁移到 Azure Cosmos DB 中的 SQL API 帐户。

## <a name="differences-in-nomenclature"></a>术语差别

下面是相比于 Couchbase，在 Azure Cosmos DB 中以不同方式工作的重要功能：

|   Couchbase     |   Azure Cosmos DB   |
| ---------------|-------------------|
|Couchbase 服务器| 帐户       |
|桶           | 数据库      |
|桶           | 容器/集合 |
|JSON 文档    | 项/文档 |

## <a name="key-differences"></a>主要区别

* Azure Cosmos DB 在文档中有一个“ID”字段，而 Couchbase 将 ID 用作桶的一部分。 “ID”字段在整个分区中是唯一的。

* Azure Cosmos DB 使用分区或分片技术进行缩放。 这意味着它会将数据拆分为多个分片/分区。 这些分区/分片是基于提供的分区键属性创建的。 可以选择分区键来优化读取以及写入操作，或者优化读/写操作。 若要了解详细信息，请参阅[分区](./partition-data.md)一文。

* 在 Azure Cosmos DB 中，顶级层次结构无需表示集合，因为集合名称已存在。 此功能大幅简化了 JSON 结构。 以下示例展示了 Couchbase 与 Azure Cosmos DB 之间的数据模型差别：

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

   **Azure Cosmos DB**：引用文档中的“ID”，如下所示

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

Azure Cosmos DB 提供以下 SDK 来支持不同的 Java 框架：

* 异步 SDK
* Spring Boot SDK

以下部分将介绍何时使用其中的每种 SDK。 假设我们有三种类型的工作负荷：

## <a name="couchbase-as-document-repository--spring-data-based-custom-queries"></a>用作文档存储库的 Couchbase，以及基于 Spring Data 的自定义查询

如果要迁移的工作负荷基于 Spring Boot SDK，则可以使用以下步骤：

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

1. 在资源下面添加应用程序属性，并指定以下语句。 请务必替换 URL、密钥和数据库名称参数：

   ```java
      azure.cosmosdb.uri=<your-cosmosDB-URL>
      azure.cosmosdb.key=<your-cosmosDB-key>
      azure.cosmosdb.database=<your-cosmosDB-dbName>
   ```

1. 在模型中定义集合的名称。 还可以指定其他注释。 例如，指定 ID、分区键以显式表示资源：

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

其中 _repo 是存储库的对象，而 doc 是 POJO 类的对象。 可以使用 `.save` 执行插入或更新插入（如果找到了具有指定 ID 的文档）。 以下代码片段演示如何插入或更新 doc 对象：

```_repo.save(doc);```

### <a name="delete-operation"></a>删除操作

考虑以下代码片段，其中，doc 对象包含查找和删除对象所必需的 ID 和分区键：

```_repo.delete(doc);```

### <a name="read-operation"></a>读取操作

无论指定还是不指定分区键，均可读取文档。 如果未指定分区键，则将其视为跨分区查询。 考虑以下代码示例。第一个示例使用 ID 和分区键字段执行操作。 第二个示例使用常规字段，且不指定分区键字段。

* ```_repo.findByIdAndName(objDoc.getId(),objDoc.getName());```
* ```_repo.findAllByStatus(objDoc.getStatus());```

就这么简单，你现在可以将应用程序用于 Azure Cosmos DB。 本文档中所述示例的完整代码示例已在 [CouchbaseToCosmosDB-SpringCosmos](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/SpringCosmos) GitHub 存储库中提供。

## <a name="couchbase-as-a-document-repository--using-n1ql-queries"></a>用作文档存储库的 Couchbase，使用 N1QL 查询

可以通过 N1QL 查询在 Couchbase 中定义查询。

|N1QL 查询 | Azure CosmosDB 查询|
|-------------------|-------------------|
|SELECT META(`TravelDocument`).id AS id, `TravelDocument`.* FROM `TravelDocument` WHERE `_type` = "com.xx.xx.xx.xxx.xxx.xxxx " and country = 'India’ and ANY m in Visas SATISFIES m.type == 'Multi-Entry' and m.Country IN ['India', Bhutan’] ORDER BY ` Validity` DESC LIMIT 25 OFFSET 0   | SELECT c.id,c FROM c JOIN m in  c.country=’India’ WHERE c._type = " com.xx.xx.xx.xxx.xxx.xxxx" and c.country = 'India' and m.type = 'Multi-Entry' and m.Country IN ('India', 'Bhutan') ORDER BY c.Validity DESC OFFSET 0 LIMIT 25 |

在 N1QL 查询中，可以注意到以下更改：

* 不需要使用 META 关键字或引用第一级文档。 你可以创建自己的对容器的引用。 在此示例中，我们已将其视为“c”（可以是任何内容）。 此引用用作所有第一级字段的前缀。 例如，c.id、c.country，等等。

* 在不指定“ANY”的情况下，现在可以针对子文档执行联接，并使用专用别名（例如“m”）来引用它。 为子文档创建别名后，需要使用别名。 例如 m.Country。

* OFFSET 的顺序在 Azure Cosmos DB 查询中是不同的，需要先指定 OFFSET，再指定 LIMIT。 如果使用了最大数目的自定义查询，我们建议不要使用 Spring Data SDK，否则在将查询传递给 Azure Cosmos DB 时，可能会在客户端上产生不必要的开销。 我们提供了一个直接异步 Java SDK，在这种情况下，可以更有效地利用该 SDK。

### <a name="read-operation"></a>读取操作

通过以下步骤使用异步 Java SDK：

1. 将以下依赖项配置到 POM.xml 文件中：

   ```java
   <!-- https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb -->
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmos</artifactId>
       <version>3.0.0</version>
   </dependency>
   ```

1. 如以下示例中所示，使用 `ConnectionBuilder` 方法为 Azure Cosmos DB 创建一个连接对象。 请确保将此声明放入 bean 中，以便仅执行以下代码一次：

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

1. 若要执行查询，需运行以下代码片段：

   ```java
   Flux<FeedResponse<CosmosItemProperties>> objFlux= container.queryItems(query, fo);
   ```

现在，借助上述方法，可以非常顺利地传递并执行多个查询。 如果必须执行一个可拆分为多个查询的较大查询，请尝试运行以下代码片段，而不要运行前面所述的代码：

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

使用上述代码可以并行运行查询，并增加分布式执行以进行优化。 此外，还可以运行插入和更新操作：

### <a name="insert-operation"></a>插入操作

若要插入文档，请运行以下代码：

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

然后按如下所示订阅 Mono：

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

更新插入操作要求指定所需更新的文档。 若要提取完整文档，可以使用“读取操作”标题下提到的代码片段，然后修改所需的字段。 以下代码片段将更新插入文档：

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
然后订阅 Mono。 请参考“插入操作”中的 Mono 订阅代码片段。

### <a name="delete-operation"></a>删除操作

以下代码片段执行删除操作：

```java     
CosmosItem objItem= container.getItem(doc.Id, doc.Tenant);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

然后订阅 Mono。请参考“插入操作”中的 Mono 订阅代码片段。 [CouchbaseToCosmosDB-AsyncInSpring](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncInSpring) GitHub 存储库中提供了完整的代码示例。

## <a name="couchbase-as-a-keyvalue-pair"></a>用作键/值对的 Couchbase

这是一个简单类型的工作负荷，在其中可以执行查找而不是查询。 针对键/值对使用以下步骤：

1. 考虑使用“/ID”作为主键，以确保可以直接在特定的分区中执行查找操作。 创建一个集合，并指定“/ID”作为分区键。

1. 完全关闭索引功能。 由于执行的是查找操作，因此不会带来任何索引开销。 若要禁用索引功能，请登录到 Azure 门户并转到“Azure Cosmos DB 帐户”。 打开“数据资源管理器”，选择你的**数据库**和**容器**。 打开“规模和设置”选项卡，然后选择“索引策略”。  索引策略目前如下所示：
    
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

1. 使用以下代码片段创建连接对象。 连接对象（将放在 @Bean 中，或设为静态）：

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

现在，可按如下所示执行 CRUD 操作：

### <a name="read-operation"></a>读取操作

若要读取项，请使用以下代码片段：

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

若要插入项，可执行以下代码：

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

然后按如下所示订阅 Mono：

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

若要更新项的值，请参考以下代码片段：

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
然后订阅 Mono。请参考“插入操作”中的 Mono 订阅代码片段。

### <a name="delete-operation"></a>删除操作

使用以下代码片段执行删除操作：

```java     
CosmosItem objItem= container.getItem(id, id);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

然后订阅 Mono。请参考“插入操作”中的 Mono 订阅代码片段。 [CouchbaseToCosmosDB-AsyncKeyValue](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncKeyValue) GitHub 存储库中提供了完整的代码示例。

## <a name="data-migration"></a>数据迁移

可通过两种方式迁移数据。

* **使用 Azure 数据工厂：** 强烈建议使用此方法来迁移数据。 将源配置为 Couchbase，将接收器配置为 Azure Cosmos DB SQL API。有关详细步骤，请参阅 [Azure Cosmos DB 数据工厂连接器](../data-factory/connector-azure-cosmos-db.md)一文。

* **使用 Azure Cosmos DB 数据导入工具：** 使用包含少量数据的 VM 进行迁移时，建议使用此选项。 有关详细步骤，请参阅[数据导入工具](./import-data.md)一文。

## <a name="next-steps"></a>后续步骤

* 若要执行性能测试，请参阅[使用 Azure Cosmos DB 执行性能和规模测试](./performance-testing.md)一文。
* 若要优化代码，请参阅 [Azure Cosmos DB 性能提示](./performance-tips-async-java.md)一文。
* 若要探索 Java 异步 V3 SDK，请访问 [SDK 参考](https://github.com/Azure/azure-cosmosdb-java/tree/v3) GitHub 存储库。
