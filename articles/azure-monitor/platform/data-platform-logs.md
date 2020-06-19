---
title: Azure Monitor 中的日志 | Microsoft Docs
description: 介绍 Azure Monitor 中用于对监视数据进行高级分析的日志。
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: dae72454cd9c0b3cb7370873619595840b770ed3
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2020
ms.locfileid: "83799981"
---
# <a name="logs-in-azure-monitor"></a>Azure Monitor 中的日志

> [!NOTE]
> Azure Monitor 收集的所有数据属于以下两种基本类型之一：指标和日志。 本文对日志进行了介绍。 有关指标的详细说明，请参阅 [Azure Monitor 中的指标](data-platform-metrics.md)；有关两者的比较，请参阅 [Azure Monitor 收集的监视数据](data-platform.md)。

Azure Monitor 中的日志对于跨各种来源的数据执行复杂分析特别有用。 本文介绍了 Azure Monitor 中日志的结构和数据用途，并标识了在日志中存储数据的不同数据源。

> [!NOTE]
> 区分 Azure Monitor 日志和 Azure 中的日志数据源很重要。 例如，Azure 中的订阅级别事件将写入[活动日志](platform-logs-overview.md)，你可以从 Azure Monitor 菜单查看该日志。 大多数资源将操作信息写入[资源日志](platform-logs-overview.md)，你可以将其转发到不同的位置。 Azure Monitor 日志是一种日志数据平台，它收集活动日志和资源日志以及其他监视数据，以便对整个资源集进行深入分析。

## <a name="what-are-azure-monitor-logs"></a>什么是 Azure Monitor 日志？

Azure Monitor 中的日志包含不同类型的已经整理成记录的数据，每种类型都有不同的属性集。 日志可能包含数值（例如 Azure Monitor 指标），但通常包含带详细说明的文本数据。 日志与指标数据的不同之处还在于，日志有结构差异，且通常不按固定时间间隔收集。 与性能数据一样，事件和跟踪等遥测数据还存储 Azure Monitor 日志，因此，可将它们合并以进行分析。

常见类型的日志项是偶尔收集的事件。 事件是由应用程序或服务创建的，通常包含足够的信息，其本身提供的上下文已经很完整。 例如，事件可能会指示特定资源已创建或修改、新主机开始响应流量增高的情况，或者在应用程序中检测到了错误。

 考虑到数据的格式可能有差异，应用程序可以使用所需结构创建自定义日志。 甚至可以在日志中存储指标数据，以便将其与其他监视数据组合起来，进行趋势推断和其他数据分析。


## <a name="what-can-you-do-with-azure-monitor-logs"></a>可以对 Azure Monitor 日志执行哪些操作？
下表列出了 Azure Monitor 中的日志的不同使用方式。


