---
title: 排查 Linux 或 Windows 上的 Azure 虚拟机性能问题
description: 本文介绍虚拟机（VM）一般性能故障排除，通过监视和观察瓶颈，并为可能出现的问题提供可能的补救措施。
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
ms.openlocfilehash: 50c0a670eb492aef01c3499bc2c8605917f4c7b8
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965470"
---
# <a name="troubleshoot-azure-virtual-machine-performance-on-linux-or-windows"></a>排查 Linux 或 Windows 上的 Azure 虚拟机性能问题

本文介绍虚拟机（VM）一般性能故障排除，通过监视和观察瓶颈，并为可能出现的问题提供可能的补救措施。 除了监视之外，还可以使用 Perfinsights，它可以提供包含最佳做法建议的报表以及有关 IO/CPU/内存的关键瓶颈。 Perfinsights 适用于 Azure 中的[Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights)和[Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux) VM。

本文介绍如何使用监视来诊断性能瓶颈。

## <a name="enabling-monitoring"></a>启用监视

### <a name="azure-iaas-virtual-machine-monitoring"></a>Azure IAAS 虚拟机监视

若要监视来宾 VM，请使用 Azure VM 监视，这会提醒你某些高级资源条件。 若要检查是否已启用 VM 诊断，请参阅[Azure 资源日志概述](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-overview#collecting-resource-logs)。 如果看到以下内容，则很可能尚未启用诊断：

![未启用监视](media/troubleshoot-performance-virtual-machine-linux-windows/1-virtual-machines-monitoring-not-enabled.png)
 
### <a name="enable-vm-diagnostics-through-microsoft-azure-portal"></a>通过 microsoft Azure 门户启用 VM 诊断

若要启用 VM 诊断：

1. 中转到 VM
2. 单击 "**诊断设置**"
3. 选择存储帐户，并单击 "**启用来宾级监视**"。

   ![单击 "设置"，然后单击 "诊断"](media/troubleshoot-performance-virtual-machine-linux-windows/2-virtual-machines-diagnostics.png)

你可以从 "**诊断设置**" 下的 "**代理**" 选项卡中检查用于诊断设置的存储帐户。

![检查存储帐户](media/troubleshoot-performance-virtual-machine-linux-windows/3-check-storage-account.png)

### <a name="enable-storage-account-diagnostics-through-azure-portal"></a>通过 Azure 门户启用存储帐户诊断

当我们打算分析 Azure 中虚拟机的 IO 性能时，存储是一个非常重要的层。 对于与存储相关的指标，我们需要启用诊断作为附加步骤。 如果只想分析与存储相关的计数器，也可以启用此功能。

1. 通过选择 VM 来确定 VM 使用的存储帐户（或帐户）。 单击 "**设置**"，然后单击 "**磁盘**"：

   ![单击 "设置"，然后单击 "磁盘"](media/troubleshoot-performance-virtual-machine-linux-windows/4-storage-disks-disks-selection.png)

2. 在门户中，前往 VM 的存储帐户（或帐户）并完成以下步骤：

   1. 单击上面步骤中找到的存储帐户的 "概述"。
   2. 将显示默认度量值。 

    ![默认指标](media/troubleshoot-performance-virtual-machine-linux-windows/5-default-metrics.png)

3. 单击任意指标，将显示另一个具有更多选项的边栏选项卡，用于配置和添加度量值。

   ![添加度量值](media/troubleshoot-performance-virtual-machine-linux-windows/6-add-metrics.png)

配置以下选项：

1.  选择“指标”。
2.  选择**资源**（存储帐户）。
3.  选择**命名空间**
4.  选择 "**指标**"。
5.  选择**聚合**的类型
6.  你可以在仪表板上固定此视图。

## <a name="observing-bottlenecks"></a>观察瓶颈

完成所需指标的初始设置过程后，为 VM 和相关存储帐户启用诊断后，我们可以转到 "分析" 阶段。

### <a name="accessing-the-monitoring"></a>访问监视

选择要调查的 Azure VM，并选择 "**监视**"。

![选择监视](media/troubleshoot-performance-virtual-machine-linux-windows/7-select-monitoring.png)
 
### <a name="timelines-of-observation"></a>观察时间线

若要确定是否有任何资源瓶颈，请查看数据。 如果发现计算机运行正常，但已报告性能最近下降，请在问题发生前后，查看数据的时间范围，该数据包含性能指标数据。

### <a name="check-for-cpu-bottleneck"></a>检查 CPU 瓶颈

![检查 CPU 瓶颈](media/troubleshoot-performance-virtual-machine-linux-windows/8-cpu-bottleneck-time-range.png)

1. 编辑关系图。
2. 设置时间范围。
3. 接下来，需要添加计数器： CPU 百分比来宾操作系统
4. 保存。

### <a name="cpu-observe-trends"></a>CPU 观察趋势

查看性能问题时，请注意趋势，了解它们是否会对你产生影响。 在接下来的部分中，我们将使用门户中的监视图来显示趋势。 它们还可用于在同一时间段内交叉引用不同资源行为。 若要自定义关系图，请单击 " [Azure Monitor 数据平台](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform)"。

蜂值–蜂值可与计划的任务/已知事件相关。 如果可以识别任务，请确定任务是否以所需的性能级别运行。 如果性能是可接受的，则可能不需要增加资源。

峰值和常量–通常表示新的工作负荷。 如果它不是已识别的工作负荷，请在 VM 中启用监视以找出导致此行为的进程。 识别进程后，确定增加的消耗是否是由低效的代码或正常使用导致的。 如果正常使用，确定进程是否在所需的性能级别运行。

常量–确定 VM 是否始终在此级别运行，或者在启用诊断后是否已在该级别上运行。 如果是这样，请确定导致此问题的一个或多个进程，并考虑添加更多的资源。

持续增加–消耗的恒定增加通常是低效的代码或处理更多用户工作负荷的进程。

### <a name="high-cpu-utilization-remediation"></a>CPU 使用率较高的修正

如果你的应用程序或进程未以正确的性能级别运行，并且你看到的是 95% + CPU 使用率常量，则可以执行以下任一任务：

* 若要立即止裂槽，请将 VM 的大小增加到具有更多内核的大小
* 了解问题–查找应用程序/进程，并相应地进行故障排除。

如果已增加 VM，并且 CPU 仍在运行95%，则确定此设置是否为可接受级别提供更好的性能或更高的应用程序吞吐量。 如果不是，则解决单个 application\process。

可以使用[Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights)或[Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux)的 Perfinsights 来分析哪个进程正在驱动 CPU 消耗。 

## <a name="check-for-memory-bottleneck"></a>检查内存瓶颈

查看指标：

1. 添加一个节。
2. 添加磁贴。
3. 打开库。
4. 选择内存使用情况并拖动。 停靠磁贴时，右键单击并选择 " **6x4**"。

### <a name="memory-observe-trends"></a>内存观察趋势

内存使用量显示 VM 正在使用的内存量。 了解趋势以及它是否映射到出现问题的时间。 你的可用内存应始终超过 100 MB。

峰值和常量/恒定稳定消耗-高内存使用率可能不是性能不佳，因为某些应用程序（例如关系数据库引擎）分配了大量内存，并且此利用率可能并不重要。 但是，如果有多个内存密集型应用程序，则可能会出现内存争用导致修整和分页/交换到磁盘的性能下降。 这种性能不佳通常是应用程序性能影响的明显原因。

持续增长的消耗–可能的应用程序的 "预热"，这种消耗是在启动数据库引擎的情况下进行的。 但是，它也可以是应用程序中的内存泄漏的符号。 确定应用程序并了解行为是否正常。

页面或交换文件使用情况–检查是否正在使用 Windows 分页文件（位于 D：\) 或 Linux 交换文件（位于 `/dev/sdb`上）是否使用过度。 如果这些文件以外的卷上没有内容，请检查这些磁盘上的读/写是否较高。 此问题表示内存不足的情况。

