---
title: Azure 自动化常见问题 |微软文档
description: 回答有关 Azure 自动化 的常见问题的解答。
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 3fa29f3df5f0434c4c61e8d12adbb3f55156a29f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405963"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure 自动化常见问题

此 Microsoft 常见问题解答是关于 Azure 自动化的常见问题列表。 如果您对其功能有任何疑问，请前往论坛并发布您的问题。 当某个问题经常被问到时，我们会将该问题添加到本文中，以便可以轻松快捷地找到该问题。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可以使用["如何更新 Azure 自动化 中的 Azure PowerShell"模块](automation-update-azure-modules.md)将模块更新到最新版本。

## <a name="update-management-solution"></a>更新管理解决方案

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>我能否防止意外的操作系统级升级？

在某些 Linux 变体（如红帽企业 Linux）上，操作系统级别的升级可能通过包进行。 这可能会导致运行更新管理，其中操作系统版本号会更改。 由于更新管理使用与管理员在 Linux 计算机上本地使用的软件包相同的方法来更新包，因此此行为是有意的。

要避免通过更新管理部署更新操作系统版本，请使用**排除**功能。

在红帽企业 Linux 中，要排除的`redhat-release-server.x86_64`包名称为 。

### <a name="why-arent-criticalsecurity-updates-applied"></a>为什么没有应用关键/安全更新？

将更新部署到 Linux 计算机时，可以选择更新分类。 此选项筛选满足指定条件的更新。 部署更新时，会在计算机本地应用此筛选器。

由于更新管理在云中执行更新扩充，因此您可以在更新管理中标记某些更新具有安全影响，即使本地计算机没有该信息也是如此。 如果将关键更新应用于 Linux 计算机，则可能有一些更新未标记为对该计算机具有安全影响，因此不会应用这些更新。 但是，更新管理可能仍报告该计算机不符合要求，因为它包含有关相关更新的其他信息。

通过更新分类部署更新在 CentOS 的 RTM 版本上不起作用。 要正确部署 CentOS 的更新，请选择所有分类以确保应用更新。 对于 SUSE，如果首先需要与 zypper（包管理器）或其依赖项相关的安全更新，则选择"仅**选择其他更新**作为分类"可能会导致安装一些其他安全更新。 此行为是 zypper 的一项限制。 在某些情况下，您可能需要重新运行更新部署，然后通过更新日志验证部署。

### <a name="can-i-deploy-updates-across-azure-tenants"></a>是否可以跨 Azure 租户部署更新？

如果计算机需要在另一个 Azure 租户中进行修补，报告给更新管理，则必须使用以下解决方法来计划它们。 您可以使用["新建-AzAutomation计划](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0)"cmdlet 与指定的`ForUpdateConfiguration`参数一起创建计划。 您可以使用[New-AzAutomationSoftwareUpdate 配置](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0)cmdlet 并将另一个租户中的计算机传递给参数`NonAzureComputer`。 以下示例演示如何执行此操作。

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>后续步骤

如果您的问题未在此处回答，您可以参考以下来源以获取其他问题和答案。

- [Azure 自动化](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)
- [反馈论坛](https://feedback.azure.com/forums/905242-update-management)
