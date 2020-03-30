---
title: Azure Cosmos DB 的 API for MongoDB 中的索引编制
description: 概述 Azure Cosmos DB 的 API for MongoDB 中的索引编制功能。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 03/27/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: 7c75f0d6f74fe8cf1417e0dc40a5ad01615d7057
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371077"
---
# <a name="indexing-using-azure-cosmos-dbs-api-for-mongodb"></a>使用 Azure Cosmos DB 的 API for MongoDB 编制索引

Azure Cosmos DB 的蒙戈DB API 利用了 Azure Cosmos DB 的核心索引管理功能。 本文档重点介绍如何使用 Azure Cosmos DB 的 MongoDB API 添加索引。 您还可以阅读 Azure [Cosmos DB 中](index-overview.md)与所有 API 相关的索引概述。

## <a name="indexing-for-version-36"></a>版本 3.6 的索引

该`_id`字段始终自动编制索引，不能删除。 Azure Cosmos DB 的 MongoDB API 自动强制每个`_id`分片键的字段的唯一性。

要索引其他字段，应应用 MongoDB 索引管理命令。 与 MongoDB 中一`_id`样，只有字段自动编制索引。 此默认索引策略不同于 Azure Cosmos DB SQL API，后者默认对所有字段进行索引。

要将排序应用于查询，必须在排序操作中使用的字段上创建索引。

## <a name="index-types"></a>索引类型

### <a name="single-field"></a>单字段

您可以在任何单个字段上创建索引。 单个字段索引的排序顺序并不重要。 以下命令将在字段`name`上创建索引：

`db.coll.createIndex({name:1})`

一个查询将使用多个单字段索引（如果可用）。

### <a name="compound-indexes-36"></a>复合指数 （3.6）

对于使用 3.6 线协议的帐户，支持复合索引。 复合索引中最多可以包含 8 个字段。 与 MongoDB 不同，仅当查询需要同时对多个字段进行有效排序时，才应创建复合索引。 对于具有多个筛选器不需要排序的查询，应创建多个单个字段索引，而不是单个复合索引。

以下命令将在字段`name`上创建复合索引，并`age`：

`db.coll.createIndex({name:1,age:1})`

复合索引可用于一次对多个字段进行高效排序，例如：

`db.coll.find().sort({name:1,age:1})`

上述复合索引还可用于对所有字段上具有相反排序顺序的查询进行有效排序。 下面是一个示例：

`db.coll.find().sort({name:-1,age:-1})`

但是，复合索引中的路径序列必须与查询完全匹配。 下面是需要附加复合索引的查询示例：

`db.coll.find().sort({age:1,name:1})`

### <a name="multikey-indexes"></a>多键索引

Azure Cosmos DB 创建多键索引来索引存储在数组中的内容。 如果使用数组值对字段编制索引，则 Azure Cosmos DB 会自动为数组中的每个元素编制索引。

### <a name="geospatial-indexes"></a>地理空间索引

许多地理空间运算符将受益于地理空间索引。 目前，Azure Cosmos DB 的蒙戈DB `2dsphere` API 支持索引。 `2d`尚不支持索引。

下面是在字段上创建地理空间索引的示例`location`：

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>文本索引

当前不支持文本索引。 对于字符串上的文本搜索查询，应使用[Azure 认知搜索](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb)与 Azure Cosmos DB 的集成。

## <a name="index-properties"></a>索引属性

