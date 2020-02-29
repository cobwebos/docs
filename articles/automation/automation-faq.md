---
title: Azure 自动化常见问题解答 |Microsoft Docs
description: 有关 Azure 自动化的常见问题的解答。
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 129a5316c2e7be329b479c79706791e993d20b74
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925808"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure 自动化常见问题解答

此 Microsoft FAQ 列出了有关 Azure 自动化的常见问题。 如果对其功能有任何其他问题，请前往讨论论坛并发布你的问题。 当某个问题经常被问到时，我们会将该问题添加到本文中，以便可以轻松快捷地找到该问题。

## <a name="update-management-solution"></a>更新管理解决方案

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>我能防止意外的操作系统级升级吗？

在某些 Linux 变体（如 Red Hat Enterprise Linux）上，可能会通过包进行操作系统级别的升级。 这可能会导致运行更新管理并更改 OS 版本号。 由于更新管理使用相同的方法来更新管理员将在 Linux 计算机上本地使用的包，因此此行为是有意的。

若要避免通过更新管理部署更新 OS 版本，请使用**排除**功能。

在 Red Hat Enterprise Linux 中，要排除的包名称为 redhat-release-server.x86_64。

### <a name="why-arent-criticalsecurity-updates-applied"></a>为什么不应用严重/安全更新？

将更新部署到 Linux 计算机时，可以选择更新分类。 此选项筛选应用到满足指定条件的计算机的更新。 部署更新时，会在计算机本地应用此筛选器。

由于更新管理在云中执行更新扩充，因此某些更新可能会在更新管理中标记为具有安全影响，即使本地计算机没有该信息也是如此。 因此，如果将关键更新应用于 Linux 计算机，则可能存在未标记为对该计算机产生安全影响的更新，因此不会应用更新。 但是，更新管理可能仍会将该计算机报告为不合规，因为它包含有关相关更新的其他信息。

通过更新分类部署更新不适用于 CentOS 的 RTM 版本。 若要正确部署 CentOS 的更新，请选择 "所有分类" 以确保应用更新。 对于 SUSE，*仅*选择**其他更新**作为分类可能会导致某些安全更新（如果首先需要与 zypper （包管理器）或其依赖项相关的安全更新）。 此行为是 zypper 的一项限制。 在某些情况下，可能需要重新运行更新部署。 若要验证，请检查更新日志。

### <a name="can-i-deploy-updates-across-azure-tenants"></a>能否在 Azure 租户之间部署更新？

如果在另一个 Azure 租户报表中有要更新管理需要修补的计算机，则必须使用以下解决方法来计划这些计算机。 你可以将[或者使用 set-azurermautomationschedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) cmdlet 与 `-ForUpdate` 开关一起使用来创建一个计划，并使用[AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) cmdlet 并将另一个租户中的计算机传递给 `-NonAzureComputer` 参数。 以下示例介绍如何执行此操作：

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>后续步骤

如果此处未解答你的问题，你可以参考以下论坛获取其他问题和答案。

- [Azure 自动化](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)

有关更新管理解决方案的一般反馈，请访问[反馈论坛](https://feedback.azure.com/forums/905242-update-management)。