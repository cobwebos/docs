---
title: 部署用于 VM 的 Azure Monitor（预览版）| Microsoft Docs
description: 本文介绍如何部署和配置用于 VM 的 Azure Monitor，以便可以开始了解分布式应用程序的性能，以及确定的运行状况问题。
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
ms.date: 12/07/2018
ms.author: magoedte
ms.openlocfilehash: 741288bd1a927b12705b3b31c5a1c60d6b94db5b
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2018
ms.locfileid: "53194243"
---
# <a name="deploy-azure-monitor-for-vms-preview"></a>部署用于 VM 的 Azure Monitor（预览版）
本文介绍如何设置用于 VM 的 Azure Monitor。 该服务监视 Azure 虚拟机 (VM)、虚拟机规模集和环境中的虚拟机的操作系统运行状况。 此监视包括发现和映射可能在其上托管的应用程序依赖关系。 

可使用以下方法之一启用用于 VM 的 Azure Monitor：  

* 直接在 VM 中选择“见解(预览)”来启用单个 Azure 虚拟机。
* 使用 Azure Policy 启用两个或更多 Azure VM。 通过此方法，可安装并正确配置现有 VM 和新 VM 的所需依赖项。 系统会报告不合规的 VM，以便你可以决定是否启用它们以及如何进行修正。 
* 使用 PowerShell 跨指定的订阅或资源组启用两个或更多 Azure VM，或启用虚拟机规模集。

本文稍后介绍有关每个方法的其他信息。

## <a name="prerequisites"></a>先决条件
在开始之前，请确保理解以下部分中的信息。

### <a name="log-analytics"></a>Log Analytics 

以下区域目前支持 Log Analytics 工作区：

  - 美国中西部  
  - 美国东部  
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

如果要从 Azure 门户启用对单个 Azure VM 的监视，可在此过程中创建工作区。 

若要为大规模方案启用该解决方案，首先请在 Log Analytics 工作区中配置以下各项：

* 安装 ServiceMap 和 InfrastructureInsights 解决方案。 仅可使用本文中提供的 Azure 资源管理器模板完成此安装。  
* 配置 Log Analytics 工作区以收集性能计数器。

