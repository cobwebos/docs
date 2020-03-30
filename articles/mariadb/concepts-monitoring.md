---
title: 监视 - MariaDB 的 Azure 数据库
description: 本文介绍了用于对 Azure Database for MariaDB 进行监视并发出警报的指标，包括 CPU、存储和连接统计信息。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 20a2066dc0228fe9c2fee09387d96bf6aafeb32f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531968"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>在 Azure Database for MariaDB 中进行监视
监视服务器的相关数据有助于排查工作负荷故障及优化工作负荷。 Azure Database for MariaDB 提供了各种指标来帮助用户深入了解服务器的行为。

## <a name="metrics"></a>指标
所有 Azure 指标的频率都是一分钟，每个指标提供 30 天的历史记录。 可针对指标配置警报。 其他任务包括设置自动操作、执行高级分析和存档历史记录。 有关详细信息，请参阅 [Azure 指标概述](../monitoring-and-diagnostics/monitoring-overview-metrics.md)。

有关分步指南，请参阅[如何设置警报](howto-alert-metric.md)。

### <a name="list-of-metrics"></a>指标列表
以下指标适用于 Azure Database for MariaDB：

|指标|指标显示名称|单位|描述|
|---|---|---|---|
|cpu_percent|CPU 百分比|百分比|使用的 CPU 百分比。|
|memory_percent|内存百分比|百分比|使用的内存百分比。|
|io_consumption_percent|IO 百分比|百分比|使用的 IO 百分比。|
|storage_percent|存储百分比|百分比|所用存储占服务器最大存储的百分比。|
|storage_used|已用的存储量|字节|使用的存储量。 服务使用的存储可能包括数据库文件、事务日志和服务器日志。|
|serverlog_storage_percent|服务器日志存储空间百分比|百分比|所用的服务器日志存储占服务器的服务器日志最大存储的百分比。|
|serverlog_storage_usage|服务器日志已用的存储量|字节|使用的服务器日志存储量。|
|serverlog_storage_limit|服务器存储空间上限|字节|此服务器的最大服务器日志存储。|
|storage_limit|存储限制|字节|此服务器的最大存储。|
|active_connections|活动连接数|Count|服务器的活动连接数。|
|connections_failed|失败的连接数|Count|服务器的失败连接数。|
|network_bytes_egress|网络传出|字节|跨活动连接数的网络传出。|
|network_bytes_ingress|网络传入|字节|跨活动连接数的网络传入。|

## <a name="server-logs"></a>服务器日志

可以在服务器上启用慢查询日志。 这些日志也可通过 Azure Monitor 日志、事件中心和存储帐户中的 Azure 诊断日志获得。 若要了解有关日志记录的详细信息，请访问 [服务器日志](concepts-server-logs.md)页。

## <a name="query-store"></a>查询存储

[查询存储](concepts-query-store.md)可以随着时间的推移跟踪查询性能，包括查询运行时统计信息和等待事件。 此功能将查询运行时性能信息保留在 **mysql** 架构中。 你可以通过各种配置旋钮控制数据的收集和存储。

## <a name="query-performance-insight"></a>查询性能见解

[Query Performance Insight](concepts-query-performance-insight.md) 与查询存储协同工作以提供可以从 Azure 门户访问的可视化效果。 这些图表使你能够识别对性能造成影响的关键查询。 查询性能见解可在 MariaDB 服务器门户页的 Azure 数据库的**智能性能**部分中访问。

## <a name="performance-recommendations"></a>性能建议

[性能建议](concepts-performance-recommendations.md)功能可以确定提高工作负荷性能的机会。 性能建议为您提供了创建新索引的建议，这些索引有可能提高工作负载的性能。 若要生成索引建议，该功能会考虑各种数据库特征，包括其架构以及如“查询存储”报告的工作负载。 实施任何性能建议后，客户应测试性能以评估这些更改的影响。

## <a name="planned-maintenance-notification"></a>计划维护通知

**计划维护通知**允许您接收即将计划对 MariaDB Azure 数据库进行的维护警报。 这些通知与[服务运行状况的计划](../service-health/overview.md)维护集成，允许您在一个位置查看订阅的所有计划维护。 它还有助于将通知扩展到不同资源组的正确访问群体，因为您可能有不同的联系人负责不同的资源。 您将在活动前 72 小时收到有关即将进行的维护的通知。

> [!Note]
> 我们将尽力为所有事件提供**计划维护通知**72 小时通知。 但是，在严重或安全修补程序的情况下，可能会将通知发送得更接近事件或被省略。

### <a name="to-receive-planned-maintenance-notification"></a>接收计划维护通知

1. 在[门户](https://portal.azure.com)中，选择“服务运行状况”****。
2. 在“警报”**** 部分中，选择“运行状况警报”****。
3. 选择 **= 添加服务运行状况警报**并填写字段。
4. 填写所需的字段。 
5. 选择**事件类型**，选择 **"计划维护**"或 **"全部"选择**
6. 在 **"操作"组中**定义您希望如何接收警报（获取电子邮件、触发逻辑应用等）  
7. 确保创建时的启用规则设置为"是"。
8. 选择 **"创建警报规则**"以完成警报

有关如何创建**服务运行状况警报**的详细步骤，请参阅[在服务通知上创建活动日志警报](../service-health/alerts-activity-log-service-notifications.md)。

> [!IMPORTANT]
> 计划维护通知当前处于预览状态

## <a name="next-steps"></a>后续步骤

- 若要深入了解如何使用 Azure 门户、REST API 或 CLI 访问和导出指标，请参阅 [Azure 指标概述](../monitoring-and-diagnostics/monitoring-overview-metrics.md)。
  - 有关如何基于指标创建警报的指南，请参阅[如何设置警报](howto-alert-metric.md)。
