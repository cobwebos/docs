---
title: 教程：使用 Azure Cosmos DB API for MongoDB 设置全局分发
description: 了解如何使用 Azure Cosmos DB 的用于 MongoDB 的 API 设置全局分发。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/26/2018
ms.reviewer: sngun
ms.openlocfilehash: b446697977395aa9bbbcf2192aa232fbc85a0b68
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444673"
---
# <a name="set-up-global-distributed-database-using-azure-cosmos-dbs-api-for-mongodb"></a>使用 Azure Cosmos DB 的用于 MongoDB 的 API 设置全局分布式数据库

本文将展示如何使用 Azure 门户设置一个全局分布式数据库，并使用 Azure Cosmos DB 的用于 MongoDB 的 API 连接到该数据库。

本文涵盖以下任务： 

> [!div class="checklist"]
> * 使用 Azure 门户配置全局分发
> * 使用 [Azure Cosmos DB 的用于 MongoDB 的 API](mongodb-introduction.md) 配置全局分发

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="verifying-your-regional-setup"></a>验证区域设置 
检查 Azure Cosmos DB 的用于 MongoDB 的 API 中的全局配置的一种简单方法是从 Mongo Shell 运行 *isMaster()* 命令。

从 Mongo Shell：

   ```
      db.isMaster()
   ```
   
示例结果：

   ```JSON
      {
         "_t": "IsMasterResponse",
         "ok": 1,
         "ismaster": true,
         "maxMessageSizeBytes": 4194304,
         "maxWriteBatchSize": 1000,
         "minWireVersion": 0,
         "maxWireVersion": 2,
         "tags": {
            "region": "South India"
         },
         "hosts": [
            "vishi-api-for-mongodb-southcentralus.documents.azure.com:10255",
            "vishi-api-for-mongodb-westeurope.documents.azure.com:10255",
            "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
         ],
         "setName": "globaldb",
         "setVersion": 1,
         "primary": "vishi-api-for-mongodb-southindia.documents.azure.com:10255",
         "me": "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
      }
   ```

## <a name="connecting-to-a-preferred-region"></a>连接到首选区域 

使用 Azure Cosmos DB 的用于 MongoDB 的 API，可以为全局分布式数据库指定集合的读取首选项。 为实现低延迟读取和全局高可用性，建议将集合的读取首选项设置为“就近”。  当读取首选项配置为“就近”时，将从最近的区域进行读取。 

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Nearest));
```

对于具有主读取/写入区域和用于灾难恢复 (DR) 方案的辅助区域的应用程序，建议将集合的读取首选项设置为“辅助优先”。  当读取首选项配置为“辅助优先”时，如果主区域不可用，将从辅助区域进行读取。 

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.SecondaryPreferred));
```

最后，如果愿意，可以手动指定读取区域。 可以在读取首选项内设置区域标记。

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
var tag = new Tag("region", "Southeast Asia");
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Secondary, new[] { new TagSet(new[] { tag }) }));
```

本教程到此结束。 阅读 [Azure Cosmos DB 中的一致性级别](consistency-levels.md)，了解如何管理全局复制帐户的一致性。 若要深入了解 Azure Cosmos DB 中全局数据库复制的工作原理，请参阅[使用 Azure Cosmos DB 全局分发数据](distribute-data-globally.md)。

## <a name="next-steps"></a>后续步骤

在本教程中，已完成以下内容：

> [!div class="checklist"]
> * 使用 Azure 门户配置全局分发
> * 使用 Cosmos DB 的用于 MongoDB 的 API 配置全局分发

现在可以继续学习下一个教程，了解如何使用 Azure Cosmos DB 本地模拟器在本地开发。

> [!div class="nextstepaction"]
> [使用 Azure Cosmos DB 模拟器在本地进行开发](local-emulator.md)
