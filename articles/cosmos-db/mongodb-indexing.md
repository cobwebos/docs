---
title: 管理在 Azure Cosmos DB 的 MongoDB API 中的索引
description: 本文概述了使用 MongoDB API 的 Azure Cosmos DB 索引功能。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/03/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: fd602f88acf26e821e57e0a844f543aac08dad0d
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732704"
---
# <a name="manage-indexing-in-azure-cosmos-dbs-api-for-mongodb"></a>管理在 Azure Cosmos DB 的 MongoDB API 中的索引

Azure Cosmos DB 的蒙戈DB API 利用了 Azure Cosmos DB 的核心索引管理功能。 本文重点介绍如何使用 Azure Cosmos DB 的 MongoDB API 添加索引。 您还可以阅读 Azure [Cosmos DB 中](index-overview.md)与所有 API 相关的索引概述。

## <a name="indexing-for-mongodb-server-version-36"></a>MongoDB 服务器版本 3.6 的索引

Azure Cosmos DB 的 MongoDB 服务器版本 3.6`_id`的 API 自动索引字段，无法删除该字段。 它自动强制每个分片键的`_id`字段的唯一性。

要索引其他字段，请应用 MongoDB 索引管理命令。 与 MongoDB 一样，Azure Cosmos DB 的 MongoDB `_id` API 仅自动索引字段。 此默认索引策略不同于 Azure Cosmos DB SQL API，后者默认对所有字段进行索引。

要将排序应用于查询，必须在排序操作中使用的字段上创建索引。

## <a name="index-types"></a>索引类型

### <a name="single-field"></a>单字段

您可以在任何单个字段上创建索引。 单个字段索引的排序顺序并不重要。 以下命令在字段`name`上创建索引：

`db.coll.createIndex({name:1})`

一个查询使用多个单个字段索引（如果可用）。 每个容器最多可以创建 500 个单字段索引。

### <a name="compound-indexes-mongodb-server-version-36"></a>复合索引（MongoDB 服务器版本 3.6）

Azure Cosmos DB 的 MongoDB API 支持使用版本 3.6 线协议的帐户的复合索引。 复合索引中最多可以包含八个字段。 与 MongoDB 不同，仅当查询需要同时对多个字段进行有效排序时，才应创建复合索引。 对于具有多个不需要排序的筛选器的查询，创建多个单个字段索引，而不是单个复合索引。

以下命令在字段`name`和 上创建复合索引，`age`然后 ：

`db.coll.createIndex({name:1,age:1})`

您可以使用复合索引一次对多个字段进行有效排序，如以下示例所示：

`db.coll.find().sort({name:1,age:1})`

您还可以使用前面的复合索引对所有字段上具有相反排序顺序的查询进行有效排序。 下面是一个示例：

`db.coll.find().sort({name:-1,age:-1})`

但是，复合索引中的路径序列必须与查询完全匹配。 下面是需要附加复合索引的查询示例：

`db.coll.find().sort({age:1,name:1})`

### <a name="multikey-indexes"></a>多键索引

Azure Cosmos DB 创建多键索引来索引存储在数组中的内容。 如果使用数组值对字段编制索引，则 Azure Cosmos DB 会自动为数组中的每个元素编制索引。

### <a name="geospatial-indexes"></a>地理空间索引

许多地理空间运算符将受益于地理空间索引。 目前，Azure Cosmos DB 的蒙戈DB `2dsphere` API 支持索引。 API 尚不支持`2d`索引。

下面是在字段上创建地理空间索引的示例`location`：

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>文本索引

Azure Cosmos DB 的蒙戈DB API 当前不支持文本索引。 对于字符串上的文本搜索查询，应使用[Azure 认知搜索](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb)与 Azure Cosmos DB 集成。

## <a name="index-properties"></a>索引属性

