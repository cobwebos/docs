---
title: 规模服务器组-超大规模 (Citus) -Azure Database for PostgreSQL
description: 调整服务器组内存、磁盘和 CPU 资源来处理增加的负载
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 9/18/2020
ms.openlocfilehash: fef873d5122fefb48c85281f71e206f95f3fbe48
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90986703"
---
# <a name="server-group-size"></a>服务器组大小

超大规模 (Citus) 部署选项使用协作数据库服务器并行执行查询，并存储更多数据。 服务器组 "大小" 指的是服务器的数量，以及每个服务器的硬件资源。

## <a name="picking-initial-size"></a>选取初始大小

服务器组的大小（节点数及其硬件容量）很容易改变 ([参见下面](#scale-a-hyperscale-citus-server-group)) 。 但仍需为新服务器组选择初始大小。 下面是适用于合理选择的一些提示。

### <a name="multi-tenant-saas-use-case"></a>多租户 SaaS 使用情况

对于从现有的单节点 PostgreSQL 数据库实例迁移到超大规模 (Citus) 的迁移程序，我们建议选择一个群集，其中 "工作线程数" 和 "RAM 总数" 等于原始实例的数目。 在这种情况下，我们已了解到2倍的性能改进，因为分片可提高资源利用率，并允许较小的索引等等。

协调器节点所需的 Vcore 数取决于现有工作负荷 (写入/读取吞吐量) 。 协调器节点不需要像辅助角色节点一样多的 RAM，但根据 vCore 计数确定 RAM 分配 (如 [超大规模配置) 选项](concepts-hyperscale-configuration-options.md) 中所述，因此 vCore 计数实质上是真正的决策。

### <a name="real-time-analytics-use-case"></a>实时分析用例

Total Vcore：当工作数据适合 RAM 时，超大规模 (Citus 的线性性能提高) 与辅助角色内核数成正比。 若要根据需要确定正确的 Vcore 数，请考虑单节点数据库中查询的当前延迟，以及超大规模 (Citus) 中所需的延迟时间。 将当前延迟除以必需延迟，对结果进行舍入。

辅助角色 RAM：最佳情况是提供足够的内存，使大部分工作集可容纳在内存中。 应用程序使用的查询类型会影响内存要求。 您可以运行对查询进行分析，以确定其所需的内存量。 请记住，Vcore 和 RAM 按 [超大规模配置选项](concepts-hyperscale-configuration-options.md) 一文中所述进行扩展。

## <a name="scale-a-hyperscale-citus-server-group"></a>缩放超大规模 (Citus) 服务器组

Azure Database for PostgreSQL-超大规模 (Citus) 提供自助扩展，以处理增加的负载。 Azure 门户可以轻松地添加新的辅助角色节点，以及增加现有节点的 Vcore。 添加节点会导致不停机，甚至会将分片移动到新节点 (称为 [分片重新平衡](#rebalance-shards)) 发生，而不会中断查询。

### <a name="add-worker-nodes"></a>添加辅助角色节点

若要添加节点，请在超大规模 (Citus) 服务器组中转到 " **计算 + 存储** " 选项卡。  拖动 **辅助节点计数** 的滑块将更改该值。

:::image type="content" source="./media/howto-hyperscale-scaling/01-sliders-workers.png" alt-text="资源滑块":::

单击 " **保存** " 按钮以使更改的值生效。

> [!NOTE]
> 增加并保存后，使用滑块无法减少工作节点数。

#### <a name="rebalance-shards"></a>重新平衡分片

若要利用新添加的节点，必须重新平衡分布式表 [分片](concepts-hyperscale-distributed-data.md#shards)，这意味着将某些分片从现有节点移到新节点。 首先，验证新辅助角色是否已成功完成设置。 然后，通过使用 psql 连接到群集协调器节点并运行来启动分片 rebalancer：

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

`rebalance_table_shards`函数间重新平衡其参数中命名的表的[归置](concepts-hyperscale-colocation.md)组中的所有表。 因此，您不必为每个分布式表调用函数，只需在每个归置组的代表性表中调用它。

### <a name="increase-or-decrease-vcores-on-nodes"></a>增加或减少节点上的 Vcore

除了添加新节点以外，还可以增加现有节点的功能。 向上和向下调整计算容量可用于性能试验，以及对流量需求的短期或长期更改。

若要更改所有辅助节点的 Vcore，请在 "**配置 (每个辅助角色节点) **下调整**vcore**滑块。 协调器节点的 Vcore 可以独立进行调整。 调整 "**配置 (协调器" 节点**下的 " **vcore** " 滑块) 。

## <a name="next-steps"></a>后续步骤

- 了解有关服务器组 [性能选项](concepts-hyperscale-configuration-options.md)的详细信息。
