---
title: "MongoDB API 的 Azure Cosmos DB 全局分发教程 | Microsoft Docs"
description: "了解如何使用 MongoDB API 设置 Azure Cosmos DB 全局分发。"
services: cosmos-db
keywords: "全局分发, MongoDB"
documentationcenter: 
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 119ebb3f4966de08934c7d1fbd139229bda1d060
ms.contentlocale: zh-cn
ms.lasthandoff: 05/31/2017


---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-mongodb-api"></a>如何使用 MongoDB API 设置 Azure Cosmos DB 全局分发

本文介绍了如何使用 Azure 门户设置 Azure Cosmos DB 全局分发，然后使用 MongoDB API 进行连接。

本文涵盖以下任务： 

> [!div class="checklist"]
> * 使用 Azure 门户配置全局分发
> * 使用 [MongoDB API](mongodb-introduction.md) 配置全局分发

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="verifying-your-regional-setup-using-the-mongodb-api"></a>使用 MongoDB API 验证区域设置
仔细检查 MongoDB 的 API 中的全局配置的最简单方法是从 Mongo Shell 运行 *isMaster()* 命令。

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
            "vishi-api-for-mongodb-southcentralus.documents.azure.com:10250",
            "vishi-api-for-mongodb-westeurope.documents.azure.com:10250",
            "vishi-api-for-mongodb-southindia.documents.azure.com:10250"
         ],
         "setName": "globaldb",
         "setVersion": 1,
         "primary": "vishi-api-for-mongodb-southindia.documents.azure.com:10250",
         "me": "vishi-api-for-mongodb-southindia.documents.azure.com:10250"
      }
   ```

## <a name="connecting-to-a-preferred-region-using-the-mongodb-api"></a>使用 MongoDB API 连接到首选区域

使用 MongoDB API，可以为全局分布式数据库指定集合的读取首选项。 为实现低延迟读取和全局高可用性，建议你将集合的读取首选项设置为“就近”。 当读取首选项配置为“就近”时，将从最近的区域进行读取。

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Nearest));
```

对于具有主读取/写入区域和用于灾难恢复 (DR) 方案的辅助区域的应用程序，建议你将集合的读取首选项设置为“辅助优先”。 当读取首选项配置为“辅助优先”时，如果主区域不可用，将从辅助区域进行读取。

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.SecondaryPreferred));
```

最后，如果你愿意，可以手动指定读取区域。 可以在你的读取首选项内设置区域标记。

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
> * 使用 DocumentDB API 配置全局分发

现在可以继续学习下一个教程，了解如何使用 Azure Cosmos DB 本地模拟器在本地开发。

> [!div class="nextstepaction"]
> [通过模拟器在本地开发](local-emulator.md)