### <a name="high-memory-utilization-remediation"></a>高内存利用率修正

若要解决内存使用率高的问题，请执行以下任何任务：

* 对于立即止裂槽或页面或交换文件使用情况，请将 VM 大小增加到具有更多内存的内存，然后监视。
* 了解问题–查找用于识别高消耗内存应用程序的应用程序/进程和故障排除。
* 如果知道该应用程序，请查看是否可以限制内存分配。

如果升级到更大的 VM，你会发现，在100% 之前，你仍有持续稳定的增长，可以确定应用程序/进程并进行故障排除。

可以使用[Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights)或[Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux)的 Perfinsights 来分析哪个进程正在驱动内存消耗。 

## <a name="check-for-disk-bottleneck"></a>检查磁盘瓶颈

若要检查 VM 的存储子系统，请在 Azure VM 级别通过使用 VM 诊断中的计数器以及存储帐户诊断来检查诊断。

对于 VM 特定的故障排除，可以使用适用于[Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights)或[Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux)的 Perfinsights，这有助于分析正在驱动 IO 的进程。 

请注意，我们没有用于区域冗余和高级存储帐户的计数器。 对于与这些计数器相关的问题，请提出支持案例。

### <a name="viewing-storage-account-diagnostics-in-monitoring"></a>查看监视中的存储帐户诊断

若要处理以下项，请在门户中访问 VM 的存储帐户：

![查看监视中的存储帐户诊断](media/troubleshoot-performance-virtual-machine-linux-windows/9-virtual-machine-storage-account.png)

1. 编辑监视图。
2. 设置时间范围。
3. 添加以下步骤中所述的计数器。
4. 保存更改。

### <a name="disk-observe-trends-standard-storage-only"></a>磁盘观察趋势（仅限标准存储）

若要确定存储的问题，请查看存储帐户诊断和 VM 诊断中的性能度量值。

对于下面的每个检查，请在问题的时间范围内发生问题时查找关键趋势。

#### <a name="check-azure-storage-availability--add-the-storage-account-metric-availability"></a>检查 Azure 存储可用性-添加存储帐户指标：可用性

