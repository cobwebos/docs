---
title: "如何在 Microsoft Azure 中使用 PerfInsights | Microsoft Docs"
description: "了解如何使用 PerfInsights 来排查 Windows VM 性能问题。"
services: virtual-machines-windows'
documentationcenter: 
author: genlin
manager: cshepard
editor: na
tags: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: f15875610e2035c6f4c10c36e19c02f3e045b3ea
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2017
---
# <a name="how-to-use-perfinsights"></a>如何使用 PerfInsights 

[PerfInsights](http://aka.ms/perfinsightsdownload) 是自动执行的脚本，可收集有用的诊断信息。 它还可以运行 I/O 压力负载以及提供分析报告，以帮助排查 Azure 中的 Windows 虚拟机性能问题。 安装 [Azure 性能诊断 VM 扩展](performance-diagnostics-vm-extension.md)后，它可以作为独立脚本在虚拟机上运行，或直接从门户运行。

如果你遇到虚拟机性能问题，我们建议在联系支持人员之前先运行此脚本。

## <a name="supported-troubleshooting-scenarios"></a>支持的故障排除方案

PerfInsights 可以收集和分析多种信息。 以下部分介绍了常见方案。

### <a name="collect-basic-configuration"></a>收集基本配置 

此方案收集磁盘配置和其他重要信息，包括：

-   事件日志

-   所有传入和传出连接的网络状态

-   网络和防火墙配置设置

-   任务列表，用于当前在系统中运行的所有应用程序

-   由 msinfo32 针对虚拟机创建的信息文件

-   Microsoft SQL Server 数据库配置设置（前提是已确定 VM 为运行 SQL Server 的服务器）

-   存储可靠性计数器

-   重要的 Windows 修补程序

-   已安装的筛选器驱动程序

这属于被动收集信息，不会影响系统。 

>[!Note]
>此方案自动包括在下述每个方案中。

### <a name="benchmarking"></a>基准测试

此方案针对所有附加到 VM 的驱动器运行 [Diskspd](https://github.com/Microsoft/diskspd) 基准测试（IOPS 和 MBPS）。 

> [!Note]
> 此方案可能影响系统，不应在实时生产系统中运行。 必要时可在专用维护时段运行此方案，以免出现问题。 因跟踪或基准测试而导致工作负荷增加时，可能会对 VM 性能造成负面影响。
>

### <a name="slow-vm-analysis"></a>慢速 VM 分析 

此方案使用在 Generalcounters.txt 文件中指定的计数器运行[性能计数器](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx)跟踪。 如果确定该 VM 是运行 SQL Server 的服务器，它会运行性能计数器跟踪。 为此，该 VM 会使用 Sqlcounters.txt 中的计数器，并包含性能诊断数据。

### <a name="slow-vm-analysis-and-benchmarking"></a>慢速 VM 分析和基准测试

此方案运行[性能计数器](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx)跟踪，随后运行 [Diskspd](https://github.com/Microsoft/diskspd) 基准测试。 

> [!Note]
> 此方案可能影响系统，不应在实时生产系统中运行。 必要时可在专用维护时段运行此方案，以免出现问题。 因跟踪或基准测试而导致工作负荷增加时，可能会对 VM 性能造成负面影响。
>

### <a name="azure-files-analysis"></a>Azure 文件分析 

此方案运行特殊的性能计数器捕获和网络跟踪。 捕获包括所有的“服务器消息块 (SMB) 客户端共享”计数器。 以下是一些关键的 SMB 客户端共享性能计数器，属于捕获的一部分：

| **类型**     | **SMB 客户端共享计数器** |
|--------------|-------------------------------|
| IOPS         | 数据请求/秒             |
|              | 读取请求/秒             |
|              | 写入请求/秒            |
| Latency      | 平均秒数/数据请求         |
|              | 平均秒数/读取操作                 |
|              | 平均秒数/写入操作                |
| IO 大小      | 平均字节/数据请求       |
|              | 平均字节/读取               |
|              | 平均字节/写入              |
| Throughput   | 数据字节/秒                |
|              | 读取字节/秒                |
|              | 写入字节/秒               |
| 队列长度 | 平均读取队列长度        |
|              | 平均写入队列长度       |
|              | 平均数据队列长度        |

### <a name="custom-slow-vm-analysis"></a>自定义慢速 VM 分析 

运行自定义的慢速 VM 分析时，可以选择要并行运行的跟踪。 如果需要，可以运行所有这些跟踪（性能计数器、Xperf、网络和 StorPort）。 跟踪完成后，工具将运行 Diskspd 基准测试（如果已选择）。 

> [!Note]
> 此方案可能影响系统，不应在实时生产系统中运行。 必要时可在专用维护时段运行此方案，以免出现问题。 因跟踪或基准测试而导致工作负荷增加时，可能会对 VM 性能造成负面影响。
>

## <a name="what-kind-of-information-is-collected-by-the-script"></a>脚本收集哪类信息？

将会收集有关 Windows VM、磁盘或存储池配置、性能计数器、日志和各种跟踪的信息。 收集的内容取决于所用的性能方案。 下表提供了详细信息：

|收集的数据                              |  |  | 性能方案 |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                              | 收集基本配置 | 基准测试 | 慢速 VM 分析 | 慢速 VM 分析和基准测试 | Azure 文件分析 | 自定义慢速 VM 分析 |
| 事件日志中的信息      | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 系统信息               | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 卷映射                       | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 磁盘映射                         | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 正在运行的任务                    | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 存储可靠性计数器     | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 存储信息              | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| Fsutil 输出                    | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 筛选驱动程序信息               | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| Netstat 输出                   | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 网络配置            | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 防火墙配置           | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| SQL Server 配置         | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 性能诊断跟踪 * | 是                        | 是                                | 是                      |                                | 是                  | 是                  |
| 性能计数器跟踪 **     |                            |                                    |                          |                                |                      | 是                  |
| SMB 计数器跟踪 **             |                            |                                    |                          |                                | 是                  |                      |
| SQL Server 计数器跟踪 **      |                            |                                    |                          |                                |                      | 是                  |
| Xperf 跟踪                      |                            |                                    |                          |                                |                      | 是                  |
| StorPort 跟踪                   |                            |                                    |                          |                                |                      | 是                  |
| 网络跟踪                    |                            |                                    |                          |                                | 是                  | 是                  |
| Diskspd 基准跟踪 ***      |                            | 是                                |                          | 是                            |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>性能诊断跟踪 (*)

在后台运行基于规则的引擎即可收集数据并诊断当前的性能问题。 当前支持以下规则：

- HighCpuUsage 规则：检测 CPU 使用率高的期间，并显示在这些期间 CPU 使用率高的使用者。
- HighDiskUsage 规则：检测物理磁盘上磁盘使用率高的期间，并显示在这些期间磁盘使用率高的使用者。
- HighResolutionDiskMetric 规则：显示每个物理磁盘每 50 毫秒的 IOPS、吞吐量和 I/O 延迟指标。 此规则有助于快速确定磁盘限制期间。
- HighMemoryUsage 规则：检测内存使用率高的期间，并显示在这些期间内存使用率高的使用者。

> [!NOTE] 
> 目前支持包含 .NET Framework 3.5 或更高版本的 Windows 版本。

### <a name="performance-counter-trace-"></a>性能计数器跟踪 (**)

收集以下性能计数器：

- \Process、\Processor、\Memory、\Thread、\PhysicalDisk 和 \LogicalDisk
- \Cache\Dirty Pages、\Cache\Lazy Write Flushes/sec、\Server\Pool Nonpaged、Failures 和 \Server\Pool Paged Failures
- 在 \Network Interface、\IPv4\Datagrams、\IPv6\Datagrams、\TCPv4\Segments、\TCPv6\Segments、\Network Adapter、\WFPv4\Packets、\WFPv6\Packets、\UDPv4\Datagrams、\UDPv6\Datagrams、\TCPv4\Connection、\TCPv6\Connection、\Network QoS Policy\Packets、\Per Processor Network Interface Card Activity 和 \Microsoft Winsock BSP 下选定的计数器

#### <a name="for-sql-server-instances"></a>适用于 SQL Server 实例
- \SQL Server:Buffer Manager、\SQLServer:Resource Pool Stats 和 \SQLServer:SQL Statistics\
- \SQLServer:Locks、\SQLServer:General、Statistics
- \SQLServer:Access Methods

#### <a name="for-azure-files"></a>适用于 Azure 文件
\SMB Client Shares

### <a name="diskspd-benchmark-trace-"></a>Diskspd 基准跟踪 (***)
Diskspd I/O 工作负荷测试（OS 磁盘 [写入] 和池驱动器 [读/写]）

## <a name="run-the-perfinsights-script-on-your-vm"></a>在 VM 上运行 PerfInsights 脚本

### <a name="what-do-i-have-to-know-before-i-run-the-script"></a>在运行此脚本之前，必须了解什么？ 

#### <a name="script-requirements"></a>脚本要求

-  此脚本必须在有性能问题的 VM 上运行。 

-  支持以下操作系统：Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2、Windows Server 2016、Windows 8.1 和 Windows 10。

#### <a name="possible-problems-when-you-run-the-script-on-production-vms"></a>在生产 VM 上运行该脚本时可能会出现的问题：

-  对于任何基准测试方案或配置为使用 Xperf 或 Diskspd 的“自定义慢速 VM 分析”方案，该脚本可能对 VM 的性能造成负面影响。 不应在生产环境中运行这些方案。

-  对于任何基准测试方案或配置为使用 Diskspd 的“自定义慢速 VM 分析”方案，请确保没有其他后台活动干扰 I/O 工作负荷。

-  默认情况下，此脚本使用临时存储驱动器来收集数据。 如果跟踪保持启用状态较长的时间，则收集的数据量会与之相关。 这会降低临时磁盘上空间的可用性，因此可能影响任何依赖于该驱动器的应用程序。

### <a name="how-do-i-run-perfinsights"></a>如何运行 PerfInsights？ 

可以通过安装 [Azure 性能诊断 VM 扩展](performance-diagnostics-vm-extension.md)在虚拟机上运行 PerfInsights。 也可以将其作为独立脚本运行。 

**安装和运行来自 Azure 门户的 PerfInsights**

有关此选项的详细信息，请参阅[安装 Azure 性能诊断 VM 扩展](performance-diagnostics-vm-extension.md#install-the-extension)。  

**在独立模式下运行 PerfInsights 脚本**

若要运行 PerfInsights 脚本，请按照以下步骤操作：


1. 下载 [PerfInsights.zip](http://aka.ms/perfinsightsdownload)。

2. 取消阻止 PerfInsights.zip 文件。 为此，请右键单击 PerfInsights.zip 文件，并选择“属性”。 在“常规”选项卡上，选择“取消阻止”，然后选择“确定”。 这可以确保运行脚本时不会出现其他任何安全提示。  

    ![PerfInsights 的“属性”屏幕截图，其中突出显示了“解除锁定”](media/how-to-use-perfInsights/unlock-file.png)

3.  将压缩的 PerfInsights.zip 文件展开到临时驱动器（默认情况下，通常为 D 驱动器）中。 压缩的文件应包含以下文件和文件夹：

    ![zip 文件夹中的文件屏幕截图](media/how-to-use-perfInsights/file-folder.png)

4.  以管理员身份打开 Windows PowerShell，然后运行 PerfInsights.ps1 脚本。

    ```
    cd <the path of PerfInsights folder >
    Powershell.exe -ExecutionPolicy UnRestricted -NoProfile -File .\\PerfInsights.ps1
    ```

    可能需要输入“y”确认想要更改执行策略。

    在“声明和许可”对话框中，可以选择将诊断信息与 Microsoft 支持部门共享。 此外还必须同意许可协议才能继续。 做出选择，然后选择“运行脚本”。

    ![“声明和许可”对话框屏幕截图](media/how-to-use-perfInsights/disclaimer.png)

5.  运行脚本时，请提交案例号（如果已提供）。 然后选择“确定”。
    
    ![支持 ID 对话框的屏幕截图](media/how-to-use-perfInsights/enter-support-number.png)

6.  选择临时存储驱动器。 该脚本可以自动检测驱动器的驱动器号。 如果此阶段出现问题，系统可能会提示你选择驱动器（默认驱动器为 D）。 生成的日志存储在此处的 log\_collection 文件夹中。 输入或接受驱动器号后，选择“确定”。

    ![临时驱动器对话框的屏幕截图](media/how-to-use-perfInsights/enter-drive.png)

7.  从提供的列表中选择故障排除方案。

       ![故障排除方案列表的屏幕截图](media/how-to-use-perfInsights/select-scenarios.png)

还可以在没有 UI 的情况下运行 PerfInsights。 在没有 UI 的情况下，以下命令可以运行“慢速 VM 分析”故障排除方案。 它会提示你同意免责声明和 EULA（与在步骤 4 中提到的相同）。

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30"

如果希望 PerfInsights 在无提示模式下运行，请使用 -AcceptDisclaimerAndShareDiagnostics 参数。 例如，使用以下命令：

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30 -AcceptDisclaimerAndShareDiagnostics"

### <a name="how-do-i-troubleshoot-issues-while-running-the-script"></a>如何在运行脚本时排查问题？

如果脚本异常终止，可以通过将脚本与 cleanup 开关一起运行来恢复一致状态，如下所示：

    powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -Cleanup"

如果在自动检测临时驱动器时出现问题，系统可能会提示你选择驱动器（默认驱动器为 D）。

![临时驱动器对话框的屏幕截图](media/how-to-use-perfInsights/enter-drive.png)

脚本会卸载实用程序工具并删除临时文件夹。

### <a name="troubleshoot-other-script-issues"></a>排查其他脚本问题 

如果运行脚本时出现问题，请按 Ctrl+C 中断脚本。 如果在多次尝试后仍遇到脚本失败问题，请以调试模式运行脚本，方法是在启动时使用“-Debug”参数选项。

故障发生后，请复制 PowerShell 控制台的完整输出，并将其发送给 Microsoft 支持部门代理，后者会协助你排查问题。

### <a name="how-do-i-run-the-script-in-custom-slow-vm-analysis-mode"></a>如何在“自定义慢速 VM 分析”模式下运行脚本？

选择“自定义慢速 VM 分析”可以启用多个并行跟踪（使用 Shift 键可选择多个跟踪）：

![方案列表的屏幕截图](media/how-to-use-perfInsights/select-scenario.png)

选择“性能计数器跟踪”、“Xperf 跟踪”、“网络跟踪”或“Storport 跟踪”方案时，请遵照后续对话框中的说明操作。 尝试在启动跟踪后重现性能变慢的问题。

通过以下对话框启动跟踪：

![“启动性能计数器跟踪”对话框的屏幕截图](media/how-to-use-perfInsights/start-trace-message.png)

若要停止跟踪，必须在第二个对话框中确认命令。

![“停止性能计数器跟踪”对话框的屏幕截图](media/how-to-use-perfInsights/stop-trace-message.png)
![“停止所有跟踪”对话框的屏幕截图](media/how-to-use-perfInsights/ok-trace-message.png)

完成跟踪或操作后，D:\\log\_collection （或临时驱动器）中会出现一个新文件。 该文件的名称为 **CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip**。 可将此文件发送给支持代理进行分析。

## <a name="review-the-diagnostics-report"></a>查看诊断报告

在 **CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip** 文件中，可以找到一份 HTML 报告，其中详细说明了 PerfInsights 的发现结果。 若要查看该报告，请展开 CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip 文件，然后打开 PerfInsights Report.html 文件。

选择“发现”选项卡。

![PerfInsights 报告的屏幕截图](media/how-to-use-perfInsights/findingtab.png)
![PerfInsights 报告的屏幕截图](media/how-to-use-perfInsights/findings.PNG)

> [!NOTE] 
> 分类为“关键”的发现结果是可能会导致性能问题的已知问题。 分类为“重要”的发现结果表示配置未优化，但不一定导致性能问题。 分类“信息”的发现结果只是参考性的陈述。

请查看有关所有关键和重要发现结果的建议与链接。 了解这些发现结果对性能造成的影响，以及有关性能优化配置的最佳做法。

### <a name="storage-tab"></a>存储选项卡

“结果”部分显示有关存储的各种发现结果和建议。

“磁盘映射”和“卷映射”部分描述了逻辑卷与物理磁盘的相互关系。

物理磁盘透视图（“磁盘映射”）中的表格显示了磁盘上运行的所有逻辑卷。 在以下示例中，**PhysicalDrive2** 运行多个分区中创建的两个逻辑卷（J 和 H）：

![磁盘选项卡的屏幕截图](media/how-to-use-perfInsights/disktab.png)

卷透视图（“卷映射”）中的表格显示了每个逻辑卷下的所有物理磁盘。 请注意，对于 RAID/动态磁盘，可以在多个物理磁盘上运行一个逻辑卷。 在以下示例中，*C:\\mount* 是配置为 *SpannedDisk* 的装载点，位于物理磁盘 2 和 3 上：

![卷选项卡的屏幕截图](media/how-to-use-perfInsights/volumetab.png)

### <a name="sql-tab"></a>SQL 选项卡

如果目标 VM 托管了任何 SQL Server 实例，则报告中会显示名为“SQL”的另一个选项卡：

![“SQL”选项卡的屏幕截图](media/how-to-use-perfInsights/sqltab.png)

此部分包含“结果”选项卡，及其对应于 VM 上托管的每个 SQL Server 实例的其他选项卡。

“结果”选项卡包含找到的所有 SQL 相关性能问题列表和建议。

以下示例中显示了 **PhysicalDrive0**（运行 C 驱动器）。 之所以显示此项，是因为 **modeldev** 和 **modellog** 文件位于 C 驱动器上，并且属于不同的类型（例如，一个为数据文件，一个为事务日志）。

![日志信息的屏幕截图](media/how-to-use-perfInsights/loginfo.png)

SQL Server 的特定实例的选项卡包含一个常规部分，其中显示了所选实例的基本信息。 该选项卡还包含其他部分，用于显示设置、配置和用户选项等高级信息。

### <a name="diagnostic-tab"></a>诊断选项卡
“诊断”选项卡包含有关在运行 PerfInsights 过程中，计算机上的 CPU、磁盘和内存消耗量最高的几个组件的信息。 还可以在其中找到有关系统可能缺少的关键修补程序、任务列表和重要系统事件的信息。 

## <a name="references-to-the-external-tools-used"></a>所用外部工具参考

### <a name="diskspd"></a>Diskspd

Diskspd 是 Microsoft 开发的一个存储负载生成器和性能测试工具。 有关详细信息，请参阅 [Diskspd](https://github.com/Microsoft/diskspd)。

### <a name="xperf"></a>Xperf

Xperf 是一个命令行工具，用于通过 Windows 性能工具包捕获跟踪。 有关详细信息，请参阅 [Windows Performance Toolkit – Xperf](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/)（Windows 性能工具包 – Xperf）。

## <a name="next-steps"></a>后续步骤

可将诊断日志和报告上传到 Microsoft 支持部门供进一步审查。 支持人员可能要求传输 PerfInsights 生成的输出，以帮助进行故障排除。

以下屏幕截图显示了你可能会收到的消息示例：

![Microsoft 支持部门发送的示例消息屏幕截图](media/how-to-use-perfInsights/supportemail.png)

请遵照消息中的说明访问文件传输工作区。 为了提高安全性，首次使用时必须更改密码。

登录后，可以通过一个对话框来上传 PerfInsights 收集的 **CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip** 文件。