|  |  |
|:---|:---|
| 分析 | 使用 Azure 门户中的 [Log Analytics](../log-query/get-started-portal.md) 编写[日志查询](../log-query/log-query-overview.md)，并使用功能强大的数据资源管理器分析引擎以交互方式分析日志数据。<br>使用 Azure 门户中的 [Application Insights 分析控制台](../app/analytics.md)来编写日志查询并以交互方式分析 Application Insights 中的日志数据。 |
| 可视化 | 将以表格或图表形式呈现的查询结果固定到 [Azure 仪表板](../../azure-portal/azure-portal-dashboards.md)。<br>创建一个[工作簿](../platform/workbooks-overview.md)以与交互式报表中的多组数据合并。 <br>将查询结果导出到 [Power BI](powerbi.md)，以使用不同的可视化效果并与 Azure 外部的用户共享。<br>将查询结果导出到 [Grafana](grafana-plugin.md) 以利用其仪表板并与其他数据源合并。|
| 警报 | 配置[日志警报规则](alerts-log.md)，以便在查询结果与特定的结果匹配时发送通知或执行[自动化操作](action-groups.md)。<br>对提取为指标的某些日志数据日志配置[指标警报规则](alerts-metric-logs.md)。 |
| 检索 | 使用 [Azure CLI](/cli/azure/ext/log-analytics/monitor/log-analytics) 从命令行访问日志查询结果。<br>使用 [PowerShell cmdlet](https://docs.microsoft.com/powershell/module/az.operationalinsights) 从命令行访问日志查询结果。<br>使用 [REST API](https://dev.loganalytics.io/) 从自定义应用程序访问日志查询结果。 |
| 导出 | 构建一个工作流来检索日志数据，并使用[逻辑应用](~/articles/logic-apps/index.yml)将其复制到外部位置。 |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Azure Monitor 日志中的数据结构是怎样的？
Azure Monitor 日志收集的数据存储在 [Log Analytics 工作区](../platform/design-logs-deployment.md)中。 每个工作区包含多个表，每个表存储来自特定源的数据。 虽然所有表都共享[一些公共属性](log-standard-properties.md)，但根据存储的数据类型，每个表都有一组唯一的属性。 新工作区将包含一组标准表，不同的监视解决方案和其他写入工作区的服务将添加更多的表。

来自 Application Insights 的日志数据使用与工作区相同的 Log Analytics 引擎，但它是为每个受监视的应用程序单独存储的。 每个应用程序都有一组标准的表来保存数据，如应用程序请求、异常和页面视图。

日志查询将使用来自 Log Analytics 工作区或 Application Insights 应用程序的数据。 你可使用[跨资源查询](../log-query/cross-workspace-query.md)将应用程序数据与其他日志数据一起分析，或者创建包含多个工作区或应用程序的查询。

![工作区](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>日志查询
Azure Monitor 日志中的数据都是使用以 [Kusto 查询语言](../log-query/get-started-queries.md)编写的[日志查询](../log-query/log-query-overview.md)检索的，这使得你可以快速检索、合并和分析所收集的数据。 可以在 Azure 门户中使用 [Log Analytics](../log-query/portals.md) 编写和测试日志查询。 这样你可以交互方式使用结果，也可将其固定到某个仪表板，与其他可视化效果一起查看。

![Log Analytics](media/data-platform-logs/log-analytics.png)

打开 [Application Insights Log Analytics](../app/analytics.md) 以分析 Application Insights 数据。

![Application Insights Analytics](media/data-platform-logs/app-insights-analytics.png)

还可以使用 [Log Analytics API](https://dev.loganalytics.io/documentation/overview) 和 [Application Insights REST API](https://dev.applicationinsights.io/documentation/overview) 检索日志数据。


## <a name="sources-of-azure-monitor-logs"></a>Azure Monitor 日志的源
Azure Monitor 可从 Azure 和本地资源中的各种源收集日志数据。 下表列出了不同资源中可用于将数据写入 Azure Monitor 日志的不同数据源。 每个数据源都有一个链接，指向任何所需配置的详细信息。

### <a name="azure-tenant-and-subscription"></a>Azure 租户和订阅

| 数据 | 说明 |
|:---|:---|
| Azure Active Directory 审核日志 | 通过每个目录的诊断设置进行配置。 请参阅[将 Azure AD 日志与 Azure Monitor 日志集成](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)。  |
| 活动日志 | 默认情况下单独存储，可用于近实时的警报。 安装 Activity Log Analytics 解决方案以写入 Log Analytics 工作区。 请参阅[收集和分析 Log Analytics 中的 Azure 活动日志](activity-log-collect.md)。 |

### <a name="azure-resources"></a>Azure 资源

| 数据 | 说明 |
|:---|:---|
| 资源诊断 | 配置诊断设置以写入诊断数据，包括写入 Log Analytics 工作区的指标。 请参阅[将 Azure 资源日志流式传输到 Log Analytics](resource-logs-collect-workspace.md)。 |
| 监视解决方案 | 监视解决方案将收集的数据写入其 Log Analytics 工作区。 如需查看解决方案列表，请参阅 [Azure 中的管理解决方案的数据收集详细信息](../insights/solutions-inventory.md)。 要详细了解如何安装和使用解决方案，请参阅 [Azure Monitor 中的监视解决方案](../insights/solutions.md)。 |
| 指标 | 将 Azure Monitor 资源的平台指标发送到 Log Analytics 工作区，以便在较长时间内保留日志数据，并使用 [Kusto 查询语言](/azure/kusto/query/)对其他数据类型执行复杂分析。 请参阅[将 Azure 资源日志流式传输到 Log Analytics](resource-logs-collect-storage.md)。 |
| Azure 表存储 | 从某些 Azure 资源写入监视数据的 Azure 存储中收集数据。 请参阅[将适用于 IIS 的 Azure Blob 存储和适用于事件的 Azure 表存储与 Log Analytics 配合使用](diagnostics-extension-logs.md)。 |

### <a name="virtual-machines"></a>虚拟机

| 数据 | 说明 |
|:---|:---|
|  代理数据源 | 从 [Windows](agent-windows.md) 和 [Linux](../learn/quick-collect-linux-computer.md) 代理收集的数据源包括事件、性能数据和自定义日志。 有关数据源列表和配置的详细信息，请参阅 [Azure Monitor 中的代理数据源](data-sources.md)。 |
| 监视解决方案 | 监视解决方案将从代理处收集的数据写入其 Log Analytics 工作区。 如需查看解决方案列表，请参阅 [Azure 中的管理解决方案的数据收集详细信息](../insights/solutions-inventory.md)。 要详细了解如何安装和使用解决方案，请参阅 [Azure Monitor 中的监视解决方案](../insights/solutions.md)。 |
| System Center Operations Manager | 将 Operations Manager 管理组连接到 Azure Monitor，以将事件和性能数据从本地代理收集到日志中。 有关此配置的详细信息，请参阅[将 Operations Manager 连接到 Log Analytics](om-agents.md)。 |


### <a name="applications"></a>应用程序

| 数据 | 说明 |
|:---|:---|
| 请求和异常 | 应用程序请求和异常的相关详细数据位于“requests”、“pageViews”和“exceptions”表中  。 对[外部组件](../app/asp-net-dependencies.md)的调用位于“dependencies”表中。 |
| 使用情况和性能 | 应用程序的性能数据位于“requests”、“browserTimings”和“performanceCounters”表中  。 [自定义指标](../app/api-custom-events-metrics.md#trackevent)的数据位于“customMetrics”表中。|
| 跟踪数据 | 来自[分布式跟踪](../app/distributed-tracing.md)的结果存储在“traces”表中。 |
| 可用性测试 | 来自[可用性测试](../app/monitor-web-app-availability.md)的摘要数据存储在“availabilityResults”表中。 这些测试的详细数据存储在单独的存储中，你可从 Azure 门户中的 Application Insights 对其进行访问。 |

### <a name="insights"></a>洞察力

| 数据 | 说明 |
|:---|:---|
| 用于容器的 Azure Monitor | 由[用于容器的 Azure Monitor](../insights/container-insights-overview.md) 收集的清单和性能数据。 有关表的列表，请参阅[容器数据收集详细信息](../insights/container-insights-log-search.md#container-records)。 |
| 用于 VM 的 Azure Monitor | 由[用于 VM 的 Azure Monitor](../insights/vminsights-overview.md) 收集的映射和性能数据。 要详细了解如何查询这些数据，请参阅[如何从用于 VM 的 Azure Monitor 查询日志](../insights/vminsights-log-search.md)。 |

### <a name="custom"></a>自定义 

| 数据 | 说明 |
|:---|:---|
| REST API | 从任何 REST 客户端向 Log Analytics 工作区写入数据。 有关详细信息，请参阅[使用 HTTP 数据收集器 API 将日志数据发送到 Azure Monitor](data-collector-api.md)。
| 逻辑应用 | 使用“Azure Log Analytics 数据收集器”操作从逻辑应用工作流将任何数据写入 Log Analytics 工作区。 |

### <a name="security"></a>安全性

| 数据 | 说明 |
|:---|:---|
| Azure 安全中心 | [Azure 安全中心](/azure/security-center/)将其收集的数据存储在 Log Analytics 工作区中，你可在该工作区中使用其他日志数据进行分析。 有关工作区配置的详细信息，请参阅 [Azure 安全中心中的数据收集](../../security-center/security-center-enable-data-collection.md)。 |
| Azure Sentinel | [Azure Sentinel](/azure/sentinel/) 将数据源中的数据存储到 Log Analytics 工作区中。 请参阅[连接数据源](/azure/sentinel/connect-data-sources)。  |


## <a name="next-steps"></a>后续步骤

- 了解 [Azure Monitor 数据平台](data-platform.md)的详细信息。
- 了解 [Azure Monitor 中的指标](data-platform-metrics.md)。
- 了解适用于 Azure 中不同资源的[监视数据](data-sources.md)。
