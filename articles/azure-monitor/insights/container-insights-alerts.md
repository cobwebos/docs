---
title: 使用用于容器的 Azure Monitor 创建性能警报 | Microsoft Docs
description: 本文介绍如何在用于容器的 Azure Monitor 中基于内存和 CPU 利用率日志查询创建自定义 Azure 警报。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: magoedte
ms.openlocfilehash: 5bb0a727adcfb35b5d840a063b6fdb478d150953
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2019
ms.locfileid: "58804814"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>如何为 Azure Monitor 中的容器的性能问题设置警报
用于容器的 Azure Monitor 可监视部署到 Azure 容器实例或 Azure Kubernetes 服务 (AKS) 上托管的托管 Kubernetes 群集的容器工作负荷的性能。 

本文介绍如何启用针对以下情况下发出警报：

* 当 CPU 或内存在节点上的群集利用率超过定义的阈值。
* 当任何控制器中的容器上的 CPU 或内存使用率超出了与相应的资源设置的限制相比你定义的阈值。
* **NotReady**状态节点计数
* Pod 阶段计数**失败**，**挂起**，**未知**，**运行**，或**成功**

发出警报时 CPU 或内存使用率较高的群集节点上，则可以创建指标警报或使用提供的日志查询的指标度量警报规则。 指标警报有以上的日志警报的延迟更低，而日志警报提供了高级查询和比指标警报的复杂性。 用于日志警报查询比较到使用现在运算符最新的日期时间，并可回溯一小时。 用于容器的 Azure Monitor 存储的所有日期均采用 UTC 格式。

在开始之前，如果您不熟悉 Azure Monitor 中的警报，请参阅[Microsoft Azure 中的警报概述](../platform/alerts-overview.md)。 若要了解有关使用日志查询警报的详细信息，请参阅[Azure Monitor 中的日志警报](../platform/alerts-unified-log.md)。 若要了解有关指标警报的详细信息，请参阅[指标警报 Azure Monitor 中的](../platform/alerts-metric-overview.md)。

