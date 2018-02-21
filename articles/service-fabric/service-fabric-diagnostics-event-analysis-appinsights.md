---
title: "使用 Application Insights 进行 Azure Service Fabric 事件分析 | Microsoft Docs"
description: "了解通过使用 Application Insights 可视化和分析事件来监视和诊断 Azure Service Fabric 群集。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 479e486dca432020d5fcbaf98971a9803888bf98
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>使用 Application Insights 进行事件分析和可视化

Azure Application Insights 是用于应用程序监视和诊断的可扩展平台。 它具有强大的分析和查询工具、可自定义的仪表板和可视化效果，以及包括自动报警在内的其他选项。 推荐使用该平台为 Service Fabric 应用程序和服务进行监视和诊断。

## <a name="setting-up-application-insights"></a>设置 Application Insights

### <a name="creating-an-ai-resource"></a>创建 AI 资源

若要创建 AI 资源，请转到 Azure Marketplace 并搜索“Application Insights”。 它应显示为首个解决方案（位于“Web + Mobile”类别下）。 发现相应资源时单击“创建”（请确保路径与以下图像匹配）。

![新建 Application Insights 资源](media/service-fabric-diagnostics-event-analysis-appinsights/create-new-ai-resource.png)

需要填写一些信息以正确预配资源。 若将使用任何 Service Fabric 编程模型或向群集发布 .NET 应用程序，请在“应用程序类型”字段中使用“ASP.NET Web 应用程序”。 若将部署来宾可执行文件和容器，请使用“常规”。 通常情况下默认使用“ASP.NET Web 应用程序”，使选项保持在将来可更改。 名称可自定，且资源组和订阅都是该资源的可更改后期部署。 建议 AI 资源与群集处于相同资源组。 如需详细信息，请参阅[创建 Application Insights 资源](../application-insights/app-insights-create-new-resource.md)

使用事件聚合工具配置 AI 需要 AI 检测密钥。 设置 AI 资源后（验证部署后需要几分钟时间），请导航至该资源，并在左侧导航栏上查找“属性”部分。 将打开新的边栏选项卡，显示“检测密钥”。 若要更改订阅或资源的资源组，也可在此完成。

### <a name="configuring-ai-with-wad"></a>使用 WAD 配置 AI

>[!NOTE]
>目前仅适用于 Windows 群集。

可通过两种方式将数据从 WAD 发送到 Azure AI，这一过程是通过向 WAD 配置添加 AI 接收器实现，如[本文](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md)所述。

#### <a name="add-an-ai-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>在 Azure 门户中创建群集时添加 AI 检测密钥

![添加 AI 密钥](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

创建群集时，如果诊断处于打开状态，会显示要求输入 Application Insights 检测密钥的可选字段。 若在此处粘贴 AI 密钥，将在用于部署群集的 Resource Manager 模板中自动为你配置该 AI 接收器。

#### <a name="add-the-ai-sink-to-the-resource-manager-template"></a>将 AI 接收器添加到 Resource Manager 模板

在 Resource Manager 模板的“WadCfg”中，通过应用以下两项更改添加“接收器”：

1. 在声明完 `DiagnosticMonitorConfiguration` 后，直接添加接收器配置：

    ```json
    "SinksConfig": {
        "Sink": [
            {
                "name": "applicationInsights",
                "ApplicationInsights": "***ADD INSTRUMENTATION KEY HERE***"
            }
        ]
    }

    ```

2. 在 `DiagnosticMonitorConfiguration` 中添加接收器，具体方法是在 `WadCfg` 的 `DiagnosticMonitorConfiguration` 中添加以下代码行（紧靠声明的 `EtwProviders` 前面）：

    ```json
    "sinks": "applicationInsights"
    ```

在以上两个代码片段中，名称“applicationInsights”用于描述该接收器。 这不是必需的，并且只要接收器名称包含在“接收器”中，就可将名称设定为任何字符串。

目前，群集的日志会在 AI 日志查看器中作为跟踪显示。 由于来自平台的大部分跟踪信息都是“参考”级别，因此还可以考虑将接收器配置更改为仅发送类型为“关键”或“错误”的日志。 这可通过将“通道”添加到接收器完成，如[本文](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md)所示。

>[!NOTE]
>若在门户或 Resource Manager 模板中使用了错误的 AI 密钥，则必须手动更改密钥并更新/重新部署群集。 

### <a name="configuring-ai-with-eventflow"></a>使用 EventFlow 配置 AI

若正在使用 EventFlow 聚合事件，请务必导入 `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights` NuGet 包。 以下内容须包含在 eventFlowConfig.json 的输出部分：

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        // (replace the following value with your AI resource's instrumentation key)
        "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
]
```

请确保在筛选器中应用必要的更改，并包含任何其他输入（及其各自的 NuGet 包）。

## <a name="aisdk"></a>AI.SDK

通常建议使用 EventFlow 和 WAD 作为聚合解决方案，因为它们允许使用更加模块化的方法，方便诊断和监视。例如，若想从 EventFlow 更改输出，不需要更改实际检测，仅需对配置文件进行简单修改。 然而，若决定投资使用 Application Insights，且不太可能更改到其他平台，则应使用 AI 的新 SDK 以聚合事件并将它们发送到 AI。 这意味着不再非得配置 EventFlow 将数据发送到 AI，而是安装 Application Insight 的 Service Fabric NuGet 包。 可在[此处](https://github.com/Microsoft/ApplicationInsights-ServiceFabric)找到此包的详细信息。

[微服务和容器的 Application Insights 支持](https://azure.microsoft.com/en-us/blog/app-insights-microservices/)会显示一些开发中的新功能（当前仍为 beta 版本），通过它们可以使用更加丰富的现成 AI 监视选项。 这包含依赖项跟踪（用于生成群集中所有服务和应用程序的 AppMap 以及它们之间的通信），以及服务的更好跟踪关联（有助于更好地查明应用或服务的工作流中的问题）。

若在 .NET 中进行开发，将来可能会使用一些 Service Fabric 编程模型，且愿意使用 AI 作为可视化和分析事件和日志数据的平台，那我们建议选取 AI SDK 途径作为监视和诊断工作流。 请参阅[本文](../application-insights/app-insights-asp-net-more.md)和[本文](../application-insights/app-insights-asp-net-trace-logs.md)，开始使用 AI 收集和显示日志。

## <a name="navigating-the-ai-resource-in-azure-portal"></a>在 Azure 门户中浏览 AI 资源

配置 AI 作为事件和日志输出的数分钟后，AI 资源中应开始显示信息。 导航到该 AI 资源，你将转到 AI 资源仪表板。 单击 AI 任务栏中的“搜索”，查看其最近收到的跟踪，并能够筛选它们。

指标资源管理器是非常有用的工具，能够基于应用程序、服务和群集报告的指标创建自定义仪表板。 请参阅[了解 Application Insights 中的指标](../application-insights/app-insights-metrics-explorer.md)，基于收集的数据为自身设置数个图表。

单击“分析”会转到 Application Insights 分析门户，可在此处基于更广的范围和可选性查询事件和跟踪。 若要阅读详细信息，请转到 [Application Insights 中的分析](../application-insights/app-insights-analytics.md)。

## <a name="next-steps"></a>后续步骤

* [在 AI 中设置警报](../application-insights/app-insights-alerts.md)以获取有关性能或使用情况的通知
* [Application Insights 中的智能检测](../application-insights/app-insights-proactive-diagnostics.md)针对发送给 AI 的遥测进行主动分析，向你警告潜在的性能问题