以下操作对于遵守线路协议版本 3.6 的帐户和遵守早期线路协议版本的帐户都是常用的。 您还可以了解有关[受支持的索引和索引属性](mongodb-feature-support-36.md#indexes-and-index-properties)的更多。

### <a name="unique-indexes"></a>唯一索引

对于编制了索引的字段，[唯一索引](unique-keys.md)用于确保没有两个或两个以上的文档包含同一值。

>[!Important]
> 仅当集合为空（不包含任何文档）时，才能创建唯一索引。

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

对于分片集合，创建唯一索引需要提供分片（分区）键。 换言之，在分片集合上的所有唯一索引都是复合索引，其中的一个字段是分区键。

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

### <a name="ttl-indexes"></a>TTL 索引

若要在特定集合中启用文档过期，需创建[“TTL 索引”（生存时间索引）](../cosmos-db/time-to-live.md)。 TTL 索引是 _ts 字段上的索引，其值为“expireAfterSeconds”。

示例：

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

前一命令会导致 ```db.coll``` 集合中未在过去 10 秒内修改的任何文档被删除。

> [!NOTE]
> **_ts**是特定于 Azure Cosmos 的特定于 DB 的字段，无法从 MongoDB 客户端访问。 它是一个保留（系统）属性，其中包含文档上一次修改的时间戳。

## <a name="track-the-index-progress"></a>跟踪索引进度

3.6 版本的 Azure Cosmos DB 的 MongoDB 帐户`currentOp()`API 支持跟踪数据库实例上的索引进度的命令。 此命令返回包含有关数据库实例上正在进行的操作的信息的文档。 该`currentOp`命令用于跟踪本机 MongoDB 中的所有正在进行的操作，而在 Azure Cosmos DB 的 MongoDB API 中，此命令仅支持跟踪索引操作。

下面是一些演示如何使用 命令`currentOp`跟踪索引进度的示例：

* 获取集合的索引进度：

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

* 获取数据库中所有集合的索引进度：

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

* 获取 Azure Cosmos 帐户中所有数据库和集合的索引进度：

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

索引进度详细信息包含当前索引操作的进度百分比。 下面的示例显示了索引进度不同阶段的输出文档格式：

1. 如果"foo"集合和具有 60% 索引完成的"bar"数据库上的索引操作将具有以下输出文档。 `Inprog[0].progress.total`将显示 100 作为目标完成。

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

2. 对于刚刚开始在"foo"集合和"bar"数据库上启动的索引操作，输出文档可能会显示 0% 的进度，直到达到可测量级别。

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

3. 当正在进行的索引操作完成时，输出文档将显示空的 inprog 操作。

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

### <a name="background-index-updates"></a>背景索引更新

无论为**背景**索引属性指定的值如何，索引更新始终在后台完成。 索引更新使用 RU 的优先级低于其他数据库操作。 因此，索引更改不会导致写入、更新或删除的任何停机时间。

添加新索引时，查询将立即使用它。 这意味着查询可能不会返回所有匹配的结果，并且不会在不返回任何错误的情况下返回。 索引转换完成后，查询结果将一致。 您可以[跟踪索引进度](#track-the-index-progress)。

## <a name="migrating-collections-with-indexes"></a>迁移带索引的集合

目前，创建唯一索引的前提是集合不含文档。 常用 MongoDB 迁移工具会尝试在导入数据后创建唯一索引。 若要避免此问题，建议用户手动创建相应的集合和唯一索引，而不是让迁移工具来创建（对于 ```mongorestore```，可以通过在命令行中使用 `--noIndexRestore` 标志来实现此行为）。

## <a name="indexing-for-version-32"></a>版本 3.2 的索引

对于与 3.2 版本的 MongoDB 有线协议兼容的 Azure Cosmos DB 帐户，可用的索引功能和默认值是不同的。 您可以[检查您的帐户版本](mongodb-feature-support-36.md#protocol-support)。 您可以通过提交[支持请求](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)升级到 3.6 版本。

如果您使用的是版本 3.2，本节概述了版本 3.6 的关键区别。

### <a name="dropping-the-default-indexes-32"></a>删除默认索引 (3.2)

与 3.6 版本的 Azure Cosmos DB 的 MongoDB API 不同，3.2 版本默认索引每个属性。 以下命令可用于删除集合```coll```的这些默认索引：

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

删除默认索引后，可以添加其他索引，如版本 3.6 中所做的那样。

### <a name="compound-indexes-32"></a>复合指数 （3.2）

复合索引包含对文档多个字段的引用。 如果要创建复合索引，请通过提交[支持请求](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)升级到 3.6 版本。

## <a name="next-steps"></a>后续步骤

* [Azure Cosmos DB 中的索引](../cosmos-db/index-policy.md)
* [利用生存时间使 Azure Cosmos DB 中的数据自动过期](../cosmos-db/time-to-live.md)
