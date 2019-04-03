---
title: MongoDB 扩展命令来管理存储在 Azure Cosmos DB API for MongoDB 数据
description: 本文介绍如何使用 MongoDB 扩展命令来管理数据存储在 Azure Cosmos DB API for MongoDB。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: aef77f121f20d867c8ec5e764d8c9639c961713d
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2019
ms.locfileid: "58876882"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>使用 MongoDB 扩展命令来管理存储在 Azure Cosmos DB API for MongoDB 数据 

Azure Cosmos DB 由 Microsoft 提供，是全球分布的多模型数据库服务。 您可以进行通信使用 Azure Cosmos DB 的 API 为 MongoDB 使用任何开放源代码[MongoDB 客户端驱动程序](https://docs.mongodb.org/ecosystem/drivers)。 Azure Cosmos DB API for MongoDB，现有客户端驱动程序使用通过遵守[MongoDB 有线协议](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)。

使用 Azure Cosmos DB API for MongoDB，您可以享受权益 Cosmos DB 全局分发、 自动分片、 高可用性、 延迟保证、 自动、 静态加密，备份，如和很多多，而保留你的投资在将 MongoDB 应用。

## <a name="mongodb-protocol-support"></a>MongoDB 协议支持

默认情况下，Azure Cosmos DB 的 API 为 MongoDB 适用于 MongoDB 服务器版本 3.2，有关详细信息，请参阅[支持的功能和语法](mongodb-feature-support.md)。 功能或添加 MongoDB 3.4 版中的查询运算符是当前提供预览版 Azure Cosmos DB API for MongoDB 中。 以下扩展命令执行 CRUD 操作上的数据存储在 Azure Cosmos DB API for MongoDB 时支持 Azure Cosmos DB 的特定功能：

* [创建数据库](#create-database)
* [更新数据库](#update-database)
* [获取数据库](#get-database)
* [创建集合](#create-collection)
* [更新集合](#update-collection)
* [获取集合](#get-collection)

## <a id="create-database"></a> 创建数据库

创建数据库扩展命令创建新的 MongoDB 数据库。 从执行命令所针对的数据库上下文使用的数据库名称。 CreateDatabase 命令的格式如下所示：

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

下表描述该命令中的参数：

|**字段**|**Type** |**描述** |
|---------|---------|---------|
| customAction   |  字符串  |   名称的自定义命令，它必须是"CreateDatabase"。      |
| offerThroughput | int  | 在数据库设置的预配的吞吐量。 此参数是可选的。 |

### <a name="output"></a>输出

返回默认自定义命令响应。 请参阅[默认输出](#default-output)的自定义命令的输出中的参数。

### <a name="examples"></a>示例

**创建数据库**

若要创建名为"test"的数据库，请使用以下命令：

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**创建具有吞吐量的数据库**

若要创建的数据库名为"test"和预配的吞吐量的 1000 Ru，请使用以下命令：

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a id="update-database"></a> 更新数据库

更新数据库扩展命令将更新与指定的数据库关联的属性。 目前，只能更新"offerThroughput"属性。

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

下表描述该命令中的参数：

|**字段**|**Type** |**描述** |
|---------|---------|---------|
| customAction    |    字符串     |   自定义命令的名称。 必须是"UpdateDatabase"。      |
|  offerThroughput   |  int       |     你想要在数据库上设置的新预配的吞吐量。    |

### <a name="output"></a>输出

返回默认自定义命令响应。 请参阅[默认输出](#default-output)的自定义命令的输出中的参数。

### <a name="examples"></a>示例

**更新与数据库关联的预配的吞吐量**

若要使用名称"test"到 1200 Ru 更新数据库的预配的吞吐量，请使用以下命令：

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a id="get-database"></a> 获取数据库

获取数据库扩展命令返回的数据库对象。 从执行命令所针对的数据库上下文使用的数据库名称。

```
{
  customAction: "GetDatabase"
}
```

下表描述该命令中的参数：


|**字段**|**Type** |**描述** |
|---------|---------|---------|
|  customAction   |   字符串      |   自定义命令的名称。 必须是"GetDatabase"|
        
### <a name="output"></a>输出

如果命令成功，则响应包含具有以下字段的文档：

|**字段**|**Type** |**描述** |
|---------|---------|---------|
|  `ok`   |   `int`     |   响应的状态。 1 = = 成功。 0 = = 失败。      |
| `database`    |    `string`        |   数据库的名称。      |
|   `provisionedThroughput`  |    `int`      |    在数据库设置的预配的吞吐量。 这是一个可选响应参数。     |

如果该命令将失败，则返回默认自定义命令响应。 请参阅[默认输出](#default-output)的自定义命令的输出中的参数。

### <a name="examples"></a>示例

**获取数据库**

若要获取名为"test"的数据库的数据库对象，使用以下命令：

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a id="create-collection"></a> 创建集合

创建集合扩展命令创建新的 MongoDB 集合。 从执行命令所针对的数据库上下文使用的数据库名称。 CreateCollection 命令的格式如下所示：

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

下表描述该命令中的参数：

|**字段**|**Type** |**描述** |
|---------|---------|---------|
| customAction    | 字符串 | 自定义命令的名称。 必须是"CreateDatabase"     |
| collection      | 字符串 | 集合的名称                                   |
| offerThroughput | int    | 若要在数据库上设置的预配的吞吐量。 它是一个可选参数 |
| shardKey        | 字符串 | 要创建分片集合的分片键路径。 它是一个可选参数 |

### <a name="output"></a>输出

返回默认自定义命令响应。 请参阅[默认输出](#default-output)的自定义命令的输出中的参数。

### <a name="examples"></a>示例

**创建 unsharded 集合**

若要创建具有名称"testCollection"和预配的吞吐量的 1000 Ru unsharded 集合，请使用以下命令： 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**创建分片集合**

若要创建具有名称"testCollection"和预配的吞吐量的 1000 Ru 的分片集合，请使用以下命令：

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a id="update-collection"></a> 更新集合

更新集合扩展命令将更新与指定集合关联的属性。

```
{
  customAction: "UpdateCollection",
  collection: <Name of the collection that you want to update>,
  offerThroughput: <New throughput that you want to provision on the collection> 
}
```

下表描述该命令中的参数：

|**字段**|**Type** |**描述** |
|---------|---------|---------|
|  customAction   |   字符串      |   自定义命令的名称。 必须是"UpdateCollection"。      |
|  collection   |   字符串      |   集合的名称。       |
| offerThroughput   |int|   若要将集合设置的预配的吞吐量。|

## <a name="output"></a>输出

返回默认自定义命令响应。 请参阅[默认输出](#default-output)的自定义命令的输出中的参数。

### <a name="examples"></a>示例

**更新与集合相关联的预配的吞吐量**

若要更新具有名称"testCollection"的集合的预配的吞吐量为 1200 RUs，请使用以下命令：

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a id="get-collection"></a> 获取集合

获取集合自定义命令返回的集合对象。

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

下表描述该命令中的参数：


|**字段**|**Type** |**描述** |
|---------|---------|---------|
| customAction    |   字符串      |   自定义命令的名称。 必须是"GetCollection"。      |
| collection    |    字符串     |    集合的名称。     |

### <a name="output"></a>输出

如果命令成功，则响应包含具有以下字段的文档


|**字段**|**Type** |**描述** |
|---------|---------|---------|
|  `ok`   |    `int`     |   响应的状态。 1 = = 成功。 0 = = 失败。      |
| `database`    |    `string`     |   数据库的名称。      |
| `collection`    |    `string`     |    集合的名称。     |
|  `shardKeyDefinition`   |   `document`      |  用作分片键索引规范文档。 这是一个可选响应参数。       |
|  `provisionedThroughput`   |   `int`      |    若要将集合设置的预配的吞吐量。 这是一个可选响应参数。     |

如果该命令将失败，则返回默认自定义命令响应。 请参阅[默认输出](#default-output)的自定义命令的输出中的参数。

### <a name="examples"></a>示例

**获取的集合。**

若要获取名为"testCollection"的集合的集合对象，请使用以下命令：

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a id="default-output"></a> 默认输出的自定义命令

如果未指定，则自定义响应包含具有以下字段的文档：

|**字段**|**Type** |**描述** |
|---------|---------|---------|
|  `ok`   |    `int`     |   响应的状态。 1 = = 成功。 0 = = 失败。      |
| `code`    |   `int`      |   该命令失败时，才返回 （即确定 = = 0）。 包含 MongoDB 错误代码。 这是一个可选响应参数。      |
|  `errMsg`   |  `string`      |    该命令失败时，才返回 （即确定 = = 0）。 包含的用户友好错误消息。 这是一个可选响应参数。      |

## <a name="next-steps"></a>后续步骤

接下来可以继续学习以下的 Azure Cosmos DB 概念： 

* [Azure Cosmos DB 中的索引](../cosmos-db/index-policy.md)
* [使 Azure Cosmos DB 中自动利用生存时间的数据过期](../cosmos-db/time-to-live.md)