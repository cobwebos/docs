---
title: 启用用于 VM 的 Azure Monitor （预览版）概述 |Microsoft Docs
description: 了解如何部署和配置用于 VM 的 Azure Monitor。 找出系统要求。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/14/2019
ms.openlocfilehash: ce95223e9501ad7bec2bd260a8fe6f1537db5593
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75400624"
---
# <a name="enable-azure-monitor-for-vms-preview-overview"></a>启用用于 VM 的 Azure Monitor （预览）概述

本文概述了可用于设置用于 VM 的 Azure Monitor 的选项。 使用用于 VM 的 Azure Monitor 来监视运行状况和性能。 发现在 Azure 虚拟机（Vm）和虚拟机规模集、本地 Vm 或托管在其他云环境中的 Vm 上运行的应用程序依赖项。  

设置用于 VM 的 Azure Monitor：

* 直接从 VM 或虚拟机规模集中选择 "**见解（预览版）** "，启用单个 Azure VM 或虚拟机规模集。
* 使用 Azure 策略启用两个或更多个 Azure Vm 和虚拟机规模集。 此方法确保在现有的和新的 Vm 和规模集上，已安装并正确配置所需的依赖项。 将报告不符合要求的 Vm 和规模集，因此你可以决定是否启用它们并对其进行修正。
* 使用 PowerShell 跨指定的订阅或资源组启用两个或更多 Azure VM，或启用虚拟机规模集。
* 启用用于 VM 的 Azure Monitor 以监视公司网络或其他云环境中托管的 Vm 或物理计算机。

## <a name="prerequisites"></a>必备组件

在开始之前，请确保理解以下部分中的信息。 

>[!NOTE]
>本部分中所述的以下信息也适用于[服务映射解决方案](service-map.md)。  

### <a name="log-analytics"></a>Log Analytics

用于 VM 的 Azure Monitor 支持以下区域中的 Log Analytics 工作区：

- 美国中西部
- 美国西部
- 美国西部 2
- 美国中南部
- 美国东部
- 美国东部 2
- 美国中部
- 美国中北部
- 加拿大中部
- 英国南部
- 欧洲北部
- 欧洲西部
- 东亚
- 亚洲东南部
- 印度中部
- 日本东部
- 澳大利亚东部
- 澳大利亚东南部

>[!NOTE]
>你可以从任何区域部署 Azure Vm。 这些 Vm 并不限于 Log Analytics 工作区所支持的区域。
>

如果没有工作区，可以使用以下资源之一创建一个工作区：
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure 门户](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure 资源管理器](../../azure-monitor/platform/template-workspace-configuration.md)

你还可以在为 Azure 门户中的单个 Azure VM 或虚拟机规模集启用监视时创建工作区。

若要在 Log Analytics 工作区中设置使用 Azure 策略、Azure PowerShell 或 Azure 资源管理器模板的大规模方案：

* 安装 ServiceMap 和 InfrastructureInsights 解决方案。 可以使用提供的 Azure 资源管理器模板来完成此安装。 或在 "**入门**" 选项卡上，选择 "**配置工作区**"。
* 配置 Log Analytics 工作区以收集性能计数器。

若要为大规模方案配置工作区，请使用以下方法之一：

* 使用[Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace)。
* 在 "用于 VM 的 Azure Monitor[**策略覆盖范围**](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview)" 页上，选择 "**配置工作区**"。 

### <a name="supported-operating-systems"></a>支持的操作系统

下表列出了用于 VM 的 Azure Monitor 支持的 Windows 和 Linux 操作系统。 稍后在本部分中，你将找到一个完整列表，详细介绍了主要和次要 Linux OS 版本和受支持的内核版本。

|OS 版本 |性能 |地图 |
|-----------|------------|-----|
|Windows Server 2019 | X | X |
|Windows Server 2016 1803 | X | X |
|Windows Server 2016 | X | X |
|Windows Server 2012 R2 | X | X |
|Windows Server 2012 | X | X |
|Windows Server 2008 R2 | X | X|
|Windows 10 1803 | X | X |
|Windows 8.1 | X | X |
|Windows 8 | X | X |
|Windows 7 SP1 | X | X |
|Red Hat Enterprise Linux （RHEL）6、7| X | X| 
|Ubuntu 18.04、16.04 | X | X |
|CentOS Linux 7、6 | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X |
|Debian 9.4、8 | X<sup>1</sup> | |

<sup>1</sup> 用于 VM 的 Azure Monitor 的“性能”功能仅在 Azure Monitor 中可用。 它无法直接从 Azure VM 的左窗格中获取。

