---
title: 监视和性能优化
description: Azure SQL 数据库中的监视和性能调优功能和方法概述。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 837d88665c1fdffe902c9c478e5d6dc65a2e402a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268725"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database"></a>Azure SQL 数据库中的监视和性能调优

要监视 Azure SQL 数据库中数据库的性能，请首先监视工作负荷相对于选择特定服务层和性能级别的数据库性能级别使用的 CPU 和 IO 资源。 为此，Azure SQL 数据库会发出可在 Azure 门户中查看的资源指标，或者通过使用以下 SQL 管理工具之一[：Azure 数据工作室](https://docs.microsoft.com/sql/azure-data-studio/what-is)或[SQL 服务器管理工作室](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)（SSMS） 进行查看。

对于单个数据库和池数据库，Azure SQL 数据库提供了许多数据库顾问，以提供智能性能调优建议和自动调优选项以提高性能。 此外，查询性能洞察显示有关负责单个数据库和池数据库 CPU 和 IO 使用率最多的查询的详细信息。

Azure SQL 数据库提供由人工智能支持的其他监视和调优功能，以帮助您排除故障并最大限度地提高数据库和解决方案的性能。 您可以选择将这些[智能见解](sql-database-intelligent-insights.md)和其他 SQL 数据库资源日志和指标的[流式导出](sql-database-metrics-diag-logging.md)配置为多个用于消费和分析的目标之一，尤其是使用[SQL 分析](../azure-monitor/insights/azure-sql.md)。 Azure SQL 分析是一种高级云监视解决方案，用于大规模监视所有 Azure SQL 数据库的性能，并在单个视图中跨多个订阅进行监视。 有关可以导出的日志和指标的列表，请参阅[导出的诊断遥测数据](sql-database-metrics-diag-logging.md#diagnostic-telemetry-for-export-for-azure-sql-database)

最后，SQL 具有自己的监视和诊断功能，具有[SQL Server 查询存储](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)和[动态管理视图 （DMV）。](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) 请参阅[使用 DMV 监视](sql-database-monitoring-with-dmvs.md)脚本以监视各种性能问题。

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Azure 门户中的监视和调优功能

在 Azure 门户中，Azure SQL 数据库提供所有部署类型的资源指标的监视。 另外，对于单个数据库和池数据库，数据库顾问和查询性能透视提供查询调优建议和查询性能分析。 最后，在 Azure 门户中，可以为逻辑服务器及其单个数据库和池数据库启用自动。

### <a name="sql-database-resource-monitoring"></a>SQL 数据库资源监视

您可以在 **"指标"** 视图中的 Azure 门户中快速监视以下资源指标：

- **DTU 使用**

  检查数据库或弹性池是否在较长时间内达到 DTU 使用量的 100%。 DTU 使用率高表示您的工作负载可能需要更多 CPU 或 IO 资源。 它还可能指示需要优化的查询。
- **CPU 使用率**

  检查数据库、弹性池或托管实例是否在较长时间内达到 CPU 使用率的 100%。 高 CPU 表示您的工作负载可能需要更多 CPU 或 IO 资源。 它还可能指示需要优化的查询。
- **IO 使用情况**

  检查数据库、弹性池或管理实例是否达到基础存储的 IO 限制。 IO 使用率高表示您的工作负载可能需要更多 CPU 或 IO 资源。 它还可能指示需要优化的查询。

  ![资源指标](./media/sql-database-monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors"></a>数据库顾问
Azure SQL 数据库包括[数据库顾问](sql-database-advisor.md)，这些顾问为单个数据库和池数据库提供性能调优建议。 这些建议在 Azure 门户中使用，并且通过使用[PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseadvisor)。 您还可以启用[自动调优](sql-database-automatic-tuning.md)，以便 SQL 数据库可以自动实现这些调优建议。

### <a name="query-performance-insight"></a>查询性能见解

[查询性能洞察](sql-database-query-performance.md)显示 Azure 门户中用于单个和池数据库的最长消耗和最长运行查询的性能。

## <a name="generate-intelligent-assessments-of-performance-issues"></a>生成性能问题的智能评估

Azure SQL 数据库[智能见解](sql-database-intelligent-insights.md)使用内置智能通过人工智能持续监视数据库使用情况，并检测导致性能不佳的破坏性事件。 智能见解根据查询执行等待时间、错误或超时自动检测 Azure SQL 数据库中数据库的性能问题。 检测到后，将执行详细的分析，生成资源日志（称为 SQLInsights），并[智能评估问题](sql-database-intelligent-insights-troubleshoot-performance.md)。 此评估包含对数据库性能问题的根本原因分析，以及为性能改进而提供的可行性建议。

Intelligent Insights 是 Azure 内置智能的一项独特功能，提供以下功能价值：

- 主动监视
- 定制的性能见解
- 数据库性能下降的早期检测
- 已检测出问题的根本原因分析
- 性能改进建议
- 数以十万计的数据库上的横向扩展功能
- 对 DevOps 资源和总拥有成本的积极影响

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>启用指标和资源日志的流式导出

您可以启用和配置[诊断遥测的流式导出](sql-database-metrics-diag-logging.md)到多个目标之一，包括智能见解资源日志。 使用[SQL 分析](../azure-monitor/insights/azure-sql.md)和其他功能使用此附加诊断遥测来识别和解决性能问题。

将诊断设置配置为将单个数据库、池数据库、弹性池、托管实例和实例数据库的指标和资源日志类别流式传输到以下 Azure 资源之一。

### <a name="log-analytics-workspace-in-azure-monitor"></a>Azure 监视器中的日志分析工作区

可以将指标和资源日志流式传输到[Azure 监视器 中的日志分析工作区](../azure-monitor/platform/resource-logs-collect-workspace.md)。 此处流的数据可由 SQL [Analytics （SQL Analytics）](../azure-monitor/insights/azure-sql.md)使用，这是一种仅云的监视解决方案，提供对数据库的智能监视，其中包括性能报告、警报和缓解建议。 流式传输到日志分析工作区的数据可以使用收集的其他监视数据进行分析，还使您能够利用其他 Azure 监视器功能（如警报和可视化）。

### <a name="azure-event-hubs"></a>Azure 事件中心

可以将指标和资源日志流式传输到 Azure[事件中心](../azure-monitor/platform/resource-logs-stream-event-hubs.md)。 将诊断遥测流式传输到事件中心，以提供以下功能：

- **将日志流式传输到第三方日志记录和遥测系统**

  将所有指标和资源日志流式传输到单个事件中心，以将日志数据传送到第三方 SIEM 或日志分析工具。
- **构建自定义遥测和日志记录平台**

  事件中心的高度可扩展的发布-订阅特性允许您灵活地将指标和资源日志引入自定义遥测平台。 有关详细信息，请参阅 [Designing and Sizing a Global Scale Telemetry Platform on Azure Event Hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)（在 Azure 事件中心设计全球规模的遥测平台并设置其大小）。
- **通过将数据流式传输到 Power BI 来查看服务运行状况**

  使用事件中心、流分析和 Power BI 将诊断数据转换为有关 Azure 服务的近乎实时的见解。 有关此解决方案的详细信息[，请参阅流分析和 Power BI：有关流数据的实时分析仪表板](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard)。

### <a name="azure-storage"></a>Azure 存储

将指标和资源日志流式传输到[Azure 存储](../azure-monitor/platform/resource-logs-collect-storage.md)。 使用 Azure 存储存档大量诊断遥测，但成本只是前两个流式处理选项的一小部分。

## <a name="use-extended-events-in-the-sql-database-engine"></a>在 SQL 数据库引擎中使用扩展事件

此外，您还可以在 SQL 中使用[扩展事件](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events)进行其他高级监视和故障排除。 扩展事件体系结构使用户能够收集尽可能多的或尽可能少的数据，以排除故障或识别性能问题。 有关在 SQL 数据库中使用扩展事件的信息，请参阅[SQL 数据库中的扩展事件](sql-database-xevent-db-diff-from-svr.md)。

## <a name="next-steps"></a>后续步骤

- 有关单个数据库和池数据库的智能性能建议的详细信息，请参阅[数据库顾问性能建议](sql-database-advisor.md)。
- 有关借助自动诊断和性能问题的根本原因分析自动监视数据库性能的详细信息，请参阅 [Azure SQL 智能见解](sql-database-intelligent-insights.md)。
'''''''''