以下操作对于提供有线协议版本 3.6 的帐户和提供早期版本的帐户很常见。 您可以了解有关[受支持的索引和索引属性](mongodb-feature-support-36.md#indexes-and-index-properties)的更多。

### <a name="unique-indexes"></a>唯一索引

[唯一索引](unique-keys.md)可用于强制两个或多个文档不包含索引字段的相同值。

> [!IMPORTANT]
> 仅当集合为空（不包含任何文档）时，才能创建唯一索引。

以下命令在字段`student_id`上创建唯一索引：

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

对于分片集合，必须提供分片（分区）键才能创建唯一索引。 换言之，在分片集合上的所有唯一索引都是复合索引，其中的一个字段是分区键。

以下命令```coll```创建分片集合（分片键为```university```），字段`student_id`上具有唯一索引，并且`university`：

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

在前面的示例中，省略子```"university":1```句返回一个错误，其中带有以下消息：

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

### <a name="ttl-indexes"></a>TTL 索引

要启用特定集合中的文档过期，您需要创建[一个生存时间 （TTL） 索引](../cosmos-db/time-to-live.md)。 TTL 索引是字段上具有值`_ts`的`expireAfterSeconds`索引。

示例：

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

前面的命令删除```db.coll```集合中最近 10 秒内未修改的任何文档。

> [!NOTE]
> **_ts**字段特定于 Azure Cosmos DB，并且无法从 MongoDB 客户端访问。 它是一个保留（系统）属性，其中包含文档上次修改的时间戳。

## <a name="track-index-progress"></a>跟踪索引进度

Azure Cosmos DB 的 MongoDB API 版本 3.6 支持跟踪数据库实例上的索引进度`currentOp()`的命令。 此命令返回包含有关数据库实例上正在进行的操作的信息的文档。 使用 该`currentOp`命令跟踪本机 MongoDB 中的所有正在进行的操作。 在 Azure Cosmos DB 的蒙戈DB API 中，此命令仅支持跟踪索引操作。

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

### <a name="examples-of-index-progress-output"></a>索引进度输出示例

索引进度详细信息显示当前索引操作的进度百分比。 下面是一个示例，该示例显示了索引进度的不同阶段的输出文档格式：

- 对"foo"集合和 60% 完成的"bar"数据库的索引操作将具有以下输出文档。 此字段`Inprog[0].progress.total`显示 100 作为目标完成百分比。

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

- 如果索引操作刚刚在"foo"集合和"bar"数据库中启动，则输出文档可能会显示 0% 的进度，直到达到可衡量的水平。

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

- 当正在进行的索引操作完成时，输出文档将显示空`inprog`操作。

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

### <a name="background-index-updates"></a>背景索引更新

无论为**背景**索引属性指定的值如何，索引更新始终在后台完成。 由于索引更新使用请求单位 （RUs） 的优先级低于其他数据库操作，因此索引更改不会导致写入、更新或删除的任何停机时间。

添加新索引时，查询将立即使用该索引。 这意味着查询可能不会返回所有匹配的结果，并且不会返回任何错误。 索引转换完成后，查询结果将一致。 您可以[跟踪索引进度](#track-index-progress)。

## <a name="migrate-collections-with-indexes"></a>使用索引迁移集合

目前，只有在集合不包含文档时，才能创建唯一索引。 流行的 MongoDB 迁移工具尝试在导入数据后创建唯一索引。 若要规避此问题，可以手动创建相应的集合和唯一索引，而不是允许尝试迁移工具。 （您可以通过在命令行中使用```mongorestore````--noIndexRestore`标志来实现此行为。

## <a name="indexing-for-mongodb-version-32"></a>MongoDB 版本 3.2 的索引

对于与 MongoDB 有线协议版本 3.2 兼容的 Azure Cosmos 帐户，可用的索引功能和默认值是不同的。 您可以[检查您的帐户版本](mongodb-feature-support-36.md#protocol-support)。 您可以通过提交[支持请求](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)升级到 3.6 版本。

如果您使用的是版本 3.2，本节概述了版本 3.6 的关键区别。

### <a name="dropping-default-indexes-version-32"></a>删除默认索引（版本 3.2）

与 3.6 版本的 Azure Cosmos DB 的 MongoDB API 不同，版本 3.2 默认索引每个属性。 可以使用以下命令删除集合的这些默认索引 （```coll```：

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

删除默认索引后，可以像在版本 3.6 中添加更多索引。

### <a name="compound-indexes-version-32"></a>复合索引（版本 3.2）

复合索引包含对文档多个字段的引用。 如果要创建复合索引，请通过提交[支持请求](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)升级到版本 3.6。

## <a name="next-steps"></a>后续步骤

* [Azure Cosmos DB 中的索引](../cosmos-db/index-policy.md)
* [利用生存时间使 Azure Cosmos DB 中的数据自动过期](../cosmos-db/time-to-live.md)
