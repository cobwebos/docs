---
title: 管理 Azure Cosmos DB 的适用于 MongoDB 的 API 中的索引
description: 本文概述了使用 MongoDB API Azure Cosmos DB 索引功能。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/03/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: fd602f88acf26e821e57e0a844f543aac08dad0d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732704"
---
# <a name="manage-indexing-in-azure-cosmos-dbs-api-for-mongodb"></a>管理 Azure Cosmos DB 的适用于 MongoDB 的 API 中的索引

Azure Cosmos DB 的 MongoDB API 利用了 Azure Cosmos DB 的核心索引管理功能。 本文重点介绍如何使用 Azure Cosmos DB 的 API for MongoDB 添加索引。 你还可以阅读与所有 Api 相关的[Azure Cosmos DB 中的索引的概述](index-overview.md)。

## <a name="indexing-for-mongodb-server-version-36"></a>MongoDB 服务器版本3.6 的索引

适用于 MongoDB 服务器版本3.6 的 Azure Cosmos DB API 自动为`_id`字段编制索引，无法删除该字段。 它会自动强制执行每个`_id`分片键的字段的唯一性。

若要为其他字段编制索引，请应用 MongoDB 索引管理命令。 与 MongoDB 一样，Azure Cosmos DB 的适用于 MongoDB 的 API 仅`_id`自动为字段编制索引。 此默认索引策略不同于 Azure Cosmos DB SQL API，默认情况下对所有字段进行索引。

若要对查询应用排序，必须为排序操作中使用的字段创建索引。

## <a name="index-types"></a>索引类型

### <a name="single-field"></a>单个字段

您可以在任何单个字段上创建索引。 单字段索引的排序顺序并不重要。 以下命令在字段`name`上创建索引：

`db.coll.createIndex({name:1})`

一个查询使用多个单字段索引（如果有）。 每个容器最多可以创建500个字段索引。

### <a name="compound-indexes-mongodb-server-version-36"></a>复合索引（MongoDB 服务器版本3.6）

Azure Cosmos DB 的 MongoDB API 支持使用版本3.6 网络协议的帐户的复合索引。 复合索引中最多可以包含八个字段。 与 MongoDB 不同，仅当查询需要同时对多个字段进行有效排序时，才应创建复合索引。 对于包含多个不需要排序的筛选器的查询，请创建多个单字段索引，而不是单个复合索引。

以下命令在字段`name`和`age`上创建复合索引：

`db.coll.createIndex({name:1,age:1})`

可以使用复合索引一次有效地对多个字段进行排序，如以下示例中所示：

`db.coll.find().sort({name:1,age:1})`

您还可以使用前面的复合索引对所有字段上以相反的排序顺序进行排序。 下面是一个示例：

`db.coll.find().sort({name:-1,age:-1})`

但是，复合索引中的路径序列必须与查询完全匹配。 下面是需要额外的复合索引的查询示例：

`db.coll.find().sort({age:1,name:1})`

### <a name="multikey-indexes"></a>多键索引

Azure Cosmos DB 创建多键索引来索引存储在数组中的内容。 如果使用数组值为字段编制索引，则 Azure Cosmos DB 会自动为数组中的每个元素编制索引。

### <a name="geospatial-indexes"></a>地理空间索引

许多地理空间运算符将受益于地理空间索引。 目前，Azure Cosmos DB 的 MongoDB API 支持`2dsphere`索引。 API 尚不支持`2d`索引。

下面是在`location`字段上创建地理空间索引的示例：

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>文本索引

Azure Cosmos DB 的 MongoDB API 目前不支持文本索引。 对于字符串的文本搜索查询，你应该使用[Azure 认知搜索](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb)与 Azure Cosmos DB 集成。

## <a name="index-properties"></a>索引属性

