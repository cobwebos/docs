---
title: "Azure Log Analytics 中的容量和性能解决方案 | Microsoft Docs"
description: "使用 Log Analytics 中的容量和性能解决方案来帮助理解 Hyper-V 服务器的容量。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 51617a6f-ffdd-4ed2-8b74-1257149ce3d4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: a26b1f6c13b11d0fb6b47599fee43f955cbf7b7a
ms.lasthandoff: 04/18/2017


---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-preview"></a>使用容量和性能解决方案（预览版）计划 Hyper-V 虚拟机容量

![容量和性能解决方案](./media/log-analytics-capacity/capacity-solution.png) 可以使用 Log Analytics 中的容量和性能解决方案来帮助理解 Hyper-V 服务器的容量。 可以通过该解决方案查看在这些 Hyper-V 主机上运行的主机和 VM 的总体利用率（CPU、内存和磁盘），从而深入了解 Hyper-V 环境。 将收集在这些 Hyper-V 主机上运行的所有主机和 VM 的 CPU、内存和磁盘的指标。

解决方案：

-    显示 CPU 和内存利用率最高和最低的主机
-    显示 CPU 和内存利用率最高和最低的 VM
-    显示 IOPS 和吞吐量利用率最高和最低的 VM
-    显示哪些 VM 运行在哪些主机上
-    显示群集共享卷中吞吐量、IOPS 和延迟较高的前几个磁盘
- 允许根据组进行自定义和筛选

> [!NOTE]
> 名为“容量管理”的旧版容量和性能解决方案需要 System Center Operations Manager 和 System Center Virtual Machine Manager。 此更新后的解决方案没有这些依赖项。


## <a name="connected-sources"></a>连接的源

下表介绍了该解决方案支持的连接的源。

| 连接的源 | 支持 | 说明 |
|---|---|---|
| [Windows 代理](log-analytics-windows-agents.md) | 是 | 解决方案从 Windows 代理收集容量和性能数据信息。 |
| [Linux 代理](log-analytics-linux-agents.md) | 否    | 解决方案不从直接 Linux 代理收集容量和性能数据信息。|
| [SCOM 管理组](log-analytics-om-agents.md) | 是 |解决方案从连接的 SCOM 管理组中的代理收集容量和性能数据。 不需要从 SCOM 代理直接连接到 OMS。 数据从管理组转发到 OMS 存储库。|
| [Azure 存储帐户](log-analytics-azure-storage.md) | 否 | Azure 存储不包括容量和性能数据。|

## <a name="prerequisites"></a>先决条件

- 必须在 Windows Server 2012 或更高版本的 Hyper-V 主机而非虚拟机上安装 Windows 或 Operations Manager 代理。


## <a name="configuration"></a>配置

执行以下步骤，将容量和性能解决方案添加到工作区。

- 使用[从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)中描述的过程，将容量和性能解决方案添加到 OMS 工作区。

## <a name="management-packs"></a>管理包

如果 SCOM 管理组已连接到 OMS 工作区，则添加该解决方案时会在 SCOM 中安装以下管理包。 无需对这些管理包进行任何配置或维护。

- Microsoft.IntelligencePacks.CapacityPerformance

1201 事件类似于：


```
New Management Pack with id:"Microsoft.IntelligencePacks.CapacityPerformance", version:"1.10.3190.0" received.
```

更新容量和性能解决方案后，版本号会更改。

有关如何更新解决方案管理包的详细信息，请参阅[将 Operations Manager 连接到 Log Analytics](log-analytics-om-agents.md)。

## <a name="using-the-solution"></a>使用解决方案

将容量和性能解决方案添加到工作区时，会将“容量和性能”添加到“概览”仪表板。 此磁贴显示当前处于活动状态的 Hyper-V 主机的计数，以及曾在所选时间段内受监视的活动虚拟机的计数。

![“容量和性能”磁贴](./media/log-analytics-capacity/capacity-tile.png)


### <a name="review-utilization"></a>查看利用率

单击“容量和性能”磁贴，打开“容量和性能”仪表板。 仪表板包含下表中的列。 每个列按照指定范围和时间范围列出了匹配该列条件的最多十项。 可单击该列底部的“查看全部”或单击列标题运行返回所有记录的日志搜索。

- **主机**
    - **主机 CPU 利用率**：根据所选时间段显示主计算机的 CPU 利用率图形趋势和主机的列表。 将鼠标悬停在折线图上即可查看特定时间点的详细信息。 单击图表即可在日志搜索中查看更多详细信息。 单击任意主机名称即可打开日志搜索并查看托管 VM 的 CPU 计数器详细信息。
    - **主机内存利用率**：根据所选时间段显示主计算机的内存利用率图形趋势和主机的列表。 将鼠标悬停在折线图上即可查看特定时间点的详细信息。 单击图表即可在日志搜索中查看更多详细信息。 单击任意主机名称即可打开日志搜索并查看托管 VM 的内存计数器详细信息。
