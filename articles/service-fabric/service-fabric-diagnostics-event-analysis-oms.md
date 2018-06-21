---
title: 使用 Log Analytics 进行 Azure Service Fabric 事件分析 | Microsoft Docs
description: 了解如何通过使用 Log Analytics 可视化和分析事件来监视和诊断 Azure Service Fabric 群集。
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/29/2018
ms.author: srrengar
ms.openlocfilehash: 184faa0f6171ff00ab3c2398f693e9c7ad015d33
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839582"
---
# <a name="event-analysis-and-visualization-with-log-analytics"></a>使用 Log Analytics 进行事件分析和可视化

Log Analytics（也称为 OMS (Operations Management Suite)）集中了管理服务，有助于监视和诊断云中托管的应用程序的服务。 本文概述如何在 Log Analytics 中运行查询，以获取见解并排查群集中发生的问题。 本文解决以下常见问题：

* 如何排查运行状况事件问题？
* 如何知道节点已关闭？
* 如何知道我的应用程序服务已启动或停止？

## <a name="log-analytics-workspace"></a>Log Analytics 工作区

Log Analytics 从托管资源（包括 Azure 存储表或代理）收集数据，并在中央存储库中维护它们。 之后这些数据可用于分析、报警、可视化或进一步导出。 Log Analytics 支持事件、性能数据或其他任何自定义数据。 查看[配置诊断扩展以聚合事件的步骤](service-fabric-diagnostics-event-aggregation-wad.md)和[创建 Log Analytics 工作区以从存储中的事件读取数据的步骤](service-fabric-diagnostics-oms-setup.md)，确保数据流入 Log Analytics。

Log Analytics 收到数据后，Azure 会提供多个预打包的管理解决方案，用于监视传入数据，且根据不同情形进行自定义。 包括 Service Fabric 分析解决方案和容器解决方案。使用 Service Fabric 群集时，这两种解决方案与诊断和监视最为相关。 本文介绍如何使用在工作区中创建的 Service Fabric 分析解决方案。

## <a name="access-the-service-fabric-analytics-solution"></a>访问 Service Fabric 分析解决方案

1. 在 Azure 门户中，转到在其中创建了 Service Fabric 分析解决方案的资源组。

2. 选择资源 **ServiceFabric\<nameOfOMSWorkspace\>**。

2. 在“摘要”中，将看到每个已启用的解决方案的图形形式的磁贴，包括 Service Fabric 的磁贴。 单击“Service Fabric”图形（下面的第一张图）转到 Service Fabric 分析解决方案（下面的第二张图）。

    ![OMS SF 解决方案](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_summary.PNG)

    ![OMS SF 解决方案](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_solution.PNG)

上图是 Service Fabric 分析解决方案的主页。 这是群集中发生的情况的快照视图。 如果创建群集时启用了诊断，则可以看到以下对象的事件： 

* [操作通道](service-fabric-diagnostics-event-generation-operational.md)：Service Fabric 平台（系统服务集合）执行的较高级操作。
* [Reliable Actors 编程模型事件](service-fabric-reliable-actors-diagnostics.md)
* [Reliable Services 编程模型事件](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>除了操作通道以外，可以通过[更新诊断扩展的配置](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)来收集更详细的系统事件。

### <a name="view-service-fabric-events-including-actions-on-nodes"></a>查看 Service Fabric 事件，包括对节点执行的操作

1. 在“Service Fabric 分析”页上，单击“Service Fabric 事件”对应的图形。

    ![OMS SF 解决方案操作通道](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events_selection.png)

2. 单击“列表”，在列表中查看事件。 打开该列表后，将会看到已收集的所有系统事件。 以下内容插自 Azure 存储帐户中的 WADServiceFabricSystemEventsTable 以供参考，类似地，接下来看到的 Reliable Services 和 Reliable Actors 事件也都摘自相应的表。
    
    ![OMS 查询操作通道](media/service-fabric-diagnostics-event-analysis-oms/oms_service_fabric_events.png)

或者，可以单击左侧的放大镜并使用 Kusto 查询语言找到所需的内容。 例如，若要查找针对群集中的节点执行的所有操作，可以使用以下查询。 在[操作通道事件参考](service-fabric-diagnostics-event-generation-operational.md)中可以找到下面使用的事件 ID。

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

可以基于其他许多字段执行查询，例如，特定的节点（计算机）、系统服务 (TaskName)。

### <a name="view-service-fabric-reliable-service-and-actor-events"></a>查看 Service Fabric Reliable Services 和 Reliable Actors 事件

1. 在“Service Fabric 分析”页上，单击“Reliable Services”对应的图形。

    ![OMS SF 解决方案 Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_services_events_selection.png)

2. 单击“列表”，在列表中查看事件。 在此处可以查看来自 Reliable Services 的事件。 可以看到服务 runasync 在启动和完成（通常发生在部署和升级时）时的不同事件。 

    ![OMS 查询 Reliable Services](media/service-fabric-diagnostics-event-analysis-oms/oms_reliable_service_events.png)

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

![每个节点的 OMS 查询事件](media/service-fabric-diagnostics-event-analysis-oms/oms_kusto_query.png)

## <a name="next-steps"></a>后续步骤

* 若要启用基础结构监视（例如性能计数器），请转到[添加 OMS 代理](service-fabric-diagnostics-oms-agent.md)。 该代理将收集性能计数器，并将其添加到现有工作区。
* 对于本地群集，OMS 提供可用于向 OMS 发送数据的网关（HTTP 正向代理）。 有关更多信息，请参阅[使用 OMS 网关将无法访问 Internet 的计算机连接到 OMS](../log-analytics/log-analytics-oms-gateway.md)
* 配置 OMS，以设置[自动警报](../log-analytics/log-analytics-alerts.md)来帮助进行检测和诊断。
* 掌握 Log Analytics 中提供的[日志搜索和查询](../log-analytics/log-analytics-log-searches.md)功能
* 有关 Log Analytics 及其功能的更详细概述，请参阅[什么是 Log Analytics？](../operations-management-suite/operations-management-suite-overview.md)
