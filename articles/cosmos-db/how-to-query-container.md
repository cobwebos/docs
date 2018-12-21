---
title: 在 Azure Cosmos DB 中查询容器
description: 了解如何在 Azure Cosmos DB 中查询容器
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 11c68b61802f6c7b3755da71c176ea777f171e4c
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409830"
---
# <a name="query-containers-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中查询容器

本文介绍如何在 Azure Cosmos DB 中查询容器（集合、图形、表）。

## <a name="in-partition-query"></a>分区中查询

从容器中查询数据时，如果查询指定了分区键筛选器，Azure Cosmos DB 会自动将查询路由到筛选器中所指定分区键值对应的分区。 例如，以下查询将路由到 DeviceId 分区，该分区保存与分区键值“XMS-0001”对应的所有文档。

```csharp
// Query using partition key into a class called, DeviceReading
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```

## <a name="cross-partition-query"></a>跨分区查询

下面的查询在分区键 (DeviceId) 上没有筛选器，并且以扇形展开到针对分区索引执行该查询的所有分区。 若要执行跨分区查询，请将 **EnableCrossPartitionQuery** 设置为 true（或在 REST API 中设置 x-ms-documentdb-query-enablecrosspartition）。

```csharp
// Query across partition keys into a class called, DeviceReading
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Cosmos DB 支持使用 SQL 对容器执行聚合函数 COUNT、MIN、MAX 和 AVG。 从 SDK 版本 1.12.0 及更高版本开始，可对容器执行聚合函数。 查询必须包括单个聚合运算符，并且必须在投影中包括单个值。

## <a name="parallel-cross-partition-query"></a>并行跨分区查询

Cosmos DB SDK 1.9.0 及更高版本支持并行查询执行选项。  并行跨分区查询可用于执行低延迟、跨分区查询。 例如，以下查询配置为跨分区并行运行。

```csharp
// Cross-partition Order By Query with parallel execution
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),  
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```

可以通过调整以下参数来管理并行查询执行：

- **MaxDegreeOfParallelism**：设置容器分区的最大并发网络连接数。 如果将此属性设置为 -1，则由 SDK 管理并行度。 如果 MaxDegreeOfParallelism 未指定或设置为 0（默认值），则与容器的分区的网络连接将有一个。

- **MaxBufferedItemCount**：权衡查询延迟与客户端内存利用率。 如果省略此选项或将其设置为 -1，则由 SDK 管理并行查询执行过程中缓冲的项目数。

使用相同状态的集合，并行查询以串行执行相同的顺序返回结果。 执行包含排序运算符（ORDER BY 和/或 TOP）的跨分区查询时，Azure Cosmos DB SDK 跨分区发出并行查询，并合并客户端中的部分排序结果，以生成全局范围内有序的结果。

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解 Cosmos DB 中的分区：

- [Azure Cosmos DB 中的分区](partitioning-overview.md)
- [Azure Cosmos DB 中的综合分区键](synthetic-partition-keys.md)
