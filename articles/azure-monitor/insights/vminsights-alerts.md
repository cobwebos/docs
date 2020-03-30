---
title: 来自 Azure 监视器的 VM 警报
description: 介绍如何从 Azure 监视器为 VM 收集的性能数据创建警报规则。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/23/2020
ms.openlocfilehash: 987537d8497b3d8f2728941334d8328320ec6997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289597"
---
# <a name="how-to-create-alerts-from-azure-monitor-for-vms"></a>如何从 Azure 监视器为 VM 创建警报
[Azure 监视器中的警报](../platform/alerts-overview.md)会主动通知您监视数据中有趣的数据和模式。 VM 的 Azure 监视器不包括预先配置的警报规则，但您可以根据它收集的数据创建自己的警报规则。 本文提供有关创建警报规则（包括一组示例查询）的指导。


## <a name="alert-rule-types"></a>警报规则类型
Azure 监视器具有不同类型的[警报规则](../platform/alerts-overview.md#what-you-can-alert-on)，具体取决于用于创建警报的数据。 Azure 监视器为 VM 收集的所有数据都存储在支持[日志警报的](../platform/alerts-log.md)Azure 监视器日志中。 当前不能将[指标警报](../platform/alerts-log.md)与从 Azure 监视器收集的 VM 性能数据一起使用，因为数据不会收集到 Azure 监视器指标中。 要收集指标警报的数据，请安装 Windows VM 的[诊断扩展](../platform/diagnostics-extension-overview.md)或 Linux VM 的[Telegraf 代理](../platform/collect-custom-metrics-linux-telegraf.md)，以便将性能数据收集到指标中。

Azure 监视器中有两种类型的日志警报：

- 当查询返回至少指定数量的记录时，[结果警报数](../platform/alerts-unified-log.md#number-of-results-alert-rules)创建单个警报。 这些是日志分析代理收集的非数字数据以及[日志分析代理](../platform/log-analytics-agent.md)收集的 Windows 和 Syslog 事件或分析多台计算机的性能趋势的理想选择。
- [指标度量警报](../platform/alerts-unified-log.md#metric-measurement-alert-rules)为查询中的每个记录创建单独的警报，该记录的值超过警报规则中定义的阈值。 这些警报规则非常适合 Azure 监视器为 VM 收集的性能数据，因为它们可以为每台计算机创建单独的警报。


## <a name="alert-rule-walkthrough"></a>警报规则演练
本节介绍使用来自 VM Azure 监视器的性能数据创建指标度量警报规则。 您可以将此基本过程与各种日志查询一起用于在不同的性能计数器上发出警报。

首先，[在使用 Azure 监视器 中创建、查看和管理日志警报](../platform/alerts-log.md)的过程之后创建新的警报规则。 对于**资源**，选择 Azure 监视器 VM 在订阅中使用的日志分析工作区。 由于日志警报规则的目标资源始终是日志分析工作区，因此日志查询必须包含特定虚拟机或虚拟机缩放集的任何筛选器。 

对于警报规则**的条件**，请使用[以下部分](#sample-alert-queries)中的查询之一作为**搜索查询**。 查询必须返回名为*聚合值*的数字属性。 它应按计算机汇总数据，以便可以为每个超过阈值的虚拟机创建单独的警报。

在 **"警报"逻辑**中，选择 **"指标"度量**，然后提供**阈值**。 在 **"基于触发警报"** 中，指定在创建警报之前必须超出阈值的次数。 例如，您可能不关心处理器是否超过阈值一次，然后恢复正常，但您确实在多次连续测量中是否继续超过阈值。

**基于"评估"** 部分定义查询的运行频率和查询的时间窗口。 在下面的示例中，查询将每 15 分钟运行一次，并计算在过去 15 分钟内收集的性能值。


![指标测量警报规则](media/vminsights-alerts/metric-measurement-alert.png)

## <a name="sample-alert-queries"></a>示例警报查询
使用 Azure 监视器为 VM 收集的性能数据，可以使用以下查询与指标度量警报规则一起使用。 每个计算机汇总数据，以便为每个值超过阈值的每台计算机创建警报。

### <a name="cpu-utilization"></a>CPU 使用率

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-mb"></a>可用内存（MB）

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-percentage"></a>可用内存百分比

```kusto
InsightsMetrics 
| where Origin == "vm.azm.ms" 
| where Namespace == "Memory" and Name == "AvailableMB" 
| extend TotalMemory = toreal(todynamic(Tags)["vm.azm.ms/memorySizeMB"])
| extend AvailableMemoryPercentage = (toreal(Val) / TotalMemory) * 100.0 
| summarize AggregatedValue = avg(AvailableMemoryPercentage) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---all-disks-on-each-computer"></a>使用逻辑磁盘 - 每台计算机上的所有磁盘

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---individual-disks"></a>使用的逻辑磁盘 - 单个磁盘

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, Disk
```

### <a name="logical-disk-iops"></a>逻辑磁盘 IOPS

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "TransfersPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) ), Computer, _ResourceId, Disk
```

### <a name="logical-disk-data-rate"></a>逻辑磁盘数据速率

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "BytesPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) , Computer, _ResourceId, Disk
```

### <a name="network-interfaces-bytes-received---all-interfaces"></a>接收的网络接口字节 - 所有接口

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="network-interfaces-bytes-received---individual-interfaces"></a>接收的网络接口字节 - 单个接口

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="network-interfaces-bytes-sent---all-interfaces"></a>发送的网络接口字节 - 所有接口

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="network-interfaces-bytes-sent---individual-interfaces"></a>发送的网络接口字节 - 单个接口

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="virtual-machine-scale-set"></a>虚拟机规模集
使用订阅 ID、资源组和虚拟机缩放集名称进行修改。

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/my-vm-scaleset" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="specific-virtual-machine"></a>特定虚拟机
使用订阅 ID、资源组和 VM 名称进行修改。

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId =~ "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m)
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-subscription"></a>订阅中所有计算资源的 CPU 利用率
使用订阅 ID 进行修改。

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" and (_ResourceId contains "/providers/Microsoft.Compute/virtualMachines/" or _ResourceId contains "/providers/Microsoft.Compute/virtualMachineScaleSets/")
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-resource-group"></a>资源组中所有计算资源的 CPU 利用率
使用订阅 ID 和资源组进行修改。

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/"
or _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId

```

## <a name="next-steps"></a>后续步骤

- 了解有关[Azure 监视器 中的警报](../platform/alerts-overview.md)的更多详细信息。
- 详细了解使用[Azure 监视器中的数据为 VM 的日志查询](vminsights-log-search.md)。
