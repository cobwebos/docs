---
title: 在 Azure 监视器中日志 |Microsoft Docs
description: 介绍 Azure 监视器用于监视数据的高级分析的日志。
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 0203/26/2019
ms.author: bwren
ms.openlocfilehash: ec037b16840afe669ac3934beaa832f850cdcfb0
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2019
ms.locfileid: "60003677"
---
# <a name="logs-in-azure-monitor"></a>Azure Monitor 中的日志

> [!NOTE]
> 通过 Azure Monitor 收集的所有数据都适合指标和日志的两种基本类型之一。 本指南介绍了日志。 请参阅[Azure Monitor 中的指标](data-platform-metrics.md)有关的度量值和的详细说明[由 Azure Monitor 监视数据收集](data-platform.md)有关的两个比较。

Azure Monitor 中的日志时跨不同的源中的数据执行复杂的分析特别有用。 本文介绍如何日志在 Azure 监视器中的数据，可以执行的操作的结构和标识在日志中存储数据的不同数据源。

> [!NOTE]
> 请务必区分 Azure Monitor 日志和 Azure 中的日志数据源。 例如，在 Azure 中的订阅级别事件将写入[活动日志](activity-logs-overview.md)，您可以从 Azure Monitor 菜单。 最多的资源会将操作信息写入[诊断日志](diagnostic-logs-overview.md)可转发到不同位置。 Azure Monitor 日志是一个将活动日志和诊断日志以及其他监视数据，以在你的整个资源集之间提供深入分析收集的日志数据平台。

## <a name="what-are-azure-monitor-logs"></a>什么是 Azure Monitor 日志？

Azure Monitor 中的日志包含不同类型的数据组织到具有不同的每种类型的属性集的记录。 日志可能包含数字值，如 Azure Monitor 指标，但通常包含文本数据的详细说明。 它们进一步不同于指标数据，它们及其结构的各不相同，且通常不收集在固定的时间间隔。 如事件和跟踪遥测数据将存储 Azure Monitor 日志以及性能数据，以便它可以所有组合以进行分析。

日志条目的通用类型是个别情况下收集的事件。 事件创建的应用程序或服务，并通常包括足够的信息来自行提供完整的上下文。 例如，事件可能会指示特定资源已创建或修改、新主机开始响应流量增高的情况，或者在应用程序中检测到了错误。

 考虑到数据的格式可能有差异，应用程序可以使用所需结构创建自定义日志。 甚至可以在日志中以将功能与其他监视数据的趋势和其他数据分析存储指标数据。


## <a name="what-can-you-do-with-azure-monitor-logs"></a>使用 Azure Monitor 日志可以做什么？
下表列出了不同的方式，你可以在 Azure Monitor 中使用日志。


