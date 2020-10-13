---
title: 使用 Application Insights 进行 Azure Service Fabric 事件分析
description: 了解通过使用 Application Insights 可视化和分析事件来监视和诊断 Azure Service Fabric 群集。
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 61a1d7cb3a5f43aa8100f1c7e8a102ab19b803f5
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91932441"
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>使用 Application Insights 进行事件分析和可视化

作为 Azure Monitor 的一部分，Application Insights 是用于应用程序监视和诊断的可扩展平台。 它具有强大的分析和查询工具、可自定义的仪表板和可视化效果，以及包括自动报警在内的其他选项。 Application Insights 与 Service Fabric 的集成包括 Visual Studio 和 Azure 门户的工具体验，以及 Service Fabric 特定的指标，从而提供综合性的现成日志记录体验。 尽管使用 Application Insights 时会自动创建和收集许多日志，但我们建议将更多的自定义日志记录添加到应用程序以创建更丰富的诊断体验。

本文帮助解决以下常见问题：

* 如何知道应用程序和服务中发生的情况，如何收集遥测数据？
* 如何排查应用程序问题，尤其是服务之间相互通信的问题？
* 如何获取与服务性能相关的指标，例如页面加载时间和 HTTP 请求数？

本文旨在介绍如何从 Application Insights 获取见解和进行故障排除。 如果想要了解如何使用 Service Fabric 设置和配置 Application Insights，请查看[此教程](service-fabric-tutorial-monitoring-aspnet.md)。

## <a name="monitoring-in-application-insights"></a>Application Insights 中的监视

使用 Service Fabric 时，可以直接使用 Application Insights。 在概述页中，Application Insights 提供有关服务的重要信息，例如响应时间和处理的请求数。 单击顶部的“搜索”按钮可以看到应用程序中最近请求的列表。 此外，可在此处查看失败的请求数，以及诊断可能发生的错误。

![Application Insights 概述](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

在上图的右侧面板上，列表中有两种主要类型的条目：请求和事件。 在本例中，请求是通过 HTTP 请求对应用 API 发出的调用，事件是自定义事件，充当可以添加到代码中任意位置的遥测数据。 可以在[用于处理自定义事件和指标的 Application Insights API](../azure-monitor/app/api-custom-events-metrics.md) 中进一步了解如何检测应用程序。 单击某一请求将显示更多详细信息，如下图所示，其中包括 Application Insights Service Fabric NuGet 包中收集的特定于 Service Fabric 的数据。 在排查和了解应用程序的状态时，此信息非常有用；所有这些信息都可以在 Application Insights 中搜索

![Application Insights 请求详细信息](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

Application Insights 提供指定的视图用于查询所有传入的数据。 选择 "概述" 页顶部的 "指标资源管理器"，导航到 Application Insights 门户。 在此处，可以使用 Kusto 查询语言，针对前面所述的自定义事件、请求、异常、性能计数器和其他指标运行查询。 以下示例演示过去 1 小时内的所有请求。

![Application Insights 请求详细信息](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

若要进一步了解 Application Insights 门户的功能，请转到 [Application Insights 门户文档](../azure-monitor/app/overview-dashboard.md)。

### <a name="configuring-application-insights-with-eventflow"></a>使用 EventFlow 配置 Application Insights

若正在使用 EventFlow 聚合事件，请务必导入 `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights` NuGet 包。 需要在 *eventFlowConfig.json* 的 *outputs* 节中包含以下代码：

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

请确保在筛选器中应用必要的更改，并包含任何其他输入（及其各自的 NuGet 包）。

## <a name="application-insights-sdk"></a>Application Insights SDK

建议将 EventFlow 和 WAD 用作聚合解决方案，因为它们允许更具模块化的诊断和监视方法，也就是说，如果你要从 EventFlow 更改输出，不需要更改实际检测，只需对配置文件进行简单修改。 如果决定使用 Application Insights 进行投资，并且不可能更改为其他平台，则应查看如何使用 Application Insights 的新 SDK 来聚合事件并将其发送到 Application Insights。 这意味着不再非得配置 EventFlow 将数据发送到 Application Insights，而是安装 Application Insight 的 Service Fabric NuGet 包。 可在[此处](https://github.com/Microsoft/ApplicationInsights-ServiceFabric)找到此包的详细信息。

[微服务和容器的 Application Insights 支持](https://azure.microsoft.com/blog/app-insights-microservices/)会显示一些开发中的新功能（当前仍为 beta 版本），通过它们可以使用更加丰富的现成 Application Insights 监视选项。 这包含依赖项跟踪（用于生成群集中所有服务和应用程序的 AppMap 以及它们之间的通信），以及来自服务更好的跟踪关联（有助于更好地查明应用程序或服务的工作流中的问题）。

若在 .NET 中进行开发，将来可能会使用一些 Service Fabric 编程模型，且愿意使用 Application Insights 作为可视化和分析事件和日志数据的平台，那我们建议选取 Application Insights SDK 途径作为监视和诊断工作流。 若要开始使用 Application Insights 收集和显示日志，请阅读 [Application Insights 文档](../azure-monitor/azure-monitor-app-hub.yml) 和 [跟踪日志文档](../azure-monitor/app/asp-net-trace-logs.md) 。

## <a name="navigating-the-application-insights-resource-in-azure-portal"></a>在 Azure 门户中导航到 Application Insights 资源。

配置 Application Insights 作为事件和日志输出的数分钟后，Application Insights 资源中应开始显示信息。 导航到 Application Insights 资源，此时会转到 Application Insights 资源仪表板。 在 Application Insights 任务栏中选择 " **搜索** "，查看其已接收的最新跟踪，并能够进行筛选。

指标资源管理器  是非常有用的工具，能够基于应用程序、服务和群集报告的指标创建自定义仪表板。 请参阅[了解 Application Insights 中的指标](../azure-monitor/platform/metrics-charts.md)，基于收集的数据为自身设置数个图表。

单击“分析”会转到 Application Insights 分析门户，可在此处基于更广的范围和可选性查询事件和跟踪。  若要阅读详细信息，请转到 [Application Insights 中的分析](../azure-monitor/log-query/log-query-overview.md)。

## <a name="next-steps"></a>后续步骤

* [在 AI 中设置警报](../azure-monitor/platform/alerts-log.md)以获取有关性能或使用情况的通知
* [Application Insights 中的智能检测](../azure-monitor/app/proactive-diagnostics.md)针对发送给 Application Insights 的遥测进行主动分析，向你警告潜在的性能问题
