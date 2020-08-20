---
title: 在 Azure 自动化中部署 Windows 混合 Runbook 辅助角色
description: 本文介绍如何部署混合 Runbook 辅助角色，你可使用该角色在本地数据中心或云环境的基于 Windows 的计算机上运行 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 08/20/2020
ms.topic: conceptual
ms.openlocfilehash: 74657743d14b9365f66ed3373592b708a07e11dc
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88660506"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>部署 Windows 混合 Runbook 辅助角色

利用 Azure 自动化的混合 Runbook 辅助角色功能，既可以直接在托管角色的计算机上运行 Runbook，也可以对环境中的资源运行 Runbook，从而管理这些本地资源。 Azure 自动化将存储并管理 Runbook，然后将其传送到一台或多台指定的计算机。 本文介绍如何在 Windows 计算机上部署混合 Runbook 辅助角色、如何删除辅助角色，以及如何删除混合 Runbook 辅助角色组。

成功部署 Runbook 辅助角色后，请查看[在混合 Runbook 辅助角色上运行 Runbook](automation-hrw-run-runbooks.md)，了解如何配置 Runbook，使本地数据中心或其他云环境中的过程实现自动化。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保你具备以下内容。

### <a name="a-log-analytics-workspace"></a>Log Analytics 工作区

混合 Runbook 辅助角色依赖于 Azure Monitor Log Analytics 工作区来安装和配置角色。 你可通过 [Azure 资源管理器](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace)、[PowerShell](../azure-monitor/scripts/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json) 或在 [Azure 门户](../azure-monitor/learn/quick-create-workspace.md)中创建该工作区。

如果没有 Azure Monitor Log Analytics 工作区，请在创建工作区前查看 [Azure Monitor 日志设计指南](../azure-monitor/platform/design-logs-deployment.md)。

如果你有工作区，但它没有与你的自动化帐户关联，则启用自动化功能会添加 Azure 自动化的功能，包括对混合 Runbook 辅助角色的支持。 当你在 Log Analytics 工作区中启用 Azure 自动化功能之一时，特别 [更新管理](update-management/update-mgmt-overview.md) 或 [更改跟踪和清单](change-tracking.md)，辅助角色组件会自动推送到代理计算机。

