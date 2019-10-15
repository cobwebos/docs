---
title: 缩放 Azure Database for PostgreSQL 超大规模（Citus）服务器组
description: 调整服务器组内存、磁盘和 CPU 资源来处理增加的负载
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/17/2019
ms.openlocfilehash: a69b4988a5ee835381de986edaa5899b09aa9e4e
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262487"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>缩放超大规模（Citus）服务器组

Azure Database for PostgreSQL 超大规模（Citus）提供了自助扩展，以处理增加的负载。 Azure 门户可以轻松地添加新的辅助角色节点。

为此，请跳到超大规模（Citus）服务器组中的 "**配置**" 选项卡。
拖动**辅助角色节点计数**的滑块以更改值。

![资源滑块](./media/howto-hyperscale-scaling/01-sliders-workers.png)

单击 "保存" 按钮以使更改的值生效。

> [!NOTE]
> 增加并保存后，使用滑块无法减少工作节点数。
>
> 此外，还不能在协调器或具有此用户界面的工作线程上调整 Vcore 和存储。 如果需要在协调器或工作节点上缩放计算，请打开支持票证。

若要利用新添加的节点，必须重新平衡分布式表[分片](concepts-hyperscale-distributed-data.md#shards)，这意味着将某些分片从现有节点移到新节点。 若要启动分片 rebalancer，请连接到具有 psql 的群集协调器节点并运行：

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

`rebalance_table_shards`函数间重新平衡其参数中命名的表的[归置](concepts-hyperscale-colocation.md)组中的所有表。 因此，您不必为每个分布式表调用函数，只需在每个归置组的代表性表中调用它。

## <a name="next-steps"></a>后续步骤

了解有关服务器组[性能选项](concepts-hyperscale-configuration-options.md)的详细信息。
