---
title: 管理 Azure Cosmos DB 的用于 MongoDB 的 API 中的索引编制
description: 本文概述了使用 Azure Cosmos DB 的 MongoDB API Azure Cosmos DB 索引功能
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: how-to
ms.date: 08/06/2020
author: timsander1
ms.author: tisande
ms.custom: devx-track-javascript
ms.openlocfilehash: e47b8727eccd1b185f381ae3f8474fe13a406501
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87843804"
---
# <a name="manage-indexing-in-azure-cosmos-dbs-api-for-mongodb"></a>管理 Azure Cosmos DB 的用于 MongoDB 的 API 中的索引编制

Azure Cosmos DB 的用于 MongoDB 的 API 利用 Azure Cosmos DB 的核心索引管理功能。 本文重点介绍如何使用 Azure Cosmos DB 的用于 MongoDB 的 API 添加索引。 你还可以阅读与所有 API 相关的 [Azure Cosmos DB 中的索引编制概述](index-overview.md)。

## <a name="indexing-for-mongodb-server-version-36"></a>适用于 MongoDB 服务器版本 3.6 的索引编制功能

Azure Cosmos DB 的用于 MongoDB 服务器版本 3.6 的 API 会自动为无法删除的 `_id` 字段编制索引。 它会自动强制确保每个分片密钥的 `_id` 字段的唯一性。 在 Azure Cosmos DB 的 MongoDB API 中，分片和索引是单独的概念。 无需为分片键编制索引。 但是，与文档中的任何其他属性一样，如果此属性是查询中的常见筛选器，我们建议为分片键编制索引。

若要为其他字段编制索引，请应用 MongoDB 索引管理命令。 与在 MongoDB 中一样，Azure Cosmos DB 的用于 MongoDB 的 API 仅自动为 `_id` 字段编制索引。 此默认索引编制策略不同于 Azure Cosmos DB SQL API，后者在默认情况下会为所有字段编制索引。

要将排序应用于查询，必须对排序操作中使用的字段创建索引。

## <a name="index-types"></a>索引类型

### <a name="single-field"></a>单个字段

只能对任何单个字段创建索引。 单字段索引的排序顺序并不重要。 以下命令对字段 `name` 创建索引：

`db.coll.createIndex({name:1})`

在适用的情况下，一个查询将使用多个单字段索引。 对于每个容器，最多可以创建 500 个单字段索引。

### <a name="compound-indexes-mongodb-server-version-36"></a>复合索引（MongoDB 服务器版本 3.6）

Azure Cosmos DB 的用于 MongoDB 的 API 对使用版本 3.6 Wire Protocol 的帐户支持复合索引。 一个复合索引中最多可以包含 8 个字段。 与在 MongoDB 中不同，仅当查询需要一次对多个字段进行高效排序时，才应创建复合索引。 对于包含多个不需要排序的筛选器的查询，请创建多个单字段索引，而不是创建单个复合索引。

以下命令对字段 `name` 和 `age` 创建复合索引：

`db.coll.createIndex({name:1,age:1})`

可以使用复合索引来同时对多个字段进行高效排序，如以下示例中所示：

`db.coll.find().sort({name:1,age:1})`

还可以使用前面的复合索引，在一个查询中对所有字段按降序进行高效排序。 下面是一个示例：

`db.coll.find().sort({name:-1,age:-1})`

但是，复合索引中的路径顺序必须与查询完全匹配。 下面是一个需要其他复合索引的查询示例：

`db.coll.find().sort({age:1,name:1})`

### <a name="multikey-indexes"></a>多键索引

Azure Cosmos DB 创建多键索引来为数组中存储的内容编制索引。 如果为带有数组值的字段编制索引，则 Azure Cosmos DB 会自动为数组中的每个元素编制索引。

### <a name="geospatial-indexes"></a>空间索引

许多地理空间运算符可受益于地理空间索引。 Azure Cosmos DB 的 API for MongoDB 目前支持 `2dsphere` 索引。 该 API 尚不支持 `2d` 索引。

下面是对 `location` 字段创建地理空间索引的示例：

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>文本索引

Azure Cosmos DB 的用于 MongoDB 的 API 目前支持文本索引。 要对字符串运行文本搜索查询，应使用 [Azure 认知搜索](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb)与 Azure Cosmos DB 的集成。

