---
title: 在 Azure Cosmos DB 中查询容器
description: 了解如何使用分区内和跨分区查询在 Azure Cosmos DB 中查询容器
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 3/18/2019
ms.author: mjbrown
ms.openlocfilehash: 299980b67caaea85fbfb40cb1a30ee50fa32d0f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80131401"
---
# <a name="query-an-azure-cosmos-container"></a>查询 Azure Cosmos 容器

本文介绍如何在 Azure Cosmos DB 中查询容器（集合、图形或表）。 具体而言，它介绍了在 Azure Cosmos DB 中分区和跨分区查询的工作方式。

## <a name="in-partition-query"></a>分区中查询

在查询容器中的数据时，如果查询指定了分区键筛选器，则 Azure Cosmos DB 自动优化查询。 它将查询路由到与筛选器中指定的分区键值对应的[物理分区](partition-data.md#physical-partitions)。

例如，请考虑下面带有相等筛选器的查询`DeviceId`。 如果在分区的容器上运行此查询`DeviceId`，此查询将筛选为单个物理分区。

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001'
```

与前面的示例一样，此查询也将筛选为单个分区。 添加其他筛选器不`Location`会更改此内容：

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001' AND c.Location = 'Seattle'
```

下面是一个具有分区键的范围筛选器的查询，不作用于单个物理分区。 为了成为分区内查询，该查询必须具有包含分区键的相等性筛选器：

```sql
    SELECT * FROM c WHERE c.DeviceId > 'XMS-0001'
```

## <a name="cross-partition-query"></a>跨分区查询

下面的查询对分区键（`DeviceId`）没有筛选器。 因此，它必须对每个分区的索引运行的所有物理分区进行扇出：

```sql
    SELECT * FROM c WHERE c.Location = 'Seattle`
```

每个物理分区都有其自己的索引。 因此，当您对容器运行跨分区查询时，实际上是对*每个*物理分区运行一个查询。 Azure Cosmos DB 将自动聚合不同物理分区上的结果。

不同物理分区中的索引相互独立。 Azure Cosmos DB 中没有全局索引。

## <a name="parallel-cross-partition-query"></a>并行跨分区查询

Azure Cosmos DB SDK 1.9.0 及更高版本支持并行查询执行选项。 并行跨分区查询可用于执行低延迟、跨分区查询。

可以通过调整以下参数来管理并行查询执行：

- **MaxConcurrency**：设置与容器的分区同时进行的网络连接的最大数量。 如果将此属性设置为`-1`，则 SDK 会管理并行度。 如果 `MaxConcurrency`设置为`0`，则与容器的分区之间存在单个网络连接。

- **MaxBufferedItemCount**：权衡查询延迟与客户端内存利用率。 如果省略此选项或将其设置为 -1，则由 SDK 管理并行查询执行过程中缓冲的项目数。

由于 Azure Cosmos DB 对跨分区查询进行并行化的能力，因此，在系统添加[物理分区](partition-data.md#physical-partitions)时，查询延迟将会很好地进行缩放。 但是，只要物理分区总数增加，RU 费用就会大幅增加。

运行跨分区查询时，你实质上是对每个物理分区执行单独的查询。 尽管跨分区查询查询将使用索引（如果可用），但与分区内查询相比，它们的效率并不接近。

## <a name="useful-example"></a>有用示例

下面是更好地理解跨分区查询的类比：

假设您是一个交付驱动程序，必须将包交付到不同的单元组合。 每个单元的复杂程度都有一个包含所有居民单元号的本地列表。 我们可以将每个单元的复杂程度与物理分区和每个列表进行比较，以物理分区的索引。

我们可以使用以下示例来比较分区和跨分区查询：

### <a name="in-partition-query"></a>分区中查询

如果交付驱动程序知道正确的单元复杂（物理分区），则它们可以立即驱动到正确的大楼。 驱动程序可以检查常驻单元号（索引）的单元复杂列表，并快速传递适当的包。 在这种情况下，驱动程序不会浪费时间或精力来检查并查看是否有任何包接收方处于活动的单元。

### <a name="cross-partition-query-fan-out"></a>跨分区查询（扇出）

如果传递驱动程序不知道正确的单元复杂（物理分区），则需要驱动每个单元的生成，并在列表中查看所有居民的单元号（索引）。 一旦他们到达每个单元，它们仍将能够使用每个居民的地址列表。 但是，它们需要检查每个单元的复杂列表，是否有任何包接收方在此列表中存在。 这是跨分区查询的工作方式。 虽然它们可以使用索引（无需在每一门上挖空），但它们必须分别检查每个物理分区的索引。

### <a name="cross-partition-query-scoped-to-only-a-few-physical-partitions"></a>跨分区查询（仅限于几个物理分区）

如果交付驱动程序知道所有包接收方在某个特定的单元组合中都处于活动，则无需驱动每个包。 尽管驱动到几个单元组合仍需比仅访问一座建筑更多的工作，但交付驱动程序仍然节省了大量时间和精力。 如果查询的筛选器中有包含`IN`关键字的分区键，则它将仅检查相关物理分区的数据索引。

## <a name="avoiding-cross-partition-queries"></a>避免跨分区查询

对于大多数容器，你将无法使用某些跨分区查询。 有一些跨分区查询是正常的！ 几乎跨分区（逻辑分区键和物理分区）支持所有查询操作。 Azure Cosmos DB 还在查询引擎和客户端 Sdk 中进行了许多优化，以跨物理分区并行执行查询。

对于大多数读取繁重方案，我们建议只在查询筛选器中选择最常见的属性。 还应确保分区键符合其他[分区键选择最佳实践](partitioning-overview.md#choose-partitionkey)。

避免跨分区查询通常只对大型容器很重要。 每次检查物理分区的索引以获得结果时，你最少需要大约 2.5 RU，即使物理分区中没有与查询的筛选器匹配的项也是如此。 这种情况下，如果物理分区只有一个（或几个），则跨分区查询将不会比分区内查询消耗更多的 RU。

物理分区数与预配的 RU 的数量关联。 每个物理分区最多允许10000个预配的 RU，最多可存储 50 GB 的数据。 Azure Cosmos DB 将自动为你管理物理分区。 容器中的物理分区数取决于预配的吞吐量和使用的存储。

如果工作负载满足以下条件，则应尝试避免跨分区查询：
- 计划预配超过 30000 RU
- 你计划存储超过 100 GB 的数据

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解 Azure Cosmos DB 中的分区：

- [Azure Cosmos DB 中的分区](partitioning-overview.md)
- [Azure Cosmos DB 中的合成分区键](synthetic-partition-keys.md)
