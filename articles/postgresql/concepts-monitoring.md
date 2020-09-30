---
title: 监视和优化 - Azure Database for PostgreSQL（单一服务器）
description: 本文介绍了 Azure Database for PostgreSQL（单一服务器）中的监视和优化功能。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/10/2020
ms.openlocfilehash: 2f57d20df6adbb7c1a0c73958cc10035126e29b8
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91530980"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---single-server"></a>监视和优化 Azure Database for PostgreSQL（单一服务器）
监视服务器的相关数据有助于排查工作负荷故障及优化工作负荷。 Azure Database for PostgreSQL 提供各种监视选项，帮助用户深入了解服务器的行为。

## <a name="metrics"></a>指标
Azure Database for PostgreSQL 提供了各种指标来帮助用户深入了解为 PostgreSQL 服务器提供支持的资源的行为。 每项指标以一分钟为频率发出，[历史记录长达 93 天](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#retention-of-metrics)。 可针对指标配置警报。 有关分步指南，请参阅[如何设置警报](howto-alert-on-metric.md)。 其他任务包括设置自动操作、执行高级分析和存档历史记录。 有关详细信息，请参阅 [Azure 指标概述](../monitoring-and-diagnostics/monitoring-overview-metrics.md)。

### <a name="list-of-metrics"></a>指标列表
这些指标适用于 Azure Database for PostgreSQL：

|指标|指标显示名称|计价单位|说明|
|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|使用的 CPU 百分比。|
|memory_percent|内存百分比|百分比|使用的内存百分比。|
|io_consumption_percent|IO 百分比|百分比|使用的 IO 百分比。 （不适用于基本层服务器。）|
|storage_percent|存储百分比|百分比|所用存储占服务器最大存储的百分比。|
|storage_used|已用的存储量|字节|使用的存储量。 服务使用的存储可能包括数据库文件、事务日志和服务器日志。|
|storage_limit|存储限制|字节|此服务器的最大存储。|
|serverlog_storage_percent|服务器日志存储空间百分比|百分比|所用服务器日志存储占服务器最大服务器日志存储的百分比。|
|serverlog_storage_usage|服务器日志已用的存储量|字节|使用的服务器日志存储量。|
|serverlog_storage_limit|服务器存储空间上限|字节|此服务器的最大服务器日志存储。|
|active_connections|活动连接数|计数|服务器的活动连接数。|
|connections_failed|失败的连接数|Count|所建立的失败的连接数。|
|network_bytes_egress|网络传出|字节|跨活动连接的网络传出。|
|network_bytes_ingress|网络传入|字节|跨活动连接的网络传入。|
|backup_storage_used|使用的备份存储|字节|已使用的备份存储量。 此指标表示根据为服务器设置的备份保留期保留的所有完整数据库备份、差异备份和日志备份所消耗的存储的总和。 备份的频率由服务管理，并在[概念文章](concepts-backup.md)中进行了说明。 对于异地冗余存储，备份存储使用率是本地冗余存储的两倍。|
|pg_replica_log_delay_in_bytes|副本的最大滞后时间|字节|主副本和最滞后副本之间的延迟（以字节为单位）。 此指标仅在主服务器上可用。|
|pg_replica_log_delay_in_seconds|副本滞后时间|秒|自上次重放事务以来所经历的时间。 此指标仅适用于副本服务器。|

## <a name="server-logs"></a>服务器日志
可以在服务器上启用日志记录。 这些资源日志可发送到[ Azure Monitor 日志](../azure-monitor/log-query/log-query-overview.md)、事件中心和存储帐户。 若要了解有关日志记录的详细信息，请访问[服务器日志](concepts-server-logs.md)页。

## <a name="query-store"></a>查询存储
[查询存储](concepts-query-store.md)可以随着时间的推移跟踪查询性能，包括查询运行时统计信息和等待事件。 此功能将查询运行时性能信息保留在 query_store 架构下名为 azure_sys 的一个系统数据库中。 你可以通过各种配置旋钮控制数据的收集和存储。

## <a name="query-performance-insight"></a>Query Performance Insight
[Query Performance Insight](concepts-query-performance-insight.md) 与查询存储协同工作以提供可以从 Azure 门户访问的可视化效果。 这些图表使你能够识别对性能造成影响的关键查询。 Query Performance Insight 可以从 Azure Database for PostgreSQL 服务器门户页的“支持 + 疑难解答”部分中进行访问。

## <a name="performance-recommendations"></a>性能建议
[性能建议](concepts-performance-recommendations.md)功能可以确定提高工作负荷性能的机会。 性能建议为你提供了有关创建新索引的建议，这些索引可能会提高工作负荷的性能。 若要生成索引建议，该功能会考虑各种数据库特征，包括其架构以及如“查询存储”报告的工作负载。 实施任何性能建议后，客户应测试性能以评估这些更改的影响。 

## <a name="planned-maintenance-notification"></a>计划内维护通知

“计划内维护通知”允许你接收有关 Azure Database for PostgreSQL - 单一服务器即将进行的计划内维护的警报。 这些通知与[服务运行状况](../service-health/overview.md)计划内维护集成，允许你在同一位置查看你的订阅的所有计划内维护。 它还有助于将通知扩展到不同资源组的适当受众，因为你可能有不同的联系人负责不同的资源。 你将在事件发生前的 72 小时收到有关即将进行的维护的通知。

> [!Note]
> 我们将尽一切努力为所有事件提供**计划内维护通知** 72 小时通知。 但是，对于关键或安全修补程序，通知可能会在事件快要发生时更晚一点发送，或者会被忽略。

### <a name="to-receive-planned-maintenance-notification"></a>接收计划内维护通知

1. 在[门户](https://portal.azure.com)中，选择“服务运行状况”。
2. 在“警报”部分中，选择“运行状况警报”。
3. 选择“+ 添加服务运行状况警报”，并填写字段。
4. 填写所需的字段。 
5. 选择“事件类型”，然后选择“计划内维护”或“全选”
6. 在“操作组”中，定义接收警报的方式（获取电子邮件、触发逻辑应用等）。  
7. 确保“创建后启用规则”设置为“是”。
8. 选择“创建警报规则”以完成警报

有关如何创建服务运行状况警报的详细步骤，请参阅 [创建有关服务通知的活动日志警报](../service-health/alerts-activity-log-service-notifications.md)。

> [!IMPORTANT]
> 计划内维护通知当前在美国西部地区 **以外** 的所有区域中提供预览版

## <a name="next-steps"></a>后续步骤
- 有关如何基于指标创建警报的指南，请参阅[如何设置警报](howto-alert-on-metric.md)。
- 若要深入了解如何使用 Azure 门户、REST API 或 CLI 访问和导出指标，请参阅 [Azure 指标概述](../monitoring-and-diagnostics/monitoring-overview-metrics.md)。
- 阅读我们的博客，了解[监控服务器的最佳做法](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/)。
