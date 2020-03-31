---
title: 在 Linux 或 Windows 上对 Azure 虚拟机性能进行故障排除
description: 本文介绍了虚拟机 （VM） 通过监视和观察瓶颈进行通用性能故障排除，并为可能出现的问题提供了可能的补救措施。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: v-miegge
ms.openlocfilehash: 176b0634fe2c7ee2f47162e439c4ea16bde77a8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75772612"
---
# <a name="troubleshoot-azure-virtual-machine-performance-on-linux-or-windows"></a>在 Linux 或 Windows 上对 Azure 虚拟机性能进行故障排除

本文介绍了虚拟机 （VM） 通过监视和观察瓶颈进行通用性能故障排除，并为可能出现的问题提供了可能的补救措施。 除了监视之外，您还可以使用 Perfinsights，它可以提供包含最佳实践建议和 IO/CPU/内存关键瓶颈的报告。 Perfinsights 可用于 Azure 中的[Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights)和[Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux) VM。

本文将演练使用监视来诊断性能瓶颈。

## <a name="enabling-monitoring"></a>启用监视

### <a name="azure-iaas-virtual-machine-monitoring"></a>Azure IAAS 虚拟机监视

要监视来宾 VM，请使用 Azure VM 监视，这将提醒您某些高级资源条件。 要检查是否已启用 VM 诊断，请参阅[Azure 资源日志概述](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs)。 如果您看到以下内容，则很可能未启用诊断：

![未启用监视](media/troubleshoot-performance-virtual-machine-linux-windows/1-virtual-machines-monitoring-not-enabled.png)
 
### <a name="enable-vm-diagnostics-through-microsoft-azure-portal"></a>通过 Microsoft Azure 门户启用 VM 诊断

要启用 VM 诊断：

1. 转到 VM
2. 单击**诊断设置**
3. 选择存储帐户，然后单击**启用来宾级监视**。

   ![单击"设置"，然后单击诊断](media/troubleshoot-performance-virtual-machine-linux-windows/2-virtual-machines-diagnostics.png)

您可以在 **"诊断设置**"下从 **"代理"** 选项卡检查用于诊断设置的存储帐户。

![检查存储帐户](media/troubleshoot-performance-virtual-machine-linux-windows/3-check-storage-account.png)

### <a name="enable-storage-account-diagnostics-through-azure-portal"></a>通过 Azure 门户启用存储帐户诊断

当我们打算分析 Azure 中的虚拟机的 IO 性能时，存储是一个非常重要的层。 对于存储相关指标，我们需要启用诊断作为附加步骤。 如果我们只想分析与存储相关的计数器，也可以启用此功能。

1. 通过选择 VM 确定 VM 正在使用哪些存储帐户（或帐户）。 单击 **"设置"，** 然后单击 **"磁盘**" ：

   ![单击"设置"，然后单击磁盘](media/troubleshoot-performance-virtual-machine-linux-windows/4-storage-disks-disks-selection.png)

2. 在门户中，转到 VM 的存储帐户（或帐户）并完成以下步骤：

   1. 单击使用上面的步骤找到的存储帐户的概述。
   2. 将显示默认指标。 

    ![默认指标](media/troubleshoot-performance-virtual-machine-linux-windows/5-default-metrics.png)

3. 单击任何指标，这将显示另一个包含更多选项来配置和添加指标的边栏选项卡。

   ![添加度量值](media/troubleshoot-performance-virtual-machine-linux-windows/6-add-metrics.png)

要配置这些选项：

1.  选择“指标”****。
2.  选择**资源**（存储帐户）。
3.  选择**命名空间**
4.  选择**指标**。
5.  选择**聚合**类型
6.  您可以将此视图固定在仪表板上。

## <a name="observing-bottlenecks"></a>观察瓶颈

完成所需指标的初始设置过程，并在启用 VM 和相关存储帐户的诊断后，我们可以转移到分析阶段。

### <a name="accessing-the-monitoring"></a>访问监视

选择要调查的 Azure VM 并选择 **"监视**"。

![选择“监视”](media/troubleshoot-performance-virtual-machine-linux-windows/7-select-monitoring.png)
 
### <a name="timelines-of-observation"></a>观测时间轴

要确定是否有任何资源瓶颈，请查看数据。 如果您发现计算机运行良好，但已报告性能最近下降，请查看在报告更改之前、问题期间和之后包含性能指标数据的时间范围。

### <a name="check-for-cpu-bottleneck"></a>检查 CPU 瓶颈

![检查 CPU 瓶颈](media/troubleshoot-performance-virtual-machine-linux-windows/8-cpu-bottleneck-time-range.png)

1. 编辑图形。
2. 设置时间范围。
3. 然后，您需要在计数器中添加：CPU 百分比来宾操作系统
4. 保存。

### <a name="cpu-observe-trends"></a>CPU 观察趋势

在查看性能问题时，了解趋势并了解这些趋势是否影响您。 在下一节中，我们将使用门户中的"监视"图表来显示趋势。 它们对于在同一时间段内交叉引用差异资源行为也很有用。 要自定义图形，请单击[Azure 监视器数据平台](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform)。