|  |  |
|:---|:---|
| 分析 | 使用[Log Analytics](../log-query/get-started-portal.md)在 Azure 门户中编写[记录查询](../log-query/log-query-overview.md)以交互方式使用和分析日志数据的功能强大的数据资源管理器中分析引擎。<br>使用[Application Insights analytics 控制台](../app/analytics.md)编写日志查询并以交互方式分析从 Application Insights 的日志数据在 Azure 门户中。 |
| 可视化 | 将查询结果呈现为表或图表固定[Azure 仪表板](../../azure-portal/azure-portal-dashboards.md)。<br>创建[工作簿](../app/usage-workbooks.md)来组合和多个的交互式报表中的数据集。 <br>将查询结果导出到 [Power BI](powerbi.md)，以使用不同的可视化效果并与 Azure 外部的用户共享。<br>导出到查询的结果[Grafana](grafana-plugin.md)利用其仪表板建设并结合其他数据源。|
| 警报 | 配置[日志警报规则](alerts-log.md)，以便在查询结果与特定的结果匹配时发送通知或执行[自动化操作](action-groups.md)。<br>配置[指标预警规则](alerts-metric-logs.md)上某些日志数据日志提取为指标。 |
| 检索 | 从命令行使用访问日志查询结果[Azure CLI](/cli/azure/ext/log-analytics/monitor/log-analytics)。<br>从命令行使用访问日志查询结果[PowerShell cmdlet](https://docs.microsoft.com/powershell/module/az.operationalinsights)。<br>从自定义应用程序使用访问日志查询结果[REST API](https://dev.loganalytics.io/)。 |
| 导出 | 生成要检索日志数据，并将其复制到外部位置使用的工作流[逻辑应用](~/articles/logic-apps/index.yml)。 |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>在 Azure Monitor 日志结构化数据的方式
通过 Azure Monitor 日志收集的数据存储中[Log Analytics 工作区](../platform/manage-access.md)。 你可以[创建多个工作区](manage-access.md#determine-the-number-of-workspaces-you-need)中你的订阅来管理不同的日志数据集。 每个工作区包含多个表，每个存储来自特定源的数据。 而所有表都共享[一些通用属性](log-standard-properties.md)，每个都有一组唯一的具体取决于它将存储的数据类型的属性。 新的工作区都有标准套表，并将通过不同的监视解决方案并将写入到工作区的其他服务添加更多的表。

从 Application Insights 的日志数据使用相同的日志分析引擎为工作区，但它单独存储的每个监视的应用程序。 每个应用程序具有一组标准的表来保存数据，例如应用程序请求、 异常和页面视图。

日志查询将使用的数据来自 Log Analytics 工作区或 Application Insights 应用程序。 可以使用[跨资源查询](../log-query/cross-workspace-query.md)来分析以及其他日志数据的应用程序数据或创建包括多个工作区或应用程序的查询。

![工作区](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>日志查询
在 Azure Monitor 日志中的数据使用检索[日志查询](../log-query/log-query-overview.md)与一起写入[Kusto 查询语言](../log-query/get-started-queries.md)，可用于快速检索、 整合和分析收集的数据。 使用[Log Analytics](../log-query/portals.md)可以编写和测试在 Azure 门户中的日志查询。 它允许您以交互方式处理结果或将其固定到仪表板来查看与其他可视化效果。

![Log Analytics](media/data-platform-logs/log-analytics.png)

打开[Log Analytics 将从 Application Insights](../app/analytics.md)分析 Application Insights 的数据。

![Application Insights Analytics](media/data-platform-logs/app-insights-analytics.png)

此外可以通过使用检索的日志数据[Log Analytics API](https://dev.loganalytics.io/documentation/overview)并[Application Insights REST API](https://dev.applicationinsights.io/documentation/overview)。


## <a name="sources-of-azure-monitor-logs"></a>Azure Monitor 日志的源
Azure Monitor 可从 Azure 和本地资源中的各种源收集日志数据。 下表列出可从不同的资源将数据写入到 Azure Monitor 日志不同的数据源。 每个对任何所需的配置信息的链接。

### <a name="azure-tenant-and-subscription"></a>Azure 租户和订阅

| 数据 | 描述 |
|:---|:---|
| Azure Active Directory 审核日志 | 通过为每个目录的诊断设置配置。 请参阅[集成 Azure AD 与 Azure Monitor 日志的日志](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)。  |
| 活动日志 | 默认情况下单独存储，并可用于准实时警报。 安装 Activity Log Analytics 解决方案，以将写入到 Log Analytics 工作区。 请参阅[收集和分析 Log Analytics 中的 Azure 活动日志](collect-activity-logs.md)。 |

### <a name="azure-resources"></a>Azure 资源

| 数据 | 描述 |
|:---|:---|
| 资源诊断 | 配置诊断设置将写入诊断数据，包括指标到 Log Analytics 工作区。 请参阅[Stream 到 Log Analytics 的 Azure 诊断日志](diagnostic-logs-stream-log-store.md)。 |
| 监视解决方案 | 监视解决方案将数据写入所收集到其 Log Analytics 工作区。 请参阅[的管理解决方案在 Azure 中的数据集合详细信息](../insights/solutions-inventory.md)有关解决方案的列表。 请参阅[监视 Azure Monitor 中的解决方案](../insights/solutions.md)有关安装和使用解决方案的详细信息。 |
| 度量值 | 将平台的 Azure Monitor 资源指标发送到 Log Analytics 工作区以保留更长时间的日志数据并使用其他数据类型执行复杂的分析[Kusto 查询语言](/azure/kusto/query/)。 请参阅[Stream 到 Log Analytics 的 Azure 诊断日志](diagnostic-logs-stream-log-store.md)。 |
| Azure 表存储 | 从 Azure 存储收集数据，编写一些 Azure 资源监视数据。 请参阅[使用 Azure blob 存储事件的 Log Analytics 的 IIS 和 Azure 表存储用于](azure-storage-iis-table.md)。 |

### <a name="virtual-machines"></a>虚拟机

| 数据 | 描述 |
|:---|:---|
|  代理数据源 | 从收集的数据源[Windows](agent-windows.md)并[Linux](../learn/quick-collect-linux-computer.md)代理包括事件、 性能数据和自定义日志。 请参阅[Azure Monitor 中的代理数据源](data-sources.md)有关数据源和配置的详细信息的列表。 |
| 监视解决方案 | 监视解决方案将数据写入它们从代理收集到其 Log Analytics 工作区。 请参阅[的管理解决方案在 Azure 中的数据集合详细信息](../insights/solutions-inventory.md)有关解决方案的列表。 请参阅[监视 Azure Monitor 中的解决方案](../insights/solutions.md)有关安装和使用解决方案的详细信息。 |
| System Center Operations Manager | Operations Manager 管理组连接到 Azure Monitor，若要从本地代理到日志收集事件和性能数据。 请参阅[将 Operations Manager 连接到 Log Analytics](om-agents.md)有关此配置的详细信息。 |


### <a name="applications"></a>应用程序

| 数据 | 描述 |
|:---|:---|
| 请求和异常 | 有关应用程序请求和异常的详细的数据位于_请求_， _pageViews_，并_异常_表。 调用[外部组件](../app/asp-net-dependencies.md)处于_依赖项_表。 |
| 使用情况和性能 | 应用程序的性能现已推出_请求_， _browserTimings_并_performanceCounters_表。 数据[自定义指标](../app/api-custom-events-metrics.md#trackevent)处于_customMetrics_表。|
| 跟踪数据 | 得出[分布式跟踪](../app/distributed-tracing.md)存储在_跟踪_表。 |
| 可用性测试 | 从摘要数据[可用性测试](../app/monitor-web-app-availability.md)存储在_availabilityResults_表。 这些测试中的详细的数据位于单独的存储和访问从 Application Insights 在 Azure 门户中。 |

### <a name="insights"></a>洞察力

| 数据 | 描述 |
|:---|:---|
| 用于容器的 Azure Monitor | 清单和性能数据收集[容器的 Azure Monitor](../insights/container-insights-overview.md)。 请参阅[容器数据收集详细信息](../insights/container-insights-log-search.md#container-records)的表的列表。 |
| 用于 VM 的 Azure Monitor | 映射和性能数据收集[Vm 的 Azure Monitor](../insights/vminsights-overview.md)。 请参阅[如何为虚拟机中查询 Azure Monitor 中的日志](../insights/vminsights-log-search.md)有关查询此数据的详细信息。 |

### <a name="custom"></a>“自定义” 

| 数据 | 描述 |
|:---|:---|
| REST API | 从任何 REST 客户端将数据写入到 Log Analytics 工作区。 请参阅[日志数据发送到 Azure Monitor 使用 HTTP 数据收集器 API](data-collector-api.md)有关详细信息。
| 逻辑应用 | 从具有的逻辑应用工作流将任何数据写入到 Log Analytics 工作区**Azure Log Analytics 数据收集器**操作。 |

### <a name="security"></a>安全

| 数据 | 描述 |
|:---|:---|
| Azure 安全中心 | [Azure 安全中心](/azure/security-center/)将存储在其中进行与其他日志数据分析的 Log Analytics 工作区中收集的数据。 请参阅[Azure 安全中心中的数据收集](../../security-center/security-center-enable-data-collection.md)有关工作区配置的详细信息。 |
| Azure Sentinel | [Azure Sentinel](/azure/sentinel/)将来自数据源的数据存储到 Log Analytics 工作区。 请参阅[数据源连接](/azure/sentinel/connect-data-sources)。  |


## <a name="next-steps"></a>后续步骤

- 详细了解如何[Azure Monitor 数据平台](data-platform.md)。
- 了解如何[Azure Monitor 中的指标](data-platform-metrics.md)。
- 了解适用于 Azure 中不同资源的[监视数据](data-sources.md)。