若要为大规模方案配置工作区，请参阅[为大规模部署设置 Log Analytics 工作区](#setup-log-analytics-workspace)。

### <a name="supported-operating-systems"></a>支持的操作系统

下表列出了用于 VM 的 Azure Monitor 支持的 Windows 和 Linux 操作系统。 本部分稍后将提供详细说明主要和次要 Linux OS 版本以及支持的内核版本的完整列表。

|OS 版本 |性能 |地图 |运行状况 |  
|-----------|------------|-----|-------|  
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |  
|Windows Server 2012 R2 | X | X | |  
|Windows Server 2012 | X | X | |  
|Windows Server 2008 R2 | X | X| |  
|Red Hat Enterprise Linux (RHEL) 7、6| X | X| X |  
|Ubuntu 18.04、16.04、14.04 | X | X | X |  
|CentOS Linux 7、6 | X | X | X |  
|SUSE Linux Enterprise Server (SLES) 12 | X | X | X |  
|Oracle Linux 7 | X<sup>1</sup> | | X |  
|Oracle Linux 6 | X | X | X |  
|Debian 9.4、8 | X<sup>1</sup> | | X | 

<sup>1</sup> 用于 VM 的 Azure Monitor 的“性能”功能仅在 Azure Monitor 中可用。 直接从 Azure VM 的左窗格进行访问时不会提供此功能。 

>[!NOTE]
>以下信息适用于 Linux 操作系统的支持：  
> - 仅默认版本和 SMP Linux 内核版本受支持。 
> - 任何 Linux 发行版都不支持非标准内核版本（例如物理地址扩展 [PAE] 和 Xen）。 例如，不支持版本字符串为 *2.6.16.21-0.8-xen* 的系统。 
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

### <a name="the-microsoft-dependency-agent"></a>Microsoft Dependency Agent
用于 VM 的 Azure Monitor 映射功能从 Microsoft Dependency Agent 获取其数据。 依赖项代理依赖于使用 Log Analytics 代理连接到 Log Analytics。 因此，必须在系统上连同 Dependency Agent 一起安装和配置 Log Analytics 代理。 

无论是为单个 Azure VM 启用用于 VM 的 Azure Monitor，还是使用大规模部署方法，都需要在体验过程中使用 Azure VM Dependency Agent 扩展来安装代理。 

在混合环境中，可使用以下两种方式之一下载并安装 Dependency Agent：手动下载并安装，或使用自动部署方法在 Azure 外部托管的虚拟机中下载并安装。 

下表描述了映射功能在混合环境中支持的连接源。

| 连接的源 | 支持 | Description |
|:--|:--|:--|
| Windows 代理 | 是 | 除[适用于 Windows 的 Log Analytics 代理](../../azure-monitor/platform/log-analytics-agent.md)外，Windows 代理还需要 Microsoft Dependency Agent。 有关操作系统版本的完整列表，请参阅[支持的操作系统](#supported-operating-systems)。 |
| Linux 代理 | 是 | 除[适用于 Linux 的 Log Analytics 代理](../../azure-monitor/platform/log-analytics-agent.md)外，Linux 代理还需要 Microsoft Dependency Agent。 有关操作系统版本的完整列表，请参阅[支持的操作系统](#supported-operating-systems)。 |
| System Center Operations Manager 管理组 | 否 | |  

可从以下位置下载 Dependency Agent：

| 文件 | 操作系统 | 版本 | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |

## <a name="role-based-access-control"></a>基于角色的访问控制
若要在用于 VM 的 Azure Monitor 中启用和访问这些功能，需要获得以下访问角色： 
  
- 若要启用解决方案，必须拥有 *Log Analytics 参与者*角色。 

- 若要查看性能、运行状况，以及映射数据，必须拥有 Azure VM 的*监视读取者*角色。 必须为用于 VM 的 Azure Monitor 配置 Log Analytics 工作区。  

有关如何控制对 Log Analytics 工作区的访问的详细信息，请参阅[管理工作区](../../azure-monitor/platform/manage-access.md)。

## <a name="enable-monitoring-in-the-azure-portal"></a>在 Azure 门户中启用监视
若要在 Azure 门户中启用对 Azure VM 的监视，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。 
1. 选择“虚拟机”。 
1. 从列表中选择一个虚拟机。 
1. 在“VM”页上的“监视”部分，选择“见解(预览版)”。
1. 在“见解(预览版)”页上，选择“立即试用”。

    ![为 VM 启用用于 VM 的 Azure Monitor](./media/vminsights-onboard/enable-vminsights-vm-portal-01.png)

1. 在“Azure Monitor Insights 载入”页上，如果现有的 Log Analytics 工作区与群集在同一订阅中，请从下拉列表中选择该工作区。  
    列表中预先选择了虚拟机在订阅中部署到的默认工作区和位置。 

    >[!NOTE]
    >若要创建新的 Log Analytics 工作区以存储来自 VM 的监视数据，请在前面列出的某个受支持区域中按照[创建 Log Analytics 工作区](../../azure-monitor/learn/quick-create-workspace.md)中的说明进行操作。  

启用监视后，可能需要约 10 分钟才能查看虚拟机的运行状况指标。 

![启用用于 VM 的 Azure Monitor 来监视部署处理](./media/vminsights-onboard/onboard-vminsights-vm-portal-status.png)


## <a name="deploy-at-scale"></a>大规模部署
在本部分中，你将使用 Azure Policy 或 Azure PowerShell 大规模部署用于 VM 的 Azure Monitor。 

在部署 VM 前，请通过执行以下操作对 Log Analytics 工作区进行预先配置：

1. 如果尚未拥有工作区，请创建一个可以支持用于 VM 的 Azure Monitor 的工作区。  
    在继续操作前，请参阅[管理工作区](../../log-analytics/log-analytics-manage-access.md?toc=/azure/azure-monitor/toc.json)，了解成本、管理和合规性注意事项。      

1. 创建一个可用来支持用于 VM 的 Azure Monitor 的新工作区（如果尚不存在）。 在创建新工作区并继续操作之前，请查看[管理工作区](../../azure-monitor/platform/manage-access.md?toc=/azure/azure-monitor/toc.json)，以了解成本、管理与合规性注意事项。       

1. 在该工作区中启用性能计数器，用于在 Linux 和 Windows VM 上收集数据。

1. 在工作区中安装并启用 ServiceMap 和 InfrastructureInsights 解决方案。 

### <a name="set-up-a-log-analytics-workspace"></a>设置 Log Analytics 工作区
如果尚未拥有 Log Analytics 工作区，请通过查看[“先决条件”](#log-analytics)部分中建议的方法，创建一个工作区。 

#### <a name="enable-performance-counters"></a>启用性能计数器
如果解决方案引用的 Log Analytics 工作区尚未配置为收集解决方案所需的性能计数器，则需要启用性能计数器。 可通过两种方式中的一种进行启用：
* 手动方式，如 [Log Analytics 中的 Windows 和 Linux 性能数据源](../../azure-monitor/platform/data-sources-performance-counters.md)所述
* 通过下载并运行可从 [Azure PowerShell 库](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)获取的 PowerShell 脚本
 
#### <a name="install-the-servicemap-and-infrastructureinsights-solutions"></a>安装 ServiceMap 和 InfrastructureInsights 解决方案
此方法包含一个 JSON 模板，其中指定了用于在 Log Analytics 工作区中启用解决方案组件的配置。 

如果对使用模板部署资源不熟悉，请参阅：
* [使用 Resource Manager 模板和 Azure PowerShell 部署资源](../../azure-resource-manager/resource-group-template-deploy.md)
* [使用资源管理器模板和 Azure CLI 部署资源](../../azure-resource-manager/resource-group-template-deploy-cli.md) 

如果选择使用 Azure CLI，首先需要在本地安装和使用 CLI。 必须运行 Azure CLI 2.0.27 版或更高版本。 若要确定版本，请运行 `az --version`。 如果需要安装或升级 Azure CLI，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 

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

1. 使用 *installsolutionsforvminsights.json* 文件名将此文件保存到某个本地文件夹中。
1. 编辑 *WorkspaceName*、*ResourceGroupName* 和 *WorkspaceLocation* 的值。 *WorkspaceName* 的值是 Log Analytics 工作区的完整资源 ID，其中包含工作区名称。 *WorkspaceLocation* 的值是在其中定义工作区的区域。
1. 现在，可以使用以下 PowerShell 命令部署此模板：

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    配置更改可能需要几分钟才能完成。 完成后，系统会显示包含结果的消息，如下所示：

    ```powershell
    provisioningState       : Succeeded
    ```

### <a name="enable-by-using-azure-policy"></a>通过使用 Azure Policy 启用
若要以有助于确保持续合规并使新预配的 VM 自动起作用的方式大规模启用用于 VM 的 Azure Monitor，我们建议使用 [Azure Policy](../../azure-policy/azure-policy-introduction.md)。 这些策略：

* 部署 Log Analytics 代理和 Dependency Agent。 
* 报告合规性结果。 
* 修正不合规的 VM。

若要在租户中使用 Azure Policy 启用用于 VM 的 Azure Monitor，请执行以下操作： 

- 将计划分配到范围：管理组、订阅或资源组 
- 检查和修正合规性结果  

有关分配 Azure Policy 的详细信息，请参阅 [Azure Policy 概述](../../governance/policy/overview.md#policy-assignment)，并在继续操作前查看[管理组概述](../../governance/management-groups/index.md)。 

下表列出策略定义： 

|名称 |Description |类型 |  
|-----|------------|-----|  
|[预览]：启用用于 VM 的 Azure Monitor |在指定范围（管理组、订阅或资源组）中启用用于虚拟机 (VM) 的 Azure Monitor。 将 Log Analytics 工作区用作参数。 |计划 |  
|[预览]：审核 Dependency Agent 部署 — VM 映像 (OS) 未列出 |如果 VM 映像 (OS) 未在列表中定义且未安装代理，则报告 VM 不合规。 |策略 |  
|[预览]：审核 Log Analytics 代理部署 — VM 映像 (OS) 未列出 |如果 VM 映像 (OS) 未在列表中定义且未安装代理，则报告 VM 不合规。 |策略 |  
|[预览]：为 Linux VM 部署 Dependency Agent |如果 VM 映像 (OS) 在列表中定义但未安装代理，请为 Linux VM 部署 Dependency Agent。 |策略 |  
|[预览]：为 Windows VM 部署 Dependency Agent |如果 VM 映像 (OS) 在列表中定义但未安装代理，请为 Windows VM 部署 Dependency Agent。 |策略 |  
|[预览]：为 Linux VM 部署 Log Analytics 代理 |如果 VM 映像 (OS) 在列表中定义但未安装代理，请为 Linux VM 部署 Log Analytics 代理。 |策略 |  
|[预览]：为 Windows VM 部署 Log Analytics 代理 |如果 VM 映像 (OS) 在列表中定义但未安装代理，请为 Windows VM 部署 Log Analytics 代理。 |策略 |  

以下介绍独立策略（未包含在计划中）： 

|名称 |Description |类型 |  
|-----|------------|-----|  
|[预览]：审核 VM 的 Log Analytics 工作区 — 报告不匹配 |如果 VM 未登录到策略/计划分配中指定的 Log Analytics 工作区，则报告 VM 不合规。 |策略 |

#### <a name="assign-the-azure-monitor-initiative"></a>分配 Azure Monitor 计划
在此初始版本中，只能在 Azure 门户中创建策略分配。 若要了解如何完成这些步骤，请参阅 [从 Azure 门户创建策略分配](../../governance/policy/assign-policy-portal.md)。 

1. 若要在 Azure 门户中启动 Azure Policy 服务，请选择“所有服务”，然后搜索并选择“策略”。 
1. 在“Azure Policy”页面的左侧窗格中，选择“分配”。  
    分配即为在特定范围内分配策略以供执行。
1. 在“策略 - 分配”页面的顶部，选择“分配计划”。
1. 在“分配计划”页面，通过单击省略号选择“范围”，并选择管理组或订阅。  
    在我们的示例中，范围将策略分配限制为用于执行的一组虚拟机。
1. 在“范围”页面底部，通过选择“选择”来保存更改。
1. （可选）若要从范围中删除一个或多个资源，请选择“排除项”。 
1. 选择“计划定义”省略号 (...) 以显示可用定义的列表，选择“[预览]启用用于 VM 的 Azure Monitor”，然后选择“选择”。  
    “分配名称”框会自动填充所选的计划名称，但可对其进行更改。 还可添加可选的说明。 “分配者”框根据登录的用户自动填充，此值为可选值。
1. 在受支持区域的“Log Analytics 工作区”下拉列表中，选择一个工作区。

    >[!NOTE]
    >如果工作区超出分配范围，则将 *Log Analytics 参与者*权限授予策略分配的主体 ID。 如果不这样做，可能会看到部署失败，例如：`The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ... `若要授予访问权限，请查看[如何手动配置托管标识](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity)。
    >  
    “托管标识”复选框会被选中，因为要分配的计划包含具有 *deployIfNotExists* 效果的策略。 
1. 在“管理标识位置”下拉列表中，选择适当的区域。 
1. 选择“分配”。

#### <a name="review-and-remediate-the-compliance-results"></a>检查和修正合规结果 

可阅读[识别不合规结果](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources)一文，了解如何检查合规结果。 在左侧窗格中，选择“合规性”，然后根据创建的分配，针对不合规的 VM 找到“[预览]启用用于 VM 的 Azure Monitor”计划。

![Azure VM 的策略合规性](./media/vminsights-onboard/policy-view-compliance-01.png)

根据计划中包含的策略的结果，VM 在以下情况中报告为不合规：  
  
* 未部署 Log Analytics 代理或 Dependency Agent。 
   这对于已经拥有 VM 的范围来说是典型情况。 若要解决此问题，请在不合规的策略上[创建修正任务](../../governance/policy/how-to/remediate-resources.md)来部署所需的代理。   
 
    - [预览]: Deploy Dependency Agent for Linux VMs   
    - [预览]: Deploy Dependency Agent for Windows VMs  
    - [预览]: Deploy Log Analytics Agent for Linux VMs  
    - [预览]: Deploy Log Analytics Agent for Windows VMs  

* VM 映像 (OS) 未在策略定义中确定。 
   部署策略标准仅包含通过已知 Azure VM 映像部署的 VM。 请查看文档，了解 VM OS 是否受支持。 如果不受支持，则复制部署策略并更新或修改它来使映像合规。 
  
    - [预览]：审核 Dependency Agent 部署 — VM 映像 (OS) 未列出  
    - [预览]：审核 Log Analytics 代理部署 — VM 映像 (OS) 未列出

* VM 未登录到指定的 Log Analytics 工作区。  
    可能存在计划范围中的某些 VM 登录到策略部署中指定的 Log Analytics 工作区以外的 Log Analytics 工作区的情况。 此策略是用于确定向不合规工作区报告的 VM 的工具。 
 
    - [预览]: Audit Log Analytics Workspace for VM - Report Mismatch  

### <a name="enable-with-powershell"></a>使用 PowerShell 启用
若要为多个 VM 或虚拟机规模集启用用于 VM 的 Azure Monitor，可以使用 Azure PowerShell 库中提供的 PowerShell 脚本 [Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0)。 此脚本会循环访问订阅中、*ResourceGroup* 指定的限定资源组中的每个虚拟机和虚拟机规模集，或循环访问 *Name* 指定的单个 VM 或虚拟机规模集。 对于每个 VM 或虚拟机规模集，该脚本将验证是否已安装 VM 扩展。 如果未安装 VM 扩展，脚本会尝试重新安装它。 如果已安装 VM 扩展，脚本将安装 Log Analytics 代理和 Dependency Agent VM 扩展。  

此脚本需要 Azure PowerShell 模块 5.7.0 或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)。 如果在本地运行 PowerShell，则还需运行 `Connect-AzureRmAccount` 以创建与 Azure 的连接。

若要获取脚本的参数详细信息和示例用法的列表，请运行 `Get-Help`。  

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and the Dependency agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or re-configures following on VMs and VM Scale Sets:
    - Log Analytics VM Extension configured to supplied Log Analytics Workspace
    - Dependency Agent VM Extension

    Can be applied to:
    - Subscription
    - Resource Group in a Subscription
    - Specific VM/VM Scale Set
    - Compliance results of a policy for a VM or VM Extension

    Script will show you list of VMs/VM Scale Sets that will apply to and let you confirm to continue.
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
        If using PolicyAssignmentName parameter, subscription that VMs are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VMs to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VMs/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Supported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
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

    Install for all VMs in a Resource Group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to reinstall extensions even if already installed, for example to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source, and to reinstall (move to a new workspace)
```

以下示例演示如何在文件夹中使用 PowerShell 命令来启用用于 VM 的 Azure Monitor，并了解预期的输出：

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VMs or VM ScaleSets matching criteria specified

VMs or VM ScaleSets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency agent extensions on above 2 VMs or VM Scale Sets.
VMs in a non-running state will be skipped.
Extension will not be reinstalled if already installed. Use -ReInstall if desired, for example to update workspace

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

## <a name="enable-for-a-hybrid-environment"></a>为混合环境启用
本部分介绍如何部署在数据中心或其他云环境中托管并受用于 VM 的 Azure Monitor 监视的虚拟机或物理计算机。 

用于 VM 的 Azure Monitor 映射依赖项代理本身不传输任何数据，它不需要对防火墙或端口做出任何更改。 映射数据始终由 Log Analytics 代理直接传输到 Azure Monitor 服务；如果 IT 安全策略不允许网络中的计算机连接到 Internet，则通过 [OMS 网关](../../azure-monitor/platform/gateway.md)传输这些数据。

查看 [Log Analytics Linux 和 Windows 代理](../../log-analytics/log-analytics-agent-overview.md)的要求与部署方法。 

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

步骤归纳如下：

1. 安装适用于 Windows 或 Linux 的 Log Analytics 代理。
1. 下载并安装适用于 [Windows](https://aka.ms/dependencyagentwindows) 或 [Linux](https://aka.ms/dependencyagentlinux) 的用于 VM 的 Azure Monitor 映射依赖项代理。
1. 启用性能计数器收集。
1. 部署用于 VM 的 Azure Monitor。

### <a name="install-the-dependency-agent-on-windows"></a>在 Windows 上安装依赖项代理 
可通过运行 `InstallDependencyAgent-Windows.exe` 在 Windows 计算机上手动安装 Dependency Agent。 如果在没有任何选项的情况下运行此可执行文件，它将启动一个安装向导，以交互方式指导用户安装代理。 

>[!NOTE]
>需要*管理员*特权才能安装或卸载代理。

下表突出显示了通过命令行安装代理时支持的参数。 

| 参数 | Description |
|:--|:--|
| /? | 返回命令行选项的列表。 |
| /S | 执行无需用户交互的无提示安装。 |

例如，若要使用 `/?` 参数运行安装程序，请键入 **InstallDependencyAgent-Windows.exe /?**。

默认情况下，Windows Dependency Agent 的文件在 *C:\Program Files\Microsoft Dependency Agent* 中安装。 如果完成安装后依赖项代理无法启动，请检查日志以获取详细的错误信息。 日志目录为 *%Programfiles%\Microsoft Dependency Agent\logs*。 

### <a name="install-the-dependency-agent-on-linux"></a>在 Linux 上安装 Dependency Agent
通过 *InstallDependencyAgent-Linux64.bin*（具有自解压二进制文件的 Shell 脚本）在 Linux 服务器上安装 Dependency Agent。 可使用 `sh` 来运行该文件或将执行权限添加到文件本身。

>[!NOTE]
> 需要根目录访问才能安装或配置代理。
> 

| 参数 | Description |
|:--|:--|
| -help | 获取命令行选项列表。 |
| -s | 执行无提示安装，无用户提示。 |
| --check | 检查权限和操作系统，但不安装代理。 |

例如，若要使用 `-help` 参数运行安装程序，请键入 **InstallDependencyAgent-Linux64.bin -help**。

运行以下命令将 Linux Dependency Agent 作为根安装：`sh InstallDependencyAgent-Linux64.bin`。
    
如果 Dependency Agent 无法启动，请检查日志以获取详细的错误信息。 在 Linux 代理上，日志目录是 */var/opt/microsoft/dependency-agent/log*。

Dependency Agent 的文件放置在以下目录中：

| 文件 | 位置 |
|:--|:--|
| 核心文件 | /opt/microsoft/dependency-agent |
| 日志文件 | /var/opt/microsoft/dependency-agent/log |
| 配置文件 | /etc/opt/microsoft/dependency-agent/config |
| 服务可执行文件 | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| 二进制存储文件 | /var/opt/microsoft/dependency-agent/storage |

### <a name="enable-performance-counters"></a>启用性能计数器
如果解决方案引用的 Log Analytics 工作区尚未配置为收集解决方案所需的性能计数器，则需要启用性能计数器。 可通过两种方式中的一种进行启用： 
* 手动方式，如 [Log Analytics 中的 Windows 和 Linux 性能数据源](../../azure-monitor/platform/data-sources-performance-counters.md)所述
* 通过下载并运行可从 [Azure PowerShell 库](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)获取的 PowerShell 脚本
 
### <a name="deploy-azure-monitor-for-vms"></a>部署用于 VM 的 Azure Monitor
此方法包含一个 JSON 模板，其中指定了用于在 Log Analytics 工作区中启用解决方案组件的配置。 

如果对使用模板部署资源不熟悉，请参阅：
* [使用 Resource Manager 模板和 Azure PowerShell 部署资源](../../azure-resource-manager/resource-group-template-deploy.md)
* [使用资源管理器模板和 Azure CLI 部署资源](../../azure-resource-manager/resource-group-template-deploy-cli.md) 

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

1. 使用 *installsolutionsforvminsights.json* 文件名将此文件保存到某个本地文件夹中。
1. 编辑 *WorkspaceName*、*ResourceGroupName* 和 *WorkspaceLocation* 的值。 *WorkspaceName* 的值是 Log Analytics 工作区的完整资源 ID，其中包含工作区名称。 *WorkspaceLocation* 的值是在其中定义工作区的区域。
1. 现在，可以使用以下 PowerShell 命令部署此模板：

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    配置更改可能需要几分钟才能完成。 完成后，系统会显示包含结果的消息，如下所示：

    ```powershell
    provisioningState       : Succeeded
    ```
启用监视后，可能需要约 10 分钟才能查看混合计算机的运行状况和指标。 

## <a name="performance-counters-enabled"></a>已启用性能计数器
用于 VM 的 Azure Monitor 将配置 Log Analytics 工作区，以收集解决方案使用的性能计数器。 下表列出了解决方案配置的、每隔 60 秒收集一次的对象和计数器。

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