>[!NOTE]
>在 Linux 操作系统中：
> - 仅默认版本和 SMP Linux 内核版本受支持。
> - 任何 Linux 发行版都不支持非标准内核版本（例如物理地址扩展 [PAE] 和 Xen）。 例如，不支持版本字符串为 *2.6.16.21-0.8-xen* 的系统。
> - 不支持自定义内核（包括标准内核的重新编译）。
> - 支持 CentOSPlus 内核。
> - 必须为 Spectre 漏洞修补 Linux 内核。 有关更多详细信息，请咨询 Linux 分销商。

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| OS 版本 | 内核版本 |
|:--|:--|
| 7.6 | 3.10.0-957 |
| 7.5 | 3.10.0-862 |
| 7.4 | 3.10.0-693 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| OS 版本 | 内核版本 |
|:--|:--|
| 6.10 | 2.6.32-754 |
| 6.9 | 2.6.32-696 |

#### <a name="centosplus"></a>CentOSPlus

| OS 版本 | 内核版本 |
|:--|:--|
| 6.10 | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
| 6.9 | 2.6.32-696.30.1<br>2.6.32-696.18.7 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| OS 版本 | 内核版本 |
|:--|:--|
| 18.04 | 5.0 （包括 Azure 优化内核）<br>4.18 *<br>4.15* |
| 16.04.3 | 4.15. * |
| 16.04 | 4.13.\*<br>4.11.\*<br>4.10.\*<br>4.8.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| OS 版本 | 内核版本 |
|:--|:--|
|12 SP4 | 4.12. * （包括 Azure 优化内核） |
|12 SP3 | 4.4.* |
|12 SP2 | 4.4.* |

#### <a name="debian"></a>Debian 

| OS 版本 | 内核版本 |
|:--|:--|
| 9 | 4.9 | 

### <a name="the-microsoft-dependency-agent"></a>Microsoft Dependency Agent

用于 VM 的 Azure Monitor 中的映射功能将从 Microsoft 依赖关系代理获取其数据。 依赖项代理依赖于使用 Log Analytics 代理连接到 Log Analytics。 因此，您的系统必须安装 Log Analytics 代理，并使用依赖关系代理进行配置。

无论是为单个 Azure VM 启用用于 VM 的 Azure Monitor 还是使用大规模部署方法，都可以使用适用于[Windows](../../virtual-machines/extensions/agent-dependency-windows.md)或[LINUX](../../virtual-machines/extensions/agent-dependency-linux.md)的 Azure VM 依赖关系代理扩展来安装代理作为体验的一部分。

>[!NOTE]
>本部分中所述的以下信息也适用于[服务映射解决方案](service-map.md)。  

在混合环境中，可以手动下载和安装依赖关系代理，也可以使用自动化方法。

下表描述了映射功能在混合环境中支持的连接源。

