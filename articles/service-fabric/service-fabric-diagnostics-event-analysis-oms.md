---
title: Azure Service Fabric 事件分析与 Azure Monitor 日志
description: 了解如何使用 Azure Monitor 日志来可视化和分析事件，以便对 Azure Service Fabric 群集进行监视和诊断。
author: srrengar
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: srrengar
ms.openlocfilehash: 40dd930aa21e3056d5ecc908359215d6874ed8ae
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464736"
---
# <a name="event-analysis-and-visualization-with-azure-monitor-logs"></a>Azure Monitor 日志进行事件分析和可视化
 Azure Monitor 日志收集并分析云中托管的应用程序和服务的遥测，并提供分析工具以帮助最大限度地提高其可用性和性能。 本文概述了如何在 Azure Monitor 日志中运行查询，以获取见解并解决群集中发生的问题。 本文解决以下常见问题：

* 如何排查运行状况事件问题？
* 如何知道节点已关闭？
* 如何知道我的应用程序服务已启动或停止？

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="overview-of-the-log-analytics-workspace"></a>Log Analytics 工作区概述

>[!NOTE] 
>虽然默认情况下诊断存储已在群集创建时启用，但你仍必须设置 Log Analytics 工作区以从诊断存储中读取。

Azure Monitor 日志从托管资源（包括 Azure 存储表或代理）收集数据，并在中央存储库中维护数据。 之后这些数据可用于分析、报警、可视化或进一步导出。 Azure Monitor 日志支持事件、性能数据或任何其他自定义数据。 查看[配置诊断扩展以聚合事件的步骤](service-fabric-diagnostics-event-aggregation-wad.md)，以及[创建 Log Analytics 工作区以从存储中的事件中进行读取的步骤](service-fabric-diagnostics-oms-setup.md)，以确保数据流入 Azure Monitor 日志。

Azure Monitor 日志接收到数据后，Azure 提供多个*监视解决方案*，这些解决方案是预打包的解决方案或操作面板，用于监视传入数据，并针对多个方案进行了自定义。 包括 Service Fabric 分析解决方案和容器解决方案。使用 Service Fabric 群集时，这两种解决方案与诊断和监视最为相关。 本文介绍如何使用在工作区中创建的 Service Fabric 分析解决方案。

## <a name="access-the-service-fabric-analytics-solution"></a>访问 Service Fabric 分析解决方案

在[Azure 门户](https://portal.azure.com)中，切换到在其中创建了 Service Fabric 分析解决方案的资源组。

选择资源 **ServiceFabric\<nameOfOMSWorkspace\>** 。

在 `Summary` 中，将看到每个已启用的解决方案的图形形式的磁贴，包括 Service Fabric 的磁贴。 单击 **Service Fabric** 图形以转到 Service Fabric 分析解决方案。

![Service Fabric 解决方案](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

下图是 Service Fabric 分析解决方案的主页。 此主页提供了群集中正在发生的事件的快照视图。

![Service Fabric 解决方案](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

 如果创建群集时启用了诊断，则可以看到以下对象的事件： 

* [Service Fabric 群集事件](service-fabric-diagnostics-event-generation-operational.md)
* [Reliable Actors 编程模型事件](service-fabric-reliable-actors-diagnostics.md)
* [Reliable Services 编程模型事件](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>除了现成的 Service Fabric 事件之外，可以通过[更新诊断扩展的配置](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)来收集更详细的系统事件。

## <a name="view-service-fabric-events-including-actions-on-nodes"></a>查看 Service Fabric 事件，包括对节点执行的操作

在“Service Fabric 分析”页上，单击“Service Fabric 事件”对应的图形。

![Service Fabric 解决方案操作通道](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

单击“列表”，在列表中查看事件。 打开该列表后，将会看到已收集的所有系统事件。 以下内容摘自 Azure 存储帐户中的“WADServiceFabricSystemEventsTable”以供参考，类似地，接下来看到的 Reliable Services 和 Reliable Actors 事件也都摘自相应的表。
    
![查询操作通道](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

或者，可以单击左侧的放大镜并使用 Kusto 查询语言找到所需的内容。 例如，若要查找针对群集中的节点执行的所有操作，可以使用以下查询。 在[操作通道事件参考](service-fabric-diagnostics-event-generation-operational.md)中可以找到下面使用的事件 ID。

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

可以基于其他许多字段执行查询，例如，特定的节点（计算机）、系统服务 (TaskName)。

## <a name="view-service-fabric-reliable-service-and-actor-events"></a>查看 Service Fabric Reliable Services 和 Reliable Actors 事件

在“Service Fabric 分析”页上，单击“Reliable Services”对应的图形。

![Service Fabric 解决方案 Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

单击“列表”，在列表中查看事件。 在此处可以查看来自 Reliable Services 的事件。 可以看到服务 runasync 在启动和完成（通常发生在部署和升级时）时的不同事件。 

![查询 Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

可以类似的方式查看 Reliable Actors 事件。 若要为 Reliable Actors 配置更详细的事件，需要在诊断扩展的配置中更改 `scheduledTransferKeywordFilter`（如下所示）。 [Reliable Actors 事件参考](service-fabric-reliable-actors-diagnostics.md#keywords)中提供了这些参数值的详细信息。

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

Kusto 查询语言非常强大。 可以运行另一个有用查询来找出哪些节点正在生成大多数事件。 以下屏幕截图中的查询显示了与特定服务和节点聚合的 Reliable Services 操作事件。

![每个节点的查询事件](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>后续步骤

* 若要启用基础结构监视（即性能计数器），请转到[添加 Log Analytics 代理](service-fabric-diagnostics-oms-agent.md)。 该代理将收集性能计数器，并将其添加到现有工作区。
* 对于本地群集，Azure Monitor 日志提供了一个可用于将数据发送到 Azure Monitor 日志的网关（HTTP 转发代理）。 有关详细信息，请参阅[使用 Log Analytics 网关将计算机连接到无 Internet 访问 Azure Monitor 日志](../azure-monitor/platform/gateway.md)。
* 配置[自动警报](../log-analytics/log-analytics-alerts.md)来帮助进行检测和诊断。
* 掌握 Azure Monitor 日志中提供的[日志搜索和查询](../log-analytics/log-analytics-log-searches.md)功能。
* 详细了解 Azure Monitor 日志及其提供的内容，请参阅[什么是 Azure Monitor 日志？](../operations-management-suite/operations-management-suite-overview.md)。
