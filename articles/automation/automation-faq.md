---
title: Azure 自动化常见问题解答 |Microsoft Docs
description: 有关 Azure 自动化的常见问题的解答。
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 3fa29f3df5f0434c4c61e8d12adbb3f55156a29f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81405963"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure 自动化常见问题解答

此 Microsoft FAQ 列出了有关 Azure 自动化的常见问题。 如果对其功能有任何其他问题，请前往讨论论坛并发布你的问题。 当某个问题经常被问到时，我们会将该问题添加到本文中，以便可以轻松快捷地找到该问题。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可参阅[如何更新 Azure 自动化中的 Azure PowerShell 模块](automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="update-management-solution"></a>更新管理解决方案

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>我能防止意外的操作系统级升级吗？

在某些 Linux 变体（如 Red Hat Enterprise Linux）上，可能会通过包进行操作系统级别的升级。 这可能会导致更新管理运行的 OS 版本号发生更改。 由于更新管理使用相同的方法来更新管理员在 Linux 计算机上本地使用的包，因此此行为是有意的。

若要避免通过更新管理部署更新 OS 版本，请使用**排除**功能。

在 Red Hat Enterprise Linux 中，要排除的包名称`redhat-release-server.x86_64`是。

### <a name="why-arent-criticalsecurity-updates-applied"></a>为什么不应用严重/安全更新？

将更新部署到 Linux 计算机时，可以选择更新分类。 此选项筛选满足指定条件的更新。 部署更新时，会在计算机本地应用此筛选器。

由于更新管理在云中执行更新扩充，因此你可以将更新管理中的某些更新标记为具有安全影响，即使本地计算机没有该信息也是如此。 如果将关键更新应用于 Linux 计算机，则可能存在未标记为对该计算机产生安全影响的更新，因此不会应用这些更新。 但是，更新管理可能仍会将该计算机报告为不符合要求，因为它包含有关相关更新的其他信息。

通过更新分类部署更新不适用于 CentOS 的 RTM 版本。 若要正确部署 CentOS 的更新，请选择 "所有分类" 以确保应用更新。 对于 SUSE，仅选择**其他更新**作为分类可能会导致安装一些其他安全更新（如果首先需要与 zypper （包管理器）或其依赖项相关的安全更新）。 此行为是 zypper 的一项限制。 在某些情况下，可能需要重新运行更新部署，然后通过更新日志验证部署。

### <a name="can-i-deploy-updates-across-azure-tenants"></a>能否在 Azure 租户之间部署更新？

如果计算机需要在其他 Azure 租户报表中进行修补才能更新管理，则必须使用以下解决方法来安排这些计算机。 你可以使用[AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) cmdlet 和指定的`ForUpdateConfiguration`参数创建计划。 可以使用[AzAutomationSoftwareUpdateConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) cmdlet 并将另一个租户中的计算机传递给`NonAzureComputer`参数。 以下示例演示如何执行此操作。

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>后续步骤

如果此处未解答你的问题，你可以参考以下资源以了解其他问题和答案。

- [Azure 自动化](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)
- [反馈论坛](https://feedback.azure.com/forums/905242-update-management)
