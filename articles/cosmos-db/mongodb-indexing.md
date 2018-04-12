---
title: Azure Cosmos DB MongoDB API 中的索引编制 | Microsoft Docs
description: 概述 Azure Cosmos DB MongoDB API 中的索引编制功能。
services: cosmos-db
documentationcenter: ''
author: orestis-ms
manager: kfile
editor: ''
ms.assetid: daacbabf-1bb5-497f-92db-079910703047
ms.service: cosmos-db
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: quickstart
ms.date: 03/01/2018
ms.author: orkostak
ms.openlocfilehash: 6cb267096e04d4957b166e539a4fc58aa25e04cb
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="indexing-in-the-azure-cosmos-db-mongodb-api"></a>Azure Cosmos DB: MongoDB API 中的索引编制

Azure Cosmos DB MongoDB API 可利用 Azure Cosmos DB 的自动索引管理功能。 因此，用户可以访问 Azure Cosmos DB 的默认索引编制策略。 因此，如果用户没有定义索引，或者没有删除索引，则默认情况下，所有字段都会在插入到集合中时自动编制索引。 大多数情况下，建议使用在帐户上设置的默认索引编制策略。

## <a name="dropping-the-default-indexes"></a>删除默认索引

可以使用以下命令删除集合 ```coll``` 的默认索引：

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

## <a name="creating-compound-indexes"></a>创建复合索引

复合索引包含对文档多个字段的引用。 逻辑上，复合索引相当于为每个字段创建多个单独的索引。 若要利用 Cosmos DB 索引技术具有的优势，建议创建多个单独的索引，而不是一个（非唯一）复合索引。

## <a name="creating-unique-indexes"></a>创建唯一索引

对于编制了索引的字段，[唯一索引](unique-keys.md)用于确保没有两个或两个以上的文档包含同一值。 
>[!important] 
> 目前，创建唯一索引的前提是集合为空（不含文档）。 

以下命令在字段“student_id”上创建唯一索引：

```JavaScript
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

```JavaScript
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
>

## <a name="migrating-collections-with-indexes"></a>迁移带索引的集合

目前，创建唯一索引的前提是集合不含文档。 常用 MongoDB 迁移工具会尝试在导入数据后创建唯一索引。 若要避免此问题，建议用户手动创建相应的集合和唯一索引，而不是让迁移工具来创建（对于 ```mongorestore```，可以通过在命令行中使用 --noIndexRestore 标志来实现此行为）。

## <a name="next-steps"></a>后续步骤
* [Azure Cosmos DB 如何编制数据索引？](../cosmos-db/indexing-policies.md)
* [利用生存时间使 Azure Cosmos DB 集合中的数据自动过期](../cosmos-db/time-to-live.md)
