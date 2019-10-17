---
title: 使用 Azure 门户和各种 Sdk 创建具有大分区键的 Azure Cosmos 容器。
description: 了解如何使用 Azure 门户和不同的 Sdk 在 Azure Cosmos DB 中创建具有大分区键的容器。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: be639a67f70ab40f8d7dcc0f3793cbbd4a2ec4a3
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72436811"
---
# <a name="create-containers-with-large-partition-key"></a>创建具有大分区键的容器

Azure Cosmos DB 使用基于哈希的分区方案来实现数据的水平缩放。 在 3 2019 月5月之前创建的所有 Azure Cosmos 容器均使用哈希函数，该函数根据分区键的前100字节计算哈希值。 如果有多个具有相同前100个字节的分区键，则该服务会将这些逻辑分区视为相同的逻辑分区。 这可能会导致分区大小配额不正确，以及跨分区键应用唯一索引等问题。 为了解决此问题，会引入大的分区键。 Azure Cosmos DB 现在支持大分区键，其值最大为 2 KB。

使用哈希函数的增强版本功能支持大分区键，此功能可从最大为 2 KB 的大分区键生成唯一哈希。 对于具有高分区键基数的方案（与分区键的大小无关），也建议使用此哈希版本。 分区键基数定义为唯一逻辑分区数，例如，容器中 ~ 30000 逻辑分区的顺序。 本文介绍如何使用 Azure 门户和不同的 Sdk 创建具有大分区键的容器。

## <a name="create-a-large-partition-key-azure-portal"></a>创建大分区键（Azure 门户）

若要创建大分区键，请在使用 Azure 门户创建新容器时，选中 "**我的分区键大于 100-Bytes"** 选项。 如果不需要较大的分区键，或者运行的 Sdk 版本低于1.18 的应用程序，请取消选中该复选框。

![使用 Azure 门户创建大分区键](./media/large-partition-keys/large-partition-key-with-portal.png)

## <a name="create-a-large-partition-key-powershell"></a>创建大分区键（PowerShell）

若要创建具有大分区键支持的容器，请参阅。

* [创建分区键大小较大的 Azure Cosmos 容器](manage-with-powershell.md##create-container-big-pk)

## <a name="create-a-large-partition-key-net-sdk"></a>创建大分区键（.Net SDK）

若要使用 .NET SDK 创建具有大分区键的容器，请指定 `PartitionKeyDefinitionVersion.V2` 属性。 下面的示例演示如何在 PartitionKeyDefinition 对象中指定 Version 属性，并将其设置为 PartitionKeyDefinitionVersion。

### <a name="v3-net-sdk"></a>v3 .NET SDK

```csharp
await database.CreateContainerAsync(
    new ContainerProperties(collectionName, $"/longpartitionkey")
    {
        PartitionKeyDefinitionVersion = PartitionKeyDefinitionVersion.V2,
    })
```

### <a name="v2-net-sdk"></a>v2 .NET SDK

```csharp
DocumentCollection collection = await newClient.CreateDocumentCollectionAsync(
database,
     new DocumentCollection
        {
           Id = Guid.NewGuid().ToString(),
           PartitionKey = new PartitionKeyDefinition
           {
             Paths = new Collection<string> {"/longpartitionkey" },
             Version = PartitionKeyDefinitionVersion.V2
           }
         },
      new RequestOptions { OfferThroughput = 400 });
```

## <a name="supported-sdk-versions"></a>支持的 SDK 版本

以下最小版本的 Sdk 支持较大的分区键：

|SDK 类型  | 最低版本   |
|---------|---------|
|.Net     |    1.18     |
|Java 同步     |   2.4.0      |
|Java 异步   |  2.5.0        |
| REST API | 版本高于 `2017-05-03` 通过使用 @no__t 请求标头。|

目前，在 Power BI 和 Azure 逻辑应用中，不能在中使用具有大分区键的容器。 在这些应用程序中，可以使用没有大分区键的容器。

## <a name="next-steps"></a>后续步骤

* [Azure Cosmos DB 中的分区](partitioning-overview.md)
* [Azure Cosmos DB 中的请求单位](request-units.md)
* [在容器和数据库上预配吞吐量](set-throughput.md)
* [使用 Azure Cosmos 帐户](account-overview.md)
