---
title: 启用用于 VM 的 Azure Monitor 概述
description: 了解如何部署和配置用于 VM 的 Azure Monitor。 了解系统要求。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: 072f8fd44fa45648afd15cb40cba26bb427c7b56
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86539612"
---
# <a name="enable-azure-monitor-for-vms-overview"></a>启用用于 VM 的 Azure Monitor 概述

本文概述了可用于启用用于 VM 的 Azure Monitor 来监视以下各项的运行状况和性能的选项：

- Azure 虚拟机 
- Azure 虚拟机规模集
- 与 Azure Arc 连接的混合虚拟机
- 本地虚拟机
- 托管在其他云环境中的虚拟机。  

若要设置用于 VM 的 Azure Monitor：

* 通过在 Azure 门户中直接从菜单中选择 "**见解**"，启用单个 azure VM、azure VMSS 或 azure Arc 计算机。
* 使用 Azure 策略启用多个 Azure Vm、Azure VMSS 或 Azure Arc 计算机。 此方法可确保在现有和新的 VM 与规模集上安装并正确配置所需的依赖项。 系统会报告不合规的 VM 和规模集，因此你可以决定是否要启用和修正它们。
* 使用 PowerShell 在指定的订阅或资源组中启用多个 Azure Vm、Azure Arc Vm、Azure VMSS 或 Azure Arc 计算机。
* 启用用于 VM 的 Azure Monitor，以监视企业网络或其他云环境中托管的 VM 或物理计算机。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保理解以下部分中的信息。 

>[!NOTE]
>本部分中所述的以下信息也适用于[服务映射解决方案](service-map.md)。  

### <a name="log-analytics"></a>Log Analytics

用于 VM 的 Azure Monitor 支持以下区域中的 Log Analytics 工作区：

- 美国中西部
- 美国西部
- 美国西部 2
- South Central US
- East US
- 美国东部 2
- 美国中部
- 美国中北部
- US Gov Az
- US Gov Va
- 加拿大中部
- 英国南部
- 北欧
- 西欧
- 东亚
- 东南亚
- 印度中部
- 日本东部
- 澳大利亚东部
- 澳大利亚东南部

>[!NOTE]
>可以在任何区域监视 Azure Vm。 Vm 本身并不限于 Log Analytics 工作区所支持的区域。
>

