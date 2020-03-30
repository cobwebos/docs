---
title: 缩放服务器组 - 超大规模（Citus） - 用于后格雷SQL的 Azure 数据库
description: 调整服务器组内存、磁盘和 CPU 资源，以应对增加的负载
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 3/16/2020
ms.openlocfilehash: fa48ca287c248155a0271b5134be782d8db1c785
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063105"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>缩放超大规模（Citus）服务器组

用于 PostgreSQL - 超大规模 （Citus） 的 Azure 数据库提供自助服务扩展，以处理增加的负载。 Azure 门户便于添加新辅助节点，并增加现有节点的 vCore。

## <a name="add-worker-nodes"></a>添加辅助角色节点

要添加节点，请转到超大规模 （Citus） 服务器组中的 **"配置**"选项卡。  拖动**辅助角色节点计数**的滑块会更改值。

![资源滑块](./media/howto-hyperscale-scaling/01-sliders-workers.png)

单击"**保存**"按钮以使更改的值生效。

> [!NOTE]
> 增加并保存后，无法使用滑块减少辅助节点的数量。

### <a name="rebalance-shards"></a>重新平衡分片

要利用新添加的节点，必须重新平衡分布式表[分片](concepts-hyperscale-distributed-data.md#shards)，这意味着将一些分片从现有节点移动到新节点。 首先验证新工作人员是否已成功完成预配。 然后启动分片重新平衡器，通过连接到具有 psql 的群集协调器节点并运行：

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

函数`rebalance_table_shards`重新平衡其参数中命名的表[的主机代管](concepts-hyperscale-colocation.md)组中的所有表。 因此，您不必为每个分布式表调用函数，只需在每个主机代管组中调用它。

## <a name="increase-or-decrease-vcores-on-nodes"></a>增加或减少节点上的 vCore

> [!NOTE]
> 此功能目前处于预览状态。 要请求更改服务器组中的节点的 vCore，[请与 Azure 支持 。](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

除了添加新节点外，还可以增加现有节点的功能。 上下调整计算容量可用于性能实验以及流量需求的短期或长期变化。

要更改所有辅助节点的 vCore，请调整配置下的**vCores**滑块 **（每个辅助节点）。** 协调节点的 vCore 可以独立调整。 单击**协调器节点**下的 **"更改配置**"链接。 将显示一个对话框，其中包含协调器的 vCore 和存储容量的滑块。 根据需要更改滑块，然后选择 **"确定**"。

## <a name="next-steps"></a>后续步骤

了解有关服务器组[性能选项](concepts-hyperscale-configuration-options.md)的详细信息。