尖峰 – 尖峰可能与计划的任务/已知事件有关。 如果可以标识任务，请确定任务是否在所需的性能级别运行。 如果性能可以接受，则可能不需要增加资源。

向上和持续 = 通常表示新的工作负载。 如果它不是公认的工作负载，则在 VM 中启用监视，以找出导致该行为的进程（或进程）。 识别流程后，确定增加的消耗量是由低效代码还是正常消耗引起的。 如果正常消耗，则确定流程是否在所需的性能级别运行。

常量 - 确定 VM 是否始终在此级别运行，或者它是否仅在启用诊断后才在该级别运行。 如果是这样，请确定导致问题的进程（或进程），并考虑添加更多资源。

稳步增长 – 持续增加消耗通常是低效的代码，或者是承担更多用户工作负载的过程。

### <a name="high-cpu-utilization-remediation"></a>CPU 利用率高修正

如果应用程序或进程未以正确的性能级别运行，并且看到 95% = CPU 利用率常数，则可以执行以下任一任务：

* 为立即缓解 - 将 VM 的大小增加到具有更多内核的大小
* 了解问题 – 找到应用程序/流程并相应地进行故障排除。

如果增加了 VM，并且 CPU 仍在运行 95%，则确定此设置是提供更好的性能还是更高的应用程序吞吐量到可接受的水平。 如果没有，请对单个应用程序\进程进行故障排除。

您可以使用[Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights)或[Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux)的 Perfinsights 来分析哪个进程在推动 CPU 消耗。 

## <a name="check-for-memory-bottleneck"></a>检查内存瓶颈

要查看指标：

1. 添加节。
2. 添加磁贴。
3. 打开库。
4. 选择"内存使用情况"并拖动。 当磁贴停靠时，右键单击并选择**6x4**。

### <a name="memory-observe-trends"></a>记忆观察趋势

内存使用情况显示 VM 正在消耗多少内存。 了解趋势及其是否映射到您看到问题的时间。 您应始终具有超过 100 MB 的可用内存。

峰值和恒定/恒定稳定消耗 - 高内存利用率可能不是性能不佳的原因，因为某些应用程序（如关系数据库引擎）分配了大量内存，并且此利用率可能并不显著。 但是，如果有多个需要内存的应用程序，您可能会看到内存争用导致修剪和分页/交换到磁盘的性能较差。 这种性能差通常是应用程序性能影响的明显原因。

不断增加的消耗量 – 一个可能的应用程序"预热"，这种消耗在数据库引擎启动中很常见。 然而，这也可能是应用程序内存泄漏的迹象。 确定应用程序并了解该行为是否预期。

页面或交换文件使用情况 - 检查您是否正在大量使用 Windows 分页文件（位于\)D： 或 Linux`/dev/sdb`交换文件（位于 上）。 如果这些卷上除了这些文件之外，没有任何内容，请检查这些磁盘上的高读取/写入。 此问题表示内存不足。

### <a name="high-memory-utilization-remediation"></a>高内存利用率修正

要解决高内存利用率，应执行以下任一任务：

* 要立即进行救济或页面或交换文件使用情况 - 将 VM 大小增加到具有更多内存的 VM 大小，然后进行监视。
* 了解问题 – 查找应用程序/进程和疑难解答，以确定高消耗内存应用程序。
* 如果您知道应用程序，请参阅是否可以限制内存分配。

如果在升级到更大的 VM 后，您发现您仍有持续稳步上升，直到 100%， 确定应用程序/进程和故障排除。

您可以使用[Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights)或[Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux)的 Perfinsights 来分析哪个进程推动了内存消耗。 

## <a name="check-for-disk-bottleneck"></a>检查磁盘瓶颈

要检查 VM 的存储子系统，请使用 VM 诊断中的计数器和存储帐户诊断中计数器检查 Azure VM 级别的诊断。

对于特定于 VM 的故障排除，可以使用[适用于 Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights)或[Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux)的 Perfinsights，这有助于分析哪个进程推动了 IO。 

请注意，我们没有区域冗余和高级存储帐户的计数器。 对于与这些计数器相关的问题，提出支持案例。

### <a name="viewing-storage-account-diagnostics-in-monitoring"></a>在监视中查看存储帐户诊断

要处理以下项目，请参阅门户中 VM 的存储帐户：

![在监视中查看存储帐户诊断](media/troubleshoot-performance-virtual-machine-linux-windows/9-virtual-machine-storage-account.png)

1. 编辑监视图。
2. 设置时间范围。
3. 添加以下步骤中描述的计数器。
4. 保存更改。

### <a name="disk-observe-trends-standard-storage-only"></a>磁盘观察趋势（仅限标准存储）

要识别存储问题，请查看存储帐户诊断和 VM 诊断中的性能指标。

对于下面的每一次检查，在问题的时间范围内出现问题时，查找关键趋势。

#### <a name="check-azure-storage-availability--add-the-storage-account-metric-availability"></a>检查 Azure 存储可用性 • 添加存储帐户指标：可用性

