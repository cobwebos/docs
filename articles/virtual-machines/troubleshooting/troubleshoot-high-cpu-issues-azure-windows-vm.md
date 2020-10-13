---
title: 排查 Azure Windows 虚拟机的高 CPU 问题
description: .
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: mnanda
manager: dcscontentpm
editor: v-jesits
tags: azure-resource-manager
ms.assetid: 9971cc26-916f-4e49-83cd-71eca74804f0
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 9/24/2020
ms.author: mnanda
ms.openlocfilehash: ffac5ac4d1a8143590e1d72aaafc8a02d6ab04ca
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977249"
---
# <a name="troubleshoot-high-cpu-issues-for-azure-windows-virtual-machines"></a>排查 Azure Windows 虚拟机的高 CPU 问题

## <a name="summary"></a>总结

在不同的操作系统或应用程序中出现性能问题，每个问题都需要独特的故障排除方法。 其中的大多数问题都围绕 CPU、内存、网络和输入/输出 (i/o) 作为出现问题的关键位置。 其中每个区域都将生成不同的症状 (有时会同时) 并且需要不同的诊断和解决方案。

本文介绍 Azure 虚拟机上发生的高 CPU 使用率问题， (运行 Windows 操作系统的 Vm) 。

### <a name="high-cpu-issues-on-azure-windows-vms"></a>Azure Windows Vm 上的高 CPU 问题

