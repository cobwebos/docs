---
title: Azure 自动化 Windows 混合 Runbook 辅助角色
description: 本文介绍如何安装 Azure 自动化混合 Runbook 辅助角色，该角色可用于在本地数据中心或云环境的基于 Windows 的计算机上运行 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 53dfe07ebd4925c96290db140b6e613c38eef564
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617335"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>部署 Windows 混合 Runbook 辅助角色

利用 Azure 自动化的混合 Runbook 辅助角色功能，既可以直接在托管角色的计算机上运行 Runbook，也可以对环境中的资源运行 Runbook，从而管理这些本地资源。 Azure 自动化存储和管理 Runbook，然后将它们传递到一个或多个指定的计算机。 本文介绍如何在 Windows 计算机上部署混合 Runbook 辅助角色。

成功部署 Runbook 辅助角色后，请查看[在混合 Runbook 辅助角色上运行 Runbook](automation-hrw-run-runbooks.md)，了解如何配置 Runbook，使本地数据中心或其他云环境中的过程实现自动化。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可以使用["如何更新 Azure 自动化 中的 Azure PowerShell"模块](automation-update-azure-modules.md)将模块更新到最新版本。

## <a name="windows-hybrid-runbook-worker-installation-and-configuration"></a>Windows 混合 Runbook 辅助功能安装和配置

要安装和配置 Windows 混合 Runbook 工作线程，可以使用以下方法之一。

