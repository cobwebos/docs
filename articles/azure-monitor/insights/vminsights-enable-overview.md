---
title: 有关启用用于 VM 的 Azure Monitor（预览版）的概述 | Microsoft Docs
description: 了解如何部署和配置用于 VM 的 Azure Monitor。 了解系统要求。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2019
ms.author: magoedte
ms.openlocfilehash: f395ba5d63463aa177b453d187d025a4461eff28
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69905580"
---
# <a name="enable-azure-monitor-for-vms-preview-overview"></a>有关启用用于 VM 的 Azure Monitor（预览版）的概述

本文概述可用来设置用于 VM 的 Azure Monitor 的选项。 使用用于 VM 的 Azure Monitor 可以监视运行状况和性能。 发现 Azure 虚拟机 (VM) 和虚拟机规模集、本地 VM 或其他云环境中托管的 VM 上运行的应用程序依赖项。  

若要设置用于 VM 的 Azure Monitor：

* 直接在 VM 或虚拟机规模集中选择“见解(预览版)”，以启用单个 Azure VM 或虚拟机规模集。
* 使用 Azure Policy 启用两个或更多个 Azure VM 和虚拟机规模集。 此方法可确保在现有和新的 VM 与规模集上安装并正确配置所需的依赖项。 系统会报告不合规的 VM 和规模集，因此你可以决定是否要启用和修正它们。
* 使用 PowerShell 跨指定的订阅或资源组启用两个或更多 Azure VM，或启用虚拟机规模集。
* 启用用于 VM 的 Azure Monitor，以监视企业网络或其他云环境中托管的 VM 或物理计算机。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保理解以下部分中的信息。 

>[!NOTE]
>本部分中所述的以下信息也适用于[服务映射解决方案](service-map.md)。  

### <a name="log-analytics"></a>Log Analytics

用于 VM 的 Azure Monitor 支持以下区域中的 Log Analytics 工作区：

- 美国中西部
- 美国西部 2<sup>1</sup>
- East US
- 加拿大中部
- 英国南部
- 西欧
- 东南亚
- 澳大利亚东部<sup>1</sup>
- 澳大利亚东南部<sup>1</sup>

<sup>1</sup> 此区域目前不支持用于 VM 的 Azure Monitor 的“运行状况”功能。

>[!NOTE]
>可从任何区域部署 Azure VM。 这些 VM 并不局限于 Log Analytics 工作区支持的区域。
>

如果没有工作区，可使用以下资源之一创建一个工作区：
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure 门户](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure 资源管理器](../../azure-monitor/platform/template-workspace-configuration.md)

还可以在 Azure 门户中为单个 Azure VM 或虚拟机规模集启用监视时创建工作区。

若要在 Log Analytics 工作区中设置使用 Azure Policy、Azure PowerShell 或 Azure 资源管理器模板的大规模方案：

* 安装 ServiceMap 和 InfrastructureInsights 解决方案。 可以使用提供的 Azure 资源管理器模板来完成此安装。 或者在“开始”选项卡上，选择“配置工作区”。
* 配置 Log Analytics 工作区以收集性能计数器。

若要配置大规模方案的工作区，请使用以下方法之一：

* 使用 [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace)。
* 在用于 VM 的 Azure Monitor 的[“策略覆盖范围”](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview)页上，选择“配置工作区”。 

### <a name="supported-operating-systems"></a>支持的操作系统

下表列出了用于 VM 的 Azure Monitor 支持的 Windows 和 Linux 操作系统。 本部分稍后将提供详细说明主要和次要 Linux OS 版本以及支持的内核版本的完整列表。

|OS 版本 |性能 |映射 |健康 |
|-----------|------------|-----|-------|
|Windows Server 2019 | X | X | X |
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |
|Windows Server 2012 R2 | X | X | X |
|Windows Server 2012 | X | X | |
|Windows Server 2008 R2 | X | X|  |
|Windows 10 1803 | X | X | |
|Windows 8.1 | X | X | |
|Windows 8 | X | X | |
|Windows 7 SP1 | X | X | |
|Red Hat Enterprise Linux (RHEL) 6、7| X | X| X |
|Ubuntu 14.04、16.04、18.04 | X | X | X |
|CentOS Linux 6、7 | X | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X | X |
|Debian 8、9.4 | X<sup>1</sup> | | X |

