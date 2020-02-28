---
title: Azure Monitor 中的日志 |Microsoft Docs
description: 介绍用于高级分析监视数据的 Azure Monitor 中的日志。
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 06b35fdcd9da333c3c9d94d41c84a2f25ede59ad
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77666559"
---
# <a name="logs-in-azure-monitor"></a>Azure Monitor 中的日志

> [!NOTE]
> Azure Monitor 收集的所有数据都适合以下两种基本类型之一：度量值和日志。 本文介绍了日志。 有关指标的详细说明，请参阅[Azure Monitor 中的指标](data-platform-metrics.md)，以及[监视 Azure Monitor 收集的数据](data-platform.md)以比较两者。

Azure Monitor 中的日志对于跨各种源中的数据执行复杂分析特别有用。 本文介绍如何在 Azure Monitor 中构造日志，可以对数据执行哪些操作，以及标识在日志中存储数据的不同数据源。

> [!NOTE]
> 在 Azure 中区分日志数据 Azure Monitor 日志和源，这一点很重要。 例如，Azure 中的订阅级别事件将写入到可从 "Azure Monitor" 菜单中查看的[活动日志](platform-logs-overview.md)。 大多数资源会将操作信息写入到可转发到不同位置的[资源日志](platform-logs-overview.md)。 Azure Monitor 日志是一种日志数据平台，用于收集活动日志和资源日志以及其他监视数据，以跨整个资源集提供深入分析。

## <a name="what-are-azure-monitor-logs"></a>什么是 Azure Monitor 日志？

Azure Monitor 中的日志包含不同类型的数据，这些数据组织到每种类型的不同属性集的记录中。 日志可以包含数字值（如 Azure Monitor 度量值），但通常包含文本数据以及详细说明。 它们与指标数据的不同之处在于它们的结构不同，通常不会定期收集。 诸如事件和跟踪之类的遥测还存储 Azure Monitor 日志中，以及性能数据，以便将其全部合并以便进行分析。

日志条目的常见类型是事件，该事件是偶尔收集的。 事件是由应用程序或服务创建的，通常包含足够的信息来提供自己的完整上下文。 例如，事件可能会指示特定资源已创建或修改、新主机开始响应流量增高的情况，或者在应用程序中检测到了错误。

 考虑到数据的格式可能有差异，应用程序可以使用所需结构创建自定义日志。 指标数据甚至可以存储在日志中，以便将它们与其他监视数据合并，以便进行趋势分析和其他数据分析。


## <a name="what-can-you-do-with-azure-monitor-logs"></a>您可以对 Azure Monitor 日志执行哪些操作？
下表列出了可以在 Azure Monitor 中使用日志的不同方式。


