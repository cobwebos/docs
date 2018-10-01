---
title: 载入用于 VM 的 Azure Monitor | Microsoft Docs
description: 本文介绍如何载入和配置用于 VM 的 Azure Monitor，以便可以开始了解分布式应用程序的性能，以及识别了哪些运行状况问题。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: magoedte
ms.openlocfilehash: 2f0568064eed556429675ffb34c84d588ac670d5
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2018
ms.locfileid: "47064350"
---
# <a name="how-to-onboard-the-azure-monitor-for-vms"></a>如何载入用于 VM 的 Azure Monitor 
本文介绍如何设置用于 VM 的 Azure Monitor，以监视 Azure 虚拟机的操作系统运行状况，以及发现和映射这些虚拟机上托管的应用程序依赖项。  

可以使用以下方法之一来启用用于 VM 的 Azure Monitor，本文稍后会提供每种方法的用法详细信息。  

* 直接在 VM 中选择“见解(预览版)”来为单个 Azure 虚拟机启用此功能。
* 使用 Azure Policy 为多个 Azure VM 启用此功能，以确保评估的现有 VM 和新 VM 上安装了所需的依赖项，并已经过正确的配置。  将会报告不合规的 VM，以便根据不合规的 VM 和补救方式做出决策。  
* 使用 PowerShell 为指定的订阅或资源组中的多个 Azure VM 或虚拟机规模集启用此功能。

## <a name="prerequisites"></a>先决条件
在开始之前，请确保按以下小节中所述做好准备。

### <a name="log-analytics"></a>Log Analytics 

目前支持以下区域中的 Log Analytics 工作区：

  - 美国中西部  
  - 美国东部  
  - 西欧  
  - 东南亚<sup>1</sup>  

<sup>1</sup> 此区域目前不支持用于 VM 的 Azure Monitor 的“运行状况”功能   

如果没有工作区，可以通过 [Azure 资源管理器](../log-analytics/log-analytics-template-workspace-configuration.md)、[PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json) 或 [Azure 门户](../log-analytics/log-analytics-quick-create-workspace.md)创建工作区。  

只有 Log Analytics 参与者角色的成员才能启用该解决方案。 有关如何控制对 Log Analytics 工作区的访问的详细信息，请参阅[管理工作区](../log-analytics/log-analytics-manage-access.md)。

