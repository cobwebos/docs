---
title: Scale server 组-超大规模（Citus）-Azure Database for PostgreSQL
description: 调整服务器组内存、磁盘和 CPU 资源来处理增加的负载
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 3/16/2020
ms.openlocfilehash: f8e8e1672f754e843a3bd1c75d496599d31e5f11
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584001"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>缩放超大规模（Citus）服务器组

Azure Database for PostgreSQL 超大规模（Citus）提供了自助扩展，以处理增加的负载。 Azure 门户可以轻松地添加新的辅助角色节点，以及增加现有节点的 Vcore。

## <a name="add-worker-nodes"></a>添加辅助角色节点

若要添加节点，请参阅超大规模（Citus）服务器组中的 "**配置**" 选项卡。  拖动**辅助节点计数**的滑块将更改该值。

![资源滑块](./media/howto-hyperscale-scaling/01-sliders-workers.png)

单击 "**保存**" 按钮以使更改的值生效。

> [!NOTE]
> 增加并保存后，使用滑块无法减少工作节点数。

### <a name="rebalance-shards"></a>重新平衡分片

若要利用新添加的节点，必须重新平衡分布式表[分片](concepts-hyperscale-distributed-data.md#shards)，这意味着将某些分片从现有节点移到新节点。 首先，验证新辅助角色是否已成功完成设置。 然后，通过使用 psql 连接到群集协调器节点并运行来启动分片 rebalancer：

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

`rebalance_table_shards`函数间重新平衡其参数中命名的表的[归置](concepts-hyperscale-colocation.md)组中的所有表。 因此，您不必为每个分布式表调用函数，只需在每个归置组的代表性表中调用它。

## <a name="increase-or-decrease-vcores-on-nodes"></a>增加或减少节点上的 Vcore

> [!NOTE]
> 此功能目前以预览版提供。 若要在 Vcore 中为服务器组中的节点请求更改，请[联系 Azure 支持](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

除了添加新节点以外，还可以增加现有节点的功能。 向上和向下调整计算容量可用于性能试验，以及对流量需求的短期或长期更改。

若要更改所有辅助节点的 Vcore，请在 "**配置（每个工作节点）**" 下调整**vcore**滑块。 协调器节点的 Vcore 可以独立进行调整。 单击 "**协调器" 节点**下的 "**更改配置**" 链接。 将显示一个对话框，其中包含协调器的 Vcore 和存储容量。 根据需要更改滑杆，然后选择 **"确定"**。

## <a name="next-steps"></a>后续步骤

了解有关服务器组[性能选项](concepts-hyperscale-configuration-options.md)的详细信息。