以下操作适用于服务于线路协议版本3.6 的帐户和为早期版本提供服务的帐户。 您可以了解有关[支持的索引和索引属性](mongodb-feature-support-36.md#indexes-and-index-properties)的详细信息。

### <a name="unique-indexes"></a>唯一索引

[唯一索引](unique-keys.md)用于强制执行两个或多个文档，这些文档不包含索引字段的相同值。

> [!IMPORTANT]
> 唯一索引只能在集合为空（不包含文档）时创建。

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

对于分片集合，必须提供分片（partition）键才能创建唯一索引。 换言之，在分片集合上的所有唯一索引都是复合索引，其中的一个字段是分区键。

以下命令将创建一个分片集合```coll``` （分片键为```university```），并在字段`student_id`和`university`上创建唯一索引：

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

在前面的示例中，省略```"university":1```子句将返回错误，并返回以下消息：

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

### <a name="ttl-indexes"></a>TTL 索引

若要在特定集合中启用文档过期，需要创建[生存时间（TTL）索引](../cosmos-db/time-to-live.md)。 TTL 索引是具有`_ts` `expireAfterSeconds`值的字段的索引。

例如：

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

前面的命令将删除在最近 10 ```db.coll```秒内未修改的集合中的所有文档。

> [!NOTE]
> **_Ts**字段特定于 Azure Cosmos DB，无法从 MongoDB 客户端进行访问。 它是一个保留（系统）属性，其中包含文档上次修改的时间戳。

## <a name="track-index-progress"></a>跟踪索引进度

Azure Cosmos DB 的 MongoDB API 版本3.6 支持使用`currentOp()`命令来跟踪数据库实例上的索引进度。 此命令返回一个文档，该文档包含有关数据库实例的正在进行的操作的信息。 使用`currentOp`命令可以跟踪本机 MongoDB 中的所有正在进行的操作。 在 Azure Cosmos DB 的 MongoDB API 中，此命令仅支持跟踪索引操作。

下面的示例演示如何使用`currentOp`命令来跟踪索引进度：

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

索引进度详细信息显示当前索引操作的进度百分比。 下面是一个示例，显示了索引进度的不同阶段的输出文档格式：

- "Foo" 集合和 "bar" 数据库的索引操作完成了60%。 该`Inprog[0].progress.total`字段显示100作为目标完成百分比。

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

- 如果索引操作刚刚在 "foo" 集合和 "bar" 数据库上开始，则在达到可度量的级别之前，输出文档可能会显示0% 的进度。

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

- 当正在进行的索引操作完成后，输出文档显示空`inprog`操作。

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

### <a name="background-index-updates"></a>背景索引更新

无论为 "**背景**索引" 属性指定的值是什么，索引更新总是在后台完成。 由于索引更新使用的请求单位（ru）的优先级低于其他数据库操作，因此，索引更改不会导致写入、更新或删除的任何停机时间。

添加新索引时，查询将立即使用索引。 这意味着，查询可能不会返回所有匹配的结果，也不会返回任何错误。 当索引转换完成时，查询结果将是一致的。 可以[跟踪索引进度](#track-index-progress)。

## <a name="migrate-collections-with-indexes"></a>迁移包含索引的集合

目前，如果集合不包含任何文档，则只能创建唯一索引。 常用 MongoDB 迁移工具会在导入数据后尝试创建唯一索引。 若要避免此问题，可以手动创建相应的集合和唯一索引，而不是允许迁移工具尝试。 （您可以```mongorestore```通过在命令行中使用`--noIndexRestore`标志来实现此行为。）

## <a name="indexing-for-mongodb-version-32"></a>MongoDB 版本3.2 的索引

与 MongoDB 线路协议版本3.2 兼容的 Azure Cosmos 帐户的可用索引功能和默认值不同。 你可以[检查帐户的版本](mongodb-feature-support-36.md#protocol-support)。 你可以通过填写[支持请求](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)来升级到3.6 版本。

如果使用版本3.2，本节概述了版本3.6 的主要区别。

### <a name="dropping-default-indexes-version-32"></a>删除默认索引（版本3.2）

与 Azure Cosmos DB 的 API for MongoDB 3.6 版不同，版本3.2 默认索引每个属性。 您可以使用以下命令来删除集合（```coll```）的这些默认索引：

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

删除默认索引后，可以添加更多的索引，就像在版本3.6 中那样。

### <a name="compound-indexes-version-32"></a>复合索引（版本3.2）

复合索引包含对文档多个字段的引用。 如果要创建复合索引，请通过存档[支持请求](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)来升级到版本3.6。

## <a name="next-steps"></a>后续步骤

* [Azure Cosmos DB 中的索引](../cosmos-db/index-policy.md)
* [利用生存时间使 Azure Cosmos DB 中的数据自动过期](../cosmos-db/time-to-live.md)