如果看到可用性下降，则平台可能有问题，请检查[Azure 状态](https://azure.microsoft.com/status/)。 如果那里未显示问题，请提出新的支持请求。

#### <a name="check-for-azure-storage-timeout---add-the-storage-account-metrics"></a>检查 Azure 存储超时 - 添加存储帐户指标：

* 客户端超时错误
* ServerTimeOutError
* AverageE2ELatency
* AverageServerLatency
* TotalRequests

@TimeOutError 指标中的值表示 IO 操作耗时过长且超时。完成后续步骤将有助于确定潜在原因。

平均服务器延迟在超时错误时同时增加可能是一个平台问题。 在此情况下提出新的支持请求。

平均E2E延迟表示客户端延迟。 验证应用程序如何执行 IOPS。 查找增加或持续高的"总请求"指标。 此指标表示 IOPS。 如果您开始达到存储帐户或单个 VHD 的限制，则延迟可能与限制有关。

#### <a name="check-for-azure-storage-throttling---add-the-storage-account-metrics-throttlingerror"></a>检查 Azure 存储限制 - 添加存储帐户指标：限制错误

限制值表示您在存储帐户级别受到限制，这意味着您达到帐户的 IOPS 限制。 您可以通过检查指标**Total 请求**来确定是否达到 IP 阈值。

请注意，每个 VHD 的限制为 500 IOPS 或 60 MBits，但受每个存储帐户的累积限制 20000 IOPS 的约束。

使用此指标，您无法判断哪个 Blob 导致限制，哪些受其影响。 但是，您要么达到 IOPS 或存储帐户的入口/出口限制。

要确定是否达到 IOPS 限制，请进入存储帐户诊断并检查"总请求"，查看是否接近 20000 个总计请求。 确定模式中的更改、是否首次看到限制，或者此限制是否在特定时间发生。

对于标准存储下的新磁盘产品，IOPS 和吞吐量限制可能不同，但标准存储帐户的累积限制为 20000 IOPS（高级存储在帐户或磁盘级别具有不同的限制）。 阅读有关不同标准存储磁盘产品/磁盘和每个磁盘限制的更多内容：

* [Windows 上 VM 磁盘的可伸缩性和性能目标](https://docs.microsoft.com/azure/virtual-machines/windows/disk-scalability-targets)。

#### <a name="references"></a>参考

* [高级页 blob 存储帐户的可伸缩性和性能目标](../../storage/blobs/scalability-targets-premium-page-blobs.md)

存储帐户的带宽由存储帐户指标：总入口和总出口来衡量。 根据冗余类型和区域的不同，带宽阈值不同。

* [标准存储帐户的可伸缩性和性能目标](../../storage/common/scalability-targets-standard-account.md)

根据存储帐户冗余类型和地区的入口和出口限制检查"总入口"和"总出口"。

检查附加到 VM 的 VHD 的吞吐量限制。 添加 VM 指标磁盘读取和写入。

标准存储下的新磁盘产品具有不同的 IOPS 和吞吐量限制（IOPS 不会根据 VHD 公开）。 查看数据，查看是否达到 VHD 级别 VHD 组合吞吐量 MB 的限制，使用磁盘读取和写入，然后优化 VM 存储配置以扩展超过单个 VHD 限制。 阅读有关不同标准存储磁盘产品/磁盘和每个磁盘限制的更多内容：

* [Windows 上 VM 磁盘的可伸缩性和性能目标](https://docs.microsoft.com/azure/virtual-machines/windows/disk-scalability-targets)。

### <a name="high-disk-utilizationlatency-remediation"></a>高磁盘利用率/延迟修正

减少客户端延迟并优化 VM IO 以扩展超过 VHD 限制

* [优化 Azure 中 Windows 的 IO](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-performance-best-practices/)

* [在 Azure 中优化 Linux 的 IO](https://blogs.msdn.microsoft.com/igorpag/2014/10/23/azure-storage-secrets-and-linux-io-optimizations/)

#### <a name="reduce-throttling"></a>减少限制

如果达到存储帐户的上限，则在存储帐户之间重新平衡 VHD。 请参阅[Azure 存储可伸缩性和性能目标](https://azure.microsoft.com/documentation/articles/storage-scalability-targets/)。

### <a name="increase-throughput-and-reduce-latency"></a>提高吞吐量并减少延迟

如果您有延迟敏感应用程序并且需要高吞吐量，请使用 DS 和 GS 系列 VM 将 VHD 迁移到 Azure 高级存储。

这些文章讨论具体方案：

* [迁移到 Azure 高级存储](https://azure.microsoft.com/documentation/articles/storage-migration-to-premium-storage/)

* [将 Azure 高级存储与 SQL 服务器一起使用](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-use-premium-storage/)

## <a name="next-steps"></a>后续步骤

如果本文中的任何一点都需要更多帮助，请与[MSDN Azure 和堆栈溢出论坛上的](https://azure.microsoft.com/support/forums/)Azure 专家联系。

或者，提交 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **"获取支持**"。