* 对于 Azure VM，请使用 Windows 的[虚拟机扩展名](../virtual-machines/extensions/oms-windows.md)安装 Windows 的日志分析代理。 该扩展在 Azure 虚拟机上安装日志分析代理，并使用 Azure 资源管理器模板或 PowerShell 将虚拟机注册到现有的日志分析工作区中。 安装代理后，可以将 VM 添加到自动化帐户中的混合 Runbook 辅助角色组。 请参阅["手动部署](#manual-deployment)"部分中的步骤 3 和 4。

* 使用自动化 Runbook 来彻底实现配置 Windows 计算机过程的自动化。 这是数据中心或其他云环境中的计算机推荐的方法。

* 按照分步过程操作，在非 Azure VM 上手动安装和配置混合 Runbook 辅助角色。

> [!NOTE]
> 要使用所需的状态配置 （DSC） 管理支持混合 Runbook 工作角色的服务器配置，必须将服务器添加为 DSC 节点。

### <a name="minimum-requirements-for-windows-hybrid-runbook-worker"></a>Windows 混合 Runbook 工作线程的最低要求

Windows 混合 Runbook 辅助角色的最低要求如下：

* Windows Server 2012 或更高版本
* Windows PowerShell 5.1 或更高版本 （[下载 WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616)）
* .NET Framework 4.6.2 或更高版本
* 双核
* 4 GB RAM
* 端口 443（出站）

### <a name="network-configuration"></a>网络配置

若要查看混合 Runbook 辅助角色的更多网络要求，请参阅[配置网络](automation-hybrid-runbook-worker.md#network-planning)。

### <a name="server-onboarding-for-management-with-automation-dsc"></a>使用自动化 DSC 进行服务器载入管理

有关使用 DSC 进行管理的载入服务器的信息，请参阅 Azure[自动化 DSC 管理的载入计算机](automation-dsc-onboarding.md)。

启用[更新管理解决方案](../operations-management-suite/oms-solution-update-management.md)会自动将连接到日志分析工作区的任何 Windows 计算机配置为混合 Runbook 辅助角色，以支持解决方案中包含的 Runbook。 但是，此工作人员未注册到自动化帐户中已定义的任何混合 Runbook 工作组。

### <a name="addition-of-the-computer-to-a-hybrid-runbook-worker-group"></a>将计算机添加到混合 Runbook 工作组

您可以将辅助计算机添加到自动化帐户中的混合 Runbook 工作群体。 请注意，只要对解决方案和混合 Runbook 辅助角色使用相同的帐户，就必须支持自动化 Runbook。 此功能已添加到 7.2.12024.0 版本的混合 Runbook 辅助角色。

## <a name="automated-deployment"></a>自动化部署

在目标计算机上，执行以下步骤以自动安装和配置 Windows 混合辅助角色。

### <a name="step-1---download-the-powershell-script"></a>第 1 步 - 下载 PowerShell 脚本

从[PowerShell 库](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker)下载 **"全新内部混合工作者.ps1"** 脚本。 下载应直接从运行混合 Runbook 工作角色的计算机或环境中的另一台计算机下载。 下载脚本后，将其复制到工作人员。 **New-Onpremise 混合工作线程.ps1**脚本使用执行期间描述的参数。

| 参数 | 状态 | 说明 |
| --------- | ------ | ----------- |
| `AAResourceGroupName` | 必需 | 与自动化帐户关联的资源组的名称。 |
| `AutomationAccountName` | 必需 | 自动化帐户的名称。
| `Credential` | 可选 | 登录到 Azure 环境时要使用的凭据。 |
| `HybridGroupName` | 必需 | 混合 Runbook 辅助角色组的名称，可将其指定为支持此方案的 runbook 的目标。 |
| `OMSResourceGroupName` | 可选 | Log Analytics 工作区的资源组的名称。 如果未指定此资源组，则使用 的值`AAResourceGroupName`。 |
| `SubscriptionID` | 必需 | 与自动化帐户关联的 Azure 订阅的标识符。 |
| `TenantID` | 可选 | 与自动化帐户关联的租户组织的标识符。 |
| `WorkspaceName` | 可选 | Log Analytics 工作区名称。 如果没有 Log Analytics 工作区，该脚本会创建并配置一个。 |

> [!NOTE]
> 启用解决方案时，Azure 自动化仅支持某些区域链接日志分析工作区和自动化帐户。 有关支持的映射对的列表，请参阅[自动化帐户的区域映射和日志分析工作区](how-to/region-mappings.md)。

### <a name="step-2---open-windows-powershell-command-line-shell"></a>步骤 2 - 打开 Windows PowerShell 命令行外壳

在"管理员"模式下从 **"开始"** 屏幕打开**Windows PowerShell。**

### <a name="step-3---run-the-powershell-script"></a>步骤 3 - 运行 PowerShell 脚本

在 PowerShell 命令行 shell 中，浏览到包含已下载的脚本的文件夹。 `AutomationAccountName`更改参数`AAResourceGroupName`的值 、 、 `OMSResourceGroupName` `HybridGroupName`、 `SubscriptionID`、 和`WorkspaceName`。 然后运行脚本。

运行脚本后，系统会提示在 Azure 上进行身份验证。 必须以订阅管理员角色成员和订阅共同管理员的帐户登录。

```powershell-interactive
.\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
-OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
-SubscriptionID <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
```

### <a name="step-4---install-nuget"></a>第 4 步 - 安装 NuGet

系统将提示您同意安装 NuGet，并使用 Azure 凭据进行身份验证。 如果您没有最新的 NuGet 版本，则可以从[可用的 NuGet 分发版本](https://www.nuget.org/downloads)获取它。

### <a name="step-5---verify-the-deployment"></a>步骤 5 - 验证部署

脚本完成后，“混合辅助角色组”页面会显示新组和成员数。 如果这是现有的组，则成员数会递增。 您可以从"混合辅助角色组"页上的列表中选择该组，然后选择 **"混合辅助角色"** 磁贴。 在"混合工作"页上，您可以看到列出的组的每个成员。

## <a name="manual-deployment"></a>手动部署

在目标计算机上，为自动化环境执行前两个步骤一次。 然后，对每台工作计算机执行其余步骤。

### <a name="step-1---create-a-log-analytics-workspace"></a>第 1 步 - 创建日志分析工作区

如果尚未具有日志分析工作区，请先在创建工作区之前查看[Azure 监视器日志设计指南](../azure-monitor/platform/design-logs-deployment.md)。

### <a name="step-2---add-the-automation-solution-to-the-log-analytics-workspace"></a>步骤 2 - 将自动化解决方案添加到日志分析工作区

自动化解决方案为 Azure 自动化添加了功能，包括对混合 Runbook 辅助角色的支持。 将解决方案添加到日志分析工作区时，它会自动将安装的工作组件推送到代理计算机，如下一步所述。

要将自动化解决方案添加到工作区，请运行以下 PowerShell cmdlet。

```powershell-interactive
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true -DefaultProfile <IAzureContextContainer>
```

### <a name="step-3---install-the-log-analytics-agent-for-windows"></a>步骤 3 - 安装 Windows 的日志分析代理

适用于 Windows 的 Log Analytics 代理将计算机连接到 Azure Monitor Log Analytics 工作区。 当您在计算机上安装代理并将其连接到工作区时，它会自动下载混合 Runbook 辅助角色所需的组件。

若要在计算机上安装代理，请按照[将 Windows 计算机连接到 Azure Monitor 日志](../log-analytics/log-analytics-windows-agent.md)中的说明操作。 可以对多台计算机重复此过程，以将多个辅助角色添加到环境。

当代理在几分钟后成功连接到日志分析工作区时，可以运行以下查询以验证是否向工作区发送检测信号数据。

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

在搜索结果中，应看到计算机的检测信号记录，指示计算机已连接并报告给服务。 默认情况下，每个代理都会将检测信号记录转发到其分配的工作区。 

使用以下步骤完成代理安装和设置。

1. 使解决方案能够加入代理计算机。 请参阅[工作区中的板载计算机](https://docs.microsoft.com/azure/automation/automation-onboard-solutions-from-automation-account#onboard-machines-in-the-workspace)。
2. 验证代理是否正确下载了自动化解决方案。 
3. 要确认混合 Runbook 辅助角色的版本，请浏览到**C：*程序文件\Microsoft 监视代理\代理\Azure自动化**并记下**版本**子文件夹。

### <a name="step-4---install-the-runbook-environment-and-connect-to-azure-automation"></a>步骤 4 - 安装 Runbook 环境并连接到 Azure 自动化

当您将代理配置为向日志分析工作区报告时，自动化解决方案将向下推送`HybridRegistration`包含 cmdlet 的`Add-HybridRunbookWorker`PowerShell 模块。 使用此 cmdlet 在计算机上安装 Runbook 环境并将其注册到 Azure 自动化。

在管理员模式下打开 PowerShell 会话，并运行以下命令以导入模块。

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

现在使用以下`Add-HybridRunbookWorker`语法运行 cmdlet。

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

可以从 Azure 门户的“管理密钥”页获取此 cmdlet 所需的信息。 通过在自动化帐户中的"设置"页上选择 **"按键**"打开此页面。

![管理密钥页面](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* 对于参数`GroupName`，请使用混合 Runbook 工作组的名称。 如果该组已经存在于自动化帐户中，则会将当前计算机添加到其中。 如果该组不存在，则将添加该组。
* 对于参数`EndPoint`，请使用"管理密钥"页上的**URL**条目。
* 对于参数`Token`，请使用"管理密钥"页上的 **"主访问密钥"** 条目。
* 如果需要，请设置参数`Verbose`以接收有关安装的详细信息。

### <a name="step-5----install-powershell-modules"></a>第 5 步 - 安装 PowerShell 模块

Runbook 可以使用在 Azure 自动化环境中安装的模块中定义的任何活动和 cmdlet。 由于这些模块不会自动部署到本地计算机，因此必须手动安装它们。 Azure 模块除外。 默认情况下，此模块安装并提供有关 Azure 自动化所有 Azure 服务和活动的 cmdlet 的访问。

由于混合 Runbook 工作功能的主要目的是管理本地资源，因此您很可能需要安装支持这些资源的`PowerShellGet`模块，尤其是模块。 有关安装 Windows PowerShell 模块的信息，请参阅[Windows PowerShell](https://docs.microsoft.com/powershell/scripting/developer/windows-powershell)。

安装的模块必须位于`PSModulePath`环境变量引用的位置，以便混合辅助角色可以自动导入它们。 有关详细信息，请参阅在[PSModulePath 中安装模块](https://docs.microsoft.com/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-7)。

## <a name="next-steps"></a>后续步骤

* 若要了解如何配置 Runbook，使本地数据中心或其他云环境中的过程自动化，请参阅[在混合 Runbook 辅助角色上运行 Runbook](automation-hrw-run-runbooks.md)。
* 有关删除混合 Runbook 辅助角色的说明，请参阅[删除 Azure 自动化混合 Runbook 工作簿](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker)。
* 要了解如何对混合 Runbook 辅助角色进行故障排除，请参阅[故障排除 Windows 混合 Runbook 辅助角色](troubleshoot/hybrid-runbook-worker.md#windows)。
* 有关解决更新管理问题的其他步骤，请参阅[更新管理：故障排除](troubleshoot/update-management.md)。
