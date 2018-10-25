---
title: Azure Cosmos DB 中的多主数据库冲突解决
description: 本文介绍 Azure Comsos DB 多主数据库的冲突类别和冲突解决模式，例如，“最后写入者优先”(LWW)、“自定义 - 用户定义的过程”和“自定义 - 异步”。
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: b4433beea7fd9288c8c7be33c977ae11f3c2bac1
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2018
ms.locfileid: "49426328"
---
# <a name="multi-master-conflict-resolution-in-azure-cosmos-db"></a>Azure Cosmos DB 中的多主数据库冲突解决 

Azure Cosmos DB 多主数据库提供全局冲突解决模式，确保数据在所有复制到的区域中保持一致。

## <a name="conflict-categories"></a>冲突类别

处理 Azure Cosmos DB 数据时，可能会发生三种类别的冲突：

* **插入冲突：** 当应用程序同时从两个或更多个区域插入具有相同唯一索引（例如 ID）的两个或更多个文档时，会发生此类冲突。 在这种情况下，所有写入最初可能会成功，但根据所选的冲突解决策略，只会提交一个具有原始 ID 的文档。

* **替换冲突：** 当应用程序同时从两个或更多个区域更新单个文档时，会发生此类冲突。

* **删除冲突：** 当应用程序从一个区域中删除某个文档，然后从一个或多个区域更新该文档时，会发生此类冲突。 

## <a name="conflict-resolution-modes"></a>冲突解决模式

Azure Cosmos DB 提供三种冲突解决模式。 冲突解决模式是针对每个集合定义的。

> [!NOTE]
> SQL API 用户可以从三种不同的冲突解决模式中选择。 对于其他所有 API 模型（MongoDB、Cassandra、Graph 和表），可以使用“最后写入者优先”来解决冲突。

### <a name="last-writer-wins-lww"></a>最后写入者优先 (LWW)

“最后写入者优先”是默认的冲突解决模式。 在此模式下，将会根据传入到文档中某个属性的数字值来解决冲突。

以下代码片段示范了如何在使用 .Net SDK 时配置“最后写入者优先”冲突解决策略。 “ConflictResolutionPath”定义用于解决冲突的属性的路径。 在此示例中，`/userDefinedId` 是冲突解决路径，`userDefinedId` 值最大的文档始终优先获得冲突解决方法。 若要注册“最后写入者优先”解决模式，请预配包含 ConflictResolutionPolicy 的集合，如下所示。

```csharp
DocumentCollection lwwCollection = await myClient.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("myDatabase"), new DocumentCollection
{
   Id = "myCollection", 
   ConflictResolutionPolicy = new ConflictResolutionPolicy
   {
      Mode = ConflictResolutionMode.LastWriterWins,
      ConflictResolutionPath = "/userDefinedId"
   }
});
```

 “最后写入者优先”解决模式将应用到不同的数据冲突类别，如下所示：

* **插入和更新冲突：** 如果两个或更多个文档在插入或替换操作时发生冲突，则包含最大冲突解决路径值（即 userDefinedId）的文档将成为优胜者。 如果多个文档的冲突解决路径的数字值相同，则选择的优胜者是不确定的。 但是，所有区域将融合到单个优胜者。

* **删除冲突：** 如果涉及到删除冲突，则不管冲突解决路径的值如何，则删除冲突始终优先于其他替换冲突。

### <a name="custom--user-defined-procedure"></a>自定义 - 用户定义的过程

在此模式下，用户通过将用户定义的过程 (UDP) 注册到集合来控制冲突解决。 此 UDP 具有特定的签名。 如果选择此选项但无法注册 UDP，或者 UDP 在运行时引发了异常，则冲突将写入到冲突源，在其中可以单独解决冲突。

若要注册“自定义 - 用户定义的过程”冲突解决模式，请预配包含 ConflictResolutionPolicy 的集合，如下所示。

```csharp
DocumentCollection udpCollection = await myClient.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("myDatabase"), new DocumentCollection
{
  Id = "myCollection",
  ConflictResolutionPolicy = new ConflictResolutionPolicy
  {
     Mode = ConflictResolutionMode.Custom,
     ConflictResolutionProcedure = UriFactory.CreateStoredProcedureUri("myDatabase","myCollection","myUdpStoredProcedure").ToString()
  }
});
```

接下来，将用户定义的过程添加到集合，如下所示。

```csharp
StoredProcedure myUdpStoredProc = new StoredProcedure
{
   Id = "myUdpStoredProcedure",
   Body = myUdpStoredProcText
};
await myClient.UpsertStoredProcedureAsync(UriFactory.CreateDocumentCollectionUri("myDatabase", "myCollection"), myUdpStoredProc);
```

注册到集合的存储过程具有特殊的签名。 在以下示例中，UDP 使用属性 `UserDefinedId` 来解决冲突。 包含最大值的文档优先获得冲突解决方法。