如果你发现可用性下降，则平台可能出现问题，请检查[Azure 状态](https://azure.microsoft.com/status/)。 如果没有出现问题，则提出新的支持请求。

#### <a name="check-for-azure-storage-timeout---add-the-storage-account-metrics"></a>检查 Azure 存储超时-添加存储帐户度量值：

* ClientTimeOutError
* ServerTimeOutError
* AverageE2ELatency
* AverageServerLatency
* TotalRequests

\* TimeOutError 度量值中的值指示 IO 操作花费了太长时间并超时。完成后续步骤可帮助识别潜在的原因。

AverageServerLatency 会在 TimeOutErrors 同时增加，这可能是平台问题。 在这种情况下引发新的支持请求。

AverageE2ELatency 表示客户端延迟。 验证应用程序执行 IOPS 的方式。 查找增加或持续的高 TotalRequests 指标。 此指标表示 IOPS。 如果正在开始达到存储帐户或单个 VHD 的限制，则延迟可能与限制相关。

#### <a name="check-for-azure-storage-throttling---add-the-storage-account-metrics-throttlingerror"></a>检查 Azure 存储限制-添加存储帐户指标： ThrottlingError

"限制" 的值指示你要在存储帐户级别受到限制，这意味着你达到了帐户的 IOPS 限制。 可以通过检查指标**TotalRequests**来确定是否达到 IOPs 阈值。

请注意，每个 VHD 的限制为 500 IOPS 或 60 Mb，但受每个存储帐户的 20000 IOPS 的累积限制。

如果使用此度量值，则无法判断哪个 blob 导致了中止，哪些 blob 受其影响。 不过，可以达到存储帐户的 IOPS 或入口/出口限制。

若要确定是否达到 IOPS 限制，请进入存储帐户诊断，查看 TotalRequests，查看是否接近 20000 TotalRequests。 确定模式的更改、是否首次看到限制或是否在特定时间发生此限制。

对于标准存储下的新磁盘产品，IOPS 和吞吐量限制可能不同，但标准存储帐户的累积限制为 20000 IOPS （高级存储具有不同的帐户或磁盘级别限制）。 阅读有关不同标准存储磁盘产品和每个磁盘限制的详细信息：

* [Windows 上的 VM 磁盘的可伸缩性和性能目标](https://docs.microsoft.com/azure/virtual-machines/windows/disk-scalability-targets)。

#### <a name="references"></a>参考

* [虚拟机磁盘的可伸缩性目标](https://azure.microsoft.com/documentation/articles/storage-scalability-targets/#scalability-targets-for-virtual-machine-disks)

存储帐户的带宽通过存储帐户指标来度量： TotalIngress 和 TotalEgress。 带宽的阈值不同，具体取决于冗余和区域的类型。

* [Blob、队列、表和文件的可伸缩性目标](https://azure.microsoft.com/documentation/articles/storage-scalability-targets/#scalability-targets-for-blobs-queues-tables-and-files)

检查 TotalIngress 和 TotalEgress，以了解存储帐户冗余类型和区域的入口和出口限制。

检查附加到 VM 的 Vhd 的吞吐量限制。 添加 VM 指标磁盘读取和写入。

标准存储下的新磁盘产品提供了不同的 IOPS 和吞吐量限制（每个 VHD 不公开 IOPS）。 查看数据，查看是否在 VM 级别使用磁盘读取和写入来达到了 VM 级别的最小的 VHD 吞吐量，并优化了 VM 存储配置以扩展过去的单个 VHD 限制。 阅读有关不同标准存储磁盘产品和每个磁盘限制的详细信息：

* [Windows 上的 VM 磁盘的可伸缩性和性能目标](https://docs.microsoft.com/azure/virtual-machines/windows/disk-scalability-targets)。

### <a name="high-disk-utilizationlatency-remediation"></a>高磁盘利用率/延迟修正

减少客户端延迟并优化 VM IO 以缩放过去的 VHD 限制

* [在 Azure 中优化 Windows IO](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-performance-best-practices/)

* [在 Azure 中优化 Linux 的 IO](https://blogs.msdn.microsoft.com/igorpag/2014/10/23/azure-storage-secrets-and-linux-io-optimizations/)

#### <a name="reduce-throttling"></a>减少限制

如果达到存储帐户的上限，请对存储帐户之间的 Vhd 重新平衡。 请参阅[Azure 存储可伸缩性和性能目标](https://azure.microsoft.com/documentation/articles/storage-scalability-targets/)。

### <a name="increase-throughput-and-reduce-latency"></a>提高吞吐量并减少延迟

如果有延迟敏感的应用程序并需要高吞吐量，请使用 DS 和 GS 系列 VM 将 Vhd 迁移到 Azure 高级存储。

这些文章介绍了具体的方案：

* [迁移到 Azure 高级存储](https://azure.microsoft.com/documentation/articles/storage-migration-to-premium-storage/)

* [将 Azure 高级存储用于 SQL Server](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-use-premium-storage/)

## <a name="next-steps"></a>后续步骤

如果在本文的任何位置需要更多帮助，请联系 MSDN Azure 上的 Azure 专家[并 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)。

或者，提供 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。