## <a name="wildcard-indexes"></a>通配符索引

您可以使用通配符索引来支持针对未知字段的查询。 假设有一个集合，其中包含有关系列的数据。

下面是该集合中的示例文档的一部分：

```json
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "grade": "5"
     }
  ]
```

下面是另一个示例，这一次使用的属性集略有不同 `children` ：

```json
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
      },
      {
        "familyName": "Merriam",
        "givenName": "John",
      }
  ]
```

在此集合中，文档可以有许多不同的可能属性。 如果要对数组中的所有数据编制索引 `children` ，可以使用两个选项：为每个单独的属性创建单独的索引，或者为整个数组创建一个通配符索引 `children` 。

### <a name="create-a-wildcard-index"></a>创建通配符索引

以下命令对中的任何属性创建一个通配符索引 `children` ：

`db.coll.createIndex({"children.$**" : 1})`

**与 MongoDB 不同，通配符索引可以在查询谓词中支持多个字段**。 如果使用一个通配符索引，而不是为每个属性创建一个单独的索引，则不会对查询性能产生差别。

您可以使用通配符语法创建以下索引类型：

- 单个字段
- 地理空间

### <a name="indexing-all-properties"></a>为所有属性编制索引

下面介绍了如何对所有字段创建通配符索引：

`db.coll.createIndex( { "$**" : 1 } )`

开始开发时，对所有字段创建通配符索引可能会很有用。 随着文档中索引的属性的增多，请求单位 (RU) 的写入和更新的费用将增加。 因此，如果您有一个写繁重的工作负荷，则应该选择单独索引路径，而不是使用通配符索引。

### <a name="limitations"></a>限制

通配符索引不支持以下任何一种索引类型或属性：

- 复合
- TTL
- 唯一

**不同于 mongodb**，在 AZURE COSMOS DB 的 API for mongodb 中，**不能**将通配符索引用于：

- 创建包含多个特定字段的通配符索引

`db.coll.createIndex(
    { "$**" : 1 },
    { "wildcardProjection " :
        {
           "children.givenName" : 1,
           "children.grade" : 1
        }
    }
)`

- 创建排除多个特定字段的通配符索引

`db.coll.createIndex(
    { "$**" : 1 },
    { "wildcardProjection" :
        {
           "children.givenName" : 0,
           "children.grade" : 0
        }
    }
)`

作为替代方法，可以创建多个通配符索引。

## <a name="index-properties"></a>索引属性