```javascript
function myUdpStoredProcedure(incomingDocument, existingDocument, isDeleteConflict, conflictingDocuments){
    var collection = getContext().getCollection();

    if (!incomingDocument) {
        if (existingDocument) {

            collection.deleteDocument(existingDocument._self, {}, function(err, responseOptions) {
                if (err) throw err;
            });
        }
    } else if (isDeleteConflict) {
        // delete always wins.
    } else {
        var documentToUse = incomingDocument;

        if (existingDocument) {
            if (documentToUse.userDefinedId < existingDocument.userDefinedId) {
                documentToUse = existingDocument;
            }
        }

        var i;
        for (i = 0; i < conflictingDocuments.length; i++) {
            if (documentToUse.userDefinedId < conflictingDocuments[i].userDefinedId) {
                documentToUse = conflictingDocuments[i];
            }
        }

        tryDelete(conflictingDocuments, incomingDocument, existingDocument, documentToUse);
    }

    function tryDelete(documents, incoming, existing, documentToInsert) {
        if (documents.length > 0) {
            collection.deleteDocument(documents[0]._self, {}, function(err, responseOptions) {
                if (err) throw err;

                documents.shift();
                tryDelete(documents, incoming, existing, documentToInsert);
            });
        } else if (existing) {
            collection.replaceDocument(existing._self, documentToInsert,
                function(err, documentCreated) {
                    if (err) throw err;
                });
        } else {
            collection.createDocument(collection.getSelfLink(), documentToInsert,
                function(err, documentCreated) {
                    if (err) throw err;
                });
        }
    }
}

```

过程包含四个参数：

* **incomingDocument：** 指定文档的有冲突版本。 冲突可以是插入、替换或删除冲突。 对于删除冲突，这是不包含内容的删除映像（逻辑删除）。

* **existingDocument：** 指定“rid”值与 incomingDocument 相同的文档的已提交版本。 对于插入和删除冲突，此参数设置为 null。

* **isDeleteConflict：** 布尔标志，指示传入的文档是否与以前删除的文档相冲突。 如果此参数设置为 true，则 existingDocument 也为 null。

* **conflictingDocuments：** 指定数据库中与 ID 列上的 incomingDocument 相冲突或者与其他任何唯一索引字段相冲突的所有文档的已提交版本集合。 与 incomingDocument 相比较时，这些文档具有不同的“rid”值。

用户定义的过程对 Cosmos DB 分区键拥有完全访问权限，可以执行任何存储操作来解决冲突。 如果用户定义的过程未提交冲突版本，则系统将删除冲突，而 existingDocument 将保持已提交状态。 如果用户定义的过程失败或不存在，则 Azure Cosmos DB 会将冲突添加到只读的冲突源，在其中可以异步处理冲突，如[异步冲突解决模式](#custom--asynchronous)中所述。 

### <a name="custom--asynchronous"></a>自定义 - 异步  

在此模式下，Azure Cosmos DB 会排除所有冲突（插入、替换和删除）以防止提交，并将其注册到只读的冲突源，让用户的应用程序延期解决。 应用程序可以异步执行冲突解决，并使用任何逻辑或引用任何外部源、应用程序或服务来解决冲突。

若要注册“自定义 - 异步”冲突解决模式，请预配包含 ConflictResolutionPolicy 的集合，如下所示。

```csharp
DocumentCollection manualCollection = await myClient.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("myDatabase"), new DocumentCollection
{
    Id = "myCollection",
    ConflictResolutionPolicy = new ConflictResolutionPolicy
    {
        Mode = ConflictResolutionMode.Custom
    }
});
```

若要读取和处理冲突源中的任何冲突，请实现如下所示的代码。 存储在冲突源中的数据会在一定程度上增加存储成本。 因此，建议在处理完冲突源中存储的数据后删除这些数据。

```csharp
FeedResponse<Conflict> response = await myClient.ReadConflictFeedAsync(myCollectionUri);
  foreach (Conflict conflict in response)
  {
      switch(conflict.OperationKind)
      {
         case OperationKind.Create:
         //Process Insert Conflicts.
         …
         case OperationKind.Replace:
         //Process Replace Conflicts
         …
         case OperationKind.Delete:
         //Process Delete Conflicts
         …
      }
  //Optionally delete the conflict after processed
  await myClient.DeleteConflictAsync(conflict.SelfLink);
  }
```

> [!NOTE]
> 与 Cosmos DB 中的更改源一样，冲突源不包含用于发送通知供下游处理的侦听器。 必须实现逻辑来轮询冲突源，并确定是否存在冲突。

## <a name="code-samples"></a>代码示例

以下示例应用程序演示所列 API 的冲突解决方法。 每个示例在容器中生成冲突，然后演示每个受支持的冲突解决模式如何解决冲突。

|API 模型  | SDK 中 IsInRole 中的声明 |示例 |
|---------|---------|---------|
|SQL API    | .NET    |[azure-cosmosdb-dotnet/samples/MultiMaster/](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/MultiMaster)  |
|SQL API    | 节点    |[azure-cosmos-js/samples/MultiRegionWrite/](https://github.com/Azure/azure-cosmos-js/tree/master/samples/MultiRegionWrite)  |
|SQL API    | Java    |[azure-cosmosdb-java-examples/src/main](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/main)  |
|MongoDB  | .NET    |[azure-cosmos-db-mongodb-dotnet-multi-master](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-multi-master)   |
|表 API  | .NET    |[azure-cosmos-db-table-dotnet-multi-master](https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-multi-master)       |
|Gremlin API | .NET | [azure-cosmos-db-gremlin-dotnet-multi-master](https://github.com/Azure-Samples/azure-cosmos-db-gremlin-dotnet-multi-master)|

## <a name="next-steps"></a>后续步骤

在本文中，我们已了解冲突类别和 Azure Cosmos DB 的冲突解决模式。 接下来请浏览以下资源：

* [将 MongoDB 客户端与多主数据库结合使用](multi-master-oss-nosql.md)
