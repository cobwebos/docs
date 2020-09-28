---
title: 用于管理 Azure Cosmos DB 的 MongoDB API 中的数据的 MongoDB 扩展命令
description: 本文介绍如何使用 MongoDB 扩展命令管理 Azure Cosmos DB 的 API for MongoDB 中存储的数据。
author: jasonwhowell
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/28/2020
ms.author: jasonh
ms.custom: devx-track-js
ms.openlocfilehash: a40be5212fb1335482ec5011d24c8eaf5f3d9a00
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2020
ms.locfileid: "91409673"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>使用 MongoDB 扩展命令管理存储在 MongoDB Azure Cosmos DB 的 API 中的数据 

以下文档包含特定于 Azure Cosmos DB API for MongoDB 的自定义操作命令。 这些命令可用来创建和获取特定于 [Azure Cosmos DB 容量模型](databases-containers-items.md)的数据库资源。

通过使用 Azure Cosmos DB 的适用于 MongoDB 的 API，你可以享受多种优势 Cosmos DB 例如：全局分发、自动分片、高可用性、延迟保证、自动、静态加密和备份等，同时保留你在 MongoDB 应用中的投资。 可以通过使用任何开源 [MongoDB 客户端驱动程序](https://docs.mongodb.org/ecosystem/drivers)与 AZURE COSMOS DB 的 API 进行通信。 使用 Azure Cosmos DB 的 MongoDB API，可以通过遵守 [mongodb 线路协议](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)使用现有的客户端驱动程序。

## <a name="mongodb-protocol-support"></a>MongoDB 协议支持

Azure Cosmos DB 的 MongoDB API 与 MongoDB 服务器版本3.2 和3.6 兼容。 有关更多详细信息，请参阅[支持的功能和语法](mongodb-feature-support.md)。 

以下扩展命令提供了通过数据库请求创建和修改 Azure Cosmos DB 特定资源的功能：

* [创建数据库](#create-database)
* [更新数据库](#update-database)
* [获取数据库](#get-database)
* [创建集合](#create-collection)
* [更新集合](#update-collection)
* [获取集合](#get-collection)

## <a name="create-database"></a><a id="create-database"></a> 创建数据库

“创建数据库”扩展命令可创建新的 MongoDB 数据库。 可以从 `use database` 命令设置的数据库上下文使用数据库名称。 下表描述了该命令中的参数：

|**字段**|**类型** |**说明** |
|---------|---------|---------|
| `customAction`   |  `string`  |   自定义命令的名称，必须是“CreateDatabase”。      |
| `offerThroughput` | `int`  | 对数据库设置的预配吞吐量。 此参数是可选的。 |
| `autoScaleSettings` | `Object` | 对于[自动缩放模式](provision-throughput-autoscale.md)，此字段是必需的。 此对象包含与自动缩放容量模式关联的设置。 你可以设置 `maxThroughput` 值，它描述了集合将动态增加到的最大请求单位数量。 |

### <a name="output"></a>输出

如果此命令成功，它将返回以下响应：

```javascript
{ "ok" : 1 }
```

有关输出中的参数，请参阅自定义命令的[默认输出](#default-output)。

### <a name="examples"></a>示例

#### <a name="create-a-database"></a>创建数据库

若要创建一个使用所有默认值的名为 `"test"` 的数据库，请使用以下命令：

```javascript
use test
db.runCommand({customAction: "CreateDatabase"});
```

此命令将创建一个没有数据库级别吞吐量的数据库。 这意味着，此数据库中的集合需要指定你需要使用的吞吐量。

#### <a name="create-a-database-with-throughput"></a>创建具有吞吐量的数据库

若要创建名为 `"test"` 的数据库并将[数据库级别](set-throughput.md#set-throughput-on-a-database)预配吞吐量指定为 1000 RU，请使用以下命令：

```javascript
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

这将创建一个数据库并为其设置吞吐量。 此数据库中的所有集合将共享所设置的吞吐量，除非创建集合时使用了[特定的吞吐量级别](set-throughput.md#set-throughput-on-a-database-and-a-container)。

#### <a name="create-a-database-with-autoscale-throughput"></a>创建具有自动缩放吞吐量的数据库

若要创建名为 `"test"` 的数据库，并在[数据库级别](set-throughput.md#set-throughput-on-a-database)指定 20,000 RU/秒的自动缩放最大吞吐量，请使用以下命令：

```javascript
use test
db.runCommand({customAction: "CreateDatabase", autoScaleSettings: { maxThroughput: 20000 } });
```

## <a name="update-database"></a><a id="update-database"></a> 更新数据库

“更新数据库”扩展命令可更新与指定的数据库相关联的属性。 下表描述了该命令中的参数：

|**字段**|**类型** |**说明** |
|---------|---------|---------|
| `customAction`    |    `string`     |   自定义命令的名称。 必须是“UpdateDatabase”。      |
|  `offerThroughput`   |  `int`       |     当数据库使用[数据库级别吞吐量](set-throughput.md#set-throughput-on-a-database)时你要在数据库上设置的新预配吞吐量  |
| `autoScaleSettings` | `Object` | 对于[自动缩放模式](provision-throughput-autoscale.md)，此字段是必需的。 此对象包含与自动缩放容量模式关联的设置。 你可以设置 `maxThroughput` 值，它描述了数据库将动态增加到的最大请求单位数量。 |

此命令使用在会话上下文中指定的数据库。 这是你在 `use <database>` 命令中使用的数据库。 目前，无法使用此命令更改数据库名称。

### <a name="output"></a>输出

如果此命令成功，它将返回以下响应：

```javascript
{ "ok" : 1 }
```

有关输出中的参数，请参阅自定义命令的[默认输出](#default-output)。

### <a name="examples"></a>示例

#### <a name="update-the-provisioned-throughput-associated-with-a-database"></a>更新与数据库关联的预配吞吐量

若要将名为 `"test"` 的数据库的预配吞吐量更新为 1200 RU，请使用以下命令：

```javascript
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

#### <a name="update-the-autoscale-throughput-associated-with-a-database"></a>更新与数据库关联的自动缩放吞吐量

若要将名为 `"test"` 的数据库的预配吞吐量更新为 20,000 RU，或将其转换为[自动缩放吞吐量级别](provision-throughput-autoscale.md)，请使用以下命令：

```javascript
use test
db.runCommand({customAction: "UpdateDatabase", autoScaleSettings: { maxThroughput: 20000 } });
```


## <a name="get-database"></a><a id="get-database"></a> 获取数据库

“获取数据库”扩展命令返回数据库对象。 数据库名称取自该命令所针对的数据库上下文。

```javascript
{
  customAction: "GetDatabase"
}
```

下表描述了该命令中的参数：


|**字段**|**类型** |**说明** |
|---------|---------|---------|
|  `customAction`   |   `string`      |   自定义命令的名称。 必须是“GetDatabase”|
        
### <a name="output"></a>输出

如果该命令成功，则响应包含带有以下字段的文档：

|**字段**|**类型** |**说明** |
|---------|---------|---------|
|  `ok`   |   `int`     |   响应的状态。 1 == 成功。 0 == 失败。      |
| `database`    |    `string`        |   数据库的名称。      |
|   `provisionedThroughput`  |    `int`      |    当数据库使用[手动数据库级别吞吐量](set-throughput.md#set-throughput-on-a-database)时在数据库上设置的预配吞吐量     |
| `autoScaleSettings` | `Object` | 如果使用[自动缩放模式](provision-throughput-autoscale.md)，则此对象将包含与数据库关联的容量参数。 `maxThroughput` 值描述了数据库将动态增加到的最大请求单位数量。 |

如果该命令失败，则返回默认的自定义命令响应。 有关输出中的参数，请参阅自定义命令的[默认输出](#default-output)。

### <a name="examples"></a>示例

#### <a name="get-the-database"></a>获取数据库

若要获取名为 `"test"` 的数据库的数据库对象，请使用以下命令：

```javascript
use test
db.runCommand({customAction: "GetDatabase"});
```

如果数据库没有关联的吞吐量，输出将是：

```javascript
{ "database" : "test", "ok" : 1 }
```

如果数据库具有关联的[数据库级别手动吞吐量](set-throughput.md#set-throughput-on-a-database)，则输出将显示 `provisionedThroughput` 值：

```javascript
{ "database" : "test", "provisionedThroughput" : 20000, "ok" : 1 }
```

如果数据库具有关联的[数据库级别自动缩放吞吐量](provision-throughput-autoscale.md)，则输出将显示 `provisionedThroughput`，其中描述了数据库的最小 RU/秒，以及包含 `maxThroughput`（描述了数据库的最大 RU/秒）的 `autoScaleSettings` 对象。

```javascript
{
        "database" : "test",
        "provisionedThroughput" : 2000,
        "autoScaleSettings" : {
                "maxThroughput" : 20000
        },
        "ok" : 1
}
```

## <a name="create-collection"></a><a id="create-collection"></a> 创建集合

“创建集合”扩展命令可创建新的 MongoDB 集合。 数据库名称将在 `use database` 命令设置的数据库上下文中使用。 CreateCollection 命令的格式如下：

```javascript
{
  customAction: "CreateCollection",
  collection: "<Collection Name>",
  shardKey: "<Shard key path>",
  offerThroughput: (int), // Amount of throughput allocated to a specific collection

}
```

下表描述了该命令中的参数：

| **字段** | **类型** | **必需** | **说明** |
|---------|---------|---------|---------|
| `customAction` | `string` | 必须 | 自定义命令的名称。 必须是“CreateCollection”。|
| `collection` | `string` | 必须 | 集合的名称。 不允许使用特殊字符或空格。|
| `offerThroughput` | `int` | 可选 | 要在数据库上设置的预配吞吐量。 如果未提供此参数，它将默认为最小值（400 RU/秒）。 *若要指定超过 10,000 RU/秒的吞吐量，需要使用 `shardKey` 参数。|
| `shardKey` | `string` | 对于具有较大吞吐量的集合，此字段是必需的 | 分片集合的分片键的路径。 如果在 `offerThroughput` 中设置的 RU/秒超过 10,000，则需要此参数。  如果指定了此参数，则插入的所有文档都需要此键和值。 |
| `autoScaleSettings` | `Object` | 对于[自动缩放模式](provision-throughput-autoscale.md)，此字段是必需的 | 此对象包含与自动缩放容量模式关联的设置。 你可以设置 `maxThroughput` 值，它描述了集合将动态增加到的最大请求单位数量。 |

### <a name="output"></a>输出

返回默认的自定义命令响应。 有关输出中的参数，请参阅自定义命令的[默认输出](#default-output)。

### <a name="examples"></a>示例

#### <a name="create-a-collection-with-the-minimum-configuration"></a>使用最小配置创建集合

若要创建使用名称 `"testCollection"` 和默认值的新集合，请使用以下命令： 

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection"});
```

这将生成一个新的固定的、未分片的、吞吐量为 400RU/秒的集合，并自动创建基于 `_id` 字段的索引。 通过 `insert()` 函数创建新集合时，也将应用此类型的配置。 例如： 

```javascript
use test
db.newCollection.insert({});
```

#### <a name="create-a-unsharded-collection"></a>创建未分片集合

若要创建名为 `"testCollection"` 且预配吞吐量为 1000 RU 的未分片集合，请使用以下命令： 

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

你可以创建 `offerThroughput` 最高为 10,000 RU/秒的集合，不需要指定分片键。 对于吞吐量更大的集合，请参阅下一部分。

#### <a name="create-a-sharded-collection"></a>创建分片集合

若要创建名为 `"testCollection"`、预配吞吐量为 11,000 RU 且具有 `shardkey` 属性“a.b”的分片集合，请使用以下命令：

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 11000, shardKey: "a.b" });
```

此命令现在需要 `shardKey` 参数，因为 `offerThroughput` 中指定的 RU/秒超过 10,000。

#### <a name="create-an-unsharded-autoscale-collection"></a>创建未分片自动缩放集合

若要创建名为 `'testCollection'` 且使用设置为 4,000 RU/秒的[自动缩放吞吐量容量](provision-throughput-autoscale.md)的未分片集合，请使用以下命令：

```javascript
use test
db.runCommand({ 
    customAction: "CreateCollection", collection: "testCollection", 
    autoScaleSettings:{
      maxThroughput: 4000
    } 
});
```

对于 `autoScaleSettings.maxThroughput` 值，你可以指定从 4,000 RU/秒到 10,000 RU/秒的范围，无需使用分片键。 若要实现更高的自动缩放吞吐量，需要指定 `shardKey` 参数。

#### <a name="create-a-sharded-autoscale-collection"></a>创建分片自动缩放集合

若要创建名为 `'testCollection'`、分片键名为 `'a.b'` 且使用设置为 20,000 RU/秒的[自动缩放吞吐量容量](provision-throughput-autoscale.md)的分片集合，请使用以下命令：

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", shardKey: "a.b", autoScaleSettings: { maxThroughput: 20000 }});
```

## <a name="update-collection"></a><a id="update-collection"></a> 更新集合

“更新集合”扩展命令可更新与指定的集合相关联的属性。

```javascript
{
  customAction: "UpdateCollection",
  collection: "<Name of the collection that you want to update>",
  offerThroughput: (int) // New throughput that will be set to the collection
}
```

下表描述了该命令中的参数：

|**字段**|**类型** |**说明** |
|---------|---------|---------|
|  `customAction`   |   `string`      |   自定义命令的名称。 必须是“UpdateCollection”。      |
|  `collection`   |   `string`      |   集合的名称。       |
| `offerThroughput` | `int` |   要对集合设置的预配吞吐量。|
| `autoScaleSettings` | `Object` | 对于[自动缩放模式](provision-throughput-autoscale.md)，此字段是必需的。 此对象包含与自动缩放容量模式关联的设置。 `maxThroughput` 值描述了集合将动态增加到的最大请求单位数量。 |

## <a name="output"></a>输出

返回默认的自定义命令响应。 有关输出中的参数，请参阅自定义命令的[默认输出](#default-output)。

### <a name="examples"></a>示例

#### <a name="update-the-provisioned-throughput-associated-with-a-collection"></a>更新与集合关联的预配吞吐量

若要将名为 `"testCollection"` 的集合的预配吞吐量更新为 1200 RU，请使用以下命令：

```javascript
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a> 获取集合

“获取集合”自定义命令返回集合对象。

```javascript
{
  customAction: "GetCollection",
  collection: "<Name of the collection>"
}
```

下表描述了该命令中的参数：


|**字段**|**类型** |**说明** |
|---------|---------|---------|
| `customAction`    |   `string`      |   自定义命令的名称。 必须是“GetCollection”。      |
| `collection`    |    `string`     |    集合的名称。     |

### <a name="output"></a>输出

如果该命令成功，则响应包含带有以下字段的文档


|**字段**|**类型** |**说明** |
|---------|---------|---------|
|  `ok`   |    `int`     |   响应的状态。 1 == 成功。 0 == 失败。      |
| `database`    |    `string`     |   数据库的名称。      |
| `collection`    |    `string`     |    集合的名称。     |
|  `shardKeyDefinition`   |   `document`      |  用作分片键的索引规范文档。 这是一个可选的响应参数。       |
|  `provisionedThroughput`   |   `int`      |    要对集合设置的预配吞吐量。 这是一个可选的响应参数。     |
| `autoScaleSettings` | `Object` | 如果使用[自动缩放模式](provision-throughput-autoscale.md)，则此对象将包含与数据库关联的容量参数。 `maxThroughput` 值描述了集合将动态增加到的最大请求单位数量。 |

如果该命令失败，则返回默认的自定义命令响应。 有关输出中的参数，请参阅自定义命令的[默认输出](#default-output)。

### <a name="examples"></a>示例

#### <a name="get-the-collection"></a>获取集合

若要获取名为 `"testCollection"` 的集合的集合对象，请使用以下命令：

```javascript
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

如果集合具有关联的吞吐量容量，则它将包括 `provisionedThroughput` 值，输出将是：

```javascript
{
        "database" : "test",
        "collection" : "testCollection",
        "provisionedThroughput" : 400,
        "ok" : 1
}
```

如果集合具有关联的自动缩放吞吐量，则它将包括带有 `maxThroughput` 参数的 `autoScaleSettings` 对象，该参数定义集合将动态增加到的最大吞吐量。 此外，它还将包括 `provisionedThroughput` 值，该值定义当集合中没有请求时此集合将减少到的最小吞吐量： 

```javascript
{
        "database" : "test",
        "collection" : "testCollection",
        "provisionedThroughput" : 1000,
        "autoScaleSettings" : {
            "maxThroughput" : 10000
        },
        "ok" : 1
}
```

如果集合共享[数据库级别吞吐量](set-throughput.md#set-throughput-on-a-database)（不管是自动缩放模式还是手动模式），则输出将是：

```javascript
{ "database" : "test", "collection" : "testCollection", "ok" : 1 }
```

```javascript
{
        "database" : "test",
        "provisionedThroughput" : 2000,
        "autoScaleSettings" : {
            "maxThroughput" : 20000
        },
        "ok" : 1
}
```


## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a> 自定义命令的默认输出

如果未指定，则自定义响应包含带有以下字段的文档：

|**字段**|**类型** |**说明** |
|---------|---------|---------|
|  `ok`   |    `int`     |   响应的状态。 1 == 成功。 0 == 失败。      |
| `code`    |   `int`      |   仅当命令失败时才返回（即，正常 == 0）。 包含 MongoDB 错误代码。 这是一个可选的响应参数。      |
|  `errMsg`   |  `string`      |    仅当命令失败时才返回（即，正常 == 0）。 包含用户友好的错误消息。 这是一个可选的响应参数。      |

例如：

```javascript
{ "ok" : 1 }
```

## <a name="next-steps"></a>后续步骤

接下来，可以继续学习以下 Azure Cosmos DB 概念： 

* [Azure Cosmos DB 中的索引](../cosmos-db/index-policy.md)
* [利用生存时间使 Azure Cosmos DB 中的数据自动过期](../cosmos-db/time-to-live.md)
