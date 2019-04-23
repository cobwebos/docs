---
title: 容器中的使用 Azure Monitor 创建性能警报 |Microsoft Docs
description: 本文介绍如何使用用于容器的 Azure Monitor 来创建自定义警报基于日志查询的内存和 CPU 使用率。
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
ms.openlocfilehash: ebe2c2b488e3d71597dd24f5504a14dd7ce6671e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59791535"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>如何为 Azure Monitor 中的容器的性能问题设置警报
用于容器的 azure 监视器监视将部署到 Azure 容器实例，或若要管理 Kubernetes 群集托管在 Azure Kubernetes 服务 (AKS) 的容器工作负荷的性能。

本文介绍如何在以下情况下为启用警报：

* 当在群集节点上的 CPU 或内存使用率超出定义的阈值
* 在控制器内的任何容器上的 CPU 或内存使用率超出定义的阈值设置相应的资源的限制相比时
* *NotReady*状态节点计数
*  *失败*，*挂起*，*未知*，*运行*，或*Succeeded* pod 阶段计数

若要发出警报的 cpu 使用率较高或群集节点上的内存使用率，请使用提供可在创建指标警报或指标度量警报的查询。 指标警报有以上的日志警报的延迟更低。 但日志警报提供高级查询和更高的复杂性。 日志警报查询使用比较到最新的日期时间*现在*运算符，并将推后一个小时。 （用于容器的 azure Monitor 的所有日期以协调世界时 (UTC) 格式都存储。）

如果您不熟悉 Azure 监视器将发出警报，请参阅[Microsoft Azure 中的警报概述](../platform/alerts-overview.md)在开始之前。 若要了解有关使用日志查询的警报的详细信息，请参阅[Azure Monitor 中的日志警报](../platform/alerts-unified-log.md)。 有关指标警报的详细信息，请参阅[指标警报 Azure Monitor 中的](../platform/alerts-metric-overview.md)。

## <a name="resource-utilization-log-search-queries"></a>资源使用率的日志搜索查询
在本部分中查询支持每个警报的方案。 他们正在使用中的步骤 7[创建警报](#create-an-alert-rule)本文的部分。

以下查询计算成员节点的 CPU 使用率每隔一分钟的平均 CPU 使用率的平均。  

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

以下查询计算平均值作为的成员节点的内存使用率每隔一分钟的平均内存利用率。

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
>以下查询使用的占位符值\<你的群集名称 > 和\<你的控制器名称 > 来表示你的群集和控制器。 以替换这些值特定于你的环境时设置警报。

以下查询计算的平均 CPU 使用率的控制器中的每个容器实例每隔一分钟在控制器中的所有容器的平均 CPU 利用率。 度量是限制的为容器设置的百分比。

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

以下查询计算的内存使用率的控制器中的每个容器实例每隔一分钟的平均值为控制器中的所有容器的平均内存使用率。 度量是限制的为容器设置的百分比。

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

下面的查询返回所有节点和有状态的计数*准备好*并*NotReady*。

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
下面的查询返回 pod 阶段计数基于所有阶段：*失败*，*挂起*，*未知*，*运行*，或*成功*。  

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
>若要对特定 pod 阶段中，如发出警报*挂起*， *Failed*，或*未知*，修改查询的最后一行。 例如，若要在警报*FailedCount*使用： <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

## <a name="create-an-alert-rule"></a>创建警报规则
请按照下列步骤以使用前面的日志搜索规则之一在 Azure Monitor 中创建日志警报。  

>[!NOTE]
>创建容器资源利用率需要切换到新的日志警报规则的以下过程向 API 发出警报，如中所述[日志警报的切换 API 首选项](../platform/alerts-log-api-switch.md)。
>

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 选择**监视器**上的左侧窗格中。 下**Insights**，选择**容器**。
3. 上**监视群集**选项卡上，从列表中选择一个群集。
4. 在左侧窗格中**监视**，选择**日志**以打开 Azure Monitor 日志页。 使用此页来编写和执行 Azure Log Analytics 查询。
5. 上**日志**页上，选择 **+ 新建警报规则**。
6. 在中**条件**部分中，选择**只要自定义日志搜索\<逻辑未定义 >** 预定义的自定义日志条件。 **自定义日志搜索**因为我们将直接从 Azure Monitor 日志页创建警报规则，会自动选择信号类型。  
7. 粘贴之一[查询](#resource-utilization-log-search-queries)到前面提供**搜索查询**字段。
8. 配置警报，如下所示：

    1. 从“基于”下拉列表中选择“指标度量”。 公制度量单位上面我们指定的阈值的值在查询中创建一个警报，以便每个对象。
    1. 有关**条件**，选择**大于**，然后输入**75**作为初始基线**阈值**。 或输入不同的值符合条件的。
    1. 在中**触发器警报基于上**部分中，选择**连续违规数**。 从下拉列表中，选择**大于**，并输入**2**。
    1. 若要配置一个警报，以便容器 CPU 或内存使用率，在**聚合基于**，选择**ContainerName**。 
    1. 在中**评估基于**部分中，设置**段**值设为**60 分钟**。 此规则将每隔 5 分钟运行一次，并返回从当前时间的最后一小时创建的记录。 将时间段设置为宽窗口帐户以执行潜在的数据滞后时间。 它还确保查询返回数据以避免警报永远不会触发假负。

9. 选择**完成**完成警报规则。
10. 中输入一个名称**警报规则名称**字段。 指定**说明**，它提供有关警报的详细信息。 然后从提供的选项中选择适当的严重性级别。
11. 若要立即激活警报规则，接受默认值**创建后的启用规则**。
12. 选择一个现有**操作组**或创建新的组。 此步骤可确保每次触发警报时，采取同样的操作。 配置具体取决于您的 IT 或 DevOps 运营团队管理事件。
13. 选择**创建警报规则**完成警报规则。 该警报会立即开始运行。

## <a name="next-steps"></a>后续步骤

* 视图[记录查询示例](container-insights-analyze.md#search-logs-to-analyze-data)若要了解有关预定义的查询和示例，以评估或自定义的其他警报方案。
* 若要了解有关 Azure Monitor 和如何监视你的 AKS 群集的其他方面的详细信息，请参阅[查看 Azure Kubernetes 服务运行状况](container-insights-analyze.md)。
