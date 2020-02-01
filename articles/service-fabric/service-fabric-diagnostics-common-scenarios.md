---
title: Azure Service Fabric 诊断常见方案
description: 了解排查 Azure Service Fabric 应用程序中常见监视和诊断方案的问题。
ms.topic: article
ms.date: 02/25/2019
ms.openlocfilehash: 3c7f027bad71d48db5fba002f778f23db8225fa5
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906951"
---
# <a name="diagnose-common-scenarios-with-service-fabric"></a>诊断带有 Service Fabric 的常见方案

本文说明了用户在使用 Service Fabric 监视和诊断方面遇到的常见情况。 提供的方案涵盖了 service fabric 的所有三个层：应用程序、群集和基础结构。 每个解决方案都使用 Application Insights 和 Azure Monitor 日志、Azure 监视工具来完成每个方案。 每个解决方案中的步骤为用户介绍了如何在 Service Fabric 的上下文中使用 Application Insights 和 Azure Monitor 日志。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites-and-recommendations"></a>先决条件和建议

本文中的解决方案将使用以下工具。 建议设置并配置以下内容：

* [Application Insights 与 Service Fabric](service-fabric-tutorial-monitoring-aspnet.md)
* [启用群集上的 Azure 诊断](service-fabric-diagnostics-event-aggregation-wad.md)
* [设置 Log Analytics 工作区](service-fabric-diagnostics-oms-setup.md)
* [Log Analytics 代理跟踪性能计数器](service-fabric-diagnostics-oms-agent.md)

## <a name="how-can-i-see-unhandled-exceptions-in-my-application"></a>如何在应用程序中查看未处理的异常？

1. 导航到你的应用程序配置了的 Application Insights 资源。
2. 单击左上角的 "*搜索*"。 然后在下一个面板上单击 "筛选器"。

    ![AI 概述](media/service-fabric-diagnostics-common-scenarios/ai-search-filter.png)

3. 你将看到许多类型的事件（跟踪、请求、自定义事件）。 选择 "例外" 作为筛选器。

    ![AI 筛选器列表](media/service-fabric-diagnostics-common-scenarios/ai-filter-list.png)

    通过单击列表中的异常，可以查看更多详细信息，包括服务上下文（如果使用的是 Service Fabric Application Insights SDK）。

    ![AI 异常](media/service-fabric-diagnostics-common-scenarios/ai-exception.png)

## <a name="how-do-i-view-which-http-calls-are-used-in-my-services"></a>如何实现查看服务中使用的 HTTP 调用

1. 在同一 Application Insights 资源中，可以筛选 "请求" 而不是异常，并查看发出的所有请求
2. 如果使用的是 Service Fabric Application Insights SDK，则可以看到服务彼此连接的可视化表示形式，以及成功和失败的请求数。 在左侧单击 "应用程序映射"

    ![AI 应用地图边栏选项卡](media/service-fabric-diagnostics-common-scenarios/app-map-blade.png) ![AI 应用地图](media/service-fabric-diagnostics-common-scenarios/app-map-new.png)

    有关应用程序映射的详细信息，请访问[应用程序映射文档](../azure-monitor/app/app-map.md)

## <a name="how-do-i-create-an-alert-when-a-node-goes-down"></a>如何实现在节点出现故障时创建警报

1. 节点事件由 Service Fabric 群集进行跟踪。 导航到名为 ServiceFabric 的 Service Fabric 分析解决方案资源 **（NameofResourceGroup）**
2. 单击标题为 "摘要" 的边栏选项卡底部的关系图

    ![Azure Monitor 日志解决方案](media/service-fabric-diagnostics-common-scenarios/oms-solution-azure-portal.png)