| 连接的源 | 受支持 | Description |
|:--|:--|:--|
| Windows 代理 | 是 | 除了适用于[windows 的 Log Analytics 代理](../../azure-monitor/platform/log-analytics-agent.md)，windows 代理还需要依赖项代理。 有关详细信息，请参阅[支持的操作系统](#supported-operating-systems)。 |
| Linux 代理 | 是 | Linux 代理与[适用于 linux 的 Log Analytics 代理](../../azure-monitor/platform/log-analytics-agent.md)一起需要依赖项代理。 有关详细信息，请参阅[支持的操作系统](#supported-operating-systems)。 |
| System Center Operations Manager 管理组 | 否 | |

可以从以下位置下载依赖关系代理：

| 文件 | OS | 版本 | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.9.2 | 6DFF19B9690E42CA190E3B69137C77904B657FA02895033EAA4C3A6A41DA5C6A |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.9.1 | 1CB447EF30FC042FE7499A686638F3F9B4F449692FB9D80096820F8024BE4D7C |

## <a name="role-based-access-control"></a>基于角色的访问控制

若要启用和访问用于 VM 的 Azure Monitor 中的功能，您必须具有 " *Log Analytics 参与者*" 角色。 若要查看性能、运行状况和映射数据，你必须具有 Azure VM 的 "*监视读取*者" 角色。 必须为用于 VM 的 Azure Monitor 配置 Log Analytics 工作区。

有关如何控制对 Log Analytics 工作区的访问的详细信息，请参阅[管理工作区](../../azure-monitor/platform/manage-access.md)。

## <a name="how-to-enable-azure-monitor-for-vms-preview"></a>如何启用用于 VM 的 Azure Monitor （预览）

使用此表中所述的方法之一启用用于 VM 的 Azure Monitor：

| 部署状态 | 方法 | Description |
|------------------|--------|-------------|
| 单个 Azure VM 或虚拟机规模集 | [从 VM 启用](vminsights-enable-single-vm.md) | 可以通过直接从 VM 或虚拟机规模集选择 "**见解（预览版）** " 来启用单个 Azure VM。 |
| 多个 Azure Vm 或虚拟机规模集 | [通过 Azure 策略启用](vminsights-enable-at-scale-policy.md) | 可以使用 Azure 策略和可用策略定义启用多个 Azure Vm。 |
| 多个 Azure Vm 或虚拟机规模集 | [通过 Azure PowerShell 或 Azure 资源管理器模板启用](vminsights-enable-at-scale-powershell.md) | 可以通过使用 Azure PowerShell 或 Azure 资源管理器模板跨指定的订阅或资源组启用多个 Azure Vm 或虚拟机规模集。 |
| 混合云 | [为混合环境启用](vminsights-enable-hybrid-cloud.md) | 你可以部署到你的数据中心或其他云环境中托管的 Vm 或物理计算机。 |

## <a name="performance-counters-enabled"></a>已启用性能计数器 

用于 VM 的 Azure Monitor 配置 Log Analytics 工作区以收集它使用的性能计数器。 下表列出了每60秒收集的对象和计数器。

>[!NOTE]
>以下用于 VM 的 Azure Monitor 启用的性能计数器列表不限制您启用需要从向工作区报告的 Vm 收集的其他计数器。 此外，如果禁用这些计数器，它会阻止性能功能附带的性能图表集显示 Vm 的资源利用率。

### <a name="windows-performance-counters"></a>Windows 性能计数器

|对象名称 |计数器名称 |
|------------|-------------|
|LogicalDisk |可用空间百分比 |
|LogicalDisk |每次读取的平均磁盘扇区数 |
|LogicalDisk |Avg. Disk sec/Transfer |
|LogicalDisk |每次写入的平均磁盘扇区数 |
|LogicalDisk |磁盘字节/秒 |
|LogicalDisk |磁盘读取字节数/秒 |
|LogicalDisk |磁盘读取数/秒 |
|LogicalDisk |磁盘传输数/秒 |
|LogicalDisk |磁盘写入字节数/秒 |
|LogicalDisk |磁盘写入数/秒 |
|LogicalDisk |可用 MB 数 |
|内存 |可用兆字节数 |
|网络适配器 |收到的字节数/秒 |
|网络适配器 |发送的字节数/秒 |
|处理器 |处理器时间百分比 |

### <a name="linux-performance-counters"></a>Linux 性能计数器

|对象名称 |计数器名称 |
|------------|-------------|
|逻辑磁盘 |已用空间百分比 |
|逻辑磁盘 |磁盘读取字节数/秒 |
|逻辑磁盘 |磁盘读取数/秒 |
|逻辑磁盘 |磁盘传输数/秒 |
|逻辑磁盘 |磁盘写入字节数/秒 |
|逻辑磁盘 |磁盘写入数/秒 |
|逻辑磁盘 |可用 MB 数 |
|逻辑磁盘 |逻辑磁盘字节数/秒 |
|内存 |可用内存 MB 数 |
|网络 |已接收的字节数总计 |
|网络 |已传输的字节数总计 |
|处理器 |处理器时间百分比 |

## <a name="management-packs"></a>管理包

使用 Log Analytics 工作区启用和配置用于 VM 的 Azure Monitor 时，会将管理包转发给向该工作区报告的所有 Windows 计算机。 如果已将[System Center Operations Manager 管理组](../../azure-monitor/platform/om-agents.md)与 Log Analytics 工作区集成，则服务映射管理包将从管理组部署到向管理组报告的 Windows 计算机。  

管理包名为*microsoft.intelligencepacks.updateassessment. microsoft.intelligencepacks.applicationdependencymonitor*。 其写入 `%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\` 文件夹。 管理包使用的数据源是 `%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll`。

## <a name="diagnostic-and-usage-data"></a>诊断和使用情况数据

Microsoft 使用 Azure Monitor 服务自动收集使用情况和性能数据。 Microsoft 使用这些数据来改善服务的质量、安全性和完整性。 

为提供准确且高效的疑难解答功能，地图功能包含有关软件配置的数据。 数据提供诸如操作系统和版本、IP 地址、DNS 名称和工作站名称之类的信息。 Microsoft 不收集姓名、地址或其他联系信息。

有关数据收集和使用的详细信息，请参阅 [Microsoft Online Services 隐私声明](https://go.microsoft.com/fwlink/?LinkId=512132)。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

现在，已为 VM 启用监视功能，可在用于 VM 的 Azure Monitor 中进行分析监视信息。

## <a name="next-steps"></a>后续步骤

若要了解如何使用性能监视功能，请参阅[查看用于 VM 的 Azure Monitor 性能](vminsights-performance.md)。 若要查看已发现的应用程序依赖项，请参阅[查看用于 VM 的 Azure Monitor 映射](vminsights-maps.md)。