- **虚拟机**
    - **VM CPU 利用率**：根据所选时间段显示虚拟机的 CPU 利用率图形趋势和虚拟机的列表。 将鼠标悬停在折线图上即可查看前 3 个 VM 在特定时间点的详细信息。 单击图表即可在日志搜索中查看更多详细信息。 单击任意 VM 名称即可打开日志搜索并查看 VM 的聚合 CPU 计数器详细信息。
    - **VM 内存利用率**：根据所选时间段显示虚拟机的内存利用率图形趋势和虚拟机的列表。 将鼠标悬停在折线图上即可查看前 3 个 VM 在特定时间点的详细信息。 单击图表即可在日志搜索中查看更多详细信息。 单击任意 VM 名称即可打开日志搜索并查看 VM 的聚合内存计数器详细信息。
    - **VM 总磁盘 IOPS**：根据所选时间段显示虚拟机的总磁盘 IOPS 图形趋势和包含其 IOPS 的虚拟机的列表。 将鼠标悬停在折线图上即可查看前 3 个 VM 在特定时间点的详细信息。 单击图表即可在日志搜索中查看更多详细信息。 单击任意 VM 名称即可打开日志搜索并查看 VM 的聚合磁盘 IOPS 计数器详细信息。
    - **VM 总磁盘吞吐量**：根据所选时间段显示虚拟机的总磁盘吞吐量图形趋势和包含其总磁盘吞吐量的虚拟机的列表。 将鼠标悬停在折线图上即可查看前 3 个 VM 在特定时间点的详细信息。 单击图表即可在日志搜索中查看更多详细信息。 单击任意 VM 名称即可打开日志搜索并查看 VM 的聚合总磁盘吞吐量计数器详细信息。
- **群集共享卷**
    - **总吞吐量**：显示群集共享卷上读取数和写入数的总和。
    - **总 IOPS**：显示群集共享卷上每秒输入/输出操作数的总和。
    - **总延迟**：显示群集共享卷上的总延迟。
- **主机密度**：顶部磁贴显示适用于解决方案的主机和虚拟机的总数。 单击顶部磁贴即可在日志搜索中查看其他详细信息。 此外还列出了所有主机以及所托管的虚拟机数。 单击某个主机即可深入查看日志搜索中的 VM 结果。


![仪表板“主机”边栏选项卡](./media/log-analytics-capacity/dashboard-hosts.png)

![仪表板虚拟机边栏选项卡](./media/log-analytics-capacity/dashboard-vms.png)


### <a name="evaluate-performance"></a>评估性能

不同组织的生产计算环境相差很大。 另外，容量和性能工作负荷可能取决于 VM 的运行方式以及你所认为的正常的标准。 特定的用于衡量性能的过程可能不适用于你的环境。 因此，更通用的说明性指导帮助性更大。 Microsoft 发布了各种说明性的关于如何衡量性能的指导文章。

总之，该解决方案从包括性能计数器在内的各种源收集容量和性能数据。 请使用在解决方案的各个图面中提供的该容量和性能数据，并将你的结果与 [Measuring Performance on Hyper-V](https://msdn.microsoft.com/library/cc768535.aspx)（衡量 Hyper-V 上的性能）一文中的结果进行比较。 虽然该文已发布了一段时间，但指标、注意事项和准则仍然有效。 该文包含指向其他有用资源的链接。


## <a name="sample-log-searches"></a>示例日志搜索

下表提供的示例日志搜索针对该解决方案所收集和计算的容量和性能数据。

| 查询 | 说明 |
|---|---|
| 所有主机内存配置 | <code>Type=Perf ObjectName="Capacity and Performance" CounterName="Host Assigned Memory MB" &#124; measure avg(CounterValue) as MB by InstanceName</code> |
| 所有 VM 内存配置 | <code>Type=Perf ObjectName="Capacity and Performance" CounterName="VM Assigned Memory MB" &#124; measure avg(CounterValue) as MB by InstanceName</code> |
| 所有 VM 的总磁盘 IOPS 明细 | <code>Type=Perf ObjectName="Capacity and Performance" (CounterName="VHD Reads/s" OR CounterName="VHD Writes/s") &#124; top 2500 &#124; measure avg(CounterValue) by CounterName, InstanceName interval 1HOUR</code> |
| 所有 VM 的总磁盘吞吐量明细 | <code>Type=Perf ObjectName="Capacity and Performance" (CounterName="VHD Read MB/s" OR CounterName="VHD Write MB/s") &#124; top 2500 &#124; measure avg(CounterValue) by CounterName, InstanceName interval 1HOUR</code> |
| 所有 CSV 的总 IOPS 明细 | <code>Type=Perf ObjectName="Capacity and Performance" (CounterName="CSV Reads/s" OR CounterName="CSV Writes/s") &#124; top 2500 &#124; measure avg(CounterValue) by CounterName, InstanceName interval 1HOUR</code> |
| 所有 CSV 的总吞吐量明细 | <code>Type=Perf ObjectName="Capacity and Performance" (CounterName="CSV Read MB/s" OR CounterName="CSV Write MB/s") &#124; top 2500 &#124; measure avg(CounterValue) by CounterName, InstanceName interval 1HOUR</code> |
| 所有 CSV 的总延迟明细 | <code> Type=Perf ObjectName="Capacity and Performance" (CounterName="CSV Read Latency" OR CounterName="CSV Write Latency") &#124; top 2500 &#124; measure avg(CounterValue) by CounterName, InstanceName interval 1HOUR</code> |




## <a name="next-steps"></a>后续步骤
* 使用 [Log Analytics 中的日志搜索](log-analytics-log-searches.md)查看详细的容量和性能数据。