以下操作是遵守 Wire Protocol 版本 3.6 的帐户和遵守更低版本的帐户常用的操作。 还可以详细了解[支持的索引和已编制索引的属性](mongodb-feature-support-36.md#indexes-and-index-properties)。

### <a name="unique-indexes"></a>唯一索引

对于编制了索引的字段，[唯一索引](unique-keys.md)用于确保这些字段的同一值不会存在于两个或两个以上的文档中。

> [!IMPORTANT]
> 创建唯一索引的前提是集合为空（不含文档）。

以下命令对字段 `student_id` 创建唯一索引：

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

对于分片的集合，必须提供分片（分区）键才能创建唯一索引。 换言之，在分片集合上的所有唯一索引都是复合索引，其中的一个字段是分区键。

以下命令创建一个分片的集合 ```coll```（分片键为 ```university```），该集合具有 `student_id` 和 `university` 字段上的唯一索引：

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

在前面的示例中，省略 ```"university":1``` 子句将返回包含以下消息的错误：

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

### <a name="ttl-indexes"></a>TTL 索引

若要在特定集合中启用文档过期，需创建[生存时间 (TTL) 索引](../cosmos-db/time-to-live.md)。 TTL 索引是具有 `expireAfterSeconds` 值的 `_ts` 字段上的索引。

示例：

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

上面的命令会在 ```db.coll``` 集合中删除过去 10 秒内未修改的任何文档。

> [!NOTE]
> _ts 字段是特定于 Azure Cosmos DB 的字段，不可从 MongoDB 客户端访问。 它是一个保留（系统）属性，其中包含文档上次进行修改时的时间戳。

## <a name="track-index-progress"></a>跟踪索引进度

Azure Cosmos DB 的用于 MongoDB 的 API 版本 3.6 支持使用 `currentOp()` 命令来跟踪数据库实例上的索引进度。 此命令返回一个文档，其中包含有关数据库实例上正在进行的操作的信息。 可使用 `currentOp` 命令跟踪本机 MongoDB 中所有正在进行的操作。 在 Azure Cosmos DB 的用于 MongoDB 的 API 中，此命令仅支持跟踪索引操作。

下面这些示例演示如何使用 `currentOp` 命令来跟踪索引进度：

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

索引进度详细信息显示当前索引操作的进度百分比。 以下示例显示索引进度的不同阶段的输出文档格式：

- 如果针对“foo”集合与“bar”数据库执行了索引操作，且该操作已完成 60%，那么该操作将有以下输出文档。 `Inprog[0].progress.total` 字段将 100 显示为目标完成百分比。

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

- 如果索引操作刚刚针对“foo”集合与“bar”数据库启动，那么在进度达到可度量的级别之前，输出文档可能会一直显示 0% 进度。

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

- 正在进行的索引操作完成后，输出文档将显示 `inprog` 操作为空。

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

## <a name="background-index-updates"></a>后台索引更新

无论为 Background 索引属性指定了什么值，索引更新始终会在后台完成。 由于索引更新操作使用请求单位 (RU) 的优先级低于其他数据库操作，因此，索引更改不会导致写入、更新或删除操作无法正常进行。

添加新索引时，不会影响读取可用性。 索引转换完成后，查询将只利用新索引。 在索引转换过程中，查询引擎将继续使用现有索引，因此在索引转换过程中，您将看到与您在开始索引更改之前观察到的内容类似的读取性能。 添加新索引时，不会有不完整或不一致的查询结果的风险。

删除索引和立即运行查询时，对删除的索引具有筛选器，结果可能不一致且不完整，直到索引转换完成。 如果删除索引，查询引擎在对这些新删除的索引进行筛选时，不能保证一致的或完整的结果。 大多数开发人员不会删除索引，然后立即尝试对其进行查询，实际上，这种情况不太可能发生。

> [!NOTE]
> 可以[跟踪索引进度](#track-index-progress)。

## <a name="migrate-collections-with-indexes"></a>迁移带索引的集合

目前，仅当集合不包含文档时才能创建唯一索引。 常用 MongoDB 迁移工具会尝试在导入数据后创建唯一索引。 若要规避此问题，可以手动创建相应的集合和唯一索引，而不是允许迁移工具尝试。 （可以在命令行中使用 `--noIndexRestore` 标志来为 ```mongorestore``` 实现此行为。）

## <a name="indexing-for-mongodb-version-32"></a>适用于 MongoDB 版本 3.2 的索引编制功能

对于与 MongoDB Wire Protocol 版本 3.2 兼容的 Azure Cosmos DB 帐户，可用的索引编制功能和默认值是不同的。 可以[检查帐户的版本](mongodb-feature-support-36.md#protocol-support)。 可以通过提出[支持请求](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)升级到版本 3.6。

如果使用的是版本 3.2，请阅读此本部分，其中概述了版本 3.2 与版本 3.6 之间的重要差别。

### <a name="dropping-default-indexes-version-32"></a>删除默认索引（版本 3.2）

与 Azure Cosmos DB 的用于 MongoDB 的 API 版本 3.6 不同，版本 3.2 默认会为每个属性编制索引。 可以使用以下命令删除集合 (```coll```) 的这些默认索引：

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

删除默认索引后，可以像在版本 3.6 中那样添加更多索引。

### <a name="compound-indexes-version-32"></a>复合索引（版本 3.2）

复合索引包含对文档多个字段的引用。 若要创建复合索引，请通过提出[支持请求](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)升级到版本 3.6。

### <a name="wildcard-indexes-version-32"></a> (版本 3.2) 的通配符索引

若要创建通配符索引，请通过存档[支持请求](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)来升级到版本3.6。

## <a name="next-steps"></a>后续步骤

* [Azure Cosmos DB 中的索引](../cosmos-db/index-policy.md)
* [利用生存时间使 Azure Cosmos DB 中的数据自动过期](../cosmos-db/time-to-live.md)
* 若要了解分区与索引之间的关系，请参阅如何[查询 Azure Cosmos 容器一](how-to-query-container.md)文。
