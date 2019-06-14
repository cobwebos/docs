---
title: 容器中的使用 Azure Monitor 创建性能警报 |Microsoft Docs
description: 本文介绍如何使用用于容器的 Azure Monitor 基于内存和 CPU 利用率的日志查询创建自定义警报。
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
ms.date: 04/26/2019
ms.author: magoedte
ms.openlocfilehash: 46ac6794272728069d50479f8cd097185bfeeb1a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65072390"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>如何在用于容器的 Azure Monitor 中针对性能问题设置警报
用于容器的 Azure Monitor 可以监视部署到 Azure 容器实例或 Azure Kubernetes 服务 (AKS) 上托管的托管 Kubernetes 群集的容器工作负荷的性能。

本文介绍如何针对以下情况启用警报：

- 当在群集节点上的 CPU 或内存使用率超过阈值
- 当在控制器内的任何容器上的 CPU 或内存使用率超出了相比相应的资源设置的限制阈值
- “未就绪”状态节点计数 
- “失败”、“挂起”、“未知”、“正在运行”或“成功”Pod 阶段计数     
- 当在群集节点上的可用磁盘空间超过阈值 

若要高 CPU 或内存使用率，或在群集节点上的可用磁盘空间不足空间发出警报，请使用提供的用于创建指标警报或指标度量警报的查询。 指标警报的延迟要低于日志警报。 但是，日志警报提供高级查询和更精密的信息。 日志警报查询使用 *now* 运算符将某个日期时间与当前时间进行比较，并将时间推后一个小时。 （用于容器的 Azure Monitor 以协调世界时 (UTC) 格式存储所有日期。）

如果您不熟悉 Azure 监视器将发出警报，请参阅[Microsoft Azure 中的警报概述](../platform/alerts-overview.md)在开始之前。 若要详细了解使用日志查询的警报，请参阅 [Azure Monitor 中的日志警报](../platform/alerts-unified-log.md)。 有关指标警报的详细信息，请参阅[指标警报 Azure Monitor 中的](../platform/alerts-metric-overview.md)。

## <a name="resource-utilization-log-search-queries"></a>资源利用率日志搜索查询
本部分所述的查询支持每种警报方案。 本文[创建警报](#create-an-alert-rule)部分的步骤 7 中使用了这些查询。

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
>以下查询使用占位符值 \<your-cluster-name> 和 \<your-controller-name> 来分别表示群集和控制器。 设置警报时，请将这些占位符替换为环境特定的值。

以下查询每隔一分钟计算控制器中所有容器的平均 CPU 利用率，作为控制器中每个容器实例的平均 CPU 利用率。 度量值是针对容器设置的限制百分比。

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

以下查询每隔一分钟计算控制器中所有容器的平均内存利用率，作为控制器中每个容器实例的平均内存利用率。 度量值是针对容器设置的限制百分比。

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

以下查询返回处于“就绪”和“未就绪”状态的所有节点和计数。  

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
以下查询基于所有阶段返回 Pod 阶段计数：“失败”、“挂起”、“未知”、“正在运行”或“成功”。       

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
>若要针对特定的 Pod 阶段（例如“挂起”、“失败”或“未知”）发出警报，请修改查询的最后一行。    例如，若要针对“失败计数”发出警报，请使用：  <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

以下查询将返回群集节点磁盘超过 90%的可用空间使用。 若要获取群集 ID，请首先运行以下查询并复制中的值`ClusterId`属性：

```kusto
InsightsMetrics
| extend Tags = todynamic(Tags)            
| project ClusterId = Tags['container.azm.ms/clusterId']   
| distinct tostring(ClusterId)   
``` 

```kusto
let clusterId = '<cluster-id>';
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
InsightsMetrics
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where Origin == 'container.azm.ms/telegraf'            
| where Namespace == 'disk'            
| extend Tags = todynamic(Tags)            
| project TimeGenerated, ClusterId = Tags['container.azm.ms/clusterId'], Computer = tostring(Tags.hostName), Device = tostring(Tags.device), Path = tostring(Tags.path), DiskMetricName = Name, DiskMetricValue = Val   
| where ClusterId =~ clusterId       
| where DiskMetricName == 'used_percent'
| summarize AggregatedValue = max(DiskMetricValue) by bin(TimeGenerated, trendBinSize)
| where AggregatedValue >= 90
```

## <a name="create-an-alert-rule"></a>创建警报规则
在 Azure Monitor 中，遵循以下步骤使用前面提供的日志搜索规则之一创建日志警报。  

>[!NOTE]
>遵循以下过程针对容器资源利用率创建警报规则需要根据[切换日志警报的 API 首选项](../platform/alerts-log-api-switch.md)中所述，切换到新的日志警报 API。
>

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧窗格中选择“监视”。  在“见解”下，选择“容器”。  
3. 在“监视的群集”选项卡上，从列表中选择一个群集。 
4. 在左侧窗格中的“监视”下，选择“日志”打开 Azure Monitor 日志页。   使用此页编写并执行 Azure Log Analytics 查询。
5. 在“日志”页上，选择“+新建警报规则”。  
6. 在“条件”部分，选择预定义的自定义日志条件“每当自定义日志搜索为 \<logic undefined> 时”。   系统会自动选择“自定义日志搜索”信号类型，因为我们要直接从 Azure Monitor 日志页创建警报规则。   
7. 将前面提供的某个[查询](#resource-utilization-log-search-queries)粘贴到“搜索查询”字段中。 
8. 按如下所述配置警报：

    1. 从“基于”下拉列表中选择“指标度量”   。 指标度量将为查询中其值超过指定阈值的每个对象创建一个警报。
    1. 有关**条件**，选择**大于**，然后输入**75**作为初始基线**阈值**CPU 和内存利用率警报. 对于低磁盘空间警报中，输入**90**。 或输入符合条件的其他值。
    1. 在“触发警报的条件”部分选择“连续违规”。   从下拉列表中选择“大于”并输入 **2**。 
    1. 若要针对容器 CPU 或内存利用率配置警报，请在“聚合依据”下选择“容器名称”。   若要配置的群集节点磁盘空间不足警报，请选择**ClusterId**。
    1. 在“评估依据”部分，将“时段”值设置为 **60 分钟**。   该规则将每隔 5 分钟运行一次，返回从当前时间算起过去一小时内创建的记录。 将时段设置为较宽的时限可以适应潜在的数据延迟。 这也可以确保查询返回数据，以避免漏报，导致警报永远不会激发。

9. 选择“完成”以完成警报规则。 
10. 在“警报规则名称”字段中输入一个名称。  填写“说明”以提供有关该警报的详细信息。  从提供的选项中选择适当的严重性级别。
11. 若要立即激活该警报规则，请接受“创建后启用规则”选项的默认值。 
12. 选择现有的**操作组**或创建新组。 此步骤确保每次触发警报时都执行相同的操作。 请根据 IT 或 DevOps 运营团队管理事件的方式进行配置。
13. 选择“创建警报规则”以完成警报规则。  该警报会立即开始运行。

## <a name="next-steps"></a>后续步骤

- 视图[记录查询示例](container-insights-log-search.md#search-logs-to-analyze-data)若要查看预定义的查询和示例，以评估或自定义的警报、 可视化，或分析你的群集。
- 若要详细了解 Azure Monitor 以及如何监视 AKS 群集的其他方面，请参阅[查看 Azure Kubernetes 服务运行状况](container-insights-analyze.md)。