如果没有 Log Analytics 工作区，则可以使用以下资源之一创建一个工作区：
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../platform/powershell-workspace-configuration.md)
* [Azure 门户](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

还可以在 Azure 门户中为单个 Azure VM 或虚拟机规模集启用监视时创建工作区。

若要设置使用 Azure 策略、Azure PowerShell 或 Azure 资源管理器模板的大规模方案，必须安装*VMInsights*解决方案。 可以通过以下方法之一执行此操作：

* 使用[Azure PowerShell](vminsights-enable-at-scale-powershell.md#set-up-a-log-analytics-workspace)。
* 在用于 VM 的 Azure Monitor 的[“策略覆盖范围”](vminsights-enable-at-scale-policy.md#manage-policy-coverage-feature-overview)页上，选择“配置工作区”。******** 

### <a name="azure-arc-machines"></a>Azure Arc 计算机
用于 VM 的 Azure Monitor 可用于可用 Arc 扩展服务的区域中启用了 Azure Arc 的服务器。 用户必须运行版本0.9 或更高版本的 Arc 代理，才能启用启用了 Arc 的服务器上的用于 VM 的 Azure Monitor。

### <a name="supported-operating-systems"></a>支持的操作系统

下表列出了用于 VM 的 Azure Monitor 支持的 Windows 和 Linux 操作系统。 本部分稍后将提供详细说明主要和次要 Linux OS 版本以及支持的内核版本的完整列表。

|OS 版本 |性能 |Maps |
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
|Red Hat Enterprise Linux (RHEL) 6、7| X | X| 
|Ubuntu 18.04、16.04 | X | X |
|CentOS Linux 7、6 | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X |
|Debian 9.4、8 | X<sup>1</sup> | |

<sup>1</sup> 用于 VM 的 Azure Monitor 的“性能”功能仅在 Azure Monitor 中可用。 无法直接在 Azure VM 的左窗格中使用此功能。

>[!NOTE]
>在 Linux 操作系统中：
> - 仅默认版本和 SMP Linux 内核版本受支持。
> - 任何 Linux 发行版都不支持非标准内核版本（例如物理地址扩展 [PAE] 和 Xen）。 例如，不支持版本字符串为 *2.6.16.21-0.8-xen* 的系统。
> - 不支持自定义内核（包括标准内核的重新编译）。
> - 支持 CentOSPlus 内核。
> - 必须为 Spectre 漏洞修补 Linux 内核。 有关更多详细信息，请咨询 Linux 发行版供应商。

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
| 18.04 | 5.3.0-1020<br>5.0（包括 Azure 优化内核）<br>4.18* <br> 4.15* |
| 16.04.3 | 4.15.* |
| 16.04 | 4.13.\*<br>4.11.\*<br>4.10.\*<br>4.8.\*<br>4.4.\* |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| OS 版本 | 内核版本 |
|:--|:--|
|12 SP4 | 4.12.*（包括 Azure 优化内核） |
|12 SP3 | 4.4.* |
|12 SP2 | 4.4.* |

#### <a name="debian"></a>Debian 

| OS 版本 | 内核版本 |
|:--|:--|
| 9 | 4.9 | 

### <a name="the-microsoft-dependency-agent"></a>Microsoft Dependency Agent

用于 VM 的 Azure Monitor 的映射功能从 Microsoft 依赖项代理获取其数据。 依赖项代理依赖于使用 Log Analytics 代理连接到 Log Analytics。 因此，必须在系统上连同依赖项代理一起安装和配置 Log Analytics 代理。

无论是为单个 Azure VM 启用用于 VM 的 Azure Monitor 还是使用大规模部署方法，都可以使用适用于[Windows](../../virtual-machines/extensions/agent-dependency-windows.md)或[LINUX](../../virtual-machines/extensions/agent-dependency-linux.md)的 Azure VM 依赖关系代理扩展来安装代理作为体验的一部分。

>[!NOTE]
>本部分中所述的以下信息也适用于[服务映射解决方案](service-map.md)。  

在混合环境中，可以手动或使用自动方法下载并安装 Dependency Agent。

下表描述了映射功能在混合环境中支持的连接源。

| 连接的源 | 支持 | 说明 |
|:--|:--|:--|
| Windows 代理 | 是 | 除[适用于 Windows 的 Log Analytics 代理](../../azure-monitor/platform/log-analytics-agent.md)以外，Windows 代理还需要依赖项代理。 有关详细信息，请参阅[支持的操作系统](#supported-operating-systems)。 |
| Linux 代理 | 是 | 除[适用于 Linux 的 Log Analytics 代理](../../azure-monitor/platform/log-analytics-agent.md)以外，Linux 代理还需要依赖项代理。 有关详细信息，请参阅[支持的操作系统](#supported-operating-systems)。 |
| System Center Operations Manager 管理组 | 否 | |

可从以下位置下载依赖项代理：

| 文件 | (OS) | Version | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.10.4.10090 | B4E1FF9C1E5CD254AA709AEF9723A81F04EC0763C327567C582CE99C0C5A0BAE  |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.10.4.10090 | A56E310D297CE3B343AE8F4A6F72980F1C3173862D6169F1C713C2CA09660A9F |

## <a name="role-based-access-control"></a>基于角色的访问控制

若要启用和访问用于 VM 的 Azure Monitor 中的功能，必须拥有“Log Analytics 参与者”角色。** 若要查看性能、运行状况和映射数据，你必须具有 Azure VM 的 "*监视读取*者" 角色。 必须为用于 VM 的 Azure Monitor 配置 Log Analytics 工作区。

有关如何控制对 Log Analytics 工作区的访问的详细信息，请参阅[管理工作区](../../azure-monitor/platform/manage-access.md)。

## <a name="how-to-enable-azure-monitor-for-vms"></a>如何启用用于 VM 的 Azure Monitor

使用下表中所述的方法之一启用用于 VM 的 Azure Monitor：

| 部署状态 | 方法 | 说明 |
|------------------|--------|-------------|
| 单个 Azure VM、Azure VMSS 或 Azure Arc 计算机 | [从门户启用](vminsights-enable-single-vm.md) | 直接从 Azure 门户的菜单中选择 "**见解**"。 |
| 多个 Azure VM、Azure VMSS 或 Azure Arc 计算机 | [通过 Azure Policy 启用](vminsights-enable-at-scale-policy.md) | 创建 VM 或 VMSS 时，使用 Azure 策略自动启用。 |
| | [通过 Azure PowerShell 或 Azure 资源管理器模板启用](vminsights-enable-at-scale-powershell.md) | 使用 Azure PowerShell 或 Azure 资源管理器模板，可以通过指定的订阅或资源组启用多个 Azure VM、Azure Arc VM 或 Azure VMSS。 |
| 混合云 | [为混合环境启用](vminsights-enable-hybrid-cloud.md) | 部署到你的数据中心或其他云环境中托管的 Vm 或物理计算机。 |

## <a name="management-packs"></a>管理包

使用 Log Analytics 工作区启用和配置用于 VM 的 Azure Monitor 时，会将管理包转发到向该工作区报告的所有 Windows 计算机。 如果已将[System Center Operations Manager 管理组](../../azure-monitor/platform/om-agents.md)与 Log Analytics 工作区集成，则服务映射管理包将从管理组部署到向管理组报告的 Windows 计算机。  

管理包名为*microsoft.intelligencepacks.updateassessment. microsoft.intelligencepacks.applicationdependencymonitor*。 它将写入 `%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\` 文件夹。 管理包使用的数据源为 `%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll`。

## <a name="diagnostic-and-usage-data"></a>诊断和使用情况数据

Microsoft 使用 Azure Monitor 服务自动收集使用情况和性能数据。 Microsoft 使用此数据来改进服务的质量、安全性和完整性。 

为了提供准确高效的故障排除功能，映射功能包含有关软件配置的信息。 这些数据提供操作系统和版本、IP 地址、DNS 名称及工作站名称等信息。 Microsoft 不收集姓名、地址或其他联系信息。

有关数据收集和使用的详细信息，请参阅 [Microsoft Online Services 隐私声明](https://go.microsoft.com/fwlink/?LinkId=512132)。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

为 VM 启用监视后，可以在用于 VM 的 Azure Monitor 中使用监视信息进行分析。

## <a name="next-steps"></a>后续步骤

若要了解如何使用性能监视功能，请参阅[查看用于 VM 的 Azure Monitor 性能](vminsights-performance.md)。 若要查看已发现的应用程序依赖项，请参阅[查看用于 VM 的 Azure Monitor 映射](vminsights-maps.md)。
