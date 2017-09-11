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
ms.topic: article
ms.date: 07/18/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: a9cfd6052b58fe7a800f1b58113aec47a74095e3
ms.openlocfilehash: f22bd42302b96118dba0d4e5e387c6798a0b8777
ms.contentlocale: zh-cn
ms.lasthandoff: 08/12/2017

---
# <a name="how-to-use-perfinsights"></a>如何使用 PerfInsights 

[PerfInsights](http://aka.ms/perfinsightsdownload) 是自动执行的脚本，可收集有用的诊断信息、运行 I/O 压力负载以及提供分析报告，以便排查 Microsoft Azure 中的 Windows VM 性能问题。 

对于 VM 性能问题，建议你在通过 Microsoft 开具支持票证之前先运行此脚本。

## <a name="supported-troubleshooting-scenarios"></a>支持的故障排除方案

PerfInsights 可以收集和分析多种按不同方案分组的信息。

### <a name="collect-disk-configuration"></a>收集磁盘配置 

此方案收集磁盘配置和其他重要信息，包括以下项目：

-   事件日志

-   所有传入和传出连接的网络状态

-   网络和防火墙配置设置

-   任务列表，用于当前在系统中运行的所有应用程序

-   由 msinfo32 针对虚拟机 (VM) 创建的信息文件

-   Microsoft SQL Server 数据库配置设置（前提是已确定 VM 为运行 SQL Server 的服务器）

-   存储可靠性计数器

-   重要的 Windows 修补程序

-   已安装的筛选器驱动程序

这属于被动收集信息，不会影响系统。 

>[!Note]
>此方案自动包括在下述每个方案中。

### <a name="benchmarkstorage-performance-test"></a>基准测试/存储性能测试

此方案针对所有附加到 VM 的驱动器运行 [diskspd](https://github.com/Microsoft/diskspd) 基准测试（IOPS 和 MBPS）。 

> [!Note]
> 此方案可能影响系统，不应在实时生产系统中运行。 必要时可在专用维护时段运行此方案，以免出现问题。 因跟踪或基准测试而导致工作负荷增加时，可能会对 VM 性能造成负面影响。
>

### <a name="general-vm-slow-analysis"></a>常规 VM 慢速分析 

此方案使用在 Generalcounters.txt 文件中指定的计数器运行[性能计数器](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx)跟踪。 VM 在被确定是运行 SQL Server 的服务器后，会使用在 Sqlcounters.txt 文件中找到的计数器运行性能计数器跟踪， 并且会包括性能诊断数据。

### <a name="vm-slow-analysis-and-benchmark"></a>VM 慢速分析和基准

此方案运行[性能计数器](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx)跟踪，随后运行 [diskspd](https://github.com/Microsoft/diskspd) 基准测试。 

> [!Note]
> 此方案可能影响系统，不应在实时生产系统中运行。 必要时可在专用维护时段运行此方案，以免出现问题。 因跟踪或基准测试而导致工作负荷增加时，可能会对 VM 性能造成负面影响。
>

### <a name="azure-files-analysis"></a>Azure 文件分析 

此方案运行特殊的性能计数器捕获和网络跟踪。 捕获包括所有的“SMB 客户端共享”计数器。 以下是一些关键的 SMB 客户端共享性能计数器，属于捕获的一部分：

| **类型**     | **SMB 客户端共享计数器** |
|--------------|-------------------------------|
| IOPS         | 数据请求/秒             |
|              | 读取请求/秒             |
|              | 写入请求/秒            |
| 延迟      | 平均 秒/数据请求         |
|              | 平均 秒/读取                 |
|              | 平均 秒/写入                |
| IO 大小      | 平均 字节/数据请求       |
|              | 平均 字节/读取               |
|              | 平均 字节/写入              |
| 吞吐量   | 数据字节/秒                |
|              | 读取字节/秒                |
|              | 写入字节/秒               |
| 队列长度 | 平均 读取队列长度        |
|              | 平均 写入队列长度       |
|              | 平均 数据队列长度        |

### <a name="custom-configuration"></a>自定义配置 

运行自定义配置时，是在并行运行所有跟踪（性能诊断、性能计数器、xperf、网络、storport），具体取决于选择了多少不同的跟踪。 跟踪完成后，工具会运行 diskspd 基准测试（如果已选中）。 

> [!Note]
> 此方案可能影响系统，不应在实时生产系统中运行。 必要时可在专用维护时段运行此方案，以免出现问题。 因跟踪或基准测试而导致工作负荷增加时，可能会对 VM 性能造成负面影响。
>

## <a name="what-kind-of-information-is-collected-by-the-script"></a>脚本收集哪类信息？

将会根据所使用的性能方案，收集有关 Windows VM、磁盘或存储池配置、性能计数器、日志和各种跟踪的信息：

|收集的数据                              |  |  | 性能方案 |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                              | 收集磁盘配置 | 基准测试/存储性能测试 | 常规 VM 慢速分析 | VM 慢速分析和基准 | Azure 文件分析 | 自定义配置 |
| 事件日志中的信息      | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 系统信息               | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 卷映射                       | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 磁盘映射                         | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 正在运行的任务                    | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 存储可靠性计数器     | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 存储信息              | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| Fsutil 输出                    | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 筛选器驱动程序信息               | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| Netstat 输出                   | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 网络配置            | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 防火墙配置           | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| SQL Server 配置         | 是                        | 是                                | 是                      | 是                            | 是                  | 是                  |
| 性能诊断跟踪 * |                            |                                    | 是                      |                                |                      | 是                  |
| 性能计数器跟踪 **     |                            |                                    |                          |                                |                      | 是                  |
| SMB 计数器跟踪 **             |                            |                                    |                          |                                | 是                  |                      |
| SQL Server 计数器跟踪 **      |                            |                                    |                          |                                |                      | 是                  |
| XPerf 跟踪                      |                            |                                    |                          |                                |                      | 是                  |
| StorPort 跟踪                   |                            |                                    |                          |                                |                      | 是                  |
| 网络跟踪                    |                            |                                    |                          |                                | 是                  | 是                  |
| Diskspd 基准跟踪 ***      |                            | 是                                |                          | 是                            |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>性能诊断跟踪 (*)

在后台运行基于规则的引擎即可收集数据并诊断当前的性能问题。 当前支持以下规则：

- HighCpuUsage 规则：检测 CPU 使用率高的期间，并显示在这些期间 CPU 使用率高的使用者。
- HighDiskUsage 规则：检测物理磁盘上磁盘使用率高的期间，并显示在这些期间磁盘使用率高的使用者。
- HighResolutionDiskMetric 规则：显示每个物理磁盘每 50 毫秒的 IOPS、吞吐量和 IO 延迟指标。 此规则有助于快速确定磁盘限制期间。
- HighMemoryUsage 规则：检测内存使用率高的期间，并显示在这些期间内存使用率高的使用者。

> [!NOTE] 
> 目前支持包含 .NET Framework 3.5 或更高版本的 Windows 版本。

### <a name="performance-counter-trace-"></a>性能计数器跟踪 (**)

收集以下性能计数器：

- \Process、\Processor、\Memory、\Thread、\PhysicalDisk、\LogicalDisk
- \Cache\Dirty Pages、\Cache\Lazy Write Flushes/sec、\Server\Pool Nonpaged、Failures、\Server\Pool Paged Failures
- 在 \Network Interface、\IPv4\Datagrams、\IPv6\Datagrams、\TCPv4\Segments、\TCPv6\Segments、\Network Adapter、\WFPv4\Packets、\WFPv6\Packets、\UDPv4\Datagrams、\UDPv6\Datagrams、\TCPv4\Connection、\TCPv6\Connection、\Network QoS Policy\Packets、\Per Processor Network Interface Card Activity、\Microsoft Winsock BSP 下选定的计数器

#### <a name="for-sql-server-instances"></a>适用于 SQL Server 实例
- \SQL Server:Buffer Manager、\SQLServer:Resource Pool Stats、\SQLServer:SQL Statistics\
- \SQLServer:Locks、\SQLServer:General、Statistics
- \SQLServer:Access Methods

#### <a name="for-azure-files"></a>适用于 Azure 文件
\SMB Client Shares

### <a name="diskspd-benchmark-trace-"></a>Diskspd 基准跟踪 (***)
Diskspd IO 工作负荷测试 [OS 磁盘（写入）和池驱动器（读取/写入）]

## <a name="run-the-perfinsights-on-your-vm"></a>在 VM 上运行 PerfInsights

### <a name="what-do-i-have-to-know-before-i-run-the-script"></a>在运行此脚本之前，必须了解什么？ 

**脚本要求**

1.  此脚本必须在有性能问题的 VM 上运行。 

2.  支持以下 OS：Windows Server 2008 R2、2012、2012 R2、2016；Windows 8.1 和 Windows 10。

**在生产 VM 上运行脚本时可能存在的问题：**

1.  此脚本在与通过 XPerf 或 DiskSpd 配置的“基准”或“自定义”方案一起使用时，可能会对 VM 的性能造成负面影响。 在生产环境中运行脚本时要小心。

2.  将脚本与通过 DiskSpd 配置的“基准”或“自定义”方案一起使用时，请确保没有其他后台活动干扰受测试磁盘上的 I/O 工作负荷。

3.  默认情况下，此脚本使用临时存储驱动器来收集数据。 如果跟踪保持启用状态较长的时间，则收集的数据量会与之相关。 这会降低临时磁盘上空间的可用性，因此会影响任何依赖于该驱动器的应用程序。

### <a name="how-do-i-run-perfinsights"></a>如何运行 PerfInsights？ 

若要运行该脚本，请执行以下步骤：

1. 下载 [PerfInsights.zip](http://aka.ms/perfinsightsdownload)。

2. 取消阻止 PerfInsights.zip 文件。 为此，请右键单击 PerfInsights.zip 文件，然后选择“属性”。 在“常规”选项卡上，选择“取消阻止”，然后选择“确定”。 这样可确保脚本运行时没有任何其他安全提示。  

    ![解锁 zip 文件](media/how-to-use-perfInsights/unlock-file.png)

3.  将压缩的 PerfInsights.zip 文件展开到临时驱动器（默认情况下，通常为 D 驱动器）中。 压缩的文件应包含以下文件和文件夹：

    ![zip 文件夹中的文件](media/how-to-use-perfInsights/file-folder.png)

4.  以管理员身份打开 Windows PowerShell，然后运行 PerfInsights.ps1 脚本。

    ```
    cd <the path of PerfInsights folder >
    Powershell.exe -ExecutionPolicy UnRestricted -NoProfile -File .\\PerfInsights.ps1
    ```

    如果系统要求你确认更改执行策略，则可能需要输入“y”进行确认。

    在“免责声明”对话框中，可以选择将诊断信息与 Microsoft 支持部门共享。 此外还必须同意许可协议才能继续。 进行选择，然后单击“运行脚本”。

    ![“免责声明”框](media/how-to-use-perfInsights/disclaimer.png)

5.  运行脚本时提交案例号（如果已提供），这是为了方便我们的统计。 然后单击“确定”。
    
    ![输入支持 ID](media/how-to-use-perfInsights/enter-support-number.png)

6.  选择临时存储驱动器。 脚本可以自动检测驱动器的驱动器号。 如果此阶段出现问题，系统可能会提示你选择驱动器（默认驱动器为 D）。 生成的日志存储在此处的 log\_collection 文件夹中。 输入或接受驱动器号后，单击“确定”。

    ![输入驱动器](media/how-to-use-perfInsights/enter-drive.png)

7.  从提供的列表中选择故障排除方案。

       ![选择支持方案](media/how-to-use-perfInsights/select-scenarios.png)

8.  还可以在没有 UI 的情况下运行 PerfInsights。

    以下命令会在没有 UI 提示的情况下运行“常规 VM 慢速分析”故障排除方案，并且会捕获 30 秒的数据。 它会提示你同意免责声明和 EULA（与在步骤 4 中提到的相同）。

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30"

    如果希望 PerfInsights 在无提示模式下运行，请使用 -AcceptDisclaimerAndShareDiagnostics 参数。 例如，使用以下命令：

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30 -AcceptDisclaimerAndShareDiagnostics"

### <a name="how-do-i-troubleshoot-issues-while-running-the-script"></a>如何在运行脚本时排查问题？

如果脚本异常终止，则可以通过将脚本与 -Cleanup 开关一起运行来清除不一致状态，如下所示：

    powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -Cleanup"

如果在自动检测临时驱动器时出现问题，系统可能会提示你选择驱动器（默认驱动器为 D）。

![enter-drive](media/how-to-use-perfInsights/enter-drive.png)

脚本会卸载实用程序工具并删除临时文件夹。

### <a name="troubleshooting-other-script-issues"></a>排查其他脚本问题 

如果运行脚本时出现问题，请按 Ctrl+C 中断脚本执行。 若要删除临时对象，请参阅“在异常终止后进行清除”部分。

如果在多次尝试后仍遇到脚本故障问题，则建议以“调试模式”运行脚本，方法是在启动时使用“-Debug”参数选项。

故障发生后，请复制 PowerShell 控制台的完整输出，并将其发送给 Microsoft 支持部门代理，后者会协助你排查问题。

### <a name="how-do-i-run-the-script-in-custom-configuration-mode"></a>如何在自定义配置模式下运行脚本？

选择“自定义”配置即可启用多个并行的跟踪（使用 Shift 进行多选）：

![选择方案](media/how-to-use-perfInsights/select-scenario.png)

选择“性能诊断”、“性能计数器跟踪”、“XPerf 跟踪”、“网络跟踪”或“Storport 跟踪”方案时，请按对话框中的说明操作，然后尝试在启动跟踪后重现性能变慢的问题。

可通过以下对话框启动跟踪：

![start-trace](media/how-to-use-perfInsights/start-trace-message.png)

若要停止跟踪，必须在第二个对话框中确认命令。

![stop-trace](media/how-to-use-perfInsights/stop-trace-message.png)
![stop-trace](media/how-to-use-perfInsights/ok-trace-message.png)

当跟踪或操作完成后，将会在 D:\\log\_collection（或临时驱动器）中生成名为 CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip 的新文件。 可以将该文件发送给支持代理进行分析。

## <a name="review-the-diagnostics-report-created-by-perfinsights"></a>查看 PerfInsights 创建的诊断报告

在 PerfInsights 所生成的 CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip 文件中，可以找到一个 HTML 报告，其中详细说明了 PerfInsights 的发现。 若要查看该报告，请展开 CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip 文件，然后打开 PerfInsights Report.html 文件。

选择“发现”选项卡。

![发现选项卡](media/how-to-use-perfInsights/findingtab.png)

**说明**

-   红色消息为可能会导致性能问题的已知配置问题。

-   黄色消息为警告，表示配置未优化，但不一定导致性能问题。

-   蓝色消息为声明，单纯起告知作用。

请查看所有红色错误消息的 HTTP 链接，详细了解相关发现及其对性能的影响，或者了解最佳做法，以便对配置进行性能方面的优化。

### <a name="disk-configuration-tab"></a>磁盘配置选项卡

“概览”部分显示了存储配置的不同视图，其中包括来自 Diskpart 和存储空间的信息

“DiskMap”和“VolumeMap”以双视角的方式介绍了逻辑卷和物理磁盘的相互关系。

在 PhysicalDisk 视角 (DiskMap) 中，表显示了在磁盘上运行的所有逻辑卷。 在以下示例中，PhysicalDrive2 运行在多个分区（J 和 H）中创建的 2 个逻辑卷：

![数据选项卡](media/how-to-use-perfInsights/disktab.png)

在“卷”视角 (VolumeMap) 中，表显示了在每个逻辑卷下的所有物理磁盘。 请注意，对于 RAID/动态磁盘，可以在多个物理磁盘上运行一个逻辑卷。 在以下示例中，C:\\mount 是一个配置为 SpannedDisk 的装载点，位于 PhysicalDisk \#2 和 \#3 上：

![卷选项卡](media/how-to-use-perfInsights/volumetab.png)

### <a name="sql-server-tab"></a>“SQL Server”选项卡

如果目标 VM 托管了任何 SQL Server 实例，则会在报告中看到另一名为“SQL Server”的选项卡：

![sql 选项卡](media/how-to-use-perfInsights/sqltab.png)

此部分包含“概览”以及其他的子选项卡，对应于托管在 VM 上的每个 SQL Server 实例。

“概览”部分包含一个帮助表，该表汇总了所有物理磁盘（系统磁盘和数据磁盘），这些磁盘处于运行状态，其中包含各种数据文件和事务日志文件。

在以下示例中，PhysicalDrive0（运行 C 驱动器）之所以显示，是因为 modeldev 和 modellog 文件均位于 C 驱动器上，并且属于不同的类型（例如，一个为“数据文件”，一个为“事务日志”）：

![loginfo](media/how-to-use-perfInsights/loginfo.png)

SQL Server 的特定于实例的选项卡包含一个常规部分，用于显示所选实例的基本信息；此外还有其他部分，用于显示高级信息，包括“设置”、“配置”和“用户选项”。

## <a name="references-to-the-external-tools-used"></a>所用外部工具参考

### <a name="diskspd"></a>Diskspd

DISKSPD 是一个存储负载生成器，也是一个性能测试工具，由 Windows、Windows Server 以及云服务器基础结构工程团队提供。 有关详细信息，请参阅 [Diskspd](https://github.com/Microsoft/diskspd)。

### <a name="xperf"></a>XPerf

Xperf 是一个命令行工具，用于捕获 Windows 性能工具包中的跟踪。

有关详细信息，请参阅 [Windows Performance Toolkit – Xperf](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/)（Windows 性能工具包 – Xperf）。

## <a name="next-steps"></a>后续步骤

### <a name="upload-diagnostics-logs-and-reports-to-microsoft-support-for-further-review"></a>将诊断日志和报告上传到 Microsoft 支持部门，以供进一步复查

当你咨询 Microsoft 支持部门人员时，他们可能会请求你传输 PerfInsights 生成的输出，以方便故障排除。

支持部门代理会为你创建一个 DTM 工作区，你会收到一封电子邮件，其中包含 DTM 门户 (https://filetransfer.support.microsoft.com/EFTClient/Account/Login.htm) 的链接，以及唯一的用户 ID 和密码。

该邮件将从 **CTS Automated Diagnostics Services** (ctsadiag@microsoft.com) 发送。

![邮件示例](media/how-to-use-perfInsights/supportemail.png)

为了增强安全性，需在第一次使用密码时更改密码。

登录到 DTM 以后，将会出现一个对话框，用于上传 PerfInsights 所收集的 CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip 文件。

