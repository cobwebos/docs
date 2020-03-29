---
title: 监视器和调子 - 超大规模（Citus） - 用于后格雷SQL的 Azure 数据库
description: 本文介绍 Azure 数据库中用于 PostgreSQL - 超大规模 （Citus） 的功能的监视和调优功能
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d2e9fcd6f6292c1da76e725e90deda4547b3682d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975510"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---hyperscale-citus"></a>监视和调整 Azure 数据库，用于后格雷SQL - 超大规模（Citus）

监视服务器的相关数据有助于排查工作负荷故障及优化工作负荷。 超大规模 （Citus） 提供各种监视选项，以便深入了解服务器组中节点的行为。

## <a name="metrics"></a>指标

超大规模 （Citus） 为服务器组中的每个节点提供指标。 这些指标有助于深入了解支持资源的行为。 每项指标以一分钟为频率发出，历史记录长达 30 天。

除了查看指标的图表外，您还可以配置警报。 有关分步指南，请参阅[如何设置警报](howto-hyperscale-alert-on-metric.md)。  其他任务包括设置自动操作、运行高级分析和存档历史记录。 有关详细信息，请参阅 [Azure 指标概述](../monitoring-and-diagnostics/monitoring-overview-metrics.md)。

### <a name="list-of-metrics"></a>指标列表

这些指标可用于超大规模 （Citus） 节点：

|指标|指标显示名称|单位|描述|
|---|---|---|---|
|active_connections|活动连接数|Count|服务器的活动连接数。|
|cpu_percent|CPU 百分比|百分比|使用的 CPU 百分比。|
|iops|IOPS|Count|查看[IOPS 定义](../virtual-machines/linux/premium-storage-performance.md#iops)和[超大规模吞吐量](concepts-hyperscale-configuration-options.md)|
|memory_percent|内存百分比|百分比|使用的内存百分比。|
|network_bytes_ingress|网络传入|字节|跨活动连接数的网络传入。|
|network_bytes_egress|网络传出|字节|跨活动连接数的网络传出。|
|storage_percent|存储百分比|百分比|所用存储占服务器最大存储的百分比。|
|storage_used|已用的存储量|字节|使用的存储量。 服务使用的存储可能包括数据库文件、事务日志和服务器日志。|

Azure 不为整个群集提供聚合指标，但多个节点的指标可以放在同一图形上。

## <a name="next-steps"></a>后续步骤

- [了解如何设置警报](howto-hyperscale-alert-on-metric.md)，以提供有关在指标上创建警报的指导。
