---
title: 更改 Azure 宇宙 DB MongoDB 的 API 中的流
description: 了解如何使用更改流 n Azure Cosmos DB 的 MongoDB API 来获取对数据的更改。
author: srchi
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: srchi
ms.openlocfilehash: ec1ec1a8a80953f8988355341ee7128bd29b982d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77467771"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>更改 Azure 宇宙 DB MongoDB 的 API 中的流

使用更改流 API 可在 Azure Cosmos DB 的蒙戈DB API 中[更改源](change-feed.md)支持。 应用程序可以使用更改流 API 获取对集合或者对单个分片中的项所做的更改。 以后，可以根据结果采取进一步的措施。 对集合中的项所做的更改将按照其修改时间顺序捕获，而排序顺序是按分片键提供保证的。

> [!NOTE]
> 要使用更改流，请使用 Azure Cosmos DB 的 MongoDB API 版本 3.6 或更高版本创建帐户。 如果针对早期版本运行更改流示例，您可能会看到错误`Unrecognized pipeline stage name: $changeStream`。 

以下示例演示如何获取集合中所有项的更改流。 插入、更新或替换项时，此示例会插入一个游标来监视这些项。 获取更改流需要指定 $match 阶段、$project 阶段和 fullDocument 选项。 目前不支持使用更改流来监视删除操作。 一种解决方法是，对正在删除的项添加一个软标记。 例如，可以在名为“deleted”的项中添加一个属性并将其设置为“true”，然后在该项中设置 TTL，以便可以自动删除它，并对其进行跟踪。

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

以下示例演示如何获取对单个分片中的项所做的更改。 此示例获取分片键等于“a”、分片键值等于“1”的项的更改。

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

## <a name="current-limitations"></a>当前限制

使用更改流时，以下限制适用：

* 输出文档中尚不支持 `operationType` 和 `updateDescription` 属性。
* 目前支持 `insert`、`update` 和 `replace` 操作类型。 尚不支持删除操作或其他事件。

由于存在这些限制，需要按前面示例中所示指定 $match 阶段、$project 阶段和 fullDocument 选项。

## <a name="error-handling"></a>错误处理

使用更改流时，支持以下错误代码和消息：

* **HTTP 错误代码 429** - 当更改流受到限制时，它将返回一个空页。

* **NamespaceNotFound (OperationType 失效)** - 如果对不存在的集合运行更改流，或删除了集合，则会返回 `NamespaceNotFound` 错误。 由于 `operationType` 属性无法在输出文档中返回，因此会返回 `NamespaceNotFound` 错误，而不是 `operationType Invalidate` 错误。

## <a name="next-steps"></a>后续步骤

* [在 Azure Cosmos DB's API for MongoDB 中使用生存时间自动使数据过期](mongodb-time-to-live.md)
* [Azure Cosmos DB 的 API for MongoDB 中的索引编制](mongodb-indexing.md)
