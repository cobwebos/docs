---
title: Azure Monitor 启用的 Vm （预览版） 概述 |Microsoft Docs
description: 本文介绍如何部署和配置 Azure Monitor 的 Vm 和所需的系统要求。
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
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: 4eb07b29f68c5f495bdbe5e23f5b226480b66661
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524065"
---
# <a name="enable-azure-monitor-for-vms-preview-overview"></a>Azure Monitor 启用的 Vm （预览版） 概述

本文概述的选项，可用于安装程序在 Azure Monitor 的 Vm 可以监视运行状况和性能，并发现 Azure 虚拟机和虚拟机规模集上运行的应用程序依赖关系，在本地 Vm，或中托管的 Vm另一个云环境。  

可使用以下方法之一启用用于 VM 的 Azure Monitor：

* 启用单个 Azure 虚拟机或虚拟机规模集通过选择**Insights （预览版）** 直接从 VM 或虚拟机规模集。
* 启用两个或多个 Azure 虚拟机和虚拟机规模集使用 Azure 策略。 通过此方法中，现有和新的 Vm 和规模集的必需的依赖项安装并正确配置。 不符合的 Vm 和规模集被报告，因此您可以决定是否启用它们，以及你想要修正它们。
* 使用 PowerShell 跨指定的订阅或资源组启用两个或更多 Azure VM，或启用虚拟机规模集。
* 能够监视虚拟机或物理计算机在公司网络或其他云环境中托管。

## <a name="prerequisites"></a>必备组件

在开始之前，请确保理解以下部分中的信息。

### <a name="log-analytics"></a>Log Analytics

适用于 Vm 的 azure Monitor 支持在以下区域中的 Log Analytics 工作区：

- 美国中西部
- 美国东部
- 加拿大中部<sup>1</sup>
- 英国南部<sup>1</sup>
- 西欧
- 东南亚<sup>1</sup>

<sup>1</sup> 此区域目前不支持用于 VM 的 Azure Monitor 的“运行状况”功能。

>[!NOTE]
>Azure 虚拟机可以从任何区域部署，并且不限于支持 Log Analytics 工作区的区域。
>

如果没有工作区，则可使用以下方法之一创建一个：
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure 门户](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure 资源管理器](../../azure-monitor/platform/template-workspace-configuration.md)

如果要启用单个 Azure VM 或虚拟机规模集在 Azure 门户中进行监视，您可以在此过程中创建工作区。

使用 Azure 策略、 Azure PowerShell 或 Azure 资源管理器模板在规模方案中，Log Analytics 工作区首先需要配置以下：

* 安装 ServiceMap 和 InfrastructureInsights 解决方案。 通过使用提供的 Azure 资源管理器模板，或可以完成此安装**配置工作区**上找到选项**开始**选项卡。
* 配置 Log Analytics 工作区以收集性能计数器。

若要配置你的工作区为大规模方案，可以配置它使用以下方法之一：

* [Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace)
* 从**配置工作区**Vm 的 Azure monitor 选项[策略覆盖范围](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview)页

### <a name="supported-operating-systems"></a>支持的操作系统

下表列出了用于 VM 的 Azure Monitor 支持的 Windows 和 Linux 操作系统。 本部分稍后将提供详细说明主要和次要 Linux OS 版本以及支持的内核版本的完整列表。

|OS 版本 |性能 |地图 |运行状况 |
|-----------|------------|-----|-------|
|Windows Server 2019 | X | X | |
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |
|Windows Server 2012 R2 | X | X | |
|Windows Server 2012 | X | X | |
|Windows Server 2008 R2 | X | X| |
|Red Hat Enterprise Linux (RHEL) 6 7| X | X| X |
|Ubuntu 14.04、 16.04、 18.04 | X | X | X |
|CentOS Linux 6、 7 | X | X | X |
|SUSE Linux Enterprise Server (SLES) 11、 12 | X | X | X |
|Debian 8、 9.4 | X<sup>1</sup> | | X |

<sup>1</sup> 用于 VM 的 Azure Monitor 的“性能”功能仅在 Azure Monitor 中可用。 直接从 Azure VM 的左窗格进行访问时不会提供此功能。

>[!NOTE]
>以下信息适用于 Linux 操作系统的支持：
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

### <a name="centosplus"></a>CentOSPlus
| OS 版本 | 内核版本 |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| OS 版本 | 内核版本 |
|:--|:--|
| Ubuntu 18.04 | 内核 4.15.* |
| Ubuntu 16.04.3 | 内核 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 Enterprise Server

| OS 版本 | 内核版本
|:--|:--|
|11 SP4 | 3.0.* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| OS 版本 | 内核版本
|:--|:--|
|12 SP2 | 4.4.* |
|12 SP3 | 4.4.* |

### <a name="the-microsoft-dependency-agent"></a>Microsoft Dependency Agent

