---
title: "Azure 诊断故障排除 | Microsoft Docs"
description: "排查在 Azure 虚拟机、Service Fabric 或云服务中使用 Azure 诊断时遇到的问题。"
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 66469bce-d457-4d1e-b550-a08d2be4d28c
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/12/2017
ms.author: robb
ms.translationtype: HT
ms.sourcegitcommit: 19be73fd0aec3a8f03a7cd83c12cfcc060f6e5e7
ms.openlocfilehash: df53e92b877b4790bb700f176a1988d265ec4678
ms.contentlocale: zh-cn
ms.lasthandoff: 07/13/2017

---
# <a name="azure-diagnostics-troubleshooting"></a>Azure 诊断故障排除
有关使用 Azure 诊断的故障排除信息。 有关 Azure 诊断的详细信息，请参阅 [Azure 诊断概述](azure-diagnostics.md)。

## <a name="logical-components"></a>逻辑组件
**诊断插件启动器 (DiagnosticsPluginLauncher.exe)**：启动 Azure 诊断扩展。 用作入口点进程。

**诊断插件 (DiagnosticsPlugin.exe)**：由上述启动器启动的主要进程，配置监视代理，启动并管理其生存期。 

**监视代理（MonAgent\*.exe 进程）**：这些进程执行大部分工作，例如监视、收集和传输诊断数据。  

## <a name="logartifact-paths"></a>日志/项目路径
以下是一些重要日志和项目的路径。 我们在文档的剩余部分继续引用这些路径：
### <a name="cloud-services"></a>云服务
| 项目 | 路径 |
| --- | --- |
| **Azure 诊断配置文件** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\Config.txt |
| **日志文件** | C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version>\ |
| **诊断数据的本地存储** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Tables |
| **监视代理配置文件** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MaConfig.xml |
| **Azure 诊断扩展包** | %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<version> |
| **日志收集实用工具路径** | %SystemDrive%\Packages\GuestAgent\ |
| **MonAgentHost 日志文件** | C:\Resources\Directory\<CloudServiceDeploymentID>.\<RoleName>.DiagnosticStore\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

### <a name="virtual-machines"></a>虚拟机
| 项目 | 路径 |
| --- | --- |
| **Azure 诊断配置文件** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\RuntimeSettings |
| **日志文件** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\Logs\ |
| **诊断数据的本地存储** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Tables |
| **监视代理配置文件** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MaConfig.xml |
| **状态文件** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<version>\Status |
| **Azure 诊断扩展包** | C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>|
| **日志收集实用工具路径** | C:\WindowsAzure\Packages |
| **MonAgentHost 日志文件** | C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD0107\Configuration\MonAgentHost.<seq_num>.log |

## <a name="azure-diagnostics-is-not-starting"></a>Azure Diagnostics 不启动
从上面提供的日志文件的位置查看“DiagnosticsPluginLauncher.log”和“DiagnosticsPlugin.log”，获取有关诊断未能启动的原因的信息。 

如果这些日志指示 `Monitoring Agent not reporting success after launch`，则表示启动 MonAgentHost.exe 失败。 在上面部分中指示 `MonAgentHost log file` 的位置查看其日志。

日志文件的最后一行包含退出代码。  

