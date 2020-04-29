---
title: 用于管理 Azure Cosmos DB 的 MongoDB API 中的数据的 MongoDB 扩展命令
description: 本文介绍如何使用 MongoDB 扩展命令管理 Azure Cosmos DB 的 API for MongoDB 中存储的数据。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: f99c4d096bcbe1fbdc42cac80a491d6017266cb2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80583585"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>使用 MongoDB 扩展命令管理存储在 MongoDB Azure Cosmos DB 的 API 中的数据 

Azure Cosmos DB 由 Microsoft 提供，是全球分布的多模型数据库服务。 可以通过使用任何开源[MongoDB 客户端驱动程序](https://docs.mongodb.org/ecosystem/drivers)与 AZURE COSMOS DB 的 API 进行通信。 使用 Azure Cosmos DB 的 MongoDB API，可以通过遵守[mongodb 线路协议](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)使用现有的客户端驱动程序。

通过使用 Azure Cosmos DB 的适用于 MongoDB 的 API，你可以享受多种优势 Cosmos DB 例如：全局分发、自动分片、高可用性、延迟保证、自动、静态加密和备份等，同时保留你在 MongoDB 应用中的投资。

## <a name="mongodb-protocol-support"></a>MongoDB 协议支持

默认情况下，Azure Cosmos DB 的 MongoDB API 与 MongoDB 服务器版本3.2 兼容，有关更多详细信息，请参阅[支持的功能和语法](mongodb-feature-support.md)。 MongoDB 版本3.4 中添加的功能或查询运算符当前在 Azure Cosmos DB 的适用于 MongoDB 的 API 中作为预览提供。 以下扩展命令在对存储在 MongoDB Azure Cosmos DB API 中的数据执行 CRUD 操作时支持特定 Azure Cosmos DB 功能：

* [创建数据库](#create-database)
* [更新数据库](#update-database)
* [获取数据库](#get-database)
* [创建集合](#create-collection)
* [更新集合](#update-collection)
* [获取集合](#get-collection)

## <a name="create-database"></a><a id="create-database"></a>创建数据库

“创建数据库”扩展命令可创建新的 MongoDB 数据库。 数据库名称取自该命令所针对的数据库上下文。 CreateDatabase 命令的格式如下：

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

下表描述了该命令中的参数：

|**字段**|类型  |**说明** |
|---------|---------|---------|
| customAction   |  字符串  |   自定义命令的名称，必须是“CreateDatabase”。      |
| offerThroughput | int  | 对数据库设置的预配吞吐量。 此参数是可选的。 |

### <a name="output"></a>输出

返回默认的自定义命令响应。 有关输出中的参数，请参阅自定义命令的[默认输出](#default-output)。

### <a name="examples"></a>示例

**创建数据库**

若要创建名为“test”的数据库，请使用以下命令：

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**创建具有吞吐量的数据库**

若要创建名为“test”、预配吞吐量为 1000 RU 的数据库，请使用以下命令：

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a name="update-database"></a><a id="update-database"></a>更新数据库

“更新数据库”扩展命令可更新与指定的数据库相关联的属性。 目前只能更新“offerThroughput”属性。

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

下表描述了该命令中的参数：

|**字段**|类型  |**说明** |
|---------|---------|---------|
| customAction    |    字符串     |   自定义命令的名称。 必须是“UpdateDatabase”。      |
|  offerThroughput   |  int       |     要对数据库设置的新预配吞吐量。    |

### <a name="output"></a>输出

返回默认的自定义命令响应。 有关输出中的参数，请参阅自定义命令的[默认输出](#default-output)。

### <a name="examples"></a>示例

**更新与数据库关联的预配吞吐量**

若要将名为“test”的数据库的预配吞吐量更新为 1200 RU，请使用以下命令：

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a name="get-database"></a><a id="get-database"></a>获取数据库

“获取数据库”扩展命令返回数据库对象。 数据库名称取自该命令所针对的数据库上下文。

```
{
  customAction: "GetDatabase"
}
```

下表描述了该命令中的参数：


|**字段**|类型  |**说明** |
|---------|---------|---------|
|  customAction   |   字符串      |   自定义命令的名称。 必须是“GetDatabase”|
        
### <a name="output"></a>输出

如果该命令成功，则响应包含带有以下字段的文档：

|**字段**|类型  |**说明** |
|---------|---------|---------|
|  `ok`   |   `int`     |   响应的状态。 1 == 成功。 0 == 失败。      |
| `database`    |    `string`        |   数据库的名称。      |
|   `provisionedThroughput`  |    `int`      |    对数据库设置的预配吞吐量。 这是一个可选的响应参数。     |

如果该命令失败，则返回默认的自定义命令响应。 有关输出中的参数，请参阅自定义命令的[默认输出](#default-output)。

### <a name="examples"></a>示例

**获取数据库**

若要获取名为“test”的数据库的数据库对象，请使用以下命令：

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a name="create-collection"></a><a id="create-collection"></a>创建集合

“创建集合”扩展命令可创建新的 MongoDB 集合。 数据库名称取自该命令所针对的数据库上下文。 CreateCollection 命令的格式如下：

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

下表描述了该命令中的参数：

| **字段** | 类型  | **必需** | **说明** |
|---------|---------|---------|---------|
| customAction | 字符串 | 必选 | 自定义命令的名称。 必须为 "CreateCollection"。|
| collection | 字符串 | 必选 | 集合的名称。 不允许使用特殊字符。|
| offerThroughput | int | 可有可无 | 要对数据库设置的预配吞吐量。 如果未提供此参数，将默认为最小 400 RU/秒。 * 若要指定超过 10000 RU/s 的吞吐量`shardKey` ，参数是必需的。|
| shardKey | 字符串 | 可有可无 | 分片集合的分片键的路径。 如果在中`offerThroughput`设置了 10000 RU/s，则此参数是必需的。  如果已指定，则所有插入的文档将需要此值。 |

### <a name="output"></a>输出

返回默认的自定义命令响应。 有关输出中的参数，请参阅自定义命令的[默认输出](#default-output)。

### <a name="examples"></a>示例

**创建未分片集合**

若要创建名为“testCollection”、预配吞吐量为 1000 RU 的未分片集合，请使用以下命令： 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**创建分片集合**

若要创建名为 "testCollection" 的分片集合，并预配 1000 ru 的吞吐量和 shardkey 属性 "b. b"，请使用以下命令：

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a name="update-collection"></a><a id="update-collection"></a>更新集合

“更新集合”扩展命令可更新与指定的集合相关联的属性。

```
{
  customAction: "UpdateCollection",
  collection: <Name of the collection that you want to update>,
  offerThroughput: <New throughput that you want to provision on the collection> 
}
```

下表描述了该命令中的参数：

|**字段**|**类型** |**说明** |
|---------|---------|---------|
|  customAction   |   字符串      |   自定义命令的名称。 必须是“UpdateCollection”。      |
|  collection   |   字符串      |   集合的名称。       |
| offerThroughput   |int|   要对集合设置的预配吞吐量。|

## <a name="output"></a>输出

返回默认的自定义命令响应。 有关输出中的参数，请参阅自定义命令的[默认输出](#default-output)。

### <a name="examples"></a>示例

**更新与集合关联的预配吞吐量**

若要将名为“testCollection”的集合的预配吞吐量更新为 1200 RU，请使用以下命令：

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a>获取集合

“获取集合”自定义命令返回集合对象。

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

下表描述了该命令中的参数：


|**字段**|**类型** |**说明** |
|---------|---------|---------|
| customAction    |   字符串      |   自定义命令的名称。 必须是“GetCollection”。      |
| collection    |    字符串     |    集合的名称。     |

### <a name="output"></a>输出

如果该命令成功，则响应包含带有以下字段的文档


|**字段**|**类型** |**说明** |
|---------|---------|---------|
|  `ok`   |    `int`     |   响应的状态。 1 == 成功。 0 == 失败。      |
| `database`    |    `string`     |   数据库的名称。      |
| `collection`    |    `string`     |    集合的名称。     |
|  `shardKeyDefinition`   |   `document`      |  用作分片键的索引规范文档。 这是一个可选的响应参数。       |
|  `provisionedThroughput`   |   `int`      |    要对集合设置的预配吞吐量。 这是一个可选的响应参数。     |

如果该命令失败，则返回默认的自定义命令响应。 有关输出中的参数，请参阅自定义命令的[默认输出](#default-output)。

### <a name="examples"></a>示例

**获取集合**

若要获取名为“testCollection”的集合的集合对象，请使用以下命令：

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a>自定义命令的默认输出

如果未指定，则自定义响应包含带有以下字段的文档：

|**字段**|**类型** |**说明** |
|---------|---------|---------|
|  `ok`   |    `int`     |   响应的状态。 1 == 成功。 0 == 失败。      |
| `code`    |   `int`      |   仅当命令失败时才返回（即，正常 == 0）。 包含 MongoDB 错误代码。 这是一个可选的响应参数。      |
|  `errMsg`   |  `string`      |    仅当命令失败时才返回（即，正常 == 0）。 包含用户友好的错误消息。 这是一个可选的响应参数。      |

## <a name="next-steps"></a>后续步骤

接下来，可以继续学习以下 Azure Cosmos DB 概念： 

* [Azure Cosmos DB 中的索引](../cosmos-db/index-policy.md)
* [利用生存时间使 Azure Cosmos DB 中的数据自动过期](../cosmos-db/time-to-live.md)