|  |  |
|:---|:---|
| 分析 | 使用 Azure 门户中[Log Analytics](../log-query/get-started-portal.md)可以编写[日志查询](../log-query/log-query-overview.md)，并使用强大的数据资源管理器分析引擎以交互方式分析日志数据。<br>使用 Azure 门户中的[Application Insights 分析控制台](../app/analytics.md)编写日志查询，并以交互方式从 Application Insights 中分析日志数据。 |
| 可视化 | 将呈现为表或图表的查询结果固定到[Azure 仪表板](../../azure-portal/azure-portal-dashboards.md)。<br>创建要与交互式报表中的多个数据集组合在一起的[工作簿](../app/usage-workbooks.md)。 <br>将查询结果导出到 [Power BI](powerbi.md)，以使用不同的可视化效果并与 Azure 外部的用户共享。<br>将查询结果导出到[Grafana](grafana-plugin.md) ，以利用其仪表板建设并将其与其他数据源组合。|
| 警报 | 配置[日志警报规则](alerts-log.md)，以便在查询结果与特定的结果匹配时发送通知或执行[自动化操作](action-groups.md)。<br>针对作为指标提取的某些日志数据日志配置[指标警报规则](alerts-metric-logs.md)。 |
| 检索 | 使用[Azure CLI](/cli/azure/ext/log-analytics/monitor/log-analytics)从命令行访问日志查询结果。<br>使用[PowerShell cmdlet](https://docs.microsoft.com/powershell/module/az.operationalinsights)从命令行访问日志查询结果。<br>使用[REST API](https://dev.loganalytics.io/)从自定义应用程序访问日志查询结果。 |
| Export | 使用[逻辑应用](~/articles/logic-apps/index.yml)构建工作流以检索日志数据并将其复制到外部位置。 |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Azure Monitor 日志中的数据如何结构化？
Azure Monitor 日志收集的数据存储在[Log Analytics 工作区](../platform/design-logs-deployment.md)中。 每个工作区都包含多个表，每个表都存储来自特定源的数据。 尽管所有表共享[某些通用属性](log-standard-properties.md)，但每个表都具有一组唯一的属性，具体取决于存储的数据类型。 新工作区将包含一组标准表，其他监视解决方案和其他写入工作区的服务将添加更多的表。

Application Insights 中的日志数据与工作区使用相同的 Log Analytics 引擎，但对于每个受监视的应用程序，它都单独存储。 每个应用程序都有一组标准表，用于保存应用程序请求、异常和页面视图等数据。

日志查询将使用来自 Log Analytics 工作区或 Application Insights 应用程序的数据。 您可以使用[跨资源查询](../log-query/cross-workspace-query.md)来分析应用程序数据和其他日志数据，或创建包括多个工作区或应用程序的查询。

![工作区](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>日志查询
使用[Kusto 查询语言](../log-query/get-started-queries.md)编写的[日志查询](../log-query/log-query-overview.md)来检索 Azure Monitor 日志中的数据，这使你可以快速检索、整合和分析收集的数据。 使用[Log Analytics](../log-query/portals.md)在 Azure 门户中编写和测试日志查询。 它允许以交互方式处理结果，或将结果固定到仪表板，以使用其他可视化效果查看这些结果。

![Log Analytics](media/data-platform-logs/log-analytics.png)

[从 Application Insights 打开 Log Analytics](../app/analytics.md)以分析 Application Insights 数据。

![Application Insights Analytics](media/data-platform-logs/app-insights-analytics.png)

你还可以使用[LOG ANALYTICS API](https://dev.loganalytics.io/documentation/overview)和[Application Insights REST API](https://dev.applicationinsights.io/documentation/overview)检索日志数据。


## <a name="sources-of-azure-monitor-logs"></a>Azure Monitor 日志的源
Azure Monitor 可从 Azure 和本地资源中的各种源收集日志数据。 下表列出了在将数据写入 Azure Monitor 日志的不同资源中可用的不同数据源。 每个都有一个链接，指向有关所需配置的详细信息。

### <a name="azure-tenant-and-subscription"></a>Azure 租户和订阅

| data | 说明 |
|:---|:---|
| Azure Active Directory 审核日志 | 通过每个目录的诊断设置进行配置。 请参阅将[Azure AD 日志与 Azure Monitor 日志集成](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)。  |
| 活动日志 | 默认存储，可用于近乎实时的警报。 安装活动 log Analytics 解决方案以写入 Log Analytics 工作区。 请参阅[在 Log Analytics 中收集和分析 Azure 活动日志](activity-log-collect.md)。 |

### <a name="azure-resources"></a>Azure 资源

| data | 说明 |
|:---|:---|
| 资源诊断 | 配置诊断设置以写入诊断数据，包括 Log Analytics 工作区的指标。 请参阅[将 Azure 资源日志流式传输到 Log Analytics](resource-logs-collect-storage.md)。 |
| 监视解决方案 | 监视解决方案会将其收集的数据写入 Log Analytics 的工作区。 有关解决方案列表，请参阅[Azure 中的管理解决方案的数据收集详细信息](../insights/solutions-inventory.md)。 有关安装和使用解决方案的详细信息，请参阅[Azure Monitor 中的监视解决方案](../insights/solutions.md)。 |
| 度量值 | 将 Azure Monitor 资源的平台指标发送到 Log Analytics 工作区，以保留更长时间段的日志数据，并使用[Kusto 查询语言](/azure/kusto/query/)对其他数据类型执行复杂的分析。 请参阅[将 Azure 资源日志流式传输到 Log Analytics](resource-logs-collect-storage.md)。 |
| Azure 表存储 | 从 Azure 存储中收集一些 Azure 资源写入监视数据的数据。 [有关 Log Analytics 的事件，请参阅将 azure blob 存储用于 IIS 和 azure 表存储](diagnostics-extension-logs.md)。 |

### <a name="virtual-machines"></a>虚拟机

| data | 说明 |
|:---|:---|
|  代理数据源 | 从[Windows](agent-windows.md)和[Linux](../learn/quick-collect-linux-computer.md)代理收集的数据源包括事件、性能数据和自定义日志。 有关数据源的列表和配置的详细信息，请参阅[中的代理数据源 Azure Monitor](data-sources.md) 。 |
| 监视解决方案 | 监视解决方案会将其从代理收集的数据写入 Log Analytics 的工作区。 有关解决方案列表，请参阅[Azure 中的管理解决方案的数据收集详细信息](../insights/solutions-inventory.md)。 有关安装和使用解决方案的详细信息，请参阅[Azure Monitor 中的监视解决方案](../insights/solutions.md)。 |
| System Center Operations Manager | 将 Operations Manager 管理组连接到 Azure Monitor，以将事件和性能数据从本地代理收集到日志中。 有关此配置的详细信息，请参阅[连接 Operations Manager 到 Log Analytics](om-agents.md) 。 |


### <a name="applications"></a>应用程序

| data | 说明 |
|:---|:---|
| 请求和异常 | "_请求_"、" _pageViews_" 和 "_异常_" 表中有关于应用程序请求和异常的详细数据。 对[外部组件](../app/asp-net-dependencies.md)的调用在_依赖关系_表中。 |
| 使用情况和性能 | "_请求_"、" _browserTimings_ " 和 " _performanceCounters_ " 表中提供了应用程序的性能。 [自定义指标](../app/api-custom-events-metrics.md#trackevent)的数据位于_customMetrics_表中。|
| 跟踪数据 | [分布式跟踪](../app/distributed-tracing.md)的结果存储在_跟踪_表中。 |
| 可用性测试 | [可用性测试](../app/monitor-web-app-availability.md)的摘要数据存储在_availabilityResults_表中。 这些测试的详细数据位于单独的存储中，并从 Azure 门户中的 Application Insights 访问。 |

### <a name="insights"></a>洞察力

| data | 说明 |
|:---|:---|
| 用于容器的 Azure Monitor | [为容器 Azure Monitor](../insights/container-insights-overview.md)收集的清单和性能数据。 有关表的列表，请参阅[容器数据收集详细信息](../insights/container-insights-log-search.md#container-records)。 |
| 用于 VM 的 Azure Monitor | [用于 VM 的 Azure Monitor](../insights/vminsights-overview.md)收集的地图和性能数据。 有关查询此数据的详细信息，请参阅[如何从用于 VM 的 Azure Monitor 查询日志](../insights/vminsights-log-search.md)。 |

### <a name="custom"></a>自定义 

| data | 说明 |
|:---|:---|
| REST API | 从任何 REST 客户端将数据写入 Log Analytics 工作区。 有关详细信息，请参阅[通过 HTTP 数据收集器 API 将日志数据发送到 Azure Monitor](data-collector-api.md) 。
| 逻辑应用 | 使用**Azure Log Analytics 数据收集器**操作将逻辑应用工作流中的任何数据写入 Log Analytics 工作区。 |

### <a name="security"></a>安全性

| data | 说明 |
|:---|:---|
| Azure 安全中心 | [Azure 安全中心](/azure/security-center/)将它收集的数据存储在 Log Analytics 工作区中，可以使用其他日志数据对其进行分析。 有关工作区配置的详细信息，请参阅[Azure 安全中心中的数据收集](../../security-center/security-center-enable-data-collection.md)。 |
| Azure Sentinel | [Azure Sentinel](/azure/sentinel/)将数据从数据源存储到 Log Analytics 工作区中。 请参阅[连接数据源](/azure/sentinel/connect-data-sources)。  |


## <a name="next-steps"></a>后续步骤

- 详细了解[Azure Monitor 数据平台](data-platform.md)。
- 了解[Azure Monitor 中的指标](data-platform-metrics.md)。
- 了解适用于 Azure 中不同资源的[监视数据](data-sources.md)。