> [!NOTE]
> 启用更新管理或更改跟踪和清单功能时，Azure 自动化仅支持某些区域来链接 Log Analytics 工作区和自动化帐户。 有关支持的映射对的列表，请参阅[自动化帐户和 Log Analytics 工作区的区域映射](how-to/region-mappings.md)。 启用任一功能之前，请查看 azure 自动化的 [azure 定价](https://azure.microsoft.com/pricing/details/automation/) 信息。

   若要将更新管理功能添加到工作区，请运行以下 PowerShell cmdlet：

```powershell-interactive
   Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "Updates" -Enabled $true
```

   若要将更改跟踪和清单功能添加到工作区，请运行以下 PowerShell cmdlet：

```powershell-interactive
   Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "ChangeTracking" -Enabled $true
```

### <a name="log-analytics-agent"></a>Log Analytics 代理

混合 Runbook 辅助角色要求在受支持的 Windows 操作系统上使用 [Log Analytics 代理](../azure-monitor/platform/log-analytics-agent.md)。

### <a name="supported-windows-operating-system"></a>支持的 Windows 操作系统

Windows 混合 Runbook 辅助角色官方支持以下版本的 Windows 操作系统：

* Windows Server 2019
* Windows Server 2016、版本 1709 和 1803
* Windows Server 2012、2012 R2
* Windows Server 2008 SP2 (x64)、2008 R2
* Windows 10 Enterprise（包括多会话）和 Pro
* Windows 8 企业版和专业版
* Windows 7 SP1

### <a name="minimum-requirements"></a>最低要求

Windows 混合 Runbook 辅助角色的最低要求如下：

* Windows PowerShell 5.1 或更高版本（[下载 WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616)）
* .NET Framework 4.6.2 或更高版本
* 双核
* 4 GB RAM
* 端口 443（出站）

### <a name="network-configuration"></a>网络配置

若要查看混合 Runbook 辅助角色的更多网络要求，请参阅[配置网络](automation-hybrid-runbook-worker.md#network-planning)。

### <a name="adding-a-machine-to-a-hybrid-runbook-worker-group"></a>将计算机添加到混合 Runbook 辅助角色组

你可将辅助角色计算机添加到自动化帐户中的混合 Runbook 辅助角色组。 请注意，只要对 Azure 自动化功能和混合 Runbook 辅助角色组成员身份使用同一帐户，就必须支持自动化 Runbook。 此功能已添加到 7.2.12024.0 版本的混合 Runbook 辅助角色。

>[!NOTE]
>如果启用 Azure 自动化[更新管理](update-management/update-mgmt-overview.md)，会将所有连接到 Log Analytics 工作区的 Windows 计算机自动配置为混合 Runbook 辅助角色，从而支持管理其操作系统更新。 但是，此辅助角色未注册到任何已在自动化帐户中定义的混合 Runbook 辅助角色组。

## <a name="enabling-machines-for-management-with-azure-automation-state-configuration"></a>使计算机通过 Azure Automation State Configuration 进行管理

若要了解如何让计算机能够通过 Azure Automation State Configuration 进行管理，请参阅[使计算机通过 Azure Automation State Configuration 进行管理](automation-dsc-onboarding.md)。

> [!NOTE]
> 对于支持将混合 Runbook 辅助角色用于 Desired State Configuration (DSC) 的计算机，必须将计算机添加为 DSC 节点才能管理计算机的配置。

## <a name="windows-hybrid-runbook-worker-installation-options"></a>Windows 混合 Runbook 辅助角色的安装选项

若要安装和配置 Windows 混合 Runbook 辅助角色，可以使用以下方法之一。

* 对于 Azure VM，使用[适用于 Windows 的虚拟机扩展](../virtual-machines/extensions/oms-windows.md)安装适用于 Windows 的 Log Analytics 代理。 该扩展使用 Azure 资源管理器模板或 PowerShell 在 Azure 虚拟机上安装 Log Analytics 代理，并将虚拟机注册到现有的 Log Analytics 工作区中。 安装代理后，可以将 VM 添加到自动化帐户中的混合 Runbook 辅助角色组。

* 对于非 Azure VM，请使用[将 Windows 计算机连接到 Azure Monitor](../azure-monitor/platform/agent-windows.md) 一文中所述的部署选项安装适用于 Windows 的 Log Analytics 代理。 可对多台计算机重复此过程，将多个辅助角色添加到你的环境。 安装代理后，可将 VM 添加到自动化帐户中的混合 Runbook 辅助角色组。

* 请使用提供的 PowerShell 脚本将一台或多台 Windows 计算机的配置过程完全[自动化](#automated-deployment)。 对于数据中心或其他云环境中的计算机，建议使用此方法。

## <a name="automated-deployment"></a>自动化部署

在目标计算机上执行以下步骤，使用 PowerShell 脚本 New-OnPremiseHybridWorker.ps1 实现 Windows 混合辅助角色安装和配置的自动化。 此脚本会执行以下步骤：

* 安装所需的模块
* 使用 Azure 帐户登录
* 验证是否存在指定的资源组和自动化帐户
* 创建对自动化帐户属性的引用
* 如果未指定，请创建 Azure Monitor Log Analytics 工作区
* 在工作区中启用 Azure 自动化解决方案
* 下载并安装适用于 Windows 的 Log Analytics 代理
* 将计算机注册为混合 Runbook 辅助角色

### <a name="step-1---download-the-powershell-script"></a>步骤 1 - 下载 PowerShell 脚本

从 [PowerShell 库](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker)下载 New-OnPremiseHybridWorker.ps1 脚本。 下载该脚本后，在目标计算机上复制或运行它。 在执行期间，New-OnPremiseHybridWorker.ps1 脚本使用以下参数。

| 参数 | 状态 | 说明 |
| --------- | ------ | ----------- |
| `AAResourceGroupName` | 必需 | 与自动化帐户关联的资源组的名称。 |
| `AutomationAccountName` | 必需 | 自动化帐户的名称。
| `Credential` | 可选 | 登录到 Azure 环境时要使用的凭据。 |
| `HybridGroupName` | 必需 | 混合 Runbook 辅助角色组的名称，可将其指定为支持此方案的 runbook 的目标。 |
| `OMSResourceGroupName` | 可选 | Log Analytics 工作区的资源组的名称。 如果未指定此资源组，则使用 `AAResourceGroupName` 的值。 |
| `SubscriptionID` | 必需 | 与自动化帐户关联的 Azure 订阅的标识符。 |
| `TenantID` | 可选 | 与自动化帐户关联的租户组织的标识符。 |
| `WorkspaceName` | 可选 | Log Analytics 工作区名称。 如果没有 Log Analytics 工作区，该脚本会创建并配置一个。 |

### <a name="step-2---open-windows-powershell-command-line-shell"></a>步骤 2 - 打开 Windows PowerShell 命令行 shell

在开始菜单中单击“启动”，键入 PowerShell，右键单击“Windows PowerShell”，然后单击“以管理员身份运行”    。

### <a name="step-3---run-the-powershell-script"></a>步骤 3 - 运行 PowerShell 脚本

在 PowerShell 命令行 shell 中，浏览到包含已下载脚本的文件夹。 更改参数 `AutomationAccountName`、`AAResourceGroupName`、`OMSResourceGroupName`、`HybridGroupName`、`SubscriptionID` 和 `WorkspaceName` 的值。 然后运行脚本。

运行脚本后，系统会提示在 Azure 上进行身份验证。 必须以订阅管理员角色成员和订阅共同管理员的帐户登录。

```powershell-interactive
$NewOnPremiseHybridWorkerParameters = @{
  AutomationAccountName = <nameOfAutomationAccount>
  AAResourceGroupName   = <nameOfResourceGroup>
  OMSResourceGroupName  = <nameOfResourceGroup>
  HybridGroupName       = <nameOfHRWGroup>
  SubscriptionID        = <subscriptionId>
  WorkspaceName         = <nameOfLogAnalyticsWorkspace>
}
.\New-OnPremiseHybridWorker.ps1 @NewOnPremiseHybridWorkerParameters
```

### <a name="step-4---install-nuget"></a>步骤 4 - 安装 NuGet

系统会提示用户同意安装 NuGet 并使用 Azure 凭据进行身份验证。 如果没有最新的 NuGet 版本，可从[可用的 NuGet 发行版](https://www.nuget.org/downloads)下载。

### <a name="step-5---verify-the-deployment"></a>步骤 5 - 验证部署

脚本完成后，自动化帐户中的“混合辅助角色组”页面会显示新组和成员数。 如果这是现有的组，则成员数会递增。 可以从“混合辅助角色组”页上的列表中选择组，并选择“混合辅助角色”磁贴。 在“混合辅助角色”页上，会列出组的每个成员。

## <a name="manual-deployment"></a>手动部署

若要安装和配置 Windows 混合 Runbook 辅助角色，请执行以下步骤。

### <a name="step-1---verify-agent-is-reporting-to-workspace"></a>步骤 1 - 验证代理是否向工作区报告

适用于 Windows 的 Log Analytics 代理会将计算机连接到 Azure Monitor Log Analytics 工作区。 在计算机上安装代理并将其连接到工作区时，代理会自动下载混合 Runbook 辅助角色所需的组件。

几分钟后，如果代理已成功连接到 Log Analytics 工作区，则可以运行以下查询，验证是否正在向工作区发送检测信号数据。

```kusto
Heartbeat 
| where Category == "Direct Agent"
| where TimeGenerated > ago(30m)
```

在搜索结果中，应会看到计算机的检测信号记录，它们指示计算机已连接到服务并将该结果报告给服务。 默认情况下，每个代理都会将一个检测信号记录转发到其分配的工作区。 按照以下步骤可完成代理安装和设置。

1. 启用该功能以添加代理计算机。 对于更新管理和 Azure Vm，请参阅 [从自动化帐户启用更新管理](update-management/update-mgmt-enable-automation-account.md)、 [通过浏览 Azure 门户启用更新管理](update-management/update-mgmt-enable-portal.md)、 [从 Runbook 启用更新管理](update-management/update-mgmt-enable-runbook.md)或 [从 Azure VM 启用更新管理](update-management/update-mgmt-enable-vm.md)。 有关更改跟踪和 Azure Vm，请参阅 [启用 Azure vm](automation-enable-changes-from-auto-acct.md#enable-azure-vms)和非 Azure vm，请参阅 [在工作区中启用计算机](automation-enable-changes-from-auto-acct.md#enable-machines-in-the-workspace)。

2. 要确认混合 Runbook 辅助角色的版本，请浏览到 `C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\` 并注意 version 子文件夹。

### <a name="step-2---install-the-runbook-environment-and-connect-to-azure-automation"></a>步骤 2 - 安装 Runbook 环境并连接到 Azure 自动化

将代理配置为向 Log Analytics 工作区报告时，Azure 自动化功能会向下推送 `HybridRegistration` PowerShell 模块，其中包含 `Add-HybridRunbookWorker` cmdlet。 使用此 cmdlet 将 Runbook 环境安装到计算机上，并将其注册到 Azure 自动化。

在管理员模式下打开 PowerShell 会话，并运行以下命令以导入模块。

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

现在，使用以下语法运行 `Add-HybridRunbookWorker` cmdlet。

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -Url <Url> -Key <String>
```

你可从自动化帐户中的“密钥”页面获取参数 `Url` 和 `Key` 所需的信息。 从页面左侧的“帐户设置”部分下，选择“密钥” 。

![“管理密钥”页](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* 对于 `Url` 参数，复制 URL 的值。

* 对于 `Key` 参数，复制主访问密钥的值。

* 对于 `GroupName` 参数，请使用混合 Runbook 辅助角色组的名称。 如果自动化帐户中已存在该组，则会将当前计算机添加到其中。 如果该组不存在，则将添加该组。

* 如果需要，请设置 `Verbose` 参数以接收有关安装的详细信息。

### <a name="step-3----install-powershell-modules"></a>步骤 3- 安装 PowerShell 模块

Runbook 可以使用在 Azure 自动化环境中安装的模块中定义的任何活动和 cmdlet。 这些模块不会自动部署到本地计算机，必须手动安装。 例外情况是 Azure 模块。 此模块是默认安装的，并可用于访问所有 Azure 服务的 cmdlet 以及 Azure 自动化的活动。

由于混合 Runbook 辅助角色的主要用途是管理本地资源，很可能需要安装支持这些资源的模块，尤其是 `PowerShellGet` 模块。 有关安装 Windows PowerShell 模块的信息，请参阅 [Windows PowerShell](/powershell/scripting/developer/windows-powershell)。

安装的模块必须位于 `PSModulePath` 环境变量所引用的位置，以便混合辅助角色自动将其导入。 有关详细信息，请参阅[在 PSModulePath 中安装模块](/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7)。

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-windows-machine"></a><a name="remove-windows-hybrid-runbook-worker"></a>从本地 Windows 计算机中删除混合 Runbook 辅助角色

1. 在 Azure 门户中，转到自动化帐户。

2. 在“帐户设置”下，选择“密钥”并记下“URL”和“主访问密钥”的值   。

3. 在管理员模式下打开 PowerShell 会话，并使用 URL 和主访问密钥值运行以下命令。 可使用 `Verbose` 参数获取删除过程的详细日志。 若要从混合辅助角色组中删除过时的计算机，请使用可选的 `machineName` 参数。

```powershell-interactive
Remove-HybridRunbookWorker -Url <URL> -Key <primaryAccessKey> -MachineName <computerName>
```

## <a name="remove-a-hybrid-worker-group"></a>删除混合辅助角色组

若要删除混合 Runbook 辅助角色组，首先需要从每台计算机上删除该组中包含的混合 Runbook 辅助角色。 然后，使用以下步骤删除该组：

1. 在 Azure 门户中打开自动化帐户。

2. 在“流程自动化”下选择“混合辅助角色组”。 选择要删除的组。 将显示该组的属性页。

   ![“属性”页](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. 在所选组的属性页中，选择“删除”。 系统会显示一条消息，要求确认此操作。 如果确定要继续，请选择“是”。

   ![确认消息](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   完成此过程可能需要数秒钟的时间。 可以在菜单中的“通知”下面跟踪操作进度。

## <a name="next-steps"></a>后续步骤

* 若要了解如何配置 Runbook，使本地数据中心或其他云环境中的过程自动化，请参阅[在混合 Runbook 辅助角色上运行 Runbook](automation-hrw-run-runbooks.md)。

* 若要了解如何对混合 Runbook 辅助角色进行故障排除，请参阅[排查混合 Runbook 辅助角色问题](troubleshoot/hybrid-runbook-worker.md#general)。
