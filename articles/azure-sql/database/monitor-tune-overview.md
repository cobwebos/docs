---
title: 监视和性能优化
description: 了解 Azure SQL 数据库和 Azure SQL 托管实例中的监视和性能优化功能和方法。
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 6e17e2a6e5c9151080facc3a2dd8c1a18c0580fe
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2020
ms.locfileid: "85982145"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL 数据库和 Azure SQL 托管实例中的监视和性能优化
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

若要监视 Azure SQL 数据库和 Azure SQL 托管实例中的数据库的性能，请先监视工作负荷所使用的 CPU 和 IO 资源相对于选择特定服务层和性能级别所选择的数据库性能级别。 若要完成此操作，Azure SQL 数据库和 Azure SQL 托管实例会发出资源度量，可在 Azure 门户中查看或通过使用以下 SQL Server 管理工具之一进行查看： [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is)或[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) （SSMS）。

Azure SQL 数据库提供了多个数据库顾问来提供智能性能优化建议和自动优化选项以提高性能。 此外，Query Performance Insight 还详细介绍了有关单数据库和池中数据库的最多 CPU 和 IO 使用情况的查询。

Azure SQL 数据库和 Azure SQL 托管实例提供由人工智能提供支持的高级监视和优化功能，以帮助你对数据库和解决方案的性能进行故障排除和最大化。 你可以选择将这些[智能见解](intelligent-insights-overview.md)和其他数据库资源日志和指标的[流式导出](metrics-diagnostic-telemetry-logging-streaming-export-configure.md)配置为多个目标中的一个，以实现消耗和分析，尤其是使用[SQL Analytics](../../azure-monitor/insights/azure-sql.md)）。 Azure SQL Analytics 是一种高级的云监视解决方案，用于在单个视图中大规模和跨多个订阅监视所有数据库的性能。 有关可导出的日志和指标的列表，请参阅导出的[诊断遥测](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#diagnostic-telemetry-for-export)

最后，SQL Server 具有其自己的监视和诊断功能，SQL 数据库和 SQL 托管实例利用这些功能，如[查询存储](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)和[动态管理视图（dmv）](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)。 请参阅[监视使用 dmv](monitoring-with-dmvs.md) for scripts 监视各种性能问题。

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Azure 门户中的监视和优化功能

在 Azure 门户中，Azure SQL 数据库和 Azure SQL 托管实例提供资源指标监视。 此外，Azure SQL 数据库还提供数据库顾问和 Query Performance Insight 提供查询优化建议和查询性能分析。 最后，在 Azure 门户中，可以为[逻辑 SQL 服务器](logical-servers.md)及其单个和共用数据库启用 "自动"。

### <a name="azure-sql-database-and-azure-sql-managed-instance-resource-monitoring"></a>Azure SQL 数据库和 Azure SQL 托管实例资源监视

可以在 "**指标**" 视图中的 "Azure 门户" 快速监视各种资源指标。 这些指标使你可以查看数据库是否达到了100% 的处理器、内存或 IO 资源。 高 DTU 或处理器百分比以及高 IO 百分比表明工作负荷可能需要更多的 CPU 或 IO 资源。 它还可能指示需要优化的查询。

  ![资源指标](./media/monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors-in-azure-sql-database"></a>Azure SQL 数据库中的数据库顾问

Azure SQL Database 包含为单一数据库和池中的数据库提供性能优化建议的[数据库顾问](database-advisor-implement-performance-recommendations.md)。 这些建议在 Azure 门户中提供，也可以使用[PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseadvisor)提供。 你还可以启用[自动优化](automatic-tuning-overview.md)，使 Azure SQL 数据库能够自动实现这些优化建议。

### <a name="query-performance-insight-in-azure-sql-database"></a>在 Azure SQL 数据库中 Query Performance Insight

[Query Performance Insight](query-performance-insight-use.md)显示单个数据库和入池数据库的最消耗和最长运行查询的 Azure 门户的性能。

## <a name="generate-intelligent-assessments-of-performance-issues"></a>生成性能问题的智能评估

[智能见解](intelligent-insights-overview.md)Azure sql 数据库和 azure sql 托管实例使用内置智能通过人工智能持续监视数据库使用情况，并检测导致性能不佳的干扰性事件。 智能见解会根据查询执行等待时间、错误或超时，自动检测数据库中的性能问题。 检测到后，将执行详细分析，生成资源日志（称为 SQLInsights），并对[问题进行智能评估](intelligent-insights-troubleshoot-performance.md)。 此评估包含对数据库性能问题的根本原因分析，以及为性能改进而提供的可行性建议。

Intelligent Insights 是 Azure 内置智能的一项独特功能，提供以下功能价值：

- 主动监视
- 定制的性能见解
- 数据库性能下降的早期检测
- 已检测出问题的根本原因分析
- 性能改进建议
- 数以十万计的数据库上的横向扩展功能
- 对 DevOps 资源和总拥有成本的积极影响

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>启用指标和资源日志的流式导出

可以启用和配置[诊断遥测的流式导出](metrics-diagnostic-telemetry-logging-streaming-export-configure.md)到几个目标之一，包括智能见解资源日志。 使用[SQL 分析](../../azure-monitor/insights/azure-sql.md)和其他功能来使用此附加的诊断遥测来识别和解决性能问题。

将诊断设置配置为将单个数据库、共用数据库、弹性池、托管实例和实例数据库的指标和资源日志的类别流式传输到以下 Azure 资源之一。

### <a name="log-analytics-workspace-in-azure-monitor"></a>Azure Monitor 中的 Log Analytics 工作区

可以将指标和资源日志流式传输到[Azure Monitor 中的 Log Analytics 工作区](../../azure-monitor/platform/resource-logs-collect-workspace.md)。 SQL Analytics 可以使用此处流式传输的数据， [SQL Analytics](../../azure-monitor/insights/azure-sql.md)是一种仅限云的监视解决方案，它提供对数据库的智能监视，其中包括性能报告、警报和缓解建议。 流式传输到 Log Analytics 工作区的数据可以使用收集的其他监视数据进行分析，还可以利用其他 Azure Monitor 功能，如警报和可视化效果。

### <a name="azure-event-hubs"></a>Azure 事件中心

可以将指标和资源日志流式传输到[Azure 事件中心](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)。 将诊断遥测流式传输到事件中心，以提供以下功能：

- **将日志流式传输到第三方日志记录和遥测系统**

  将所有指标和资源日志流式传输到单个事件中心，以便将日志数据传递给第三方 SIEM 或 log analytics 工具。
- **生成自定义遥测和日志记录平台**

  事件中心高度可缩放的发布-订阅特性使你可以灵活地将指标和资源日志引入自定义遥测平台。 有关详细信息，请参阅 [Designing and Sizing a Global Scale Telemetry Platform on Azure Event Hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)（在 Azure 事件中心设计全球规模的遥测平台并设置其大小）。
- **通过将数据流式传输到 Power BI 来查看服务运行状况**

  使用事件中心、流分析和 Power BI 将诊断数据转换为对 Azure 服务的近乎实时见解。 有关此解决方案的详细信息，请参阅[流分析和 Power BI：用于流式处理数据的实时分析仪表板](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard)。

### <a name="azure-storage"></a>Azure 存储

将指标和资源日志流式传输到[Azure 存储](../../azure-monitor/platform/resource-logs-collect-storage.md)。 使用 Azure 存储将大量诊断遥测数据存档，只需一小部分成本即可实现以前两个流式处理选项。

## <a name="use-extended-events"></a>使用扩展事件 

此外，还可以在 SQL Server 中使用[扩展事件](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events)进行高级监视和故障排除。 扩展事件体系结构使用户能够收集必要的数据，以进行故障排除或确定性能问题所需的数据量。 有关使用 Azure SQL 数据库中的扩展事件的信息，请参阅[AZURE Sql 数据库中的扩展事件](xevent-db-diff-from-svr.md)。

## <a name="next-steps"></a>后续步骤

- 有关单一数据库和池数据库的智能性能建议的详细信息，请参阅[数据库顾问性能建议](database-advisor-implement-performance-recommendations.md)。
- 有关借助自动诊断和性能问题的根本原因分析自动监视数据库性能的详细信息，请参阅 [Azure SQL 智能见解](intelligent-insights-overview.md)。