用于 VM 的 Azure Monitor 映射功能从 Microsoft Dependency Agent 获取其数据。 依赖项代理依赖于使用 Log Analytics 代理连接到 Log Analytics。 因此，必须在系统上连同 Dependency Agent 一起安装和配置 Log Analytics 代理。

无论是为单个 Azure VM 启用用于 VM 的 Azure Monitor，还是使用大规模部署方法，都需要在体验过程中使用 Azure VM Dependency Agent 扩展来安装代理。

在混合环境中，您可以下载并在两种方式之一安装依赖关系代理： 手动或通过使用自动的部署方法的虚拟机托管 Azure 外部的。

下表描述了映射功能在混合环境中支持的连接源。

| 连接的源 | 支持 | 描述 |
|:--|:--|:--|
| Windows 代理 | 是 | 除[适用于 Windows 的 Log Analytics 代理](../../azure-monitor/platform/log-analytics-agent.md)外，Windows 代理还需要 Microsoft Dependency Agent。 有关操作系统版本的完整列表，请参阅[支持的操作系统](#supported-operating-systems)。 |
| Linux 代理 | 是 | 除[适用于 Linux 的 Log Analytics 代理](../../azure-monitor/platform/log-analytics-agent.md)外，Linux 代理还需要 Microsoft Dependency Agent。 有关操作系统版本的完整列表，请参阅[支持的操作系统](#supported-operating-systems)。 |
| System Center Operations Manager 管理组 | 否 | |

可从以下位置下载 Dependency Agent：

| 文件 | 操作系统 | 版本 | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.8.1 | 622C99924385CBF539988D759BCFDC9146BB157E7D577C997CDD2674E27E08DD |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.8.1 | 3037934A5D3FB7911D5840A9744AE9F980F87F620A7F7B407F05E276FE7AE4A8 |

## <a name="role-based-access-control"></a>基于角色的访问控制

若要在用于 VM 的 Azure Monitor 中启用和访问这些功能，需要获得以下访问角色：

- 若要启用它，必须具有*Log Analytics 参与者*角色。

- 若要查看性能、运行状况，以及映射数据，必须拥有 Azure VM 的*监视读取者*角色。 必须为用于 VM 的 Azure Monitor 配置 Log Analytics 工作区。

有关如何控制对 Log Analytics 工作区的访问的详细信息，请参阅[管理工作区](../../azure-monitor/platform/manage-access.md)。

## <a name="how-to-enable-azure-monitor-for-vms-preview"></a>如何为 Vm （预览版） 中启用 Azure Monitor

使用下表中所述的以下方法之一为 Vm 启用 Azure Monitor。

| 部署状态 | 方法 | 描述 |
|------------------|--------|-------------|
| 单个 Azure 虚拟机或虚拟机规模集 | [直接从虚拟机](vminsights-enable-single-vm.md) | 可以通过选择启用单个 Azure 虚拟机**Insights （预览版）** 直接从 VM 或虚拟机规模集。 |
| 多个 Azure 虚拟机或虚拟机规模集 | [Azure Policy](vminsights-enable-at-scale-policy.md) | 你可以启用多个 Azure Vm 使用 Azure 策略和可用的策略定义。 |
| 多个 Azure 虚拟机或虚拟机规模集 | [Azure PowerShell 或 Azure 资源管理器模板](vminsights-enable-at-scale-powershell.md) | 在指定的订阅或资源组，将使用 Azure PowerShell 或 Azure 资源管理器模板，可以启用多个 Azure 虚拟机或虚拟机规模集。 |
| 混合云 | [启用为混合环境](vminsights-enable-hybrid-cloud.md) | 可以在数据中心或其他云环境中部署虚拟机或物理计算机承载。 |

## <a name="performance-counters-enabled"></a>已启用性能计数器

Azure 监视的 Vm 配置要收集的性能计数器，它使用的 Log Analytics 工作区。 下表列出的对象和计数器收集每隔 60 秒。

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

## <a name="diagnostic-and-usage-data"></a>诊断和使用情况数据

Microsoft 使用 Azure Monitor 服务自动收集使用情况和性能数据。 Microsoft 使用此数据提供和改进服务的质量、安全性和完整性。 映射功能的数据包括有关软件配置的信息（例如操作系统和版本、IP 地址、DNS 名称和工作站名称），以提供准确高效的故障排除功能。 Microsoft 不收集姓名、地址或其他联系信息。

有关数据收集和使用的详细信息，请参阅 [Microsoft Online Services 隐私声明](https://go.microsoft.com/fwlink/?LinkId=512132)。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>后续步骤

现已为虚拟机启用监视，可在用于 VM 的 Azure Monitor 中使用此信息进行分析。 若要了解如何使用运行状况功能，请参阅[查看用于 VM 的 Azure Monitor 的运行状况](vminsights-health.md)。 若要查看已发现的应用程序依赖项，请参阅[查看用于 VM 的 Azure Monitor 映射](vminsights-maps.md)。