[!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

### <a name="supported-operating-systems"></a>支持的操作系统

下表列出了用于 VM 的 Azure Monitor 支持的 Windows 和 Linux 操作系统。  本部分稍后将提供详细说明主要和次要 Linux OS 版本以及支持的内核版本的完整列表。

|OS 版本 |性能 |地图 |运行状况 |  
|-----------|------------|-----|-------|  
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |  
|Windows Server 2012 R2 | X | X | |  
|Windows Server 2012 | X | X | |  
|Windows Server 2008 R2 | X | X| |  
|RHEL 7、6| X | X| X |  
|Ubuntu 18.04、16.04、14.04 | X | X | X |  
|Cent OS Linux 7、6 | X | X | X |  
|SLES 12 | X | X | X |  
|Oracle Linux 7 | X<sup>1</sup> | | X |  
|Oracle Linux 6 | X | X | X |  
|Debian 9.4、8 | X<sup>1</sup> | | X | 

<sup>1</sup> 用于 VM 的 Azure Monitor 的“性能”功能只会在 Azure Monitor 中使用，直接从 Azure VM 的左窗格中访问时不会提供此功能。  

>[!NOTE]
>以下信息适用于 Linux 操作系统的支持：  
> - 仅默认版本和 SMP Linux 内核版本受支持。  
> - 任何 Linux 分发版都不支持非标准内核版本（例如 PAE 和 Xen）。 例如，不支持版本字符串为“2.6.16.21-0.8-xen”的系统。  
> - 不支持自定义内核（包括标准内核的重新编译）。  
> - 不支持 CentOSPlus 内核。  


#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| OS 版本 | 内核版本 |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| OS 版本 | 内核版本 |
|:--|:--|
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6.8 | 2.6.32-642 |
| 6.9 | 2.6.32-696 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| OS 版本 | 内核版本 |
|:--|:--|
| Ubuntu 18.04 | 内核 4.15.* |
| Ubuntu 16.04.3 | 内核 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="oracle-enterprise-linux-6-with-unbreakable-enterprise-kernel"></a>具有 Unbreakable Enterprise Kernel 的 Oracle Enterprise Linux 6
| OS 版本 | 内核版本
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-enterprise-linux-5-with-unbreakable-enterprise-kernel"></a>具有 Unbreakable Enterprise Kernel 的 Oracle Enterprise Linux 5

| OS 版本 | 内核版本
|:--|:--|
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| OS 版本 | 内核版本
|:--|:--|
|12 SP2 | 4.4.* |
|12 SP3 | 4.4.* |

### <a name="hybrid-environment-connected-sources"></a>混合环境中的连接源
用于 VM 的 Azure Monitor 映射从 Microsoft 依赖项代理获取其数据。 依赖项代理依赖于使用 Log Analytics 代理连接到 Log Analytics。 这意味着，必须在系统上连同依赖项代理一起安装并配置 Log Analytics 代理。  下表描述了映射功能在混合环境中支持的连接源。

| 连接的源 | 支持 | Description |
|:--|:--|:--|
| Windows 代理 | 是 | 除[适用于 Windows 的 Log Analytics 代理](../log-analytics/log-analytics-concept-hybrid.md)外，Windows 代理还需要 Microsoft Dependency Agent。 有关完整的操作系统版本列表，请参阅[支持的操作系统](#supported-operating-systems)。 |
| Linux 代理 | 是 | 除[适用于 Linux 的 Log Analytics 代理](../log-analytics/log-analytics-concept-hybrid.md)外，Linux 代理还需要 Microsoft Dependency Agent。 有关完整的操作系统版本列表，请参阅[支持的操作系统](#supported-operating-systems)。 |
| System Center Operations Manager 管理组 | 否 | |  

在 Windows 中，System Center Operations Manager 和 Log Analytics 都可使用 Microsoft Monitoring Agent (MMA) 收集和发送监视数据。 System Center Operations Manager 和 Log Analytics 提供不同的现成代理版本。 这些版本每个都可向 System Center Operations Manager 报告，或向 Log Analytics 报告，也可同时向两者报告。  

在 Linux 上，适用于 Linux 的 Log Analytics 代理收集监视数据并将其发送到 Log Analytics。   

如果 Windows 或 Linux 计算机无法直接连接到服务，则需要将 Log Analytics 代理配置为使用 OMS 网关连接到 Log Analytics。 有关如何部署和配置 OMS 网关的详细信息，请参阅[使用 OMS 网关连接无法访问 Internet 的计算机](../log-analytics/log-analytics-oms-gateway.md)。  

可从以下位置下载依赖项代理。

| 文件 | 操作系统 | 版本 | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.1 | 55030ABF553693D8B5112569FB2F97D7C54B66E9990014FC8CC43EFB70DE56C6 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.1 | 43C75EF0D34471A0CBCE5E396FFEEF4329C9B5517266108FA5D6131A353D29FE |

## <a name="diagnostic-and-usage-data"></a>诊断和使用情况数据
Microsoft 使用 Azure Monitor 服务自动收集使用情况和性能数据。 Microsoft 使用此数据提供和改进服务的质量、安全性和完整性。 映射功能的数据包括有关软件配置的信息（例如操作系统和版本）、IP 地址、DNS 名称和工作站名称，以提供准确高效的故障排除功能。 Microsoft 不收集姓名、地址或其他联系信息。

有关数据收集和使用的详细信息，请参阅 [Microsoft Online Services 隐私声明](https://go.microsoft.com/fwlink/?LinkId=512132)。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="performance-counters-enabled"></a>已启用性能计数器
用于 VM 的 Azure Monitor 将配置 Log Analytics 工作区，以收集解决方案使用的性能计数器。  下表列出了解决方案配置的、每隔 60 秒收集一次的对象和计数器。

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

## <a name="enable-from-the-azure-portal"></a>从 Azure 门户启用
若要在 Azure 门户中启用对 Azure VM 的监视，请执行以下操作：

1. 在 Azure 门户中，选择“虚拟机”。 
2. 从列表中选择一个虚拟机。 
3. 在“VM”页上的“监视”部分，选择“见解(预览版)”。
4. 在“见解(预览版)”页上，选择“立即试用”。

    ![为 VM 启用用于 VM 的 Azure Monitor](./media/monitoring-vminsights-onboard/enable-vminsights-vm-portal-01.png)

5. 在“Azure Monitor Insights 载入”页上，如果现有的 Log Analytics 工作区与群集在同一订阅中，请从下拉列表中选择该工作区。  列表中预先选择了虚拟机在订阅中部署到的默认工作区和位置。 

    >[!NOTE]
    >若要创建新的 Log Analytics 工作区以存储来自 VM 的监视数据，请在前面列出的某个受支持区域中按照[创建 Log Analytics 工作区](../log-analytics/log-analytics-quick-create-workspace.md)中的说明进行操作。   

启用监视后，可能需要在大约 10 分钟之后才能查看虚拟机的运行状况指标。 

![启用用于 VM 的 Azure Monitor 来监视部署处理](./media/monitoring-vminsights-onboard/onboard-vminsights-vm-portal-status.png)

## <a name="enable-using-azure-policy"></a>使用 Azure Policy 启用
若要为多个 Azure VM 启用该解决方案以确保持续合规并使新预配的 VM 自动起作用，我们建议使用 [Azure Policy](../azure-policy/azure-policy-introduction.md)。  结合提供的策略使用 Azure Policy 可为新的 VM 提供以下优势：

* 为定义的范围中的每个 VM 启用用于 VM 的 Azure Monitor
* 部署 Log Analytics 代理 
* 部署依赖项代理以发现应用程序依赖项并在映射图中显示
* 审核 Azure VM OS 映像是否在策略定义中预定义的列表内  
* 审核 Azure VM 是否将日志记录到非指定的工作区
* 报告合规性结果 
* 支持针对不合规的 VM 进行补救

若要为租户激活此功能，需要：

- 使用此处所列的步骤配置 Log Analytics 工作区
- 将计划定义导入到租户（在管理组或订阅级别）
- 将策略分配到所需的范围
- 检查合规性结果

### <a name="add-the-policies-and-initiative-to-your-subscription"></a>将策略和计划添加到订阅
若要使用策略，可以使用 Azure PowerShell 库中提供的 PowerShell 脚本[Add-VMInsightsPolicy.ps1](https://www.powershellgallery.com/packages/Add-VMInsightsPolicy/1.2) 完成此任务。 该脚本会将策略和计划添加到订阅。  执行以下步骤在订阅中配置 Azure Policy。 

1. 将 PowerShell 脚本下载到本地文件系统。

2. 在文件夹中使用以下 PowerShell 命令来添加策略。 该脚本支持以下可选参数： 

    ```powershell
    -UseLocalPolicies [<SwitchParameter>]
      <Optional> Load the policies from a local folder instead of https://raw.githubusercontent.com/dougbrad/OnBoardVMInsights/Policy/Policy/

    -SubscriptionId <String>
      <Optional> SubscriptionId to add the Policies/Initiatives to
    -ManagementGroupId <String>
      <Optional> Management Group Id to add the Policies/Initiatives to

    -Approve [<SwitchParameter>]
      <Optional> Gives the approval to add the Policies/Initiatives without any prompt
    ```  

    >[!NOTE]
    >注意：若要将计划/策略分配到多个订阅，则必须将定义存储在策略所要分配到的订阅所在的管理组中。 因此必须使用 -ManagementGroupID 参数。
    >
   
    不使用参数的示例：`.\Add-VMInsightsPolicy.ps1`

### <a name="create-a-policy-assignment"></a>创建策略分配
运行 `Add-VMInsightsPolicy.ps1` PowerShell 脚本后，将添加以下计划和策略：

* **部署适用于 Windows VM 的 Log Analytics 代理 - 预览版**
* **部署适用于 Linux VM 的 Log Analytics 代理 - 预览版**
* **部署适用于 Windows VM 的依赖项代理 - 预览版**
* **部署适用于 Linux VM 的依赖项代理 - 预览版**
* **审核 Log Analytics 代理部署 - 未列出的 VM 映像 (OS) - 预览版**
* **审核依赖项代理部署 - 未列出的 VM 映像 (OS) - 预览版**

将添加以下计划参数：

- **Log Analytice 工作区**（如果使用 PowerShell 或 CLI 应用分配，则必须提供工作区的 ResourceID）

    对于在审核策略中发现不合规的 VM（“不在 OS 范围内的 VM”），部署策略的条件仅包含从已知 Azure VM 映像部署的 VM。 请查看文档来确定 VM OS 是否受支持。  如果不受支持，则需要复制部署策略并更新/修改它，使映像在范围以内。

将添加以下独立的可选策略：

- **为不匹配的 Log Analytics 工作区配置 VM - 预览版**

    此策略可用于识别已使用 [Log Analytics VM 扩展](../virtual-machines/extensions/oms-windows.md)配置的、但使用非预期工作区（预期工作区由策略分配指定）配置的 VM。 这会采用 WorkspaceID 的参数。

在此初始版本中，只能从 Azure 门户创建策略分配。 若要了解如何完成这些步骤，请参阅[从 Azure 门户创建策略分配](../azure-policy/assign-policy-definition.md)。

## <a name="enable-with-powershell"></a>使用 PowerShell 启用
若要为多个 VM 或 VM 规模集启用用于 VM 的 Azure Monitor，可以使用 Azure PowerShell 库中提供的 PowerShell 脚本 [Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0) 完成此任务。  此脚本将循环访问订阅中、*ResourceGroup* 指定的每个限定资源组中的每个虚拟机和虚拟机规模集，或循环访问 *Name* 指定的单个 VM 或规模集。  对于每个 VM 或 VM 规模集，该脚本将验证是否已安装 VM 扩展，如果未安装，则尝试重新安装。  否则，它会继续安装 Log Analytics 和依赖项代理 VM 扩展。   

此脚本需要 Azure PowerShell 模块 5.7.0 或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzureRmAccount` 以创建与 Azure 的连接。

若要获取有关该脚本的帮助，可以运行 `Get-Help` 来获取参数详细信息列表和示例用法。   

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and Dependency Agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or re-configures following on VM's and VM Scale Sets:
    - Log Analytics VM Extension configured to supplied Log Analytics Workspace
    - Dependency Agent VM Extension

    Can be applied to:
    - Subscription
    - Resource Group in a Subscription
    - Specific VM/VM Scale Set
    - Compliance results of a policy for a VM or VM Extension

    Script will show you list of VM's/VM Scale Sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed will not install again.
    Use -ReInstall switch if you need to for example update the workspace.

    Use -WhatIf if you would like to see what would happen in terms of installs, what workspace configured to, and status of the extension.


PARAMETERS
    -WorkspaceId <String>
        Log Analytics WorkspaceID (GUID) for the data to be sent to

    -WorkspaceKey <String>
        Log Analytics Workspace primary or secondary key

    -SubscriptionId <String>
        SubscriptionId for the VMs/VM Scale Sets
        If using PolicyAssignmentName parameter, subscription that VM's are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong to

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VM's to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VM's/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Suported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
        For Health supported is: "East US","eastus","West Central US","westcentralus"

    -WhatIf [<SwitchParameter>]
        <Optional> See what would happen in terms of installs.
        If extension is already installed will show what workspace is currently configured, and status of the VM extension

    -Confirm [<SwitchParameter>]
        <Optional> Confirm every action

    <CommonParameters>
        This cmdlet supports the common parameters: Verbose, Debug,
        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
        OutBuffer, PipelineVariable, and OutVariable. For more information, see
        about_CommonParameters (https:/go.microsoft.com/fwlink/?LinkID=113216).

    -------------------------- EXAMPLE 1 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup>

    Install for all VM's in a Resource Group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to ReInstall extensions even if already installed, for example to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source, and to ReInstall (move to a new workspace)
```

以下示例演示如何在文件夹中使用 PowerShell 命令来启用用于 VM 的 Azure Monitor，并了解预期的输出：

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VM's or VM ScaleSets matching criteria specified

VM's or VM ScaleSets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency Agent extensions on above 2 VM's or VM Scale Sets.
VM's in a non-running state will be skipped.
Extension will not be re-installed if already installed. Use -ReInstall if desired, for example to update workspace

Confirm
Continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

db-ws-1 : Deploying DependencyAgentWindows with name DAExtension
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Deploying DependencyAgentWindows with name DAExtension
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Summary:

Already Onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="enable-for-hybrid-environment"></a>为混合环境启用
本部分介绍如何载入数据中心或其他云环境中托管的、受用于 VM 的 Azure Monitor 监视的虚拟机或物理计算机。  

用于 VM 的 Azure Monitor 映射依赖项代理本身不传输任何数据，它不需要对防火墙或端口做出任何更改。 映射中的数据始终由 Log Analytics 代理直接传输到 Azure Monitor 服务；如果 IT 安全策略不允许网络中的计算机连接到 Internet，则通过 [OMS 网关](../log-analytics/log-analytics-oms-gateway.md)传输这些数据。

查看 [Log Analytics Linux 和 Windows 代理](../log-analytics/log-analytics-concept-hybrid.md)的要求与部署方法。

步骤摘要：

1. 安装适用于 Windows 或 Linux 的 Log Analytics 代理
2. 安装用于 VM 的 Azure Monitor 映射依赖项代理
3. 启用性能计数器收集
4. 载入用于 VM 的 Azure Monitor

### <a name="install-the-dependency-agent-on-windows"></a>在 Windows 上安装依赖项代理 
可通过运行 `InstallDependencyAgent-Windows.exe` 在 Windows 计算机上手动安装 Dependency Agent。 如果在没有任何选项的情况下运行此可执行文件，它将启动一个安装向导，以交互方式指导用户安装。  

>[!NOTE]
>需要管理员特权才能安装或卸载代理。

下表突出显示了在命令行中安装代理时支持的特定参数。  

| 参数 | Description |
|:--|:--|
| /? | 返回命令行选项的列表。 |
| /S | 执行无需用户交互的静默安装。 |

例如，若要使用 `/?` 参数运行安装程序，请键入 `InstallDependencyAgent-Windows.exe /?`

默认情况下，Windows 依赖项代理的文件安装在 `C:\Program Files\Microsoft Dependency Agent` 中。  如果完成安装后依赖项代理无法启动，请检查日志以获取详细的错误信息。 日志目录为 `%Programfiles%\Microsoft Dependency Agent\logs`。 

### <a name="install-the-dependency-agent-on-linux"></a>在 Linux 上安装 Dependency Agent
通过 `InstallDependencyAgent-Linux64.bin`（包含自解压缩二进制文件的 Shell 脚本）在 Linux 服务器上安装依赖项代理。 可使用 `sh` 来运行该文件或将执行权限添加到文件本身。

>[!NOTE]
> 需要根目录访问才能安装或配置代理。
> 

| 参数 | Description |
|:--|:--|
| -help | 获取命令行选项列表。 |
| -s | 执行无提示安装，无用户提示。 |
| --check | 检查权限和操作系统，但不安装代理。 |

例如，若要使用 `-help` 参数运行安装程序，请键入 `InstallDependencyAgent-Linux64.bin -help`。

运行以下命令将 Linux 依赖项代理安装为根：`sh InstallDependencyAgent-Linux64.bin`
    
如果 Dependency Agent 无法启动，请检查日志以获取详细的错误信息。 在 Linux 代理上，日志目录为 `/var/opt/microsoft/dependency-agent/log`。

Dependency Agent 的文件放置在以下目录中：

| 文件 | 位置 |
|:--|:--|
| 核心文件 | /opt/microsoft/dependency-agent |
| 日志文件 | /var/opt/microsoft/dependency-agent/log |
| 配置文件 | /etc/opt/microsoft/dependency-agent/config |
| 服务可执行文件 | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| 二进制存储文件 | /var/opt/microsoft/dependency-agent/storage |

### <a name="enable-performance-counters"></a>启用性能计数器
如果解决方案引用的 Log Analytics 工作区尚未配置为收集解决方案所需的性能计数器，则需要启用性能计数器。 可根据[此文](../log-analytics/log-analytics-data-sources-performance-counters.md)所述手动启用，或者下载并运行 [Azure Powershell 库](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)中提供的某个 PowerShell 脚本。
 
### <a name="onboard-azure-monitor-for-vms"></a>载入用于 VM 的 Azure Monitor
此方法包含一个 JSON 模板，其中指定了用于在 Log Analytics 工作区中启用解决方案组件的配置。  

如果不熟悉使用模板部署资源的概念，请参阅：
* [使用 Resource Manager 模板和 Azure PowerShell 部署资源](../azure-resource-manager/resource-group-template-deploy.md)
* [使用资源管理器模板和 Azure CLI 部署资源](../azure-resource-manager/resource-group-template-deploy-cli.md) 

如果选择使用 Azure CLI，首先需要在本地安装和使用 CLI。 必须运行 Azure CLI 2.0.27 版或更高版本。 若要确定版本，请运行 `az --version`。 如果需要安装或升级 Azure CLI，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 

#### <a name="create-and-execute-a-template"></a>创建和执行模板

1. 将以下 JSON 语法复制并粘贴到文件中：

    ```json
    {

    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "WorkspaceName": {
            "type": "string"
        },
        "WorkspaceLocation": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-03-15-preview",
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('WorkspaceName')]",
            "location": "[parameters('WorkspaceLocation')]",
            "resources": [
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[parameters('WorkspaceLocation')]",
                    "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    },

                    "plan": {
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "publisher": "Microsoft",
                        "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                        "promotionCode": ""
                    }
                },
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[parameters('WorkspaceLocation')]",
                    "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    },
                    "plan": {
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "publisher": "Microsoft",
                        "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                        "promotionCode": ""
                    }
                }
            ]
        }
    ]
    ```

2. 使用 **installsolutionsforvminsights.json** 文件名将此文件保存到某个本地文件夹中。
3. 编辑 **WorkspaceName**、**ResourceGroupName** 和 **WorkspaceLocation** 的值。  **WorkspaceName** 的值是 Log Analytics 工作区的完整资源 ID（其中包括工作区名称），**WorkspaceLocation** 的值是定义工作区的区域。
4. 现在，可以使用以下 PowerShell 命令部署此模板：

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    配置更改可能需要几分钟才能完成。 完成后，系统会显示包含结果的消息，如下所示：

    ```powershell
    provisioningState       : Succeeded
    ```
启用监视后，可能需要在大约 10 分钟之后才能查看混合计算机的运行状况和指标。 

## <a name="next-steps"></a>后续步骤

为虚拟机启用监视后，可在用于 VM 的 Azure Monitor 中使用此信息进行分析。  若要了解如何使用“运行状况”功能，请参阅[查看用于 VM 的 Azure Monitor 运行状况](monitoring-vminsights-health.md)；若要查看已发现的应用程序依赖项，请参阅[查看用于 VM 的 Azure Monitor 映射](monitoring-vminsights-maps.md)。  