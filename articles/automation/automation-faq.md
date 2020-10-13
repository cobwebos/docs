---
title: Azure 自动化常见问题解答 | Microsoft Docs
description: 本文提供了有关 Azure 自动化的常见问题解答。
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 76c8d09ef2ef0130ddac856a1f37f8b68d977494
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86186225"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure 自动化常见问题解答

本 Microsoft 常见问题解答列出了有关 Azure 自动化的常见问题解答。 如果对其功能还有其他任何问题，请转到论坛并发布问题。 当某个问题经常被问到时，我们会将该问题添加到本文中，以便可以轻松快捷地找到该问题。

## <a name="update-management"></a>更新管理

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>能否阻止意外的 OS 级别升级？

在某些 Linux 版本（例如 Red Hat Enterprise Linux）中，可以通过包执行 OS 级别的升级。 这可能会导致运行更新管理并更改 OS 版本号。 由于更新管理使用相同的方法来更新管理员将在 Linux 计算机本地使用的包，因此，此行为是有意实施的。

若要避免通过更新管理部署来更新 OS 版本，可以使用“排除”功能。

在 Red Hat Enterprise Linux 中，要排除的包名称为 `redhat-release-server.x86_64`。

### <a name="why-arent-criticalsecurity-updates-applied"></a>为什么没有应用关键更新/安全更新？

将更新部署到 Linux 计算机时，可以选择更新分类。 该选项会筛选满足指定条件的更新。 部署更新时，会在计算机本地应用此筛选器。

由于更新管理在云中执行更新扩充，因此可以将更新管理中的某些更新标记为存在安全影响，但是本地计算机上不会显示该信息。 如果向 Linux 计算机应用关键更新，可能有些更新不会被标记为对该计算机具有安全影响，因此不会应用这些更新。 但是，更新管理仍可能报告该计算机不合规，因为其中包含相关更新的其他信息。

在 CentOS 的 RTM 版本上无法按更新分类部署更新。 要为 CentOS 正确部署更新，请选择所有分类以确保应用更新。 对于 SUSE，如果首先要求与 zypper（包管理器）或其依赖项相关的安全更新，则仅选择“其他更新”作为分类可能会导致安装某些其他安全更新。 此行为是 zypper 的一项限制。 在某些情况下，可能需要重新运行更新部署，然后通过更新日志验证部署。

### <a name="can-i-deploy-updates-across-azure-tenants"></a>能否在 Azure 租户之间部署更新？

如果你在向“更新管理”进行报告的另一个 Azure 租户中存在需要修补的计算机，则必须使用以下解决方法来计划它们。 可以将 [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) cmdlet 与指定的 `ForUpdateConfiguration` 参数一起使用来创建计划。 可以使用 [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) cmdlet，并将另一个租户中的计算机传递给 `NonAzureComputer` 参数。 以下示例演示如何执行此操作。

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>后续步骤

如果未在此处找到问题的答案，则可以参考下方针对其他问题和答案的资源。

- [Azure 自动化](/answers/topics/azure-automation.html)
- [反馈论坛](https://feedback.azure.com/forums/905242-update-management)
