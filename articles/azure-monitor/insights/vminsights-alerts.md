---
title: 用于 VM 的 Azure Monitor 发出的警报
description: 描述如何根据用于 VM 的 Azure Monitor 收集的性能数据创建警报规则。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/23/2020
ms.openlocfilehash: be469ab3b05c54ebc5afa6bd6d129efd8d4ba692
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91254799"
---
# <a name="how-to-create-alerts-from-azure-monitor-for-vms"></a>如何从用于 VM 的 Azure Monitor 创建警报
[Azure Monitor 中的警报在](../platform/alerts-overview.md) 监视数据中主动通知你感兴趣的数据和模式。 用于 VM 的 Azure Monitor 不包括预配置的警报规则，但你可以基于它收集的数据创建自己的警报规则。 本文提供了有关创建警报规则的指导，包括一组示例查询。


## <a name="alert-rule-types"></a>预警规则类型
Azure Monitor 具有 [不同类型的警报规则](../platform/alerts-overview.md#what-you-can-alert-on) ，这些规则基于用于创建警报的数据。 用于 VM 的 Azure Monitor 收集的所有数据都存储在支持 [日志警报](../platform/alerts-log.md)的 Azure Monitor 日志中。 你当前无法使用从用于 VM 的 Azure Monitor 收集的性能数据的 [指标警报](../platform/alerts-log.md) ，因为不会将数据收集到 Azure Monitor 指标中。 若要收集指标警报的数据，请安装适用于 Windows Vm 的 [诊断扩展](../platform/diagnostics-extension-overview.md) 或适用于 Linux Vm 的 [Telegraf 代理](../platform/collect-custom-metrics-linux-telegraf.md) ，以将性能数据收集到指标中。

Azure Monitor 中有两种类型的日志警报：

- "[结果数" 警报](../platform/alerts-unified-log.md#count-of-the-results-table-rows)在查询至少返回指定数量的记录时创建单个警报。 它们非常适合于非数值数据，如 [Log Analytics 代理](../platform/log-analytics-agent.md) 收集的 Windows 和 Syslog 事件，或用于分析多台计算机上的性能趋势。
- [指标度量警报](../platform/alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) 为查询中的每个记录创建单独的警报，该警报的值超出了预警规则中定义的阈值。 这些警报规则非常适合于用于 VM 的 Azure Monitor 收集的性能数据，因为它们可以为每台计算机创建单独的警报。


## <a name="alert-rule-walkthrough"></a>警报规则演练
本部分逐步讲解如何使用用于 VM 的 Azure Monitor 中的性能数据创建指标度量警报规则。 可以将此基本过程用于各种日志查询，以对不同的性能计数器发出警报。

首先，按照 [使用 Azure Monitor 创建、查看和管理日志警报](../platform/alerts-log.md)中的过程创建新的警报规则。 对于 **资源**，请选择 Azure Monitor vm 在订阅中使用的 Log Analytics 工作区。 由于日志警报规则的目标资源始终是 Log Analytics 工作区，因此日志查询必须包含针对特定虚拟机或虚拟机规模集的任何筛选器。 

对于警报规则的 **条件** ，请使用 [下面部分](#sample-alert-queries) 中的查询之一作为 **搜索查询**。 查询必须返回一个名为 *AggregatedValue*的数值属性。 它应按计算机汇总数据，以便您可以为每个超过阈值的虚拟机创建一个单独的警报。

在 **警报逻辑**中，选择 " **指标度量** "，然后提供 **阈值**。 在 " **基于触发警报**" 中，指定在创建警报之前必须超过阈值的次数。 例如，您可能不关心处理器是否超出阈值一次，然后返回正常，但如果它在多个连续度量的情况下继续超出阈值，则您也可以谨慎。

" **基于计算** 依据" 部分定义运行查询的频率以及查询的时间范围。 在下面所示的示例中，查询每15分钟运行一次，并评估过去15分钟内收集的性能值。


![指标度量警报规则](media/vminsights-alerts/metric-measurement-alert.png)

## <a name="sample-alert-queries"></a>示例警报查询
以下查询可用于指标度量警报规则，该规则使用用于 VM 的 Azure Monitor 收集的性能数据。 每个按计算机汇总数据，以便为值超过阈值的每台计算机创建一个警报。

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

### <a name="logical-disk-used---all-disks-on-each-computer"></a>使用的逻辑磁盘-每台计算机上的所有磁盘

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---individual-disks"></a>使用的逻辑磁盘-单个磁盘

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

### <a name="network-interfaces-bytes-received---all-interfaces"></a>网络接口接收的字节数-所有接口

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="network-interfaces-bytes-received---individual-interfaces"></a>网络接口接收的字节数-单个接口

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="network-interfaces-bytes-sent---all-interfaces"></a>网络接口发送的字节数-所有接口

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="network-interfaces-bytes-sent---individual-interfaces"></a>网络接口发送的字节数-单个接口

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="virtual-machine-scale-set"></a>虚拟机规模集
修改订阅 ID、资源组和虚拟机规模集名称。

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/my-vm-scaleset" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="specific-virtual-machine"></a>特定虚拟机
修改订阅 ID、资源组和 VM 名称。

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId =~ "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m)
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-subscription"></a>订阅中所有计算资源的 CPU 利用率
修改订阅 ID。

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" and (_ResourceId contains "/providers/Microsoft.Compute/virtualMachines/" or _ResourceId contains "/providers/Microsoft.Compute/virtualMachineScaleSets/")
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-resource-group"></a>资源组中所有计算资源的 CPU 利用率
用您的订阅 ID 和资源组进行修改。

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/"
or _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId

```

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Monitor 中的警报](../platform/alerts-overview.md)。
- 了解有关 [使用用于 VM 的 Azure Monitor 中的数据的日志查询的](vminsights-log-search.md)详细信息。
