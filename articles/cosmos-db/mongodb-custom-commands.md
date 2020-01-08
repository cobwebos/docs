---
title: 用于管理 Azure Cosmos DB 的 MongoDB API 中的数据的 MongoDB 扩展命令
description: 本文介绍如何使用 MongoDB 扩展命令管理 Azure Cosmos DB 的 API for MongoDB 中存储的数据。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: f57b274715eb1c8a4d517f5655c09c366574d412
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445219"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>使用 MongoDB 扩展命令管理存储在 MongoDB Azure Cosmos DB 的 API 中的数据 

Azure Cosmos DB 由 Microsoft 提供，是全球分布的多模型数据库服务。 可以通过使用任何开源[MongoDB 客户端驱动程序](https://docs.mongodb.org/ecosystem/drivers)与 AZURE COSMOS DB 的 API 进行通信。 使用 Azure Cosmos DB 的 MongoDB API，可以通过遵守[mongodb 线路协议](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)使用现有的客户端驱动程序。

通过使用 Azure Cosmos DB 的适用于 MongoDB 的 API，你可以享受 Cosmos DB 的优势，例如全局分发、自动分片、高可用性、延迟保证、自动、静态加密、备份等，同时保留你的投资在 MongoDB 应用中。

## <a name="mongodb-protocol-support"></a>MongoDB 协议支持

默认情况下，Azure Cosmos DB 的 MongoDB API 与 MongoDB 服务器版本3.2 兼容，有关更多详细信息，请参阅[支持的功能和语法](mongodb-feature-support.md)。 MongoDB 版本3.4 中添加的功能或查询运算符当前在 Azure Cosmos DB 的适用于 MongoDB 的 API 中作为预览提供。 以下扩展命令支持在对存储在 MongoDB Azure Cosmos DB 的 API 中的数据执行 CRUD 操作时 Azure Cosmos DB 特定功能：

* [创建数据库](#create-database)
* [更新数据库](#update-database)
* [获取数据库](#get-database)
* [创建集合](#create-collection)
* [更新集合](#update-collection)
* [获取集合](#get-collection)

## <a id="create-database"></a>创建数据库

Create database extension 命令会创建一个新的 MongoDB 数据库。 数据库名称用于执行该命令的数据库上下文。 CreateDatabase 命令的格式如下所示：

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

下表描述了命令中的参数：

|**字段**|类型 |**说明** |
|---------|---------|---------|
| customAction   |  字符串  |   自定义命令的名称，它必须为 "CreateDatabase"。      |
| offerThroughput | int  | 在数据库上设置的预配吞吐量。 此参数是可选的。 |

### <a name="output"></a>输出

返回默认的自定义命令响应。 请参阅输出中参数的自定义命令的[默认输出](#default-output)。

### <a name="examples"></a>示例

**创建数据库**

若要创建名为 "test" 的数据库，请使用以下命令：

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**创建具有吞吐量的数据库**

若要创建名为 "test" 的数据库并预配 1000 ru 的吞吐量，请使用以下命令：

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a id="update-database"></a>更新数据库

Update database extension 命令将更新与指定数据库相关联的属性。 目前，只能更新 "offerThroughput" 属性。

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

下表描述了命令中的参数：

|**字段**|类型 |**说明** |
|---------|---------|---------|
| customAction    |    字符串     |   自定义命令的名称。 必须为 "Updatedatabase.vbs"。      |
|  offerThroughput   |  int       |     要对数据库设置的新预配吞吐量。    |

### <a name="output"></a>输出

返回默认的自定义命令响应。 请参阅输出中参数的自定义命令的[默认输出](#default-output)。

### <a name="examples"></a>示例

**更新与数据库关联的预配吞吐量**

若要将名为 "test" 的数据库的预配吞吐量更新为 1200 ru，请使用以下命令：

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a id="get-database"></a>获取数据库

Get database extension 命令返回数据库对象。 数据库名称用于执行该命令的数据库上下文。

```
{
  customAction: "GetDatabase"
}
```

下表描述了命令中的参数：


|**字段**|类型 |**说明** |
|---------|---------|---------|
|  customAction   |   字符串      |   自定义命令的名称。 必须是 "GetDatabase"|
        
### <a name="output"></a>输出

如果此命令成功，则响应将包含一个包含以下字段的文档：

|**字段**|类型 |**说明** |
|---------|---------|---------|
|  `ok`   |   `int`     |   响应状态。 1 = = 成功。 0 = = 失败。      |
| `database`    |    `string`        |   数据库的名称。      |
|   `provisionedThroughput`  |    `int`      |    在数据库上设置的预配吞吐量。 这是一个可选的响应参数。     |

如果命令失败，则返回一个默认的自定义命令响应。 请参阅输出中参数的自定义命令的[默认输出](#default-output)。

### <a name="examples"></a>示例

**获取数据库**

若要获取名为 "test" 的数据库的数据库对象，请使用以下命令：

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a id="create-collection"></a>创建集合

Create collection extension 命令会创建一个新的 MongoDB 集合。 数据库名称用于执行该命令的数据库上下文。 CreateCollection 命令的格式如下所示：

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

下表描述了命令中的参数：

|**字段**|类型 |**说明** |
|---------|---------|---------|
| customAction    | 字符串 | 自定义命令的名称。 必须是 "CreateCollection"     |
| collection      | 字符串 | 集合的名称                                   |
| offerThroughput | int    | 要对数据库设置的预配吞吐量。 这是一个可选参数 |
| shardKey        | 字符串 | 用于创建分片集合的分片密钥路径。 这是一个可选参数 |

### <a name="output"></a>输出

返回默认的自定义命令响应。 请参阅输出中参数的自定义命令的[默认输出](#default-output)。

### <a name="examples"></a>示例

**创建 unsharded 集合**

若要创建名为 "testCollection" 的 unsharded 集合并预配 1000 ru 的吞吐量，请使用以下命令： 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**创建分片集合**

若要创建名为 "testCollection" 的分片集合并预配 1000 ru 的吞吐量，请使用以下命令：

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a id="update-collection"></a>更新集合

更新集合扩展命令将更新与指定集合关联的属性。

```
{
  customAction: "UpdateCollection",
  collection: <Name of the collection that you want to update>,
  offerThroughput: <New throughput that you want to provision on the collection> 
}
```

下表描述了命令中的参数：

|**字段**|类型 |**说明** |
|---------|---------|---------|
|  customAction   |   字符串      |   自定义命令的名称。 必须为 "UpdateCollection"。      |
|  collection   |   字符串      |   集合的名称。       |
| offerThroughput   |int|   要在集合上设置的预配吞吐量。|

## <a name="output"></a>输出

返回默认的自定义命令响应。 请参阅输出中参数的自定义命令的[默认输出](#default-output)。

### <a name="examples"></a>示例

**更新与集合关联的预配吞吐量**

若要将名为 "testCollection" 的集合的预配吞吐量更新为 1200 ru，请使用以下命令：

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a id="get-collection"></a>获取集合

获取集合自定义命令返回集合对象。

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

下表描述了命令中的参数：


|**字段**|类型 |**说明** |
|---------|---------|---------|
| customAction    |   字符串      |   自定义命令的名称。 必须为 "GetCollection"。      |
| collection    |    字符串     |    集合的名称。     |

### <a name="output"></a>输出

如果此命令成功，则响应将包含具有以下字段的文档


|**字段**|类型 |**说明** |
|---------|---------|---------|
|  `ok`   |    `int`     |   响应状态。 1 = = 成功。 0 = = 失败。      |
| `database`    |    `string`     |   数据库的名称。      |
| `collection`    |    `string`     |    集合的名称。     |
|  `shardKeyDefinition`   |   `document`      |  用作分片键的索引规范文档。 这是一个可选的响应参数。       |
|  `provisionedThroughput`   |   `int`      |    要在集合上设置的预配吞吐量。 这是一个可选的响应参数。     |

如果命令失败，则返回一个默认的自定义命令响应。 请参阅输出中参数的自定义命令的[默认输出](#default-output)。

### <a name="examples"></a>示例

**获取集合**

若要获取名为 "testCollection" 的集合的集合对象，请使用以下命令：

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a id="default-output"></a>自定义命令的默认输出

如果未指定，则自定义响应将包含具有以下字段的文档：

|**字段**|类型 |**说明** |
|---------|---------|---------|
|  `ok`   |    `int`     |   响应状态。 1 = = 成功。 0 = = 失败。      |
| `code`    |   `int`      |   仅当命令失败时（即 ok = = 0）返回。 包含 MongoDB 错误代码。 这是一个可选的响应参数。      |
|  `errMsg`   |  `string`      |    仅当命令失败时（即 ok = = 0）返回。 包含用户友好的错误消息。 这是一个可选的响应参数。      |

## <a name="next-steps"></a>后续步骤

接下来，你可以继续学习以下 Azure Cosmos DB 概念： 

* [Azure Cosmos DB 中的索引](../cosmos-db/index-policy.md)
* [利用生存时间使 Azure Cosmos DB 中的数据自动过期](../cosmos-db/time-to-live.md)
