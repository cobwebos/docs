---
title: "Azure Monitor 自动缩放常见指标 | Microsoft Docs"
description: "了解自动缩放云服务、虚拟机和 Web 应用时常用的指标。"
author: kamathashwin
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 189b2a13-01c8-4aca-afd5-90711903ca59
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/6/2016
ms.author: ashwink
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: bc9d9aa1cbe704de5f7fb960f1467aa522acd0b5
ms.lasthandoff: 03/31/2017


---
# <a name="azure-monitor-autoscaling-common-metrics"></a>Azure 监视器自动缩放常用指标
利用 Azure 监视器自动缩放，可以根据遥测数据（指标）增加或减少正在运行的实例数。 本文档介绍了你可能想要使用的常用指标。 在云服务和服务器场的 Azure 门户中，你可以选择要作为缩放依据的资源指标。 不过，你也可以选择其他资源的任何指标来作为缩放依据。

下列信息对于缩放虚拟机规模集同样适用。

> [!NOTE]
> 此信息仅适用于基于 Resource Manager 的 VM 和 VM 规模集。 
> 

## <a name="compute-metrics-for-resource-manager-based-vms"></a>基于 Resource Manager 的 VM 的计算指标
默认情况下，基于 Resource Manager 的虚拟机和虚拟机规模集发出基本（主机级）指标。 此外，为 Azure VM 和 VMSS 配置诊断数据集合时，Azure 诊断扩展也会发出来宾 OS性能计数器（通常称为“来宾 OS 指标”）。  可在自动缩放规则中使用所有这些指标。 

可以使用 `Get MetricDefinitions` API/PoSH/CLI 来查看 VMSS 资源的可用指标。 

如果使用 VM 规模集，而且发现特定指标未列出，可能是其在诊断扩展中*已禁用*。

如果特定指标未采样或以所需的频率传输，你可以更新诊断配置。

如果发生上述任一情况，请查看[使用 PowerShell 在运行 Windows 的虚拟机中启用 Azure 诊断](../virtual-machines/windows/ps-extensions-diagnostics.md)，将 Azure VM 诊断扩展配置和更新为启用该指标。 这篇文章还包含一个诊断配置文件示例。

### <a name="host-metrics-for-resource-manager-based-windows-and-linux-vms"></a>基于 Resource Manager 的 Windows 和 Linux VM 的主机指标
默认情况下，将向 Windows 和 Linux 实例中的 Azure VM 和 VMSS 发出以下主机级指标。 这些指标可描述 Azure VM，但这些指标是从 Azure VM 主机而不是通过来宾 VM 上安装的代理收集的。 可在自动缩放规则中使用这些指标。 

- [基于 Resource Manager 的 Windows 和 Linux VM 的主机指标](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)
- [基于 Resource Manager 的 Windows 和 Linux VM 规模集的主机指标](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)

### <a name="guest-os-metrics-resource-manager-based-windows-vms"></a>基于 Resource Manager 的 Windows VM 的来宾 OS 指标
在 Azure 中创建 VM 时，使用诊断扩展会启用诊断。 诊断扩展会发出一组从 VM 内部获取的指标。 这意味着可以自动缩放不是默认发出的指标。

可以在 PowerShell 中使用以下命令生成指标列表。

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

可以针对下列指标创建警报：

| 指标名称 | 计价单位 |
| --- | --- |
| \Processor(_Total)\% Processor Time |百分比 |
| \Processor(_Total)\% Privileged Time |百分比 |
| \Processor(_Total)\% User Time |百分比 |
| \Processor Information(_Total)\Processor Frequency |计数 |
| \System\Processes |计数 |
| \Process(_Total)\Thread Count |计数 |
| \Process(_Total)\Handle Count |计数 |
| \Memory\% Committed Bytes In Use |百分比 |
| \Memory\Available Bytes |字节 |
| \Memory\Committed Bytes |字节 |
| \Memory\Commit Limit |字节 |
| \Memory\Pool Paged Bytes |字节 |
| \Memory\Pool Nonpaged Bytes |字节 |
| \PhysicalDisk(_Total)\% Disk Time |百分比 |
| \PhysicalDisk(_Total)\% Disk Read Time |百分比 |
| \PhysicalDisk(_Total)\% Disk Write Time |百分比 |
| \PhysicalDisk(_Total)\Disk Transfers/sec |每秒计数 |
| \PhysicalDisk(_Total)\Disk Reads/sec |每秒计数 |
| \PhysicalDisk(_Total)\Disk Writes/sec |每秒计数 |
| \PhysicalDisk(_Total)\Disk Bytes/sec |每秒字节数 |
| \PhysicalDisk(_Total)\Disk Read Bytes/sec |每秒字节数 |
| \PhysicalDisk(_Total)\Disk Write Bytes/sec |每秒字节数 |
| \PhysicalDisk(_Total)\Avg. 磁盘队列长度 |计数 |
| \PhysicalDisk(_Total)\Avg. 磁盘读取队列长度 |计数 |
| \PhysicalDisk(_Total)\Avg. 磁盘写入队列长度 |计数 |
| \LogicalDisk(_Total)\% Free Space |百分比 |
| \LogicalDisk(_Total)\Free Megabytes |计数 |

