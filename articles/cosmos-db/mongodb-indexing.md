---
title: Azure Cosmos DB 的 API for MongoDB 中的索引编制
description: 概述 Azure Cosmos DB 的 API for MongoDB 中的索引编制功能。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.openlocfilehash: c8879884cf3d882e6a6b441244ed139072bedeeb
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77029463"
---
# <a name="indexing-using-azure-cosmos-dbs-api-for-mongodb"></a>使用 Azure Cosmos DB 的 API for MongoDB 编制索引

Azure Cosmos DB 的 API for MongoDB 可利用 Cosmos DB 的自动索引管理功能。 因此，用户可以访问 Cosmos DB 的默认索引编制策略。 因此，如果用户没有定义索引，或者没有删除索引，则默认情况下，所有字段都会在插入到集合中时自动编制索引。 大多数情况下，建议使用在帐户上设置的默认索引编制策略。

## <a name="indexing-for-version-36"></a>版本3.6 的索引

提供线路协议版本3.6 的帐户提供与早期版本提供的策略不同的默认索引策略。 默认情况下，仅索引 _id 字段。 若要为其他字段编制索引，用户必须应用 MongoDB 索引管理命令。 若要对查询应用排序，当前必须为排序操作中使用的字段创建索引。

### <a name="dropping-the-default-indexes-36"></a>删除默认索引（3.6）

对于提供线路协议版本3.6 的帐户，只 _id 默认索引，不能将其删除。

### <a name="creating-a-compound-index-36"></a>创建复合索引（3.6）

使用3.6 线路协议的帐户支持真正的复合索引。 以下命令将对字段 "a" 和 "b" 创建复合索引： `db.coll.createIndex({a:1,b:1})`

复合索引可用于一次对多个字段进行有效排序，如： `db.coll.find().sort({a:1,b:1})`

### <a name="track-the-index-progress"></a>跟踪索引进度

Azure Cosmos DB 的 MongoDB API 的3.6 版本支持 `currentOp()` 命令来跟踪数据库实例上的索引进度。 此命令返回一个文档，该文档包含有关数据库实例的正在进行的操作的信息。 `currentOp` 命令用于跟踪本机 MongoDB 中的所有正在进行的操作，而在 Azure Cosmos DB 的 API for MongoDB 中，此命令仅支持跟踪索引操作。

下面的示例演示如何使用 `currentOp` 命令来跟踪索引进度：

•获取集合的索引进度：

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

•获取数据库中所有集合的索引进度：

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

•获取 Azure Cosmos 帐户中所有数据库和集合的索引进度：

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

索引进度详细信息包含当前索引操作的进度百分比。 下面的示例显示了索引进度的不同阶段的输出文档格式：

1. 如果 "foo" 集合和 "bar" 数据库上的索引操作完成了60% 的索引编制，则将具有以下输出文档。 `Inprog[0].progress.total` 显示100作为目标完成。

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 60 %",
                "progress" : {
                        "done" : 60,
                        "total" : 100
                },
                …………..…..
        }
        ],
        "ok" : 1
   }
   ```

2. 对于刚在 "foo" 集合和 "bar" 数据库上启动的索引操作，输出文档在达到可度量的级别之前，可能会显示0% 的进度。

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 0 %",
                "progress" : {
                        "done" : 0,
                        "total" : 100
                },
                …………..…..
        }
        ],
       "ok" : 1
   }
   ```

3. 正在进行的索引操作完成后，输出文档显示空的 inprog 操作。

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

## <a name="indexing-for-version-32"></a>版本3.2 的索引

### <a name="dropping-the-default-indexes-32"></a>删除默认索引（3.2）

可以使用以下命令删除集合 ```coll``` 的默认索引：

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

### <a name="creating-a-compound-index-32"></a>创建复合索引（3.2）

复合索引包含对文档多个字段的引用。 逻辑上，复合索引相当于为每个字段创建多个单独的索引。 若要利用 Cosmos DB 索引技术具有的优势，建议创建多个单独的索引，而不是一个（非唯一）复合索引。

## <a name="common-indexing-operations"></a>常见索引操作

以下操作共同用于提供线路协议版本3.6 的帐户和服务早期线路协议版本的帐户。 

## <a name="creating-unique-indexes"></a>创建唯一索引

对于编制了索引的字段，[唯一索引](unique-keys.md)用于确保没有两个或两个以上的文档包含同一值。

>[!Important]
> 目前，创建唯一索引的前提是集合为空（不含文档）。

以下命令在字段“student_id”上创建唯一索引：

```shell
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1 }, {unique:true} )
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

对于分片的集合，按照 MongoDB 行为来看，创建唯一索引需要提供分片（分区）键。 换言之，在分片集合上的所有唯一索引都是复合索引，其中的一个字段是分区键。

以下命令使用字段 student_id 和 university 上的唯一索引创建分片集合 ```coll```（分片键为 ```university```）：

```shell
globaldb:PRIMARY> db.runCommand({shardCollection: db.coll._fullName, key: { university: "hashed"}});
{
        "_t" : "ShardCollectionResponse",
        "ok" : 1,
        "collectionsharded" : "test.coll"
}
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1, "university" : 1 }, {unique:true})
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 3,
        "numIndexesAfter" : 4
}
```

在上面的示例中，如果省略 ```"university":1``` 子句，则会返回包含以下消息的错误：

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

## <a name="ttl-indexes"></a>TTL 索引

若要在特定集合中启用文档过期，需创建[“TTL 索引”（生存时间索引）](../cosmos-db/time-to-live.md)。 TTL 索引是 _ts 字段上的索引，其值为“expireAfterSeconds”。

示例：

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

前一命令会导致 ```db.coll``` 集合中未在过去 10 秒内修改的任何文档被删除。

> [!NOTE]
> **_ts** 是特定于 Cosmos DB 的字段，不可从 MongoDB 客户端访问。 它是一个保留（系统）属性，其中包含文档上一次修改的时间戳。

## <a name="migrating-collections-with-indexes"></a>迁移带索引的集合

目前，创建唯一索引的前提是集合不含文档。 常用 MongoDB 迁移工具会尝试在导入数据后创建唯一索引。 若要避免此问题，建议用户手动创建相应的集合和唯一索引，而不是允许迁移工具（对于 ```mongorestore``` 此行为是通过使用命令行中的 `--noIndexRestore` 标志来实现）。

## <a name="next-steps"></a>后续步骤

* [Azure Cosmos DB 中的索引](../cosmos-db/index-policy.md)
* [利用生存时间使 Azure Cosmos DB 中的数据自动过期](../cosmos-db/time-to-live.md)
