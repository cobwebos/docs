---
title: 在 Azure Cosmos DB 中查询容器
description: 了解如何使用分区内和跨分区查询查询 Azure Cosmos DB 中的容器
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 3/18/2019
ms.author: mjbrown
ms.openlocfilehash: 299980b67caaea85fbfb40cb1a30ee50fa32d0f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131401"
---
# <a name="query-an-azure-cosmos-container"></a>查询 Azure Cosmos 容器

本文介绍如何在 Azure Cosmos DB 中查询容器（集合、图形或表）。 特别是，它涵盖了分区内查询和跨分区查询在 Azure Cosmos DB 中的工作方式。

## <a name="in-partition-query"></a>分区中查询

从容器查询数据时，如果查询指定了分区密钥筛选器，Azure Cosmos DB 将自动优化查询。 它将查询路由到与筛选器中指定的分区键值对应[的物理分区](partition-data.md#physical-partitions)。

例如，请考虑以下查询，在 上`DeviceId`使用相等性筛选器。 如果我们在分区`DeviceId`的容器上运行此查询，则此查询将筛选为单个物理分区。

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001'
```

与前面的示例一样，此查询还将筛选到单个分区。 添加附加筛选器`Location`不会更改此功能：

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001' AND c.Location = 'Seattle'
```

下面是一个查询，该查询在分区键上具有范围筛选器，并且不会限定为单个物理分区。 为了成为分区内查询，查询必须具有包含分区键的相等筛选器：

```sql
    SELECT * FROM c WHERE c.DeviceId > 'XMS-0001'
```

## <a name="cross-partition-query"></a>跨分区查询

以下查询在分区键 （）`DeviceId`上没有筛选器。 因此，它必须扇出到针对每个分区的索引运行的所有物理分区：

```sql
    SELECT * FROM c WHERE c.Location = 'Seattle`
```

每个物理分区都有自己的索引。 因此，当您在容器上运行跨分区查询时，*您实际上正在每个*物理分区运行一个查询。 Azure Cosmos DB 将自动聚合不同物理分区的结果。

不同物理分区中的索引彼此独立。 Azure 宇宙 DB 中没有全局索引。

## <a name="parallel-cross-partition-query"></a>并行跨分区查询

Azure Cosmos DB SDK 1.9.0 及更高版本支持并行查询执行选项。 并行跨分区查询可用于执行低延迟、跨分区查询。

可以通过调整以下参数来管理并行查询执行：

- **MaxConcurrency：** 设置与容器分区同时连接的最大数量。 如果将此属性设置为`-1`，SDK 将管理并行性的程度。 如果 `MaxConcurrency`设置为`0`，则与容器的分区有一个网络连接。

- **MaxBufferedItemCount**：权衡查询延迟与客户端内存利用率。 如果省略此选项或将其设置为 -1，则由 SDK 管理并行查询执行过程中缓冲的项目数。

由于 Azure Cosmos DB 能够并行化跨分区查询，因此查询延迟通常会随着系统添加[物理分区](partition-data.md#physical-partitions)而扩展。 但是，RU 电荷将显著增加，因为物理分区的总数会增加。

运行跨分区查询时，实质上是针对单个物理分区执行单独的查询。 虽然跨分区查询将使用索引（如果可用），但它们仍然不如分区内查询有效。

## <a name="useful-example"></a>有用的示例

下面是一个类比，以更好地了解跨分区查询：

假设您是一个递送司机，必须将包裹运送到不同的公寓大楼。 每个公寓大楼都有一份包含居民单位号码的房舍列表。 我们可以将每个公寓复合体与物理分区进行比较，并将每个列表与物理分区的索引进行比较。

我们可以使用此示例比较分区内查询和跨分区查询：

### <a name="in-partition-query"></a>分区中查询

如果送货司机知道正确的公寓大楼（物理分区），那么他们就可以立即开车到正确的建筑物。 驾驶员可以检查公寓大楼的居民单位编号（索引）列表，并快速交付相应的包裹。 在这种情况下，司机不会浪费任何时间和精力开车到公寓大楼检查，看看是否有任何包裹收件人住在那里。

### <a name="cross-partition-query-fan-out"></a>跨分区查询（扇出）

如果送货司机不知道正确的公寓大楼（物理分区），他们需要开车到每一栋公寓楼，并检查列表与居民的所有单位编号（索引）。 一旦他们到达每个公寓大楼，他们仍然可以使用每个居民的地址列表。 但是，他们需要检查每个公寓大楼的列表，是否有任何包裹收件人住在那里。 这是跨分区查询的工作原理。 虽然他们可以使用索引（不需要敲每扇门），但他们必须单独检查每个物理分区的索引。

### <a name="cross-partition-query-scoped-to-only-a-few-physical-partitions"></a>跨分区查询（范围仅限于几个物理分区）

如果传递驱动程序知道所有包裹收件人都住在某个公寓楼内，他们不需要开车到每个公寓。 虽然开车到几个公寓大楼仍然需要更多的工作，而不是只参观一栋建筑，交付驱动程序仍然节省了大量的时间和精力。 如果查询的筛选器中具有`IN`分区键和关键字，则仅检查相关物理分区的索引以检查数据。

## <a name="avoiding-cross-partition-queries"></a>避免跨分区查询

对于大多数容器，您不可避免地会有一些跨分区查询。 有一些跨分区查询是可以的！ 几乎所有查询操作都支持跨分区（逻辑分区键和物理分区）。 Azure Cosmos DB 在查询引擎和客户端 SDK 中也有许多优化，用于跨物理分区并行化查询执行。

对于大多数读取重方案，我们建议在查询筛选器中选择最常见的属性。 您还应确保分区密钥符合其他[分区密钥选择最佳做法](partitioning-overview.md#choose-partitionkey)。

避免跨分区查询通常只对大型容器很重要。 每次检查物理分区的索引以检查结果时，即使物理分区中的任何项与查询的筛选器匹配，您也会至少向您收取约 2.5 RU 的费用。 因此，如果您只有一个（或只有几个）物理分区，则跨分区查询不会消耗比分区内查询多得多的 RU。

物理分区的数量与预配的 RU 的数量相关联。 每个物理分区允许最多 10，000 个预配的 RU，并且可以存储多达 50 GB 的数据。 Azure 宇宙数据库将自动为您管理物理分区。 容器中的物理分区数取决于预配吞吐量和消耗的存储。

如果工作负荷满足以下条件，应尽量避免跨分区查询：
- 您计划拥有超过 30，000 RU 的预配
- 您计划存储超过 100 GB 的数据

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解 Azure Cosmos DB 中的分区：

- [Azure Cosmos DB 中的分区](partitioning-overview.md)
- [Azure Cosmos DB 中的合成分区键](synthetic-partition-keys.md)