3. 这里有许多显示各种指标的图形和磁贴。 单击其中一个图形，它将转到日志搜索。 可在此处查询任何群集事件或性能计数器。
4. 输入以下查询。 在[节点事件引用](service-fabric-diagnostics-event-generation-operational.md#application-events)中可以找到这些事件 id

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID >= 25622 and EventID <= 25626
    ```

5. 单击顶部的 "新建警报规则"，并在事件到达时根据此查询随时收到警报，你会在所选的通信方法中收到警报。

    ![Azure Monitor 日志新建警报](media/service-fabric-diagnostics-common-scenarios/oms-create-alert.png)

## <a name="how-can-i-be-alerted-of-application-upgrade-rollbacks"></a>如何向应用程序升级回滚发出警报？

1. 与前面相同的日志搜索窗口中，为升级回滚输入以下查询。 这些事件 Id 在 "[应用程序事件参考](service-fabric-diagnostics-event-generation-operational.md#application-events)" 下找到

    ```kusto
    ServiceFabricOperationalEvent
    | where EventID == 29623 or EventID == 29624
    ```

2. 单击顶部的 "新建警报规则"，并在事件到达时根据此查询随时收到警报。

## <a name="how-do-i-see-container-metrics"></a>如何实现查看容器度量值？

在具有所有关系图的同一视图中，你将看到容器性能的一些磁贴。 需要 Log Analytics 代理和[容器监视解决方案](service-fabric-diagnostics-oms-containers.md)才能填充这些磁贴。

![Log Analytics 容器指标](media/service-fabric-diagnostics-common-scenarios/containermetrics.png)

>[!NOTE]
>若要从容器**内部**检测遥测，需要为[容器添加 Application Insights nuget 包](https://github.com/Microsoft/ApplicationInsights-servicefabric#microsoftapplicationinsightsservicefabric--for-service-fabric-lift-and-shift-scenarios)。

## <a name="how-can-i-monitor-performance-counters"></a>如何监视性能计数器？

1. 将 Log Analytics 代理添加到群集后，需要添加要跟踪的特定性能计数器。导航到门户中的 "Log Analytics" 工作区的页面–从解决方案的页面左侧菜单上的 "工作区" 选项卡。

    ![Log Analytics 工作区 "选项卡](media/service-fabric-diagnostics-common-scenarios/workspacetab.png)

2. 进入工作区页面后，请在同一左侧菜单中单击 "高级设置"。

    ![Log Analytics 高级设置](media/service-fabric-diagnostics-common-scenarios/advancedsettingsoms.png)

3. 单击 "数据 > Windows 性能计数器（适用于 Linux 计算机的数据 > Linux 性能计数器"），开始通过 Log Analytics 代理收集节点中的特定计数器。 下面是要添加的计数器的格式示例

   * `.NET CLR Memory(<ProcessNameHere>)\\# Total committed Bytes`
   * `Processor(_Total)\\% Processor Time`

     在快速入门中，VotingData 和 VotingWeb 是使用的进程名称，因此跟踪这些计数器的外观如下所示

   * `.NET CLR Memory(VotingData)\\# Total committed Bytes`
   * `.NET CLR Memory(VotingWeb)\\# Total committed Bytes`

     ![Log Analytics 性能计数器](media/service-fabric-diagnostics-common-scenarios/omsperfcounters.png)

4. 这样，你就可以查看基础结构如何处理工作负载，并根据资源利用率设置相关警报。 例如，你可能需要在总处理器使用率超过90% 或低于5% 时设置警报。 用于此的计数器名称为 "处理器时间百分比"。 为此，可以为以下查询创建警报规则：

    ```kusto
    Perf | where CounterName == "% Processor Time" and InstanceName == "_Total" | where CounterValue >= 90 or CounterValue <= 5.
    ```

## <a name="how-do-i-track-performance-of-my-reliable-services-and-actors"></a>如何实现跟踪 Reliable Services 和执行组件的性能吗？

若要跟踪应用程序中 Reliable Services 或执行组件的性能，应同时收集 Service Fabric 执行组件、执行组件方法、服务和服务方法计数器。 下面是要收集的可靠服务和执行组件性能计数器的示例

>[!NOTE]
>Log Analytics 代理当前无法收集 Service Fabric 性能计数器，但可由[其他诊断解决方案](service-fabric-diagnostics-partners.md)收集。

* `Service Fabric Service(*)\\Average milliseconds per request`
* `Service Fabric Service Method(*)\\Invocations/Sec`
* `Service Fabric Actor(*)\\Average milliseconds per request`
* `Service Fabric Actor Method(*)\\Invocations/Sec`

查看这些[链接，了解](service-fabric-reliable-actors-diagnostics.md)可靠[服务](service-fabric-reliable-serviceremoting-diagnostics.md)和执行组件上的性能计数器的完整列表

## <a name="next-steps"></a>后续步骤

* [查找常见代码包激活错误](./service-fabric-diagnostics-code-package-errors.md)
* [设置 AI 中的警报](../azure-monitor/app/alerts.md)，以便在性能或使用情况发生变化时得到通知
* [Application Insights 中的智能检测](../azure-monitor/app/proactive-diagnostics.md)对发送到 AI 的遥测数据执行主动分析，以警告你存在潜在的性能问题
* 详细了解 Azure Monitor 日志[警报](../log-analytics/log-analytics-alerts.md)，以帮助检测和诊断。
* 对于本地群集，Azure Monitor 日志提供了一个可用于将数据发送到 Azure Monitor 日志的网关（HTTP 转发代理）。 [若要详细了解如何将计算机连接到无 Internet 访问权限的计算机，请参阅使用 Log Analytics 网关 Azure Monitor 日志](../azure-monitor/platform/gateway.md)
* 获取熟悉，其中包含作为 Azure Monitor 日志一部分提供的[日志搜索和查询](../log-analytics/log-analytics-log-searches.md)功能
* 详细了解 Azure Monitor 日志及其提供的内容，请参阅[什么是 Azure Monitor 日志？](../operations-management-suite/operations-management-suite-overview.md)