```
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0
```
如果发现退出代码为负数，请参阅[参考资料](#references)中的[退出代码表](#azure-diagnostics-plugin-exit-codes)。

## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>未将 Diagnostics 数据记录到 Azure 存储
确定是未否显示任何数据或仅未显示某些数据。

### <a name="diagnostics-infrastructure-logs"></a>诊断基础结构日志
诊断基础结构日志是 Azure 诊断记录遇到的任何错误的位置。 确保在配置中已启用（[如何？](#how-to-check-diagnostics-extension-configuration)）捕获诊断基础结构日志，并在配置的存储帐户中快速查找显示在 `DiagnosticInfrastructureLogsTable` 表中的任何相关错误。

### <a name="no-data-is-showing-up"></a>没有数据显示
完全丢失事件数据最常见的原因是错误地定义了存储帐户信息。

解决方案：更正诊断配置，然后重新安装 Azure 诊断。

如果存储帐户配置正确，通过远程桌面连接到计算机中并确保 DiagnosticsPlugin.exe 和 MonAgentCore.exe 正在运行。 如果未运行，按 [ Azure 诊断不启动](#azure-diagnostics-is-not-starting)中的说明操作。 如果进程正在运行，请跳转到[数据是否是本地捕获的](#is-data-getting-captured-locally)并按照该指南中的说明操作。

### <a name="part-of-the-data-is-missing"></a>部分数据丢失
如果得到一些数据，而没有得到其他数据。 这意味着数据收集/传输管道设置正确。 按照此处小节中的说明操作，缩小问题的范围：
#### <a name="is-collection-configured"></a>是否配置了集合： 
诊断配置包含指示收集特定类型数据的部分。 [查看配置](#how-to-check-diagnostics-extension-configuration)确保不查找尚未配置收集的数据。
#### <a name="is-the-host-generating-data"></a>主机是否生成数据：
- **性能计数器**：打开 perfmon 并检查计数器。
- **跟踪日志**：通过远程桌面连接到 VM 并向应用的配置文件添加 TextWriterTraceListener。  请参阅 http://msdn.microsoft.com/en-us/library/sk36c28t.aspx 以设置文本侦听器。  确保 `<trace>` 元素具有 `<trace autoflush="true">`。<br />
如果没有看到生成跟踪日志，请按照[关于跟踪日志丢失的更多信息](#more-about-trace-logs-missing)中的说明操作。
- **ETW 跟踪**：通过远程桌面连接到 VM 并安装 PerfView。  在“PerfView”中运行“文件”->“用户命令”->“侦听 etwprovder1、etwprovider2 等”。请注意，侦听命令区分大小写，ETW 提供程序的逗号分隔列表之间不能有空格。  如果命令未能运行，可以单击 Perfview 工具右下角的“日志”按钮，查看尝试运行的内容以及结果。  假设输入正确，将弹出一个新窗口，几秒钟后，你将开始看到 ETW 跟踪。
- **事件日志**：通过远程桌面连接到 VM。 打开`Event Viewer`并确保事件存在。
#### <a name="is-data-getting-captured-locally"></a>数据是否是本地捕获的：
接下来，请确保从本地获取数据。
数据本地存储在[诊断数据本地存储](#log-artifacts-path)中的 `*.tsf` 文件中。 不同类型的日志被收集在不同的 `.tsf` 文件中。 名称与 Azure 存储中的表名相似。 例如，在 `PerformanceCountersTable.tsf` 中收集`Performance Counters`，在 `WindowsEventLogsTable.tsf` 中收集事件日志。 使用[本地日志提取](#local-log-extraction)部分中的说明打开本地收集文件，并确保它们收集在磁盘上。

如果没有看到本地收集的日志，并且已验证主机正在生成数据，则可能是遇到了配置问题。 请仔细查看相应部分的配置。 还应查看为监控代理 [MaConfig.xml](#log-artifacts-path)生成的配置，确保有一些描述相关日志源的部分，并确保它不会在 Azure 诊断配置和监视代理配置之间的转换中丢失。
#### <a name="is-data-getting-transferred"></a>是否传输了数据：
如果已验证数据是本地捕获的，但仍未在存储帐户中看到它： 
- 首先，确保提供了正确的存储帐户，并且没有为给定的存储帐户滚动更新过密钥等。 对于云服务，有时我们发现人们不更新其 `useDevelopmentStorage=true`。
- 如果提供的存储帐户正确。 确保未受到一些不允许组件访问公共存储终结点的网络限制。 执行此操作的一种方法是将远程桌面连接到计算机，自行尝试向相同的存储帐户写入一些内容。
- 最后，你可以尝试并查看监视代理报告的故障。 监控代理将其日志写入位于[诊断数据本地存储](#log-artifacts-path)中的 `maeventtable.tsf`。 按照[本地日志提取](#local-log-extraction)部分中的说明打开此文件，尝试找出是否有指示未能读取本地文件或写入存储的`errors`。

### <a name="capturing--archiving-logs"></a>捕获/存档日志
已完成上述步骤，但无法弄清楚故障是什么并正考虑与支持部门联系。 他们可能会要求你做的第一件事是从计算机收集日志。 可以通过自行收集节省时间。 运行位于[日志收集实用工具路径](#log-artifacts-path)下的 `CollectGuestLogs.exe` 实用工具，它将生成一个 zip 文件，其中所有相关的 Azure 日志位于同一文件夹中。

## <a name="diagnostics-data-tables-not-found"></a>找不到诊断数据表
Azure 存储中保存 ETW 事件的表是使用以下代码命名的：

```C#
        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;
```

下面是一个示例：

```XML
        <EtwEventSourceProviderConfiguration provider=”prov1”>
          <Event id=”1” />
          <Event id=”2” eventDestination=”dest1” />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider=”prov2”>
          <DefaultEvents eventDestination=”dest2” />
        </EtwEventSourceProviderConfiguration>
```
```JSON
"EtwEventSourceProviderConfiguration": [
    {
        "provider": "prov1",
        "Event": [
            {
                "id": 1
            },
            {
                "id": 2,
                "eventDestination": "dest1"
            }
        ],
        "DefaultEvents": {
            "eventDestination": "DefaultEventDestination",
            "sinks": ""
        }
    },
    {
        "provider": "prov2",
        "DefaultEvents": {
            "eventDestination": "dest2"
        }
    }
]
```
这将产生 4 个表：

| 事件 | 表名称 |
| --- | --- |
| provider=”prov1” &lt;Event id=”1” /&gt; |WADEvent+MD5(“prov1”)+”1” |
| provider=”prov1” &lt;Event id=”2” eventDestination=”dest1” /&gt; |WADdest1 |
| provider=”prov1” &lt;DefaultEvents /&gt; |WADDefault+MD5(“prov1”) |
| provider=”prov2” &lt;DefaultEvents eventDestination=”dest2” /&gt; |WADdest2 |

## <a name="references"></a>参考

### <a name="how-to-check-diagnostics-extension-configuration"></a>如何检查诊断扩展配置
检查扩展配置的最简方法是导航到 http://resources.azure.com、再导航到 Azure 诊断扩展 (IaaSDiagnostics/PaaDiagnostics) 有问题的虚拟机或云服务。

或者，通过远程桌面连接到计算机并查看[此处](#log-artifacts-path)相应部分中所述的 Azure 诊断配置文件。

在任何一种情况下，先搜索“Microsoft.Azure.Diagnostics”，再搜索“xmlCfg”或“WadCfg”字段。 

就虚拟机这种情况而言，如果存在 WadCfg 字段，则表示配置在 JSON 中。 如果存在 xmlCfg 字段，则意味着配置在 XML 中，且已进行 base64 编码。 你需要[将其解码](http://www.bing.com/search?q=base64+decoder)才能查看诊断加载的 XML。

对于云服务角色，如果从磁盘选择配置，数据采用 base64 编码，则需要[将其解码](http://www.bing.com/search?q=base64+decoder)才能查看诊断加载的 XML。

### <a name="azure-diagnostics-plugin-exit-codes"></a>Azure 诊断插件退出代码
该插件返回以下退出代码：

| 退出代码 | 说明 |
| --- | --- |
| 0 |成功。 |
| -1 |常规错误。 |
| -2 |无法加载 rcf 文件。<p>此内部错误应仅当在 VM 上不正确地手动调用了来宾代理插件启动器时才会发生。 |
| -3 |无法加载诊断配置文件。<p><p>解决方案：这是配置文件未通过架构验证的结果。 解决方案是提供符合架构的配置文件。 |
| -4 |监视代理诊断的另一个实例已在使用本地资源目录。<p><p>解决方案：为 **LocalResourceDirectory** 指定不同的值。 |
| -6 |来宾代理插件启动器尝试使用无效的命令行启动诊断。<p><p>此内部错误应仅当在 VM 上不正确地手动调用了来宾代理插件启动器时才会发生。 |
| -10 |Diagnostics 插件退出并返回未处理的异常。 |
| -11 |来宾代理程序无法创建负责启动和监视监视代理的进程。<p><p>解决方案：验证是否有足够的系统资源用于启动新进程。<p> |
| -101 |调用诊断插件时参数无效。<p><p>此内部错误应仅当在 VM 上不正确地手动调用了来宾代理插件启动器时才会发生。 |
| -102 |插件进程将无法初始化自身。<p><p>解决方案：验证是否有足够的系统资源用于启动新进程。 |
| -103 |插件进程将无法初始化自身。 具体而言，它无法创建记录器对象。<p><p>解决方案：验证是否有足够的系统资源用于启动新进程。 |
| -104 |无法加载来宾代理提供的 rcf 文件。<p><p>此内部错误应仅当在 VM 上不正确地手动调用了来宾代理插件启动器时才会发生。 |
| -105 |诊断插件无法打开诊断配置文件。<p><p>此内部错误应仅当在 VM 上不正确地手动调用了诊断插件时才会发生。 |
| -106 |无法读取诊断配置文件。<p><p>解决方案：这是配置文件未通过架构验证的结果。 因此，解决方案是提供符合架构的配置文件。 请参阅[如何检查诊断扩展配置](#how-to-check-diagnostics-extension-configuration)。 |
| -107 |传递给监视代理的资源目录无效。<p><p>此内部错误应仅当在 VM 上不正确地手动调用了监视代理时才会发生。</p> |
| -108 |无法将诊断配置文件转换为监视代理配置文件。<p><p>此内部错误应仅当使用无效的配置文件手动调用了诊断插件时才会发生。 |
| -110 |常规诊断配置错误。<p><p>此内部错误应仅当使用无效的配置文件手动调用了诊断插件时才会发生。 |
| -111 |无法启动监视代理。<p><p>解决方案：验证是否有足够的系统资源。 |
| -112 |常规错误 |

### <a name="local-log-extraction"></a>本地日志提取
监视代理将日志和项目收集为 `.tsf` 文件。 `.tsf` 文件不可读，但可以将其转换为 `.csv`，如下所示： 

```
<Azure diagnostics extension package>\Monitor\x64\table2csv.exe <relevantLogFile>.tsf
```
将在与相应的 `.tsf` 文件相同的路径中创建一个名为 `<relevantLogFile>.csv` 的新文件。

**注意**：只需要对主 tsf 文件（例如 PerformanceCountersTable.tsf）运行此实用工具。 将自动处理随附的文件（例如 PerformanceCountersTables_\*\*001.tsf、PerformanceCountersTables_\*\*002.tsf 等）。

### <a name="more-about-trace-logs-missing"></a>关于跟踪日志丢失的更多信息

**注意：**这主要适用于云服务，除非已在于 IaaS VM 上运行的应用程序上配置了 DiagnosticsMonitorTraceListener。 

- 确保在 web.config 或 app.config 中配置了 DiagnosticMonitorTraceListener。  这是在云服务项目中默认配置的，但有些客户将其注释掉，这将导致诊断不能收集到跟踪语句。 
- 如果没有从 OnStart 或 Run 方法写入日志，请确保 DiagnosticMonitorTraceListener 位于 app.config 中。  默认情况下，它在 web.config 中，但只适用于在 w3wp.exe 中运行的代码；所以需使它位于 app.config 中才能捕获 WaIISHost.exe 中运行的跟踪。
- 确保使用的是 Diagnostics.Trace.TraceXXX，而不是 Diagnostics.Debug.WriteXXX。  将从发布版本中删除 Debug 语句。
- 确保已编译的代码实际上具有 Diagnostics.Trace 行（使用反射器、ildasm 或 ILSpy 验证）。  从已编译的二进制文件中删除了 Diagnostics.Trace 命令，除非你使用 TRACE 条件编译符号。  如果使用 MSBuild 生成项目，那么这将是一个常见问题。

## <a name="known-issues-and-mitigations"></a>已知问题和缓解措施
下面是已知问题和已知缓解措施的列表：

**1. .NET 4.5 依赖项：**

WAD 具有对 .NET 4.5 framework 或更高版本的运行时依赖项。 在撰写本文时，为云服务配置的所有计算机以及所有官方 Azure 虚拟机基础映像都安装了 .NET 4.5 或更高版本。 但仍有可能遇到这样的情况：尝试在尚未安装 .NET 4.5 或更高版本的计算机上运行 WAD。 从旧映像或快照创建计算机或者自带自定义磁盘时，会发生这种情况。

这在运行 DiagnosticsPluginLauncher.exe 时通常显示为退出代码 255。 由于未经处理的异常发生的故障： 
```
System.IO.FileLoadException: Could not load file or assembly 'System.Threading.Tasks, Version=1.5.11.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies
```

**缓解措施：**在计算机上安装 .NET 4.5 或更高版本。

**2.性能计数器数据在存储中可用，但不显示在门户中**

默认情况下，虚拟机门户体验显示某些性能计数器。 如果你没有看到它们，并且知道正在生成数据，因为它在存储中可用。 请检查：
- 存储中的数据是否有英语计数器名称。 如果计数器名称不是英语，门户指标图表将无法识别它。
- 如果性能计数器名称中使用了通配符(\*)，门户将无法关联配置和收集的计数器。

**缓解措施**：将系统帐户的计算机语言更改为英语。 控制面板->区域->管理->复制设置->取消选中“欢迎屏幕和系统帐户”，从而不向系统帐户应用自定义语言。 如果希望门户为主要消费体验，还要确保不使用通配符。
