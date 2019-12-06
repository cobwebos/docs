---
title: 为容器 Azure Monitor 创建性能警报 |Microsoft Docs
description: 本文介绍如何根据 Azure Monitor 为容器创建自定义警报，以了解内存和 CPU 利用率的日志查询。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 04/26/2019
ms.openlocfilehash: 66baa3095744c8b486430d587b992ba507d87733
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74841619"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>如何为容器的 Azure Monitor 中的性能问题设置警报

容器 Azure Monitor 用于监视部署到 Azure 容器实例的容器工作负荷的性能，或监视 Azure Kubernetes 服务（AKS）上托管的托管 Kubernetes 群集的性能。

本文介绍如何在以下情况下启用警报：

- 当群集节点上的 CPU 或内存使用率超过阈值时
- 当控制器内任何容器上的 CPU 或内存使用率超过阈值（与在相应资源上设置的限制）时
- *NotReady*状态节点计数
- *失败*、*挂起*、*未知*、*正在运行*或已*成功*的 pod 阶段计数
- 当群集节点上的可用磁盘空间超过阈值时 

若要在群集节点上发出 CPU 或内存使用率较高或可用磁盘空间不足的警报，请使用提供的查询来创建指标警报或指标度量警报。 指标警报的延迟低于日志警报。 但日志警报提供高级查询和更复杂的查询。 日志警报查询通过使用*now*运算符并返回一小时，将日期时间与当前时间进行比较。 （Azure Monitor 容器以协调世界时（UTC）格式存储所有日期。）

如果不熟悉 Azure Monitor 警报，请在开始之前参阅[Microsoft Azure 中的警报概述](../platform/alerts-overview.md)。 若要了解有关使用日志查询的警报的详细信息，请参阅[Azure Monitor 中的日志警报](../platform/alerts-unified-log.md)。 有关指标警报的详细信息，请参阅[Azure Monitor 中的指标警报](../platform/alerts-metric-overview.md)。

## <a name="resource-utilization-log-search-queries"></a>资源利用率日志搜索查询

