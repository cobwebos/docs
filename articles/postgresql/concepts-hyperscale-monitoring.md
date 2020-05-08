---
title: 监视和优化-超大规模（Citus）-Azure Database for PostgreSQL
description: 本文介绍 Azure Database for PostgreSQL-超大规模（Citus）中的监视和优化功能
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fb24100eaebc54b98adf2f09fa3e557e4d6fa0be
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82580655"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---hyperscale-citus"></a>监视和优化 Azure Database for PostgreSQL-超大规模（Citus）

监视服务器的相关数据有助于排查工作负荷故障及优化工作负荷。 超大规模（Citus）提供了多种监视选项，可让你深入了解服务器组中节点的行为。

## <a name="metrics"></a>指标

超大规模（Citus）为服务器组中的每个节点提供指标。 这些指标有助于深入了解支持资源的行为。 每项指标以一分钟为频率发出，历史记录长达 30 天。

除了查看指标图形，还可以配置警报。 有关分步指南，请参阅[如何设置警报](howto-hyperscale-alert-on-metric.md)。  其他任务包括设置自动操作、运行高级分析和存档历史记录。 有关详细信息，请参阅 [Azure 指标概述](../monitoring-and-diagnostics/monitoring-overview-metrics.md)。

### <a name="list-of-metrics"></a>指标列表

这些指标适用于超大规模（Citus）节点：

|指标|指标显示名称|单位|说明|
|---|---|---|---|
|active_connections|活动连接数|计数|服务器的活动连接数。|
|cpu_percent|CPU 百分比|百分比|使用的 CPU 百分比。|
|iops|IOPS|计数|查看[IOPS 定义](../virtual-machines/linux/premium-storage-performance.md#iops)和[超大规模吞吐量](concepts-hyperscale-configuration-options.md)|
|memory_percent|内存百分比|百分比|使用的内存百分比。|
|network_bytes_ingress|网络传入|字节|跨活动连接数的网络传入。|
|network_bytes_egress|网络传出|字节|跨活动连接数的网络传出。|
|storage_percent|存储百分比|百分比|所用存储占服务器最大存储的百分比。|
|storage_used|已用的存储量|字节|使用的存储量。 服务使用的存储可能包括数据库文件、事务日志和服务器日志。|

Azure 不提供整体群集的聚合指标，但可以将多个节点的指标放置在同一图形上。

## <a name="next-steps"></a>后续步骤

- 请参阅[如何设置警报](howto-hyperscale-alert-on-metric.md)，以获取针对指标创建警报的指南。
