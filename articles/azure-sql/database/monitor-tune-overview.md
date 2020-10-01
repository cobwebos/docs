---
title: 监视和性能优化
description: 概述 Azure SQL 数据库和 Azure SQL 托管实例中的监视和性能优化功能与方法。
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, sstein
ms.date: 09/30/2020
ms.openlocfilehash: 6c8d048d43a16191cc7b1245ad2d686ba2ca22ab
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91596967"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL 数据库与 Azure SQL 托管实例中的监视和性能优化
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

若要监视 Azure SQL 数据库和 Azure SQL 托管实例中某个数据库的性能，首先需要监视工作负荷使用的、相对于在选择特定服务层和性能级别所选的数据库性能级别的 CPU 和 IO 资源。 为了帮助实现此目的，Azure SQL 数据库和 Azure SQL 托管实例会发出资源指标，你可以在 Azure 门户中或使用以下 SQL Server 管理工具之一查看这些指标：[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) 或 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS)。

Azure SQL 数据库提供多个数据库顾问来提供智能性能优化建议以及用于提高性能的自动优化选项。 此外，Query Performance Insight 会显示有关针对单一数据库和共用数据库运行的、CPU 和 IO 占用量最高的查询的详细信息。

Azure SQL 数据库和 Azure SQL 托管实例提供基于人工智能的其他监视和优化功能，以帮助排查数据库和解决方案的性能问题并实现其最高性能。 你可以选择将这些[智能见解](intelligent-insights-overview.md)和其他数据库资源日志和指标的[流式导出](metrics-diagnostic-telemetry-logging-streaming-export-configure.md)配置为多个目标中的一个，以便使用[SQL Analytics](../../azure-monitor/insights/azure-sql.md)。 Azure SQL Analytics 是一种高级云监视解决方案，用于在单个视图中跨多个订阅大规模监视所有数据库的性能。 有关可导出的日志和指标列表，请参阅[可导出的诊断遥测数据](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#diagnostic-telemetry-for-export)

SQL Server 具有其自己的监视和诊断功能，SQL 数据库和 SQL 托管实例利用这些功能，如 (Dmv) 的 [查询存储](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) 和 [动态管理视图 ](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)。 有关用于监视各种性能问题的脚本，请参阅[使用 DMV 进行监视](monitoring-with-dmvs.md)。

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Azure 门户中的监视和优化功能

在 Azure 门户中，Azure SQL 数据库和 Azure SQL 托管实例提供对资源指标的监视。 Azure SQL 数据库提供数据库顾问，Query Performance Insight 提供查询优化建议和查询性能分析。 在 Azure 门户中，可以为 [逻辑 SQL 服务器](logical-servers.md) 及其单个和共用数据库启用自动优化。

> [!NOTE]
> 使用率极低的数据库可能会显示在门户中，其使用时间少于实际值。 由于在将双精度值转换为最接近的整数时，会发出遥测数据，因此，小于0.5 的某些使用金额将舍入为0，这会使发出的遥测精度损失。 有关详细信息，请参阅 [数据库和弹性池度量值舍入为零](#low-database-and-elastic-pool-metrics-rounding-to-zero)。

### <a name="azure-sql-database-and-azure-sql-managed-instance-resource-monitoring"></a>Azure SQL 数据库和 Azure SQL 托管实例资源监视

可以在 Azure 门户上的“指标”视图中快速监视各种资源指标。 通过这些指标，你可以查看数据库是否即将用完 100% 的处理器、内存或 IO 资源。 DTU 或处理器百分比较高以及 IO 百分比较高表明工作负载可能需要更多的 CPU 或 IO 资源。 它还可能指示查询需要优化。

  ![资源指标](./media/monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors-in-azure-sql-database"></a>Azure SQL 数据库中的数据库顾问

Azure SQL 数据库包含针对单一数据库和共用数据库提供性能优化建议的[数据库顾问](database-advisor-implement-performance-recommendations.md)。 这些建议会在 Azure 门户中提供，也可以使用 [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseadvisor) 来查看。 还可以启用[自动优化](automatic-tuning-overview.md)，使 Azure SQL 数据库能够自动实施这些优化建议。

### <a name="query-performance-insight-in-azure-sql-database"></a>Azure SQL 数据库中的 Query Performance Insight

[Query Performance Insight](query-performance-insight-use.md) 在 Azure 门户中显示针对单一数据库和共用数据库运行的、资源消耗量最高且运行时间最长的查询的性能。

### <a name="low-database-and-elastic-pool-metrics-rounding-to-zero"></a>数据库和弹性池度量值舍入为零

从2020年9月开始，使用情况极低的数据库可能会在门户中显示，并且使用情况不会超过实际使用情况。 由于在将双精度值转换为最接近的整数时，会发出遥测数据，因此，小于0.5 的某些使用金额将舍入为0，这会使发出的遥测的粒度降低。

例如：假设有一个1分钟的窗口，其中包含以下四个数据点：0.1、0.1、0.1、0.1、将这些低值向下舍入为0、0、0、0并显示平均0。 如果任何数据点大于0.5，例如：0.1、0.1、0.9、0.1，它们将舍入为0，0，1，0，并显示平均值为0.25。

受影响的数据库指标：
- cpu_percent
- log_write_percent
- workers_percent
- sessions_percent
- physical_data_read_percent
- dtu_consumption_percent2
- xtp_storage_percent

受影响的弹性池指标：
- cpu_percent
- physical_data_read_percent
- log_write_percent
- memory_usage_percent
- data_storage_percent
- peak_worker_percent
- peak_session_percent
- xtp_storage_percent
- allocated_data_storage_percent


## <a name="generate-intelligent-assessments-of-performance-issues"></a>生成性能问题的智能评估

Azure SQL 数据库的[智能见解](intelligent-insights-overview.md)和 Azure SQL 托管实例使用内置智能通过人工智能持续监视数据库使用情况，并检测导致性能不佳的干扰性事件。 Intelligent Insights 可根据查询执行等待时间、错误或超时自动检测数据库的性能问题。 检测到问题后，将执行详细的分析，以生成资源日志（称为 SQLInsights）和[问题的智能评估](intelligent-insights-troubleshoot-performance.md)。 此评估包含对数据库性能问题的根本原因分析，以及为性能改进而提供的可行性建议。

Intelligent Insights 是 Azure 内置智能的一项独特功能，提供以下功能价值：

- 主动监视
- 定制的性能见解
- 数据库性能下降的早期检测
- 已检测出问题的根本原因分析
- 性能改进建议
- 数以十万计的数据库上的横向扩展功能
- 对 DevOps 资源和总拥有成本的积极影响

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>启用指标和资源日志的流式导出

可以启用并配置将[诊断遥测数据（包括智能见解资源日志）流式导出](metrics-diagnostic-telemetry-logging-streaming-export-configure.md)到多个目标之一。 使用 [SQL 分析](../../azure-monitor/insights/azure-sql.md) 和其他功能来使用此附加的诊断遥测来识别和解决性能问题。

可以配置诊断设置，以将单一数据库、共用数据库、弹性池、托管实例和实例数据库的指标和资源日志类别流式传输到以下 Azure 资源之一。

### <a name="log-analytics-workspace-in-azure-monitor"></a>Azure Monitor 中的 Log Analytics 工作区

可将指标和资源日志流式传输到 [Azure Monitor 中的 Log Analytics 工作区](../../azure-monitor/platform/resource-logs-collect-workspace.md)。 SQL Analytics 可以使用此处流式传输的数据， [SQL Analytics](../../azure-monitor/insights/azure-sql.md)是一种仅限云的监视解决方案，它提供对数据库的智能监视，其中包括性能报告、警报和缓解建议。 流式传输到 Log Analytics 工作区的数据可以使用收集的其他监视数据进行分析，还可以利用其他 Azure Monitor 功能，如警报和可视化效果。

### <a name="azure-event-hubs"></a>Azure 事件中心

可将指标和资源日志流式传输到 [Azure 事件中心](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)。 将诊断遥测数据流式传输到事件中心以提供以下功能：

- **将日志流式传输到第三方日志记录和遥测系统**

  将所有指标和资源日志流式传输到单个事件中心，以通过管道将日志数据传送到第三方 SIEM 或日志分析工具。
- **生成自定义遥测和日志记录平台**

  可利用事件中心高度可缩放的发布-订阅功能，灵活地将指标和资源日志引入到自定义遥测平台。 有关详细信息，请参阅 [Designing and Sizing a Global Scale Telemetry Platform on Azure Event Hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)（在 Azure 事件中心设计全球规模的遥测平台并设置其大小）。
- **通过将数据流式传输到 Power BI 来查看服务运行状况**

  使用事件中心、流分析和 Power BI 将诊断数据转换为对 Azure 服务的近乎实时见解。 有关此解决方案的详细信息，请参阅 [流分析和 Power BI：用于流式处理数据的实时分析仪表板](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard) 。

### <a name="azure-storage"></a>Azure 存储

将指标和资源日志流式传输到 [Azure 存储](../../azure-monitor/platform/resource-logs-collect-storage.md)。 使用 Azure 存储存档大量诊断遥测数据，并且成本只是前两种流式传输选项的一小部分。

## <a name="use-extended-events"></a>使用扩展事件 

此外，可以使用 SQL Server 中的[扩展事件](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events)进行其他高级监视和故障排除。 扩展事件体系结构使用户能够收集必要的数据量，以排除故障或识别性能问题。 有关使用 Azure SQL 数据库中的扩展事件的信息，请参阅 [Azure SQL 数据库中的扩展事件](xevent-db-diff-from-svr.md)。

## <a name="next-steps"></a>后续步骤

- 有关针对单一数据库和共用数据库的智能性能建议的详细信息，请参阅[数据库顾问性能建议](database-advisor-implement-performance-recommendations.md)。
- 有关借助自动诊断和性能问题的根本原因分析自动监视数据库性能的详细信息，请参阅 [Azure SQL 智能见解](intelligent-insights-overview.md)。