<sup>1</sup> 用于 VM 的 Azure Monitor 的“性能”功能仅在 Azure Monitor 中可用。 无法直接在 Azure VM 的左窗格中使用此功能。

>[!NOTE]
>用于 VM 的 Azure Monitor 的运行状况功能不支持 Azure VM 中的[嵌套虚拟化](../../virtual-machines/windows/nested-virtualization.md)。
>

>[!NOTE]
>在 Linux 操作系统中：
> - 仅默认版本和 SMP Linux 内核版本受支持。
> - 任何 Linux 发行版都不支持非标准内核版本（例如物理地址扩展 [PAE] 和 Xen）。 例如，不支持版本字符串为 *2.6.16.21-0.8-xen* 的系统。
> - 不支持自定义内核（包括标准内核的重新编译）。
> - 支持 CentOSPlus 内核。

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| OS 版本 | 内核版本 |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| OS 版本 | 内核版本 |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

#### <a name="centosplus"></a>CentOSPlus
| OS 版本 | 内核版本 |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

#### <a name="ubuntu-server"></a>Ubuntu 服务器

| OS 版本 | 内核版本 |
|:--|:--|
| Ubuntu 18.04 | 内核 4.15.\*<br>4.18* |
| Ubuntu 16.04.3 | 内核 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| OS 版本 | 内核版本
|:--|:--|
|12 SP2 | 4.4.* |
|12 SP3 | 4.4.* |
|12 SP4 | 4.4.* |
|12 SP4 | Azure 优化的内核 |

### <a name="the-microsoft-dependency-agent"></a>Microsoft Dependency Agent

用于 VM 的 Azure Monitor 的映射功能从 Microsoft 依赖项代理获取其数据。 依赖项代理依赖于使用 Log Analytics 代理连接到 Log Analytics。 因此，必须在系统上连同依赖项代理一起安装和配置 Log Analytics 代理。

无论是为单个 Azure VM 启用用于 VM 的 Azure Monitor，还是使用大规模部署方法，都需要在体验过程中使用 Azure VM 依赖项代理扩展来安装该代理。

>[!NOTE]
>本部分中所述的以下信息也适用于[服务映射解决方案](service-map.md)。  

在混合环境中，可以手动下载并安装依赖项代理。 如果 VM 托管在 Azure 外部，请使用自动部署方法。

下表描述了映射功能在混合环境中支持的连接源。