本部分中的查询支持每个警报方案。 本文的[创建警报](#create-an-alert-rule)部分的步骤7中使用了它们。

下面的查询将平均 CPU 使用率计算为成员节点每分钟的平均 CPU 使用率。  

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

下面的查询将平均内存使用率计算为每分钟平均成员节点的内存使用率。

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
>以下查询使用占位符值 \<群集名称 > 并 \<控制器名称 > 来表示群集和控制器。 在设置警报时，将它们替换为特定于您的环境的值。

下面的查询计算控制器中所有容器的平均 CPU 使用率，作为控制器每分钟的每个容器实例的平均 CPU 使用率。 度量值是为容器设置的限制的百分比。

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

下面的查询计算控制器中所有容器的平均内存使用率，作为控制器每分钟的每个容器实例的平均内存使用率。 度量值是为容器设置的限制的百分比。

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

下面的查询将返回状态为 "*就绪*" 和 " *NotReady*" 的所有节点和计数。

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
以下查询根据所有阶段返回 pod 阶段计数： "*失败*"、"*挂起*"、"*未知*"、"*正在运行*" 或 "*成功*"。  

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
>若要对某些 pod 阶段（如*挂起*、*失败*或*未知*）发出警报，请修改查询的最后一行。 例如，若要在*FailedCount*上使用警报，请执行以下操作： <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

下面的查询返回的群集节点磁盘超过了90% 的可用空间。 若要获取群集 ID，请先运行下面的查询，然后从 `ClusterId` 属性复制值：

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
| where Namespace == 'container.azm.ms/disk'            
| extend Tags = todynamic(Tags)            
| project TimeGenerated, ClusterId = Tags['container.azm.ms/clusterId'], Computer = tostring(Tags.hostName), Device = tostring(Tags.device), Path = tostring(Tags.path), DiskMetricName = Name, DiskMetricValue = Val   
| where ClusterId =~ clusterId       
| where DiskMetricName == 'used_percent'
| summarize AggregatedValue = max(DiskMetricValue) by bin(TimeGenerated, trendBinSize)
| where AggregatedValue >= 90
```

## <a name="create-an-alert-rule"></a>创建警报规则

按照以下步骤，使用之前提供的一个日志搜索规则在 Azure Monitor 中创建日志警报。 若要使用 ARM 模板创建，请参阅[使用 Azure 资源模板创建示例日志警报](../platform/alerts-log.md#sample-log-alert-creation-using-azure-resource-template)。

>[!NOTE]
>以下为容器资源利用率创建警报规则的过程要求你切换到新的日志警报 API，如[日志警报的交换机 API 首选项](../platform/alerts-log-api-switch.md)中所述。
>

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 从左侧窗格中选择 "**监视器**"。 在 "**见解**" 下，选择 "**容器**"。
3. 在 "**监视的群集**" 选项卡上，从列表中选择一个分类。
4. 在左侧窗格中的 "**监视**" 下，选择 "**日志**" 以打开 Azure Monitor 日志 "页。 使用此页可以编写和执行 Azure Log Analytics 查询。
5. 在 "**日志**" 页上，选择 " **+ 新建警报规则**"。
6. 在 "**条件**" 部分中，选择 "**只要自定义日志搜索 \<逻辑未定义 >** 预定义的自定义日志条件"。 **自定义日志搜索**信号类型是自动选择的，因为我们是直接从 "Azure Monitor 日志" 页创建警报规则。  
7. 将之前提供的[查询](#resource-utilization-log-search-queries)之一粘贴到 "**搜索查询**" 字段中。
8. 按如下所示配置警报：

    1. 从“基于”下拉列表中选择“指标度量”。 指标度量为查询中每个对象创建一个警报，该警报的值高于指定的阈值。
    1. 对于 "**条件**"，选择 "**大于**"，然后输入**75**作为 CPU 和内存使用率警报的初始基线**阈值**。 对于 "磁盘空间不足" 警报，请输入**90**。 或者输入满足条件的其他值。
    1. 在 "**基于的触发器警报**" 部分中，选择 "**连续违例**"。 从下拉列表中选择 "**大于**"，然后输入**2**。
    1. 若要为容器 CPU 或内存使用率配置警报，请在 "**聚合**为" 下选择 "**容器**"。 若要配置群集节点低磁盘警报，请选择 " **ClusterId**"。
    1. 在 "**基于计算**依据" 部分中，将 "**周期**" 值设置为**60 分钟**。 此规则将每5分钟运行一次，并从当前时间返回最近一小时内创建的记录。 将时间段设置为宽窗口帐户，以获得潜在的数据延迟。 它还可确保查询返回数据，以避免警报从不触发的假负。

9. 选择 **"完成"** 以完成警报规则。
10. 在 "**警报规则名称**" 字段中输入一个名称。 指定提供警报详细信息的**说明**。 然后从提供的选项中选择适当的严重性级别。
11. 若要立即激活警报规则，请接受 "**创建时启用规则**" 的默认值。
12. 选择现有**操作组**或创建新组。 此步骤可确保每次触发警报时都执行相同的操作。 根据 IT 或 DevOps 操作团队管理事件的方式进行配置。
13. 选择 "**创建警报规则**" 以完成警报规则。 该警报会立即开始运行。

## <a name="next-steps"></a>后续步骤

- 查看[日志查询示例](container-insights-log-search.md#search-logs-to-analyze-data)，了解预定义的查询和示例，以便对群集进行警报、可视化或分析。
- 若要详细了解 Azure Monitor 以及如何监视 Kubernetes 群集的其他方面，请参阅[查看 Kubernetes 群集性能](container-insights-analyze.md)和[查看 Kubernetes 群集运行状况](container-insights-health.md)。
