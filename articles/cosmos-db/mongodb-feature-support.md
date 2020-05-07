---
title: Azure Cosmos DB 的 API for MongoDB（3.2 版本）支持的功能和语法
description: 了解 Azure Cosmos DB 的 API for MongoDB（3.2 版本）支持的功能和语法。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 10/16/2019
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 94b65b4e7947bc02b1fdaae90c8f774ec216e7bb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "80981879"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-32-version-supported-features-and-syntax"></a>Azure Cosmos DB 的 API for MongoDB（3.2 版本）：支持的功能和语法

Azure Cosmos DB 由 Microsoft 提供，是全球分布的多模型数据库服务。 可通过任何开源 MongoDB 客户端[驱动程序](https://docs.mongodb.org/ecosystem/drivers)与 Azure Cosmos DB 的 MongoDB API 进行通信。 可以按照 MongoDB [有线协议](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)规定，通过 Azure Cosmos DB 的 MongoDB API 来使用现有客户端驱动程序。

通过使用 Azure Cosmos DB 的 MongoDB API，可以像以往一样从 MongoDB 中受益，并且可使用 Cosmos DB 提供的所有企业功能：[全局分发](distribute-data-globally.md)、[自动分片](partition-data.md)、可用性和延迟保证、自动编制每个字段的索引、静态加密和备份等。

> [!NOTE]
> 本文适用于 Azure Cosmos DB 的 API for MongoDB 3.2。 对于 MongoDB 3.6 版本，请参阅 [MongoDB 3.6 支持的功能和语法](mongodb-feature-support-36.md)。

## <a name="protocol-support"></a>协议支持

Azure Cosmos DB 的 API for MongoDB 的所有新帐户都与 MongoDB 服务器版本 **3.6** 兼容。 本文介绍 MongoDB 版本 3.2。 支持的运算符以及限制或例外已列在下面。 任何理解这些协议的客户端驱动程序应该都能够连接到 Azure Cosmos DB 的 MongoDB API。

## <a name="query-language-support"></a>查询语言支持

Azure Cosmos DB 的 MongoDB API 全面支持 MongoDB 查询语言构造。 可以在下面查找当前支持的操作、运算符、阶段、命令和选项的详细列表。

## <a name="database-commands"></a>数据库命令

Azure Cosmos DB 的 MongoDB API 支持以下数据库命令：

### <a name="query-and-write-operation-commands"></a>查询和写入操作命令

- delete
- find
- findAndModify
- getLastError
- getMore
- insert
- update

### <a name="authentication-commands"></a>身份验证命令

- logout
- authenticate
- getnonce

### <a name="administration-commands"></a>管理命令

- dropDatabase
- listCollections
- drop
- create
- filemd5
- createIndexes
- listIndexes
- dropIndexes
- connectionStatus
- reIndex

### <a name="diagnostics-commands"></a>诊断命令

- buildInfo
- collStats
- dbStats
- hostInfo
- listDatabases
- whatsmyuri

<a name="aggregation-pipeline"/>

## <a name="aggregation-pipelinea"></a>聚合管道</a>

在公共预览版中，Cosmos DB 支持 MongoDB 3.2 的聚合管道。 请参阅 [Azure 博客](https://azure.microsoft.com/blog/azure-cosmosdb-extends-support-for-mongodb-aggregation-pipeline-unique-indexes-and-more/)，了解有关如何载入到公共预览版的说明。

### <a name="aggregation-commands"></a>聚合命令

- aggregate
- count
- distinct

### <a name="aggregation-stages"></a>聚合阶段

- $project
- $match
- $limit
- $skip
- $unwind
- $group
- $sample
- $sort
- $lookup
- $out
- $count
- $addFields

### <a name="aggregation-expressions"></a>聚合表达式

#### <a name="boolean-expressions"></a>布尔表达式

- $and
- $or
- $not

#### <a name="set-expressions"></a>集表达式

- $setEquals
- $setIntersection
- $setUnion
- $setDifference
- $setIsSubset
- $anyElementTrue
- $allElementsTrue

#### <a name="comparison-expressions"></a>比较表达式

- $cmp
- $eq
- $gt
- $gte
- $lt
- $lte
- $ne

#### <a name="arithmetic-expressions"></a>算术表达式

- $abs
- $add
- $ceil
- $divide
- $exp
- $floor
- $ln
- $log
- $log10
- $mod
- $multiply
- $pow
- $sqrt
- $subtract
- $trunc

#### <a name="string-expressions"></a>字符串表达式

- $concat
- $indexOfBytes
- $indexOfCP
- $split
- $strLenBytes
- $strLenCP
- $strcasecmp
- $substr
- $substrBytes
- $substrCP
- $toLower
- $toUpper

#### <a name="array-expressions"></a>数组表达式

- $arrayElemAt
- $concatArrays
- $filter
- $indexOfArray
- $isArray
- $range
- $reverseArray
- $size
- $slice
- $in

#### <a name="date-expressions"></a>日期表达式

- $dayOfYear
- $dayOfMonth
- $dayOfWeek
- $year
- $month
- $week
- $hour
- $minute
- $second
- $millisecond
- $isoDayOfWeek
- $isoWeek

#### <a name="conditional-expressions"></a>条件表达式

- $cond
- $ifNull

## <a name="aggregation-accumulators"></a>聚合累加器

- $sum
- $avg
- $first
- $last
- $max
- $min
- $push
- $addToSet

## <a name="operators"></a>运算符

以下运算符在其相应的使用示例中受支持。 请考虑下面的查询中使用的示例文档：

```json
{
  "Volcano Name": "Rainier",
  "Country": "United States",
  "Region": "US-Washington",
  "Location": {
    "type": "Point",
    "coordinates": [
      -121.758,
      46.87
    ]
  },
  "Elevation": 4392,
  "Type": "Stratovolcano",
  "Status": "Dendrochronology",
  "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

操作员 | 示例 |
--- | --- |
$eq | `{ "Volcano Name": { $eq: "Rainier" } }` |  | -
$gt | `{ "Elevation": { $gt: 4000 } }` |  | -
$gte | `{ "Elevation": { $gte: 4392 } }` |  | -
$lt | `{ "Elevation": { $lt: 5000 } }` |  | -
$lte | `{ "Elevation": { $lte: 5000 } }` | | -
$ne | `{ "Elevation": { $ne: 1 } }` |  | -
$in | `{ "Volcano Name": { $in: ["St. Helens", "Rainier", "Glacier Peak"] } }` |  | -
$nin | `{ "Volcano Name": { $nin: ["Lassen Peak", "Hood", "Baker"] } }` | | -
$or | `{ $or: [ { Elevation: { $lt: 4000 } }, { "Volcano Name": "Rainier" } ] }` |  | -
$and | `{ $and: [ { Elevation: { $gt: 4000 } }, { "Volcano Name": "Rainier" } ] }` |  | -
$not | `{ "Elevation": { $not: { $gt: 5000 } } }`|  | -
$nor | `{ $nor: [ { "Elevation": { $lt: 4000 } }, { "Volcano Name": "Baker" } ] }` |  | -
$exists | `{ "Status": { $exists: true } }`|  | -
$type | `{ "Status": { $type: "string" } }`|  | -
$mod | `{ "Elevation": { $mod: [ 4, 0 ] } }` |  | -
$regex | `{ "Volcano Name": { $regex: "^Rain"} }`|  | -

### <a name="notes"></a>说明

在 $regex 查询中，左定位表达式允许索引搜索。 但是，使用“i”修饰符（不区分大小写）和“m”修饰符（多行）会导致在所有表达式中进行回收集合扫描。
当需要包含“$”或“|”时，最好创建两个（或更多）正则表达式查询。
例如，给定以下原始查询：```find({x:{$regex: /^abc$/})```，必须按如下所示进行修改：```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```。
第一部分将使用索引将搜索限制为以 ^ abc 开头的文档，第二部分将匹配确切的条目。
竖条运算符“|”充当“or”函数 - 查询 ```find({x:{$regex: /^abc|^def/})``` 匹配字段“x”的值以“abc”或“def”开头的文档。 要使用索引，建议将查询分解为两个由 $or 运算符连接的不同查询：```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```。

### <a name="update-operators"></a>更新运算符

#### <a name="field-update-operators"></a>字段更新运算符

- $inc
- $mul
- $rename
- $setOnInsert
- $set
- $unset
- $min
- $max
- $currentDate

#### <a name="array-update-operators"></a>数组更新运算符

- $addToSet
- $pop
- $pullAll
- $pull（注意：不支持带条件的 $pull）
- $pushAll
- $push
- $each
- $slice
- $sort
- $position

#### <a name="bitwise-update-operator"></a>位更新运算符

- $bit

### <a name="geospatial-operators"></a>地理空间运算符

操作员 | 示例 | |
--- | --- | --- |
$geoWithin | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | 是 |
$geoIntersects |  ```{ "Location.coordinates": { $geoIntersects: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | 是 |
$near | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | 是 |
$nearSphere | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | 是 |
$geometry | ```{ "Location.coordinates": { $geoWithin: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | 是 |
$minDistance | ```{ "Location.coordinates": { $nearSphere : { $geometry: {type: "Point", coordinates: [ -121, 46 ]}, $minDistance: 1000, $maxDistance: 1000000 } } }``` | 是 |
$maxDistance | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | 是 |
$center | ```{ "Location.coordinates": { $geoWithin: { $center: [ [-121, 46], 1 ] } } }``` | 是 |
$centerSphere | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | 是 |
$box | ```{ "Location.coordinates": { $geoWithin: { $box:  [ [ 0, 0 ], [ -122, 47 ] ] } } }``` | 是 |
$polygon | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | 是 |

## <a name="sort-operations"></a>排序操作

使用 `findOneAndUpdate` 操作时，支持基于单个字段的排序操作，但不支持基于多个字段的排序操作。

## <a name="additional-operators"></a>其他运算符

操作员 | 示例 | 说明
--- | --- | --- |
$all | ```{ "Location.coordinates": { $all: [-121.758, 46.87] } }``` |
$elemMatch | ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } } }``` |
$size | ```{ "Location.coordinates": { $size: 2 } }``` |
$comment |  ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } }, $comment: "Negative values"}``` |
$text |  | 不支持。 改为使用 $regex。

## <a name="unsupported-operators"></a>不支持的运算符

```$where``` 和 ```$eval``` 运算符不受 Azure Cosmos DB 支持。

### <a name="methods"></a>方法

支持下列方法：

#### <a name="cursor-methods"></a>游标方法

方法 | 示例 | 说明
--- | --- | --- |
cursor.sort() | ```cursor.sort({ "Elevation": -1 })``` | 不会返回没有排序键的文件

## <a name="unique-indexes"></a>唯一索引

默认情况下，Cosmos DB 为写入数据库的文档中的每个字段编制索引。 唯一索引确保特定字段在一个集合的所有文档中都不会有重复值，类似于默认 `_id` 键保持唯一性的方式。 可以在 Cosmos DB 中使用 createIndex 命令（包括“唯一”约束）创建自定义索引。

所有使用 Azure Cosmos DB 的 MongoDB API 的 Cosmos 帐户都能使用唯一索引。

## <a name="time-to-live-ttl"></a>生存时间 (TTL)

Cosmos DB 支持基于文档时间戳的生存时间 (TTL)。 转到 [Azure 门户](https://portal.azure.com)便可以为集合启用 TTL。

## <a name="user-and-role-management"></a>用户和角色管理

Cosmos DB 尚不支持用户和角色。 不过 Cosmos DB 支持基于角色的访问控制 (RBAC) 以及读写和只读密码/密钥，可通过 [Azure 门户](https://portal.azure.com)（连接字符串页面）获取这些内容。

## <a name="replication"></a>复制

Cosmos DB 支持在最低层进行自动本机复制。 此逻辑经过扩展，还可实现低延迟和全局复制。 它不支持手动复制命令。

## <a name="write-concern"></a>写关注

某些应用程序依赖[写关注](https://docs.mongodb.com/manual/reference/write-concern/)，后者指定写入操作期间需要的响应数。 考虑到 Cosmos DB 在后台处理复制的方式，所有写入默认情况下都自动成为仲裁。 由客户端代码指定的任何写关注都会被系统忽略。 有关详细信息，请参阅[使用一致性级别最大化可用性和性能](consistency-levels.md)。

## <a name="sharding"></a>分片

Azure Cosmos DB 支持服务器端自动分片。 它自动管理分片的创建、放置和均衡。 Azure Cosmos DB 不支持手动分片命令，这意味着你不必调用 shardCollection、addShard、balancerStart、moveChunk 等命令。只需在创建容器或查询数据时指定分片键。

## <a name="next-steps"></a>后续步骤

- 了解如何将 [Studio 3T](mongodb-mongochef.md) 与 Azure Cosmos DB 的用于 MongoDB 的 API 配合使用。
- 了解如何将 [Robo 3T](mongodb-robomongo.md) 与 Azure Cosmos DB 的用于 MongoDB 的 API 配合使用。
- 使用 Azure Cosmos DB 的用于 MongoDB 的 API 浏览 MongoDB [示例](mongodb-samples.md)。

<sup>注意：本文介绍了可与 MongoDB 数据库实现网络协议兼容的 Azure Cosmos DB 功能。Microsoft 不会运行 MongoDB 数据库来提供此服务。Azure Cosmos DB 并不隶属于 MongoDB, inc.</sup>
