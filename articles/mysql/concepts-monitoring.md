---
title: "在 Azure Database for MySQL 中进行监视 | Microsoft Docs"
description: "本文介绍了用于对 Azure Database for MySQL 进行监视并发出警报的指标，包括 CPU、限制、存储和连接统计信息。"
services: mysql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/24/2017
ms.openlocfilehash: 9af447d54faa8ee96e4b79beb274b437eea57626
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2017
---
# <a name="monitoring-in-azure-database-for-mysql"></a>在 Azure Database for MySQL 中进行监视
监视服务器的相关数据有助于排查工作负荷故障及优化工作负荷。 Azure Database for MySQL 提供了各种指标来帮助用户深入了解为 MySQL 服务器提供支持的资源的行为。 

## <a name="metrics"></a>指标
所有 Azure 指标的频率都是一分钟，每个指标提供 30 天的历史记录。 

可针对指标配置警报。 有关分步指南，请参阅[如何设置警报](howto-alert-on-metric.md)。 

其他任务包括设置自动操作、执行高级分析和存档历史记录。 有关详细信息，请参阅 [Azure 指标概述](../monitoring-and-diagnostics/monitoring-overview-metrics.md)。

### <a name="list-of-metrics"></a>指标列表
这些指标适用于 Azure Database for MySQL：

|指标|指标显示名称|计价单位|说明|
|---|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|使用的 CPU 百分比。|
|compute_limit|计算单位限制|计数|此服务器的最大计算单元数|
|compute_consumption_percent|计算单位百分比|百分比|所用计算单元数占服务器最大计算单元数的百分比。|
|memory_percent|内存百分比|百分比|使用的内存百分比。|
|io_consumption_percent|IO 百分比|百分比|使用的 IO 百分比。|
|storage_percent|存储百分比|百分比|所用存储占服务器最大存储的百分比。|
|storage_used|已用的存储量|字节|使用的存储量。 服务使用的存储包括数据库文件、事务日志和服务器日志。|
|storage_limit|存储限制|字节|此服务器的最大存储。|
|active_connections|活动连接总数|计数|服务器的活动连接数。|
|connections_failed|失败连接总数|计数|服务器的失败连接数。|


> [!NOTE]
> 计算单元由内存和 CPU 构成。 计算单元百分比为“最大值(内存%, CPU%)”。 检查内存和 CPU 图表，找出是哪项导致计算单元百分比发生更改。 有关详细信息，请参阅[计算单元](concepts-compute-unit-and-storage.md)。

## <a name="next-steps"></a>后续步骤
- 有关分步指南，请参阅[如何设置警报](howto-alert-on-metric.md)。 
- 有关如何使用 Azure 门户、REST API 或 CLI 访问和导出指标的详细信息，请参阅 [Azure 指标概述](../monitoring-and-diagnostics/monitoring-overview-metrics.md)。
