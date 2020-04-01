---
title: Azure Cosmos DB's API for MongoDB 中的更改流
description: 了解如何在 Azure Cosmos DB 的蒙高DB API 中使用更改流来获取对数据所做的更改。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: tisande
ms.openlocfilehash: ecfa98241f74aac43a827b645a6ed877624d643d
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437810"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB's API for MongoDB 中的更改流

可以使用更改流 API 获取 Azure Cosmos DB's API for MongoDB 中的[更改源](change-feed.md)支持。 应用程序可以使用更改流 API 获取对集合或者对单个分片中的项所做的更改。 以后，可以根据结果采取进一步的措施。 对集合中的项所做的更改将按照其修改时间顺序捕获，而排序顺序是按分片键提供保证的。

> [!NOTE]
> 要使用更改流，请使用 Azure Cosmos DB 的 MongoDB API 版本 3.6 或更高版本创建帐户。 如果针对早期版本运行更改流示例，您可能会看到错误`Unrecognized pipeline stage name: $changeStream`。

## <a name="current-limitations"></a>当前限制

使用更改流时，以下限制适用：

* 输出文档中尚不支持 `operationType` 和 `updateDescription` 属性。
* 目前支持 `insert`、`update` 和 `replace` 操作类型。 尚不支持删除操作或其他事件。

由于存在这些限制，需要按前面示例中所示指定 $match 阶段、$project 阶段和 fullDocument 选项。

与 Azure Cosmos DB 的 SQL API 中的更改源不同，没有单独的[更改馈送处理器库](change-feed-processor.md)来使用更改流或需要租约容器。 当前不支持[Azure 函数触发器](change-feed-functions.md)来处理更改流。

## <a name="error-handling"></a>错误处理

使用更改流时，支持以下错误代码和消息：

* **HTTP 错误代码 16500** - 当更改流被限制时，它将返回一个空页。

* **NamespaceNotFound (OperationType 失效)** - 如果对不存在的集合运行更改流，或删除了集合，则会返回 `NamespaceNotFound` 错误。 由于 `operationType` 属性无法在输出文档中返回，因此会返回 `NamespaceNotFound` 错误，而不是 `operationType Invalidate` 错误。

## <a name="examples"></a>示例

以下示例演示如何获取集合中所有项的更改流。 插入、更新或替换项时，此示例会插入一个游标来监视这些项。 需要`$match`阶段、`$project`阶段和`fullDocument`选项才能获取更改流。 目前不支持使用更改流来监视删除操作。 一种解决方法是，对正在删除的项添加一个软标记。 例如，您可以在名为"已删除"的项中添加属性。 如果要删除该项目，可以将"已删除"设置为，`true`并在该项目上设置 TTL。 由于将"删除"更新为`true`更新，因此此更改将在更改流中可见。

### <a name="javascript"></a>JavaScript：

```javascript
var cursor = db.coll.watch(
    [
        { $match: { "operationType": { $in: ["insert", "update", "replace"] } } },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1 } }
    ],
    { fullDocument: "updateLookup" });

while (!cursor.isExhausted()) {
    if (cursor.hasNext()) {
        printjson(cursor.next());
    }
}
```

### <a name="c"></a>C#：

```csharp
var pipeline = new EmptyPipelineDefinition<ChangeStreamDocument<BsonDocument>>()
    .Match(change => change.OperationType == ChangeStreamOperationType.Insert || change.OperationType == ChangeStreamOperationType.Update || change.OperationType == ChangeStreamOperationType.Replace)
    .AppendStage<ChangeStreamDocument<BsonDocument>, ChangeStreamDocument<BsonDocument>, BsonDocument>(
    "{ $project: { '_id': 1, 'fullDocument': 1, 'ns': 1, 'documentKey': 1 }}");

var options = new ChangeStreamOptions{
        FullDocument = ChangeStreamFullDocumentOption.UpdateLookup
    };

var enumerator = coll.Watch(pipeline, options).ToEnumerable().GetEnumerator();

while (enumerator.MoveNext()){
        Console.WriteLine(enumerator.Current);
    }

enumerator.Dispose();
```

## <a name="changes-within-a-single-shard"></a>单个分片中的更改

下面的示例演示如何在单个分片中获取项的更改。 此示例获取分片键等于“a”、分片键值等于“1”的项的更改。 可以让不同的客户端并行读取不同分片的更改。

```javascript
var cursor = db.coll.watch(
    [
        {
            $match: {
                $and: [
                    { "fullDocument.a": 1 }, 
                    { "operationType": { $in: ["insert", "update", "replace"] } }
                ]
            }
        },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1} }
    ],
    { fullDocument: "updateLookup" });

```

## <a name="next-steps"></a>后续步骤

* [在 Azure Cosmos DB's API for MongoDB 中使用生存时间自动使数据过期](mongodb-time-to-live.md)
* [Azure Cosmos DB 的 API for MongoDB 中的索引编制](mongodb-indexing.md)
