---
title: 在 Azure Cosmos DB 中查询容器
description: 了解如何使用分区中查询和跨分区查询来查询 Azure Cosmos DB 中的容器
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 3/18/2019
ms.author: mjbrown
ms.openlocfilehash: 08ac95fe2a6b3e01d6bbcf96b120426f12f4e21c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85261250"
---
# <a name="query-an-azure-cosmos-container"></a>查询 Azure Cosmos 容器

本文介绍如何在 Azure Cosmos DB 中查询容器（集合、图形或表）。 具体而言，它介绍了分区中查询和跨分区查询在 Azure Cosmos DB 中如何工作。

## <a name="in-partition-query"></a>分区中查询

从容器中查询数据时，如果查询指定了分区键筛选器，则 Azure Cosmos DB 会自动优化查询。 它会将查询路由到筛选器中指定的分区键值所对应的[物理分区](partition-data.md#physical-partitions)。

例如，请考虑以下针对 `DeviceId` 使用等式筛选器的查询。 如果对按 `DeviceId` 分区的容器运行此查询，则此查询将筛选到单个物理分区。

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001'
```

与前面的示例一样，此查询也将筛选到单个分区。 添加针对 `Location` 的其他筛选器不会更改此行为：

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001' AND c.Location = 'Seattle'
```

下面的查询有一个针对分区键的范围筛选器，该查询的作用域不会限定于单个物理分区。 为了成为分区中查询，该查询必须具有包含分区键的等式筛选器：

```sql
    SELECT * FROM c WHERE c.DeviceId > 'XMS-0001'
```

## <a name="cross-partition-query"></a>跨分区查询

下面的查询没有针对分区键 (`DeviceId`) 的筛选器。 因此，它必须根据每个分区的索引扇出到运行它的所有物理分区：

```sql
    SELECT * FROM c WHERE c.Location = 'Seattle`
```

每个物理分区都有其自己的索引。 因此，当你在容器上运行跨分区查询时，你可以高效地针对每个物理分区运行一个查询。 Azure Cosmos DB 会自动聚合不同物理分区的结果。

不同物理分区中的索引彼此独立。 Azure Cosmos DB 中没有全局索引。

## <a name="parallel-cross-partition-query"></a>并行跨分区查询

Azure Cosmos DB SDK 1.9.0 及更高版本支持并行查询执行选项。 并行跨分区查询可用于执行低延迟、跨分区查询。

可以通过调整以下参数来管理并行查询执行：

- **MaxConcurrency**：设置容器分区的最大并发网络连接数。 如果将此属性设置为 `-1`，则由 SDK 管理并行度。 如果  `MaxConcurrency` 设置为 `0`，则与容器的分区之间存在单个网络连接。

- **MaxBufferedItemCount**：权衡查询延迟与客户端内存利用率。 如果省略此选项或将其设置为 -1，则由 SDK 管理并行查询执行过程中缓冲的项目数。

由于 Azure Cosmos DB 能够并行执行跨分区查询，因此，随着系统增加[物理分区](partition-data.md#physical-partitions)，查询延迟通常增加得不多。 但是，随着物理分区总数的增加，RU 开销会明显增大。

运行跨分区查询时，实质上是对每个物理分区执行单独的查询。 尽管跨分区查询将使用索引（如果可用），但它们仍然不如分区中查询高效。

## <a name="useful-example"></a>有用的示例

下面是一个类比，可帮助你更好地理解跨分区查询：

假设你是一个送货司机，必须把包裹送到不同的公寓楼。 每栋公寓楼都有一个列表，上面包含所有住户的单元号。 我们可以将每栋公寓楼与物理分区进行比较，并将每个列表与物理分区的索引进行比较。

我们可以使用以下示例来比较分区中查询和跨分区查询：

### <a name="in-partition-query"></a>分区中查询

如果送货司机知道正确的公寓楼（物理分区），那么他们可以立即开车到正确的大楼。 司机可以查看公寓楼的住户单元号列表（索引），并迅速送达相应的包裹。 在这种情况下，司机不需要浪费任何时间或精力开车到公寓楼去查看是否有包裹接收者住在那里。

### <a name="cross-partition-query-fan-out"></a>跨分区查询（扇出）

如果送货司机不知道正确的公寓楼（物理分区），则他们需要开车到每一栋公寓楼，并检查列表上所有住户的单元号（索引）。 在他们到达每个公寓楼后，他们仍然可以使用每个住户的地址列表。 然而，他们将需要检查每个公寓楼的列表，无论是否有包裹接收者住在那里。 这是跨分区查询的工作方式。 虽然他们可以使用索引（不需要挨户敲门），但他们必须分别检查每个物理分区的索引。

### <a name="cross-partition-query-scoped-to-only-a-few-physical-partitions"></a>跨分区查询（仅限于几个物理分区）

如果送货司机知道所有的包裹接收者都住在某几个公寓楼里，他们就不需要开车去每个公寓楼。 虽然开车去几处公寓楼需要做的工作仍然比访问一栋大楼要多，但是送货司机仍然可以节省大量的时间和精力。 如果查询的筛选器中包含分区键和 `IN` 关键字，则它仅检查相关物理分区的索引中是否有所需数据。

## <a name="avoiding-cross-partition-queries"></a>避免跨分区查询

对于大多数容器，不可避免地会使用一些跨分区查询。 使用一些跨分区查询是可以的！ 几乎所有查询操作都支持跨分区（包括逻辑分区键和物理分区）。 为了跨物理分区并行执行查询，Azure Cosmos DB 在查询引擎和客户端 SDK 中也进行了许多优化。

对于大多数读取繁忙的场景，我们建议你直接在查询筛选器中选择最常用的属性。 你还应该确保分区键遵循其他[分区键选择最佳做法](partitioning-overview.md#choose-partitionkey)。

避免跨分区查询通常只对大型容器很重要。 每次检查物理分区的索引来获取结果时，至少需要大约 2.5 RU 的开销，即使物理分区中没有项与查询的筛选器匹配。 因此，如果只有一个（或几个）物理分区，则跨分区查询消耗的 RU 不会显著高于分区中查询。

物理分区的数量与已预配 RU 的数量相关。 每个物理分区允许最多 10,000 个预配 RU，并且最多可以存储 50 GB 的数据。 Azure Cosmos DB 会自动为你管理物理分区。 容器中的物理分区数取决于预配的吞吐量和消耗的存储。

如果工作负荷满足以下条件，则应尝试避免使用跨分区查询：
- 你计划预配 30,000 个以上的 RU
- 你计划存储超过 100 GB 的数据

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解 Azure Cosmos DB 中的分区：

- [Azure Cosmos DB 中的分区](partitioning-overview.md)
- [Azure Cosmos DB 中的综合分区键](synthetic-partition-keys.md)
