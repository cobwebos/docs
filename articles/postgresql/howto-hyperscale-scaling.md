---
title: Scale server 组-超大规模（Citus）-Azure Database for PostgreSQL
description: 调整服务器组内存、磁盘和 CPU 资源来处理增加的负载
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: bec2a40d8cf5fb178418ec6bb59a52a0bfe3eb8c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280438"
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

`rebalance_table_shards` 函数间重新平衡其参数中命名的表的[归置](concepts-hyperscale-colocation.md)组中的所有表。 因此，您不必为每个分布式表调用函数，只需在每个归置组的代表性表中调用它。

## <a name="increase-vcores"></a>增加 Vcore

除了添加新节点以外，还可以增加现有节点的功能。 此功能目前以预览版提供，若要请求增加服务器组中节点的 Vcore，请[联系 Azure 支持](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## <a name="next-steps"></a>后续步骤

了解有关服务器组[性能选项](concepts-hyperscale-configuration-options.md)的详细信息。