| 连接的源 | 支持 | 描述 |
|:--|:--|:--|
| Windows 代理 | 是 | 除[适用于 Windows 的 Log Analytics 代理](../../azure-monitor/platform/log-analytics-agent.md)以外，Windows 代理还需要依赖项代理。 有关详细信息，请参阅[支持的操作系统](#supported-operating-systems)。 |
| Linux 代理 | 是 | 除[适用于 Linux 的 Log Analytics 代理](../../azure-monitor/platform/log-analytics-agent.md)以外，Linux 代理还需要依赖项代理。 有关详细信息，请参阅[支持的操作系统](#supported-operating-systems)。 |
| System Center Operations Manager 管理组 | 否 | |

可从以下位置下载依赖项代理：

| 文件 | OS | Version | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.9.1 | FCF9C1D9B20AD414051B49EE79144E595CCC411EB6D444D6D5B5A7B1874DCDEC |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.9.1 | 1CB447EF30FC042FE7499A686638F3F9B4F449692FB9D80096820F8024BE4D7C |

## <a name="role-based-access-control"></a>基于角色的访问控制

若要启用和访问用于 VM 的 Azure Monitor 中的功能，必须拥有“Log Analytics 参与者”角色。 若要查看性能、运行状况和映射数据，必须拥有 Azure VM 的“监视读取者”角色。 必须为用于 VM 的 Azure Monitor 配置 Log Analytics 工作区。

有关如何控制对 Log Analytics 工作区的访问的详细信息，请参阅[管理工作区](../../azure-monitor/platform/manage-access.md)。

## <a name="how-to-enable-azure-monitor-for-vms-preview"></a>如何启用用于 VM 的 Azure Monitor（预览版）

使用下表中所述的方法之一启用用于 VM 的 Azure Monitor：

| 部署状态 | 方法 | 描述 |
|------------------|--------|-------------|
| 单个 Azure VM 或虚拟机规模集 | [从 VM 启用](vminsights-enable-single-vm.md) | 直接在 VM 或虚拟机规模集中选择“见解(预览版)”可以启用单个 Azure VM。 |
| 多个 Azure VM 或虚拟机规模集 | [通过 Azure Policy 启用](vminsights-enable-at-scale-policy.md) | 可以使用 Azure Policy 和可用的策略定义来启用多个 Azure VM。 |
| 多个 Azure VM 或虚拟机规模集 | [通过 Azure PowerShell 或 Azure 资源管理器模板启用](vminsights-enable-at-scale-powershell.md) | 通过使用 Azure PowerShell 或 Azure 资源管理器模板启用指定订阅或资源组中的多个 Azure VM 或虚拟机规模集。 |
| 混合云 | [为混合环境启用](vminsights-enable-hybrid-cloud.md) | 可以部署到数据中心或其他云环境中托管的 VM 或物理计算机。 |

## <a name="performance-counters-enabled"></a>已启用性能计数器 

用于 VM 的 Azure Monitor 将配置 Log Analytics 工作区，以收集它使用的性能计数器。 下面的表格列出了每隔 60 秒收集的对象和计数器。

### <a name="windows-performance-counters"></a>Windows 性能计数器

|对象名称 |计数器名称 |
|------------|-------------|
|LogicalDisk |可用空间百分比 |
|LogicalDisk |平均磁盘秒数/读取 |
|LogicalDisk |平均磁盘秒数/传输 |
|LogicalDisk |平均磁盘秒数/写入 |
|LogicalDisk |磁盘字节/秒 |
|LogicalDisk |磁盘读取字节数/秒 |
|LogicalDisk |磁盘读取数/秒 |
|LogicalDisk |磁盘传输数/秒 |
|LogicalDisk |磁盘写入字节数/秒 |
|LogicalDisk |磁盘写入数/秒 |
|LogicalDisk |可用 MB 数 |
|内存 |可用字节(MB) |
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

使用 Log Analytics 工作区启用和配置用于 VM 的 Azure Monitor 时, 会将管理包转发给向该工作区报告的所有 Windows 计算机。 如果已将[System Center Operations Manager 管理组](../../azure-monitor/platform/om-agents.md)与 Log Analytics 工作区集成, 则服务映射管理包将从管理组部署到向管理组报告的 Windows 计算机。  

管理包名为*microsoft.intelligencepacks.updateassessment. microsoft.intelligencepacks.applicationdependencymonitor*。 它被写入%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\management packs Pack \ folder。 管理包所使用的数据源是 %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll。

## <a name="diagnostic-and-usage-data"></a>诊断和使用数据

Microsoft 使用 Azure Monitor 服务自动收集使用情况和性能数据。 Microsoft 使用此数据来改进服务的质量、安全性和完整性。 

为了提供准确高效的故障排除功能，映射功能包含有关软件配置的信息。 这些数据提供操作系统和版本、IP 地址、DNS 名称及工作站名称等信息。 Microsoft 不收集姓名、地址或其他联系信息。

有关数据收集和使用的详细信息，请参阅 [Microsoft Online Services 隐私声明](https://go.microsoft.com/fwlink/?LinkId=512132)。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

为 VM 启用监视后，可以在用于 VM 的 Azure Monitor 中使用监视信息进行分析。

## <a name="next-steps"></a>后续步骤

若要了解如何使用运行状况功能，请参阅[查看用于 VM 的 Azure Monitor 的运行状况](vminsights-health.md)。 若要查看已发现的应用程序依赖项，请参阅[查看用于 VM 的 Azure Monitor 映射](vminsights-maps.md)。