除了 i/o 和网络延迟问题外，CPU 和内存故障排除还需要与本地服务器相同的工具和步骤。 Microsoft 通常支持的工具之一是为 Windows 和 Linux) 提供 PerfInsights (。 PerfInsights 可以在用户友好的报表中提供 Azure VM 最佳做法诊断。 PerfInsights 也是一个包装工具，可帮助收集 Perfmon、Xperf 和 Netmon 数据，具体取决于在该工具中选择的标志。

大多数用于本地服务器的现有性能故障排除工具（例如 Perfmon 或 Procmon 配合）都适用于 Azure Windows Vm。 不过，PerfInsights 是针对 Azure Vm 明确设计的，可提供更多见解，包括 Azure 最佳做法、SQL 最佳实践、高分辨率 i/o 延迟图形、CPU 和内存选项卡等。

无论是作为 User-Mode 还是内核模式运行，活动进程的任何线程都需要 CPU 周期来运行它所生成的代码。 许多问题与工作负荷直接相关。 服务器上存在的工作负荷的类型会占用 CPU，包括 CPU。

#### <a name="common-factors"></a>常见因素

以下因素在高 CPU 情况下很常见：

- 最新的代码更改或部署，主要适用于 Internet Information Services (IIS) 、Microsoft SharePoint、Microsoft SQL Server 或第三方应用程序之类的应用程序。

- 最新的更新，可能与操作系统级别的更新有关，或与应用程序级累积更新和修补程序相关。

- 查询更改或过时的索引。 SQL Server 和 Oracle 数据层应用程序也将查询计划优化作为另一个因素。 数据更改或缺少适当的索引可能会导致多个查询的计算密集型。

- 特定于 Azure VM。 某些进程（如 RDAgent）和扩展特定进程（如监视代理、MMA 代理或安全客户端）可能会导致高 CPU 消耗。 必须从配置或已知问题角度查看这些过程。

## <a name="troubleshoot-the-issue"></a>解决问题

本文重点介绍如何隔离有问题的过程。 进一步分析将特定于驱动高 CPU 消耗的进程。

例如，如果进程 SQL Server ( # A0) ，则后续步骤将分析在特定时间段内使用最多 CPU 周期的查询。

### <a name="scope-the-issue"></a>确定问题范围

解决问题时，需要考虑以下几个问题：

- 存在问题的模式吗？ 例如，在每日、每周或每月的特定时间是否会出现高 CPU 问题？ 如果是，你是否可以将此问题与作业、报表或用户登录相关联？

- 在最近的代码更改后，高 CPU 问题是否开始？ 是否在 Windows 或应用程序中应用了更新？

- 在工作负荷更改后是否开始出现高 CPU 问题，例如增加了用户数、流入的数据量越多或报告数量更大？

- 对于 Azure，在以下任何情况下，是否启动了高 CPU 问题？

  - 最近重新部署或重新启动后
  - 如果更改了 SKU 或 VM 类型
  - 添加新扩展时
  - 进行负载均衡器更改后

### <a name="azure-caveats"></a>Azure 注意事项

了解你的工作负载。 选择 VM 时，如果你查看总体每月托管成本，你可能会低估虚拟 CPU (vCPU) 计数。 如果你的工作负荷需要计算密集型，则选择一个具有一个或两个个 vcpu 的小型 VM SKU 可能会导致工作负荷问题。 针对工作负荷测试不同的配置，以确定所需的最佳计算能力。

某些 VM 系列（如 B (突发模式) 系列）建议用于质量保证 (QA) 和测试。 在生产环境中使用这些系列可以在 CPU 信用额度用完后限制计算能力。

对于已知的应用程序（例如 SQL Server、Oracle、RDS (远程桌面服务) 、Windows 虚拟桌面、IIS 或 SharePoint），有一些 Azure 最佳实践文章，其中包括对这些工作负荷的最小配置建议。

### <a name="ongoing-high-cpu-issues"></a>持续的高 CPU 问题

如果现在发生此问题，则这是捕获进程跟踪来确定问题原因的最佳机会。 你可以使用已用于本地 Windows 服务器的现有工具来查找该进程。 Azure 支持 Azure Vm 建议使用以下工具。

### <a name="perfinsights"></a>PerfInsights

PerfInsights 是 Azure 支持 VM 性能问题的建议工具。 它旨在涵盖 CPU、内存和高分辨率 i/o 图形的最佳实践和专用分析选项卡。 可以通过 Azure 门户或从 VM 内部运行它。 可以与 Azure 支持团队共享数据。

#### <a name="run-perfinsights"></a>运行 PerfInsights

PerfInsights 适用于 [Windows](./how-to-use-perfinsights.md) 和 [Linux](./how-to-use-perfinsights-linux.md) 操作系统。 对于 Windows，可选择以下选项。

#### <a name="run-and-analyze-reports-through-azure-portal"></a>通过 Azure 门户运行和分析报表

当 [通过 Azure 门户安装](./performance-diagnostics.md)时，实际上会在 VM 上安装扩展。 用户还可以直接转到 [VM 边栏选项卡](./performance-diagnostics-vm-extension.md)中的 "扩展"，然后选择 "性能诊断" 选项，将 PerfInsights 安装为扩展。

#### <a name="azure-portal-option-1"></a>Azure 门户选项1

浏览 "VM" 边栏选项卡，并选择 " **性能诊断** " 选项。 系统将要求你安装选项， (在你为其选择的 VM 上使用扩展) 。

  ![安装性能诊断](./media/troubleshoot-high-cpu-issues-azure-windows-vm/1-install-performance-diagnostics.png)

#### <a name="azure-portal-option-2"></a>Azure 门户选项2

浏览以 **诊断并解决** vm 边栏选项卡中的问题，并查找 **vm 性能问题**。

  ![排查 VM 性能问题](./media/troubleshoot-high-cpu-issues-azure-windows-vm/2-troubleshoot-vm-performance-issues.png)

如果选择 " **故障排除**"，则会加载 PerfInsights 安装屏幕。

如果选择 " **安装**"，则安装会提供不同的集合选项。

  ![性能分析](./media/troubleshoot-high-cpu-issues-azure-windows-vm/3-performance-analysis.png)

屏幕截图中的编号选项与以下注释相关：

1. 对于 **高 CPU**选项，请选择 " **性能分析** " 或 " **高级**"。

2. 当你在此处添加症状时，它们将添加到报告中，这有助于你与 Azure 支持人员共享信息。

3. 选择数据收集的持续时间。 对于 "高 CPU" 选项，请选择至少15分钟或更长时间。 在 Azure 门户模式下，最多可以收集15分钟的数据。 对于较长的集合，必须在 VM 内将程序作为可执行文件运行。

4. 如果 Azure 支持部门要求你收集此数据，则可以在此处添加票证编号。 此字段是可选的。

5. 选择此字段以接受最终用户许可协议， (EULA) 。

6. 如果要将此报告提供给 Azure 支持团队提供此案例，请选择此字段。

报表存储在订阅下的某个存储帐户中。 稍后即可查看和下载。

#### <a name="run-perfinsights-from-within-the-vm"></a>从 VM 中运行 PerfInsights

如果要运行 PerfInsights 更长的持续时间，则可以使用此方法。 [PerfInsights 文章](./how-to-use-perfinsights.md#how-do-i-run-perfinsights)提供了以可执行文件形式运行 PerfInsights 所需的不同命令和标志的详细演练。 出于高 CPU 使用率的目的，需要以下两种模式之一：

- 高级方案

  - `PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics`

- VM 慢速 (性能) 方案

  - `PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>`

命令输出将位于保存 PerfInsights 可执行文件的同一文件夹中。

#### <a name="what-to-look-for-in-the-report"></a>要在报表中查找的内容

运行报表后，内容的位置取决于它是通过 Azure 门户还是可执行文件运行。 对于任一选项，都可访问生成的日志文件夹，或在本地 Azure 门户) 进行分析时下载 (。

### <a name="run-through-the-azure-portal"></a>运行 Azure 门户

  ![高影响性能诊断](./media/troubleshoot-high-cpu-issues-azure-windows-vm/4-high-impact-performance-diagnostics.png)

  ![下载报表](./media/troubleshoot-high-cpu-issues-azure-windows-vm/5-download-report.png)

#### <a name="run-from-within-vm"></a>从 VM 内运行

文件夹结构应类似于以下图像：

  ![选择输出](./media/troubleshoot-high-cpu-issues-azure-windows-vm/6-select-output.png)

  ![文件夹结构](./media/troubleshoot-high-cpu-issues-azure-windows-vm/7-folder-structure.png)

1. 任何其他集合，如 Perfmon、Xperf、Netmon、SMB 日志、事件日志等，都可在输出文件夹中找到。

1. 实际报表以及分析和建议。

1. 对于 "性能 (VMslow") "和" 高级 "，报表将收集 PerfInsights 运行期间的 **perfmon** 信息。

1. 事件日志显示有用的系统级或进程崩溃详细信息的快速视图。

#### <a name="where-to-start"></a>从哪里开始

打开 PerfInsights 报表。 " **发现** " 选项卡根据资源消耗记录任何离群值。 如果有高 CPU 使用率的实例，则 " **发现** " 选项卡会将其分类为 "高影响" 或 "中影响"。

  ![影响级别资源](./media/troubleshoot-high-cpu-issues-azure-windows-vm/8-impact-level-resources.png)

与上一示例类似，PerfInsights 运行30分钟。 对于这半次，突出显示的过程是在更高的一侧耗尽 CPU。 如果在整个收集时间内运行相同的进程，则影响级别将更改为 " **高**"。

如果你展开 **发现** 事件，将看到几个关键的详细信息。 该选项卡按 **平均 CPU** 消耗的顺序列出进程，并显示该进程是否与系统、Microsoft 拥有的应用 (SQL、IIS) 或第三方进程相关。

#### <a name="more-details"></a>更多详细信息

**CPU**下有一个专用子标签，可用于详细模式分析、每个核心或每个进程。

" **前 CPU 使用者** " 选项卡有两个独立的区域，可以在此处查看每个处理器的统计信息。 应用程序设计经常 Single-Threaded 或固定到单个处理器。 在此方案中，一个或多个内核在100% 运行，而其他内核在预期级别运行。 由于服务器上的平均 CPU 似乎按预期运行，因此这些方案更复杂，但在具有高使用率的内核上固定的进程将会比预期速度更慢。

  ![高 CPU 使用率](./media/troubleshoot-high-cpu-issues-azure-windows-vm/9-high-cpu-usage.png)

第二部分 (同等重要) ，是 **运行 CPU 使用者的顶级长时间**。 此部分显示进程详细信息和其 CPU 使用模式。 此列表的排序方式是在顶部 CPU 使用率较高的情况。

  ![Tom 长时间运行的 CPU 使用者](./media/troubleshoot-high-cpu-issues-azure-windows-vm/10-top-long-running-cpu-consumers.png)

这两个选项卡足以设置下一个故障排除步骤的路径。 根据驱动高 CPU 状况的过程，您必须解决之前提出的问题。 例如 SQL Server ( # A0) 或 IIS ( # A1) 的进程需要对导致此条件的查询或代码更改进行特定向下钻取。 对于诸如 WMI 或 Lsass.exe 的系统进程，必须遵循不同的路径。

对于 Azure VM 相关的过程（例如 RDAgent、OMS 和监视扩展可执行文件），你可能需要通过从 Azure 支持团队获取帮助来修复新的生成或版本。

### <a name="perfmon"></a>Perfmon

**Perfmon** 是用于排查 Windows Server 上的资源问题的最早工具之一。 它不提供包含建议或结果的清晰报告。 相反，它要求用户浏览收集的数据并在不同的计数器类别下使用特定筛选器。

PerfInsights 收集 Perfmon 作为 VMSlow 和高级方案的额外日志。 但是，可以独立收集 Perfmon，并具有以下附加优势：

- 可以远程收集。

- 可以通过 **任务**来计划。

- 可以通过使用滚动更新功能，将其收集到更长的持续时间或连续模式。

请考虑 PerfInsights 中显示的一个示例，了解 Perfmon 如何显示此数据。 所需的计数器类别如下所示：

- 处理器信息 > 处理器时间百分比 > _Total

- > 所有实例处理 >% ProcessorTime

#### <a name="where-to-start"></a>从哪里开始

Perfmon 输出文件名的 `.blg` 扩展名为。 可以单独收集这些文件，也可以使用 PerfInsights 来收集。 对于此讨论，你将使用 `.blg` PerfInsights 数据中包含的 Perfmon，并按前面的示例收集的数据。

Perfmon 中没有可供用户使用的默认报表。 更改图形类型的视图有不同，但过程筛选 (或确定发生进程的进程所需的工作) 是手动的。

> [!NOTE]
> [PAL 工具](https://github.com/clinthuffman/PAL)可以使用 `.blg` 文件并生成详细报告。

若要开始，请选择 " **添加计数器** " 类别。

1. 在 "**可用的计数器**" 下，选择 "**处理器信息**" 类别中的 " **% ProcessorTime** " 计数器。

1. 选择 **_Total**，这将提供所有组合内核的统计信息。

1. 选择 **添加** 。 该窗口在 "**已添加的计数器**" 下显示 **% ProcessorTime** 。

  ![添加处理器时间](./media/troubleshoot-high-cpu-issues-azure-windows-vm/11-add-processor-time.png)

加载计数器后，会在收集时间范围内看到线趋势图表。 您可以选择或清除计数器。 到目前为止，您只添加了一个计数器。

  ![性能监视器设置](./media/troubleshoot-high-cpu-issues-azure-windows-vm/12-performance-monitor-1.png)

每个计数器都有 **平均值**、 **最小**值和 **最大** 值。 专注于 **平均** 值和 **最大** 值，因为平均值可能因数据收集的持续时间而异。 如果在整个集合为40分钟时发现了高 CPU 活动10分钟，则平均值将小得多。

上一个趋势图显示， **处理器总数** 约为80%，大约15分钟。

#### <a name="identify-the-process"></a>确定进程

我们确定服务器在指定的时间段内 CPU 消耗较高，但我们尚未确定该驱动程序。 与使用 PerfInsights 不同的是，在这种情况下，您必须手动搜索操作程序过程。

对于此任务，必须清除或删除之前添加的 **% ProcessorTime** 计数器，然后添加新类别：

- > 所有实例处理 >% ProcessorTime

此类别将为当时运行的所有进程加载计数器。

  ![添加计数器](./media/troubleshoot-high-cpu-issues-azure-windows-vm/13-add-counters.png)

在典型的生产计算机上，数百个或进程可以运行。 因此，可能需要一段时间才能清除似乎具有低趋势图或平面趋势图的每个计数器。

若要加快此过程，请使用 **直方图** 视图，并将视图类型从 **行** 更改为 **直方图**，这将为您显示一个条形图。 你会发现，选择在收集时遇到高 CPU 使用情况的过程会更容易。

由于 **总**有一个条形，因此焦点在显示消耗率较高的条上。 您可以删除其他条形以清除视图。 现在，返回到 **行** 视图。

  ![性能监视器指示器](./media/troubleshoot-high-cpu-issues-azure-windows-vm/14-performance-monitor-2.png)

现在可以更轻松地捕获问题过程。 默认情况下，" **最大** 值" 和 " **最小** 值" 是服务器上的内核数的倍数或进程的线程数的倍数。

  ![性能监视器结果](./media/troubleshoot-high-cpu-issues-azure-windows-vm/15-performance-monitor-3.png)

可用工具的列表不会在 PerfInsights for Perfmon 上结束。 可以访问其他工具，例如 **ProcessMonitor** (procmon 配合) 或 **Xperf**。 可根据需要使用许多第三方工具。

### <a name="azure-monitoring-tools"></a>Azure 监视工具

Azure Vm 具有可靠的指标，包括 CPU、网络 i/o 和 i/o 字节等基本信息。 对于高级指标，如 Azure Monitor，你必须仅选择几个选项来配置和使用你指定的存储帐户。

#### <a name="basic-default-counters"></a>基本 (默认) 计数器

  ![图表度量值](./media/troubleshoot-high-cpu-issues-azure-windows-vm/16-chart-metrics.png)

#### <a name="enabling-azure-monitor"></a>启用 Azure Monitor

启用 Azure Monitor 度量值后，软件将在 VM 上安装扩展，并开始收集细化指标，其中包括 Perfmon 计数器。

  ![诊断存储帐户](./media/troubleshoot-high-cpu-issues-azure-windows-vm/17-diagnostics-storage-account.png)

**基本**计数器类别设置为**默认值**。 但是，您还可以设置 **自定义** 集合。

  ![性能计数器](./media/troubleshoot-high-cpu-issues-azure-windows-vm/18-performance-counters.png)

启用这些设置后，可以在 "**指标**" 部分中查看这些**来宾**计数器。 如果度量值达到特定阈值，还可以设置 **警报** (包括电子邮件) 。

  ![度量值命名空间](./media/troubleshoot-high-cpu-issues-azure-windows-vm/19-metrics-namespace.png)

有关如何使用 Azure monitor 来管理 Azure Vm 的详细信息，请参阅 [使用 Azure Monitor 监视 azure 虚拟机](../../azure-monitor/insights/monitor-vm-azure.md)。

### <a name="reactive-troubleshooting"></a>反应故障排除

如果已发生此问题，则必须首先发现导致高 CPU 问题的原因。 反应技巧可能比较棘手。 由于已发生此问题，因此数据收集模式不会很有用。

如果此问题是一次性的，则很难确定哪些应用程序导致了该问题。 如果将 Azure VM 配置为使用 OMS 或其他诊断跟踪，你仍可以深入了解导致问题的原因。

如果使用的是重复模式，请在问题可能发生时收集数据。

PerfInsights 尚无 **计划的运行** 功能。 但是，可以通过命令行运行和计划 Perfmon。

### <a name="logman-command"></a>Logman 命令

**Logman Create Counter**命令用于通过命令行运行 Perfmon 收集、通过**任务管理器**对其进行调度或远程运行。

**示例** (包括远程收集模式) 

`Logman create counter LOGNAME -u DOMAIN\USERNAME * -f bincirc -v mmddhhmm -max 300 -c "\\SERVERNAME\LogicalDisk(*)\*" "\\SERVERNAME\Memory\*" "\\SERVERNAME\Network Interface(*)\*" "\\SERVERNAME\Paging File(*)\*" "\\SERVERNAME\PhysicalDisk(*)\*" "\\SERVERNAME\Process(*)\*" "\\SERVERNAME\Redirector\*" "\\SERVERNAME\Server\*" "\\SERVERNAME\System\*" "\\SERVERNAME\Terminal Services\*" "\\SERVERNAME\Processor(*)\*" "\\SERVERNAME\Cache\*" -si 00:01:00`

还可以从同一 VNET 中的对等 Azure VM 计算机启动 Logman.exe。

若要了解有关这些参数的详细信息，请参阅 [logman create counter](/windows-server/administration/windows-commands/logman-create-counter)。

在问题发生时收集 Perfmon 数据后，分析数据的其余步骤与前面讨论的步骤相同。

## <a name="conclusion"></a>结束语

对于任何性能问题，了解工作负荷是解决问题的关键所在。 不同 VM Sku 和不同磁盘存储选项上的选项必须通过保持对生产工作负荷的关注来评估。 在不同 Vm 上测试解决方案的过程可帮助你做出最佳决策。

由于用户的操作和数据量会变化，因此始终在 VM 的计算、网络和 i/o 功能中保留一个缓冲区。 现在，工作负荷中的任何突然变化都没有什么影响。

如果你预见到工作负荷的增加，请转到具有更多计算能力的更高的 SKU。 如果工作负荷需要计算密集型，请明智地选择 VM Sku。