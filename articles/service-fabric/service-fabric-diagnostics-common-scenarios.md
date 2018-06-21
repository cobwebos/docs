---
title: Azure Service Fabric 诊断常见情况 | Microsoft Docs
description: 了解如何使用 Azure Service Fabric 对常见情况进行故障排除
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/16/2018
ms.author: srrengar
ms.openlocfilehash: bd7a7e0288ced0219a0600034b273d1acba6b09b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659184"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>使用 Service Fabric 诊断常见情况

本文阐述了用户在使用 Service Fabric 进行监视和诊断时遇到的常见情况。 所阐述的情况涵盖了 Service Fabric 的全部 3 层：应用程序、群集和基础结构。 每个解决方案均使用 Application Insights 和 Log Analytics（Azure 监视工具）来完成每种情况。 每个解决方案中的步骤都向用户介绍了如何在 Service Fabric 环境中使用 Application Insights 和 Log Analytics。

## <a name="prerequisites-and-recommendations"></a>先决条件和建议

本文中的解决方案将使用以下工具。 建议对这些工具进行设置和配置：

* [Application Insights 与 Service Fabric](service-fabric-tutorial-monitoring-aspnet.md)
* [在群集上启用 Azure 诊断](service-fabric-diagnostics-event-aggregation-wad.md)
* [设置 OMS Log Analytics 工作区](service-fabric-diagnostics-oms-setup.md)
* [用于跟踪性能计数器的 OMS 代理](service-fabric-diagnostics-oms-agent.md)

## <a name="how-can-i-see-unhandled-exceptions-in-my-application"></a>如何在应用程序中查看未经处理的异常？

1. 导航到应用程序配置的 Application Insights 资源。
2. 单击左上角的“搜索”。 然后单击下一个面板上的筛选器。

    ![AI 概述](media/service-fabric-diagnostics-common-scenarios/ai-search-filter.png)

3. 你将看到很多类型的事件（跟踪、请求、自定义事件）。 选择“异常”作为筛选器。

    ![AI 筛选器列表](media/service-fabric-diagnostics-common-scenarios/ai-filter-list.png)

    如果正在使用 Service Fabric Application Insights SDK，那么通过单击列表中的异常，可以查看更多详细信息，包括服务上下文。

    ![AI 异常](media/service-fabric-diagnostics-common-scenarios/ai-exception.png)

## <a name="how-do-i-view-which-http-calls-are-used-in-my-services"></a>如何查看服务中使用的 HTTP 调用？

1. 在同一个 Application Insights 资源中，可以筛选“请求”而不是异常，并查看发出的所有请求
2. 如果正在使用 Service Fabric Application Insights SDK，则可以看到彼此连接的服务的可视形式以及成功和失败请求的数量。 单击左侧的“应用程序映射”

    ![AI 应用映射边栏选项卡](media/service-fabric-diagnostics-common-scenarios/app-map-blade.png) ![AI 应用映射](media/service-fabric-diagnostics-common-scenarios/app-map-new.png)

    有关应用程序映射的详细信息，请访问[应用程序映射文档](../application-insights/app-insights-app-map.md)

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>如何在节点出现故障时创建警报