## <a name="resource-utilization-log-search-queries"></a>资源使用率的日志搜索查询
在本部分中的查询提供以支持每个警报的方案。 查询所需的步骤 7 下[创建警报](#create-alert-rule)下面一节。  

以下查询每隔一分钟计算平均 CPU 利用率作为成员节点的平均 CPU 利用率。  

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuCapacityNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```

以下查询每隔一分钟计算平均内存利用率作为成员节点的平均内存利用率。

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryCapacityBytes';
let usageCounterName = 'memoryRssBytes';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```
>[!IMPORTANT]
>下面的查询包含你的群集和控制器名称的占位符字符串值 < 你的群集名称 > 和 < 你的控制器名称 >。 占位符替换为特定于你的环境的值之前设置警报。 


以下查询计算的控制器中的每个容器实例的 CPU 使用率的百分比表示为容器设置的限制每隔一分钟的平均值为控制器中的所有容器的平均 CPU 利用率。

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuLimitNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

以下查询计算的控制器中的每个容器实例的内存使用率的百分比表示为容器设置的限制每隔一分钟的平均值为控制器中的所有容器的平均内存使用率。

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryLimitBytes';
let usageCounterName = 'memoryRssBytes';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

下面的查询返回所有节点和状态的计数**准备好**并**NotReady**。

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| distinct ClusterName, Computer, TimeGenerated
| summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName, Computer
| join hint.strategy=broadcast kind=inner (
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | summarize TotalCount = count(), ReadyCount = sumif(1, Status contains ('Ready'))
                by ClusterName, Computer,  bin(TimeGenerated, trendBinSize)
    | extend NotReadyCount = TotalCount - ReadyCount
) on ClusterName, Computer, TimeGenerated
| project   TimeGenerated,
            ClusterName,
            Computer,
            ReadyCount = todouble(ReadyCount) / ClusterSnapshotCount,
            NotReadyCount = todouble(NotReadyCount) / ClusterSnapshotCount
| order by ClusterName asc, Computer asc, TimeGenerated desc
```
下面的查询返回 pod 阶段计数基于所有阶段-**失败**，**挂起**，**未知**，**运行**，或**成功**。  

```kusto
let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let clusterName = '<your-cluster-name>';
    KubePodInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ClusterName == clusterName
    | distinct ClusterName, TimeGenerated
    | summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName
    | join hint.strategy=broadcast (
        KubePodInventory
        | where TimeGenerated < endDateTime
        | where TimeGenerated >= startDateTime
        | distinct ClusterName, Computer, PodUid, TimeGenerated, PodStatus
        | summarize TotalCount = count(),
                    PendingCount = sumif(1, PodStatus =~ 'Pending'),
                    RunningCount = sumif(1, PodStatus =~ 'Running'),
                    SucceededCount = sumif(1, PodStatus =~ 'Succeeded'),
                    FailedCount = sumif(1, PodStatus =~ 'Failed')
                 by ClusterName, bin(TimeGenerated, trendBinSize)
    ) on ClusterName, TimeGenerated
    | extend UnknownCount = TotalCount - PendingCount - RunningCount - SucceededCount - FailedCount
    | project TimeGenerated,
              TotalCount = todouble(TotalCount) / ClusterSnapshotCount,
              PendingCount = todouble(PendingCount) / ClusterSnapshotCount,
              RunningCount = todouble(RunningCount) / ClusterSnapshotCount,
              SucceededCount = todouble(SucceededCount) / ClusterSnapshotCount,
              FailedCount = todouble(FailedCount) / ClusterSnapshotCount,
              UnknownCount = todouble(UnknownCount) / ClusterSnapshotCount
| summarize AggregatedValue = avg(PendingCount) by bin(TimeGenerated, trendBinSize)
```

>[!NOTE]
>若要对特定 pod 阶段如发出警报**挂起**， **Failed**，或**未知**，您需要修改查询的最后一行。 有关示例与上的警报*FailedCount* `| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`。  

## <a name="create-alert-rule"></a>创建警报规则
执行以下步骤来使用先前提供的日志搜索规则之一在 Azure Monitor 中创建日志警报。  

>[!NOTE]
>下面的过程需要切换到新的日志警报 API，如中所述[日志警报的切换 API 首选项](../platform/alerts-log-api-switch.md)如果要创建容器资源利用率的警报规则。 
>

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在 Azure 门户的左窗格中选择“监视”。 在“见解”部分，选择“容器”。    
3. 在“监视的群集”选项卡上，单击列表中的某个群集名称以选择该群集。
4. 在“监视”部分下的左窗格中，选择“日志”打开 Azure Monitor 日志页上，用于编写和执行 Azure Log Analytics 查询。
5. 在“日志”页上，单击“+ 新建警报规则”。
6. 在“条件”部分下，单击预定义的自定义日志条件“每当自定义日志搜索为 <logic undefined> 时”。 系统会自动选择“自定义日志搜索”信号类型，因为警报规则的创建是直接从 Azure Monitor 日志页发起的。  
7. 粘贴之一[查询](#resource-utilization-log-search-queries)到前面提供**搜索查询**字段。 

8. 使用以下信息配置警报：

    a. 从“基于”下拉列表中选择“指标度量”。 指标度量将为查询中其值超出指定阈值的每个对象创建一个警报。  
    b. 对于“条件”，请选择“大于”并输入 **75** 作为初始基线**阈值**，或输入符合条件的值。  
    c. 在“触发警报的条件”部分选择“连续违规”，然后从下拉列表中选择“大于”并输入 **2** 作为值。  
    d. 如果在配置一个警报，以便容器 CPU 或内存使用率**聚合基于**选择**ContainerName**从下拉列表。  
    e. 在“评估依据”部分下，将“时段”值修改为 60 分钟。 此规则将每隔五分钟运行一次，返回从当前时间算起过去一小时内创建的记录。 将时间段设置为更宽的时间窗口可以解决数据延迟的可能性，并确保查询返回数据以避免警报永远不会触发的漏报。 

9. 单击“完成”，完成警报规则。
10. 在“警报规则名称”字段中提供警报的名称。 指定“说明”以详细描述该警报的具体信息，并从提供的选项中选择相应的严重性。
11. 若要在创建后立即激活警报规则，请接受“创建后启用规则”选项的默认值。
12. 在最后一个步骤中，选择现有的或创建新的**操作组**，确保每次触发警报时都执行相同的操作，而且这些操作可用于定义的每个规则。 请根据 IT 或 DevOps 运营部门管理事件的方式进行配置。 
13. 单击“创建警报规则”，完成警报规则。 该警报会立即开始运行。

## <a name="next-steps"></a>后续步骤

* 回顾一些[记录查询示例](container-insights-analyze.md#search-logs-to-analyze-data)，以了解可以评估和使用的，或者可针对其他警报方案自定义的预定义查询或示例。 
* 若要继续学习如何使用 Azure Monitor 以及如何监视 AKS 群集的其他方面，请参阅[查看 Azure Kubernetes 服务运行状况](container-insights-analyze.md)
