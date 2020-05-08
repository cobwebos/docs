---
title: 在 Azure 自动化中管理计划
description: 了解如何在 Azure 自动化中创建和管理计划，以便可以在特定时间或按重复计划自动启动 runbook。
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4cd6d4236b95a17f404df13e8b50daf989cf6072
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82652113"
---
# <a name="manage-schedules-in-azure-automation"></a>在 Azure 自动化中管理计划

要将 Azure 自动化中的 Runbook 计划为在指定的时间启动，可以将它链接到一个或多个计划。 可以将计划配置为运行一次，或在 Azure 门户中为 runbook 定期或每日计划运行一次。 还可以将它们计划为每周、每月、每周的特定几天或每月的特定几天或每月的特定一天运行。 可将一个 Runbook 链接到多个计划，一个计划可以链接多个 Runbook。

> [!NOTE]
> 计划当前不支持 Azure Automation DSC 配置。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az module 安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于你的自动化帐户，你可以通过使用[如何在 Azure 自动化中更新 Azure PowerShell 模块](../automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="powershell-cmdlets-used-to-access-schedules"></a>用于访问计划的 PowerShell cmdlet

下表中的 cmdlet 通过 PowerShell 创建和管理自动化计划。 它们作为[Az 模块](modules.md#az-modules)的一部分提供。 

| Cmdlet | 说明 |
|:--- |:--- |
| [AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationSchedule?view=azps-3.7.0) |检索计划。 |
| [AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationscheduledrunbook?view=azps-3.7.0) |检索计划 Runbook。 |
| [新-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) |创建新计划。 |
| [注册-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) |将 Runbook 与计划相关联。 |
| [AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationSchedule?view=azps-3.7.0) |删除计划。 |
| [AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) |设置现有计划的属性。 |
| [取消注册-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook?view=azps-3.7.0) |将 Runbook 与计划取消关联。 |

## <a name="create-a-schedule"></a>创建计划

可以在 Azure 门户中或使用 PowerShell 为 runbook 创建新计划。 若要避免影响 runbook 及其自动执行的进程，你应该首先测试已链接计划的任何 runbook 以及专用于测试的自动化帐户。 测试将验证计划的 runbook 是否继续正常工作。 如果出现问题，你可以在将更新的 runbook 版本迁移到生产之前，对所需的任何更改进行故障排除和应用。

> [!NOTE]
> 自动化帐户不会自动获取模块的任何新版本，除非已通过从**模块**中选择[更新 Azure 模块](../automation-update-azure-modules.md)选项来手动更新它们。 当运行新的计划作业时，Azure 自动化将在自动化帐户中使用最新模块。 

### <a name="create-a-new-schedule-in-the-azure-portal"></a>在 Azure 门户中创建新计划

1. 在 Azure 门户中，从你的自动化帐户中，从左侧的“共享资源”部分下选择“计划”。********
1. 选择页面顶部的 "**添加计划**"。
1. 在 "**新建计划**" 窗格上，输入一个名称，并根据需要输入新计划的描述。
1. 选择计划是运行一次还是按重复计划运行，方法是选择 "**一次**" 或 "**重复**"。 如果选择 "**一次**"，请指定 "开始时间"，然后选择 "**创建**"。 如果选择 "**重复**执行"，请指定开始时间。 对于 **"重复执行"**，请选择希望 runbook 重复的频率。 依次选择 "小时"、"天"、"周" 或 "月"。
    1. 如果选择 "**周**"，则会显示一周中的哪几天供你选择。 根据需要选择天数。 计划的第一次运行将在开始时间之后选择的第一天进行。 例如，若要选择周末计划，请选择 "星期六" 和 "星期日"。
    
       ![设置周末重复计划](../media/schedules/week-end-weekly-recurrence.png)

    2. 如果选择 "**月**"，则会提供不同的选项。 对于“每月进行次数”选项，请选择“每月天数”或“每周天数”************。 如果选择 "**月份日期**"，则会出现一个日历，以便可以根据需要选择任意数量的日期。 如果选择当月不存在的日期（例如 31 日），则计划将不会运行。 如果希望计划在最后一天运行，请选择 **"是**"，然后在 "**月" 的最后一天运行**。 如果**选择 "工作日"，** 则会出现 "**重复间隔**" 选项。 选择“第一”、“第二”、“第三”、“第四”或“最后”********************。 最后，选择要在其上重复的日期。

       ![在每月 1 号、15 号和最后一天运行的计划](../media/schedules/monthly-first-fifteenth-last.png)

1. 完成后，选择“创建”****。

### <a name="create-a-new-schedule-with-powershell"></a>使用 PowerShell 创建新计划

使用[AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) cmdlet 创建计划。 必须指定计划的开始时间以及运行频率。 以下示例展示了如何创建许多不同的计划方案。

#### <a name="create-a-one-time-schedule"></a>创建一次性计划

下面的示例创建一个一次性计划。

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>创建重复计划

下面的示例演示如何创建一个定期计划，该计划将在一年的下午1:00 运行。

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>创建每周重复计划

下面的示例演示如何创建仅在工作日运行的每周计划。

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>创建在工作日运行的每周重复计划

下面的示例演示如何创建仅在周末运行的每周计划。

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-the-first-fifteenth-and-last-days-of-the-month"></a>为每月的第一天、第15天和最后一天创建重复计划

下面的示例演示如何创建在一个月的第一天、第15天和最后一天运行的定期计划。

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="link-a-schedule-to-a-runbook"></a>将计划链接到 runbook

可将一个 Runbook 链接到多个计划，一个计划可以链接多个 Runbook。 如果 runbook 具有参数，则可以为其提供值。 必须为任何必需的参数提供值，并且还可以为任何可选参数提供值。 此计划每次启动 Runbook 时，都将使用这些值。 可以将同一个 Runbook 附加到另一个计划，并指定不同的参数值。

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>使用 Azure 门户将计划链接到 runbook

1. 在 Azure 门户中，从自动化帐户中选择 "**进程自动化**" 下的 " **runbook** "。
1. 选择要计划的 runbook 的名称。
1. 如果 runbook 当前未链接到计划，则将提供创建新计划或链接到现有计划的选项。
1. 如果 runbook 具有参数，则可以选择 "**修改运行设置（默认值： Azure）** " 选项，此时将显示 "**参数**" 窗格。 可在此处输入参数信息。

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>使用 PowerShell 将计划链接到 runbook

使用[AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) cmdlet 链接计划。 可以使用 Parameters 参数指定 Runbook 参数的值。 有关如何指定参数值的详细信息，请参阅[在 Azure 自动化中启动 Runbook](../automation-starting-a-runbook.md)。
下面的示例演示如何使用带参数的 Azure 资源管理器 cmdlet 将计划链接到 runbook。

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="schedule-runbooks-to-run-more-frequently"></a>计划 runbook 的运行频率

可在 Azure 自动化中配置计划的最常见时间间隔为1小时。 如果需要的计划运行频率比此更频繁，则有两个选项：

* 为 runbook 创建[webhook](../automation-webhooks.md) ，并使用[Azure 逻辑应用](../../logic-apps/logic-apps-overview.md)调用 webhook。 Azure 逻辑应用提供更精细的粒度来定义计划。

* 创建四个计划，每个计划在15分钟内开始，每隔一小时运行一次。 此方案使用不同的计划，可让 Runbook 每隔 15 分钟运行一次。

## <a name="disable-a-schedule"></a>禁用计划

禁用计划后，链接到该计划的所有 Runbook 不再按该计划运行。 可以手动禁用计划，也可以在创建带频率的计划时设置其过期时间。 到达过期时间时，会禁用该计划。

### <a name="disable-a-schedule-from-the-azure-portal"></a>从 Azure 门户禁用计划

1. 在自动化帐户中，选择 "**共享资源**" 下的 "**计划**"。
1. 选择计划的名称以打开 "详细信息" 窗格。
1. 将“已启用”**** 更改为“否”****。

> [!NOTE]
> 如果要禁用一个开始时间在过去的计划，则必须将开始日期更改为将来的某个时间，然后再保存。

### <a name="disable-a-schedule-with-powershell"></a>使用 PowerShell 禁用计划

使用[AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) cmdlet 更改现有计划的属性。 若要禁用计划，请为`IsEnabled`参数指定 False。

下面的示例演示如何使用 Azure 资源管理器 cmdlet 禁用 runbook 的计划。

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="remove-a-schedule"></a>删除计划

准备删除计划时，可以使用 Azure 门户或 PowerShell。 请记住，只能删除已禁用的计划，如前一部分中所述。

### <a name="remove-a-schedule-using-the-azure-portal"></a>使用 Azure 门户删除计划

1. 在自动化帐户中，选择 "**共享资源**" 下的 "**计划**"。
2. 单击某个计划的名称以打开详细信息窗格。
3. 单击 **“删除”** 。

### <a name="remove-a-schedule-with-powershell"></a>使用 PowerShell 删除计划

可以使用如下所`Remove-AzAutomationSchedule`示的 cmdlet 删除现有计划。 

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Remove-AzAutomationSchedule -AutomationAccountName $automationAccountName `
-Name $scheduleName -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>后续步骤

* 若要了解有关用于访问计划的 cmdlet 的详细信息，请参阅[在 Azure 自动化中管理模块](modules.md)。
* 有关 runbook 的常规信息，请参阅[在 Azure 自动化中执行 Runbook](../automation-runbook-execution.md)。