1. 节点事件由 Service Fabric 群集跟踪。 导航到名为 **ServiceFabric(NameofResourceGroup)** 的 Service Fabric 分析解决方案资源
2. 单击标题为“摘要”的边栏选项卡底部的图表

    ![OMS 解决方案](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. 此处有许多图表和磁贴，上面显示了各种指标。 单击其中一个图表，它会带你进入“日志搜索”。 在这里，你可以查询任何群集事件或性能计数器。
4. 输入以下查询。 这些事件 ID 位于[节点事件参考](service-fabric-diagnostics-event-generation-operational.md#application-events)中

    ```kusto
    ServiceFabricOperationalEvent
    | where EventId >= 25623 or EventId <= 25626
    ```

5. 单击顶部的“新建警报规则”，现在只要发生基于此查询的事件，就会通过所选通信方式收到警报。

    ![OMS 新建警报](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

## <a name="how-can-i-be-alerted-of-application-upgrade-rollbacks"></a>怎样才能收到应用程序升级回滚警报？

1. 在与之前相同的“日志搜索”窗口中，针对升级回滚输入以下查询。 这些事件 ID 位于[应用程序事件参考](service-fabric-diagnostics-event-generation-operational.md#application-events)下方

    ```kusto
    ServiceFabricOperationalEvent
    | where EventId == 29623 or EventId == 29624
    ```

2. 单击顶部的“新建警报规则”，现在只要发生基于此查询的事件，你就会收到警报。

## <a name="how-do-i-see-container-metrics"></a>如何查看容器指标？

在包含所有图表的相同视图中，你将看到有关容器性能的一些磁贴。 你需要 OMS 代理和[容器监视解决方案](service-fabric-diagnostics-oms-containers.md)来填充这些磁贴。

![OMS 容器指标](media/service-fabric-diagnostics-common-scenarios/containermetrics.png)

>[!NOTE]
>若要从容器**内部**检测遥测数据，需要为容器添加 [Application Insights nuget 包](https://github.com/Microsoft/ApplicationInsights-servicefabric#microsoftapplicationinsightsservicefabric--for-service-fabric-lift-and-shift-scenarios)。

## <a name="how-can-i-monitor-performance-counters"></a>如何监视性能计数器？

1. 向群集添加 OMS 代理后，需要添加要跟踪的特定性能计数器。导航到门户中的 OMS 工作区页面（工作区选项卡位于解决方案页面的左侧菜单中）。

    ![OMS 工作区选项卡](media/service-fabric-diagnostics-common-scenarios/workspacetab.png)

2. 进入工作区页面后，单击同一左侧菜单中的“高级设置”。

    ![OMS 高级设置](media/service-fabric-diagnostics-common-scenarios/advancedsettingsoms.png)

3. 单击“数据”>“Windows 性能计数器”（对于 Linux 计算机，则为“数据”>“Linux 性能计数器”），开始通过 OMS 代理从节点收集特定计数器。 以下是要添加的计数器的格式示例

    * `.NET CLR Memory(<ProcessNameHere>)\\# Total committed Bytes`
    * `Processor(_Total)\\% Processor Time`
    * `Service Fabric Service(*)\\Average milliseconds per request`

    在快速入门中，VotingData 和 VotingWeb 是所用进程名称，因此，将按以下格式跟踪这些计数器

    * `.NET CLR Memory(VotingData)\\# Total committed Bytes`
    * `.NET CLR Memory(VotingWeb)\\# Total committed Bytes`

    ![OMS 性能计数器](media/service-fabric-diagnostics-common-scenarios/omsperfcounters.png)

4. 这将允许你查看基础结构处理工作负荷的方式，并根据资源利用率设置相关警报。 例如，如果处理器总利用率高于 90% 或低于 5%，则可能需要设置警报。 此时将使用名为“% Processor Time”的计数器。 可通过为以下查询创建警报规则来执行此操作：

    ```kusto
    Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" | where CounterValue >= 90 or CounterValue <= 5.
    ```

## <a name="how-do-i-track-performance-of-my-reliable-services-and-actors"></a>如何跟踪 Reliable Services 和 Actors 的性能？

若要跟踪应用程序中 Reliable Services 或 Actors 的性能，还应添加 Service Fabric Actor、Actor Method、Service 和 Service Method 计数器。 可以按照与上一情况类似的方式添加这些计数器，以下是要在 OMS 中添加的 Reliable Service 和 Actor 性能计数器的示例

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

在 Reliable [Services](service-fabric-reliable-serviceremoting-diagnostics.md) 和 [Actors](service-fabric-reliable-actors-diagnostics.md) 上查看这些链接可获取性能计数器的完整列表

## <a name="next-steps"></a>后续步骤

* [在 AI 中设置警报](../application-insights/app-insights-alerts.md)以获取有关性能或使用情况的通知
* [Application Insights 中的智能检测](../application-insights/app-insights-proactive-diagnostics.md)针对发送给 AI 的遥测进行主动分析，向你警告潜在的性能问题
* 详细了解有助于进行检测和诊断的 OMS Log Analytics [警报](../log-analytics/log-analytics-alerts.md)。
* 对于本地群集，OMS 提供可用于向 OMS 发送数据的网关（HTTP 正向代理）。 有关更多信息，请参阅[使用 OMS 网关将无法访问 Internet 的计算机连接到 OMS](../log-analytics/log-analytics-oms-gateway.md)
* 掌握 Log Analytics 中提供的[日志搜索和查询](../log-analytics/log-analytics-log-searches.md)功能
* 有关 Log Analytics 及其功能的更详细概述，请参阅[什么是 Log Analytics？](../operations-management-suite/operations-management-suite-overview.md)
