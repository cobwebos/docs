---
title: 更改 Azure Cosmos DB 的适用于 MongoDB 的 API 中的流
description: 了解如何使用更改流 n Azure Cosmos DB 的 API for MongoDB 来获取对数据所做的更改。
author: srchi
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: srchi
ms.openlocfilehash: fbfce1c107fcf4b6f7d0b5f590a8ddfa64e69190
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184738"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>更改 Azure Cosmos DB 的适用于 MongoDB 的 API 中的流

通过使用更改流 API，可以使用 Azure Cosmos DB 的 API for MongoDB 中的[更改源](change-feed.md)支持。 通过使用更改流 API，你的应用程序可以获取对集合或单个分片中的项所做的更改。 稍后，你可以根据结果采取进一步操作。 对集合中的项所做的更改将按照其修改时间的顺序进行捕获，并按分片键保证排序顺序。

下面的示例演示如何获取集合中所有项的更改流。 此示例在插入、更新或替换项时创建光标来监视项。 获取更改流需要 $match 阶段、$project 阶段和 fullDocument 选项。 当前不支持监视使用更改流的删除操作。 作为一种解决方法，你可以对要删除的项添加软标记。 例如，你可以在名为 "deleted" 的项中添加一个属性，并将其设置为 "true"，并在该项上设置 TTL，以便可以自动删除它并进行跟踪。

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

下面的示例演示如何在单个分片中获取项的更改。 此示例获取项的更改，这些项的分片键等于 "a"，而分片键值等于 "1"。

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
* 当前支持 `insert`、`update`和 `replace` 操作类型。 尚不支持删除操作或其他事件。

由于这些限制，需要如前面的示例中所示的 $match 阶段、$project 阶段和 fullDocument 选项。

## <a name="error-handling"></a>错误处理。

使用更改流时，支持以下错误代码和消息：

* **HTTP 错误代码 429** -当更改流受到限制时，它将返回一个空页。

* **NamespaceNotFound （OperationType 无效）** -如果在不存在的集合上运行更改流，或删除了集合，则返回 `NamespaceNotFound` 错误。 由于不能在输出文档中返回 `operationType` 属性，而应返回 `NamespaceNotFound` 错误，而不是 `operationType Invalidate` 错误。

## <a name="next-steps"></a>后续步骤

* [使用生存时间在 Azure Cosmos DB 的 API for MongoDB 中自动使数据过期](mongodb-time-to-live.md)
* [Azure Cosmos DB 的 MongoDB API 中的索引编制](mongodb-indexing.md)