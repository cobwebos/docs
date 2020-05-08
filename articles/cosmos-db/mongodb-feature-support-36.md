---
title: Azure Cosmos DB 的 API for MongoDB（3.6 版本）支持的功能和语法
description: 了解 Azure Cosmos DB 的 API for MongoDB（3.6 版本）支持的功能和语法。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 01/15/2020
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 5df21b2c1926803a65eca911c66b059f36ee18aa
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "81393620"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-36-version-supported-features-and-syntax"></a>Azure Cosmos DB 的 API for MongoDB（3.6 版本）：支持的功能和语法

Azure Cosmos DB 由 Microsoft 提供，是全球分布的多模型数据库服务。 可通过任何开源 MongoDB 客户端[驱动程序](https://docs.mongodb.org/ecosystem/drivers)与 Azure Cosmos DB's API for MongoDB 进行通信。 可以按照 MongoDB [有线协议](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)规定，通过 Azure Cosmos DB 的 MongoDB API 来使用现有客户端驱动程序。

通过使用 Azure Cosmos DB 的 MongoDB API，可以像以往一样从 MongoDB 中受益，并且可使用 Cosmos DB 提供的所有企业功能：[全局分发](distribute-data-globally.md)、[自动分片](partition-data.md)、可用性和延迟保证、静态加密和备份等。

## <a name="protocol-support"></a>协议支持

默认情况下，对于新帐户，Azure Cosmos DB 的 API for MongoDB 与 MongoDB 服务器版本 **3.6** 兼容。 支持的运算符以及限制或例外已列在下面。 任何理解这些协议的客户端驱动程序应该都能够连接到 Azure Cosmos DB 的 MongoDB API。 请注意，使用 Azure Cosmos DB's API for MongoDB 帐户时，3.6 版的帐户的终结点格式为 `*.mongo.cosmos.azure.com`，而 3.2 版的帐户的终结点格式为 `*.documents.azure.com`。

## <a name="query-language-support"></a>查询语言支持

Azure Cosmos DB 的 MongoDB API 全面支持 MongoDB 查询语言构造。 可以在下面查找当前支持的操作、运算符、阶段、命令和选项的详细列表。

## <a name="database-commands"></a>数据库命令

Azure Cosmos DB 的 MongoDB API 支持以下数据库命令：

### <a name="query-and-write-operation-commands"></a>查询和写入操作命令

|Command  |支持 |
|---------|---------|
|delete | 是 |
|find | 是     |
|findAndModify | 是  |
|getLastError|   是 |
|getMore  |  是  |
|getPrevError | 否  |
|insert  |   是  |
|parallelCollectionScan  | 是   |
|resetError |    否  |
|update  |   是  |
|[更改流](mongodb-change-streams.md)  |  是  |
|GridFS |   是  |

### <a name="authentication-commands"></a>身份验证命令

|Command  |支持 |
|---------|---------|
|authenticate    |   是      |
|logout    |      是   |
|getnonce   |    是     |


### <a name="administration-commands"></a>管理命令

|Command  |支持 |
|---------|---------|
|Capped Collections   |   否      |
|cloneCollectionAsCapped     |   否      |
|collMod     |   否      |
|collMod: expireAfterSeconds   |   否      |
|convertToCapped   |  否       |
|copydb     |  否       |
|create   |    是     |
|createIndexes     |  是       |
|currentOp     |  是       |
|drop     |   是      |
|dropDatabase     |  是       |
|dropIndexes     |   是      |
|filemd5    |   是      |
|killCursors    |  是       |
|killOp     |   否      |
|listCollections     |  是       |
|listDatabases     |  是       |
|listIndexes     |  是       |
|reIndex     |    是     |
|renameCollection     |    否     |
|connectionStatus    |     否    |

### <a name="diagnostics-commands"></a>诊断命令

|Command  |支持 |
|---------|---------|
|buildInfo         |   是      |
|collStats    |  是       |
|connPoolStats     |  否       |
|connectionStatus     |  否       |
|dataSize     |   否      |
|dbHash    |    否     |
|dbStats     |   是      |
|explain     |   是      |
|explain: executionStats     |   是      |
|features     |    否     |
|hostInfo     |   否      |
|listDatabases         |   是      |
|listCommands     |  否       |
|profiler     |  否       |
|serverStatus     |  否       |
|top     |    否     |
|whatsmyuri     |   是      |

<a name="aggregation-pipeline"/>

## <a name="aggregation-pipelinea"></a>聚合管道</a>

### <a name="aggregation-commands"></a>聚合命令

|Command  |支持 |
|---------|---------|
|aggregate |   是  |
|count     |   是  |
|distinct  | 是 |
|mapReduce | 否 |

### <a name="aggregation-stages"></a>聚合阶段

|Command  |支持 |
|---------|---------|
|$collStats    |否|
|$project    |是|
|$match    |是|
|$redact|    是|
|$limit    |是|
|$skip    |是|
|$unwind|    是|
|$group    |    是|
|$sample|        是|
|$sort    |是|
|$geoNear|    否|
|$lookup    |    是|
|$out        |是|
|$indexStats|        否|
|$facet    |否|
|$bucket|    否|
|$bucketAuto|    否|
|$sortByCount|    是|
|$addFields    |是|
|$replaceRoot|    是|
|$count    |是|
|$currentOp|    否|
|$listLocalSessions    |否|
|$listSessions    |否|
|$graphLookup    |否|

### <a name="boolean-expressions"></a>布尔表达式

|Command  |支持 |
|---------|---------|
|$and| 是|
|$or|是|
|$not|是|

### <a name="set-expressions"></a>集表达式

|Command  |支持 |
|---------|---------|
| $setEquals | 是|
|$setIntersection|是|
| $setUnion|是|
| $setDifference|是|
| $setIsSubset|是|
| $anyElementTrue|是|
| $allElementsTrue|是|

### <a name="comparison-expressions"></a>比较表达式

|Command  |支持 |
|---------|---------|
|$cmp     |  是       |
|$eq|    是| 
|$gt |    是| 
|$gte|    是| 
|$lt    |是|
|$lte|    是| 
|$ne    |    是| 
|$in    |    是| 
|$nin    |    是| 

### <a name="arithmetic-expressions"></a>算术表达式

|Command  |支持 |
|---------|---------|
|$abs |  是       |
| $add |  是       |
| $ceil |  是       |
| $divide |  是       |
| $exp |  是       |
| $floor |  是       |
| $ln |  是       |
| $log |  是       |
| $log10 |  是       |
| $mod |  是       |
| $multiply |  是       |
| $pow |  是       |
| $sqrt |  是       |
| $subtract |  是       |
| $trunc |  是       |

### <a name="string-expressions"></a>字符串表达式

|Command  |支持 |
|---------|---------|
|$concat |  是       |
| $indexOfBytes|  是       |
| $indexOfCP|  是       |
| $split|  是       |
| $strLenBytes|  是       |
| $strLenCP|  是       |
| $strcasecmp|  是       |
| $substr|  是       |
| $substrBytes|  是       |
| $substrCP|  是       |
| $toLower|  是       |
| $toUpper|  是       |

### <a name="text-search-operator"></a>文本搜索运算符

|Command  |支持 |
|---------|---------|
| $meta | 否|

### <a name="array-expressions"></a>数组表达式

|Command  |支持 |
|---------|---------|
|$arrayElemAt    |    是|
|$arrayToObject|    是|
|$concatArrays    |    是|
|$filter    |    是|
|$indexOfArray    |是|
|$isArray    |    是|
|$objectToArray    |是|
|$range    |是|
|$reverseArray    |    是|
|$reduce|    是|
|$size    |    是|
|$slice    |    是|
|$zip    |    是|
|$in    |    是|

### <a name="variable-operators"></a>变量运算符

|Command  |支持 |
|---------|---------|
|$map    |否|
|$let    |是|

### <a name="system-variables"></a>系统变量

|Command  |支持 |
|---------|---------|
|$$CURRENT|    是|
|$$DESCEND|        是|
|$$KEEP        |是|
|$$PRUNE    |    是|
|$$REMOVE    |是|
|$$ROOT        |是|

### <a name="literal-operator"></a>文本运算符

|Command  |支持 |
|---------|---------|
|$literal    |是|

### <a name="date-expressions"></a>日期表达式

|Command  |支持 |
|---------|---------|
|$dayOfYear    |是    |
|$dayOfMonth|    是    |
|$dayOfWeek    |是    |
|$year    |是    |
|$month    |是|    
|$week    |是    |
|$hour    |是    |
|$minute|    是|    
|$second    |是    |
|$millisecond|    是|    
|$dateToString    |是    |
|$isoDayOfWeek    |是    |
|$isoWeek    |是    |
|$dateFromParts|    否|    
|$dateToParts    |否    |
|$dateFromString|    否|
|$isoWeekYear    |是    |

### <a name="conditional-expressions"></a>条件表达式

|Command  |支持 |
|---------|---------|
| $cond| 是|
| $ifNull| 是|
| $switch |是|

### <a name="data-type-operator"></a>数据类型运算符

|Command  |支持 |
|---------|---------|
| $type| 是|

### <a name="accumulator-expressions"></a>累加器表达式

|Command  |支持 |
|---------|---------|
|$sum    |是    |
|$avg    |是    |
|$first|    是|
|$last    |是    |
|$max    |是    |
|$min    |是    |
|$push|    是|
|$addToSet|    是|
|$stdDevPop|    否    |
|$stdDevSamp|    否|

### <a name="merge-operator"></a>合并运算符

|Command  |支持 |
|---------|---------|
| $mergeObjects | 是|

## <a name="data-types"></a>数据类型

|Command  |支持 |
|---------|---------|
|Double    |是    |
|字符串    |是    |
|对象    |是    |
|Array    |是    |
|Binary Data    |是|    
|ObjectId    |是    |
|Boolean    |是    |
|Date    |是    |
|Null    |是    |
|32 位整数 (int)    |是    |
|时间戳    |是    |
|64 位整数 (long)    |是    |
|MinKey    |是    |
|MaxKey    |是    |
|Decimal128    |是|    
|Regular Expression    |是|
|JavaScript    |是|
|JavaScript（带范围）|    是    |
|Undefined    |是    |

## <a name="indexes-and-index-properties"></a>索引和索引属性

### <a name="indexes"></a>索引

|Command  |支持 |
|---------|---------|
|单字段索引    |是    |
|复合索引    |是    |
|多键索引    |是    |
|文本索引    |否|
|2dsphere    |是    |
|2d 索引    |否    |
|哈希索引    | 是|

### <a name="index-properties"></a>索引属性

|Command  |支持 |
|---------|---------|
|TTL|    是    |
|唯一    |是|
|部分|    否|
|不区分大小写    |否|
|稀疏    |否 |
|背景|    是 |

## <a name="operators"></a>运算符

### <a name="logical-operators"></a>逻辑运算符

|Command  |支持 |
|---------|---------|
|$or    |    是|
|$and    |    是|
|$not    |    是|
|$nor    |    是| 

### <a name="element-operators"></a>元素运算符

|Command  |支持 |
|---------|---------|
|$exists|    是|
|$type    |    是|

### <a name="evaluation-query-operators"></a>评估查询运算符

|Command  |支持 |
|---------|---------|
|$expr    |    否|
|$jsonSchema    |    否|
|$mod    |    是|
|$regex |    是|
|$text    | 否（不支持。 请改用 $regex。）| 
|$where    |否| 

在 $regex 查询中，左定位表达式允许索引搜索。 但是，使用“i”修饰符（不区分大小写）和“m”修饰符（多行）会导致在所有表达式中进行回收集合扫描。

当需要包含“$”或“|”时，最好创建两个（或更多）正则表达式查询。 例如，给定以下原始查询：```find({x:{$regex: /^abc$/})```，必须按如下所示进行修改：

```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})``` 列中的一个值匹配。

第一部分将使用索引将搜索限制为以 ^ abc 开头的文档，第二部分将匹配确切的条目。 竖条运算符“|”充当“or”函数 - 查询 ```find({x:{$regex: /^abc|^def/})``` 匹配字段“x”的值以“abc”或“def”开头的文档。 要使用索引，建议将查询分解为两个由 $or 运算符连接的不同查询：```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```。

### <a name="array-operators"></a>数组运算符

|Command  |支持 | 
|---------|---------|
| $all | 是| 
| $elemMatch | 是| 
| $size | 是 | 

### <a name="comment-operator"></a>注释运算符

|Command  |支持 | 
|---------|---------|
$comment |是| 

### <a name="projection-operators"></a>投影运算符

|Command  |支持 |
|---------|---------|
|$elemMatch    |是|
|$meta|    否|
|$slice    | 是|

### <a name="update-operators"></a>更新运算符

#### <a name="field-update-operators"></a>字段更新运算符

|Command  |支持 |
|---------|---------|
|$inc    |    是|
|$mul    |    是|
|$rename    |    是|
|$setOnInsert|    是|
|$set    |是|
|$unset| 是|
|$min    |是|
|$max    |是|
|$currentDate    | 是|

#### <a name="array-update-operators"></a>数组更新运算符

|Command  |支持 |
|---------|---------|
|$    |是|
|$[]|    是|
|$[<identifier>]|    是|
|$addToSet    |是|
|$pop    |是|
|$pullAll|    是|
|$pull    |是|
|$push    |是|
|$pushAll| 是|


#### <a name="update-modifiers"></a>更新修饰符

|Command  |支持 |
|---------|---------|
|$each    |    是|
|$slice    |是|
|$sort    |是|
|$position    |是|

#### <a name="bitwise-update-operator"></a>位更新运算符

|Command  |支持 |
|---------|---------|
| $bit    |    是|    
|$bitsAllSet    |    否|
|$bitsAnySet    |    否|
|$bitsAllClear    |否|
|$bitsAnyClear    |否|

### <a name="geospatial-operators"></a>地理空间运算符

操作员 | 支持| 
--- | --- |
$geoWithin | 是 |
$geoIntersects | 是 | 
$near |  是 |
$nearSphere |  是 |
$geometry |  是 |
$minDistance | 是 |
$maxDistance | 是 |
$center | 是 |
$centerSphere | 是 |
$box | 是 |
$polygon |  是 |

## <a name="cursor-methods"></a>游标方法

|Command  |支持 |
|---------|---------|
|cursor.batchSize()    |    是|
|cursor.close()    |是|
|cursor.isClosed()|        是|
|cursor.collation()|    否|
|cursor.comment()    |是|
|cursor.count()    |是|
|cursor.explain()|    否|
|cursor.forEach()    |是|
|cursor.hasNext()    |是|
|cursor.hint()    |是|
|cursor.isExhausted()|    是|
|cursor.itcount()    |是|
|cursor.limit()    |是|
|cursor.map()    |是|
|cursor.maxScan()    |是|
|cursor.maxTimeMS()|    是|
|cursor.max()    |是|
|cursor.min()    |是|
|cursor.next()|    是|
|cursor.noCursorTimeout()    |否|
|cursor.objsLeftInBatch()    |是|
|cursor.pretty()|    是|
|cursor.readConcern()|    是|
|cursor.readPref()        |是|
|cursor.returnKey()    |否|
|cursor.showRecordId()|    否|
|cursor.size()    |是|
|cursor.skip()    |是|
|cursor.sort()    |    是|
|cursor.tailable()|    否|
|cursor.toArray()    |是|

## <a name="sort-operations"></a>排序运算符

使用 `findOneAndUpdate` 操作时，支持基于单个字段的排序操作，但不支持基于多个字段的排序操作。

## <a name="unique-indexes"></a>唯一索引

唯一索引确保特定字段在一个集合的所有文档中都不会有重复值，类似于默认“_id”键上保持唯一性的方式。 可以在 Cosmos DB 中使用 createIndex 命令（包括“唯一”约束）创建自定义索引。

## <a name="time-to-live-ttl"></a>生存时间 (TTL)

Cosmos DB 支持基于文档时间戳的生存时间 (TTL)。 转到 [Azure 门户](https://portal.azure.com)便可以为集合启用 TTL。

## <a name="user-and-role-management"></a>用户和角色管理

Cosmos DB 尚不支持用户和角色。 不过 Cosmos DB 支持基于角色的访问控制 (RBAC) 以及读写和只读密码/密钥，可通过 [Azure 门户](https://portal.azure.com)（连接字符串页面）获取这些内容。

## <a name="replication"></a>复制

Cosmos DB 支持在最低层进行自动本机复制。 此逻辑经过扩展，还可实现低延迟和全局复制。 它不支持手动复制命令。

## <a name="write-concern"></a>写关注

某些应用程序依赖[写关注](https://docs.mongodb.com/manual/reference/write-concern/)，后者指定写入操作期间需要的响应数。 考虑到 Cosmos DB 在后台处理复制的方式，所有写入默认情况下都自动成为仲裁。 由客户端代码指定的任何写关注都会被系统忽略。 有关详细信息，请参阅[使用一致性级别最大化可用性和性能](consistency-levels.md)。

## <a name="sharding"></a>分片

Azure Cosmos DB 支持服务器端自动分片。 它自动管理分片的创建、放置和均衡。 Azure Cosmos DB 不支持手动分片命令，这意味着你不必调用 addShard、balancerStart、moveChunk 等命令。只需在创建容器或查询数据时指定分片键。

## <a name="sessions"></a>会话

Azure Cosmos DB 尚不支持服务器端会话命令。

## <a name="next-steps"></a>后续步骤

- 有关详细信息，请查看 [Mongo 3.6 版本功能](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
- 了解如何将 [Studio 3T](mongodb-mongochef.md) 与 Azure Cosmos DB 的用于 MongoDB 的 API 配合使用。
- 了解如何将 [Robo 3T](mongodb-robomongo.md) 与 Azure Cosmos DB 的用于 MongoDB 的 API 配合使用。
- 使用 Azure Cosmos DB 的用于 MongoDB 的 API 浏览 MongoDB [示例](mongodb-samples.md)。

<sup>注意：本文介绍了可与 MongoDB 数据库实现网络协议兼容的 Azure Cosmos DB 功能。Microsoft 不会运行 MongoDB 数据库来提供此服务。Azure Cosmos DB 并不隶属于 MongoDB, inc.</sup>