### <a name="guest-os-metrics-linux-vms"></a>Linux VM 的来宾 OS 指标
在 Azure 中创建 VM 时，使用诊断扩展会默认启用诊断。

可以在 PowerShell 中使用以下命令生成指标列表。

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 可以针对下列指标创建警报：

| 指标名称 | 计价单位 |
| --- | --- |
| \Memory\AvailableMemory |字节 |
| \Memory\PercentAvailableMemory |百分比 |
| \Memory\UsedMemory |字节 |
| \Memory\PercentUsedMemory |百分比 |
| \Memory\PercentUsedByCache |百分比 |
| \Memory\PagesPerSec |每秒计数 |
| \Memory\PagesReadPerSec |每秒计数 |
| \Memory\PagesWrittenPerSec |每秒计数 |
| \Memory\AvailableSwap |字节 |
| \Memory\PercentAvailableSwap |百分比 |
| \Memory\UsedSwap |字节 |
| \Memory\PercentUsedSwap |百分比 |
| \Processor\PercentIdleTime |百分比 |
| \Processor\PercentUserTime |百分比 |
| \Processor\PercentNiceTime |百分比 |
| \Processor\PercentPrivilegedTime |百分比 |
| \Processor\PercentInterruptTime |百分比 |
| \Processor\PercentDPCTime |百分比 |
| \Processor\PercentProcessorTime |百分比 |
| \Processor\PercentIOWaitTime |百分比 |
| \PhysicalDisk\BytesPerSecond |每秒字节数 |
| \PhysicalDisk\ReadBytesPerSecond |每秒字节数 |
| \PhysicalDisk\WriteBytesPerSecond |每秒字节数 |
| \PhysicalDisk\TransfersPerSecond |每秒计数 |
| \PhysicalDisk\ReadsPerSecond |每秒计数 |
| \PhysicalDisk\WritesPerSecond |每秒计数 |
| \PhysicalDisk\AverageReadTime |秒 |
| \PhysicalDisk\AverageWriteTime |秒 |
| \PhysicalDisk\AverageTransferTime |秒 |
| \PhysicalDisk\AverageDiskQueueLength |计数 |
| \NetworkInterface\BytesTransmitted |字节 |
| \NetworkInterface\BytesReceived |字节 |
| \NetworkInterface\PacketsTransmitted |计数 |
| \NetworkInterface\PacketsReceived |计数 |
| \NetworkInterface\BytesTotal |字节 |
| \NetworkInterface\TotalRxErrors |计数 |
| \NetworkInterface\TotalTxErrors |计数 |
| \NetworkInterface\TotalCollisions |计数 |

## <a name="commonly-used-web-server-farm-metrics"></a>常用的 Web（服务器场）指标
你也可以根据常用的 Web 服务器指标（如 Http 队列长度）执行自动缩放。 其指标名为 **HttpQueueLength**。  以下部分列出了可用的服务器场（Web 应用）指标。

### <a name="web-apps-metrics"></a>Web 应用指标
你可以在 PowerShell 中使用以下命令生成 Web 应用指标列表。

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

你可以针对这些指标发出警报或以其为缩放依据。

| 指标名称 | 计价单位 |
| --- | --- |
| CpuPercentage |百分比 |
| MemoryPercentage |百分比 |
| DiskQueueLength |计数 |
| HttpQueueLength |计数 |
| BytesReceived |字节 |
| BytesSent |字节 |

## <a name="commonly-used-storage-metrics"></a>常用的存储指标
你可以将存储队列长度作为缩放依据，它是存储队列中的消息数目。 存储队列长度是一个特殊指标，阈值是每个实例的消息数。 例如，如果有两个实例并且阈值设置为 100，则当队列中的消息总数为 200 时将进行缩放。 这两个实例的消息数可能各为 100，或分别为 120 和 80，或者为其他相加大于等于 200 的数字组合。 

在 Azure 门户的“设置”边栏选项卡中配置此配置。 若使用 VM 规模集，可以将 Resource Manager 模板中的“自动缩放”设置更新为将 metricName 用作 ApproximateMessageCount，并传递存储队列的 ID 作为 metricResourceUri。

例如，对于经典存储帐户，自动缩放设置 metricTrigger 将包括：

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
 ```

对于（非经典）存储帐户，metricTrigger 将包括：

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Storage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

## <a name="commonly-used-service-bus-metrics"></a>常用的服务总线指标
你可以按服务总线队列的长度进行缩放，该长度是服务总线队列中的消息数量。 服务总线队列长度是一个特殊指标，阈值是每个实例的消息数。 例如，如果有两个实例并且阈值设置为 100，则当队列中的消息总数为 200 时将进行缩放。 这两个实例的消息数可能各为 100，或分别为 120 和 80，或者为其他相加大于等于 200 的数字组合。 

若使用 VM 规模集，可以将 Resource Manager 模板中的“自动缩放”设置更新为将 metricName 用作 ApproximateMessageCount，并传递存储队列的 ID 作为 metricResourceUri。

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

> [!NOTE]
> 若使用服务总线，则不存在资源组这一概念，但 Azure Resource Manager 会为每个区域创建一个默认资源组。 此资源组通常采用“Default-ServiceBus-[region]”的格式。 例如，“Default-ServiceBus-EastUS”、“Default-ServiceBus-WestUS”、“Default-ServiceBus-AustraliaEast”等等。
> 
> 


