---
title: 管理 Azure 自动化中的计划
description: 本文介绍如何在 Azure 自动化中创建和使用计划。
services: automation
ms.subservice: shared-capabilities
ms.date: 09/10/2020
ms.topic: conceptual
ms.openlocfilehash: 844a45c9b596522b949443b6edc311308da7806c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90004606"
---
# <a name="manage-schedules-in-azure-automation"></a>管理 Azure 自动化中的计划

要将 Azure 自动化中的 Runbook 计划为在指定的时间启动，可以将它链接到一个或多个计划。 可以在 Azure 门户中为 runbook 配置计划，使其运行一次或者每小时或每日定期运行。 还可以将它们计划为每周、每月、每周的特定几天或每月的特定几天或每月的特定一天运行。 可将一个 Runbook 链接到多个计划，一个计划可以链接多个 Runbook。

> [!NOTE]
> Azure 自动化支持夏令时并针对自动化操作进行适当的计划。

> [!NOTE]
> 当前未为 Azure 自动化 DSC 配置启用计划。

## <a name="powershell-cmdlets-used-to-access-schedules"></a>用于访问计划的 PowerShell cmdlet

下表中的 cmdlet 使用 PowerShell 创建和管理自动化计划。 它们作为 [Az 模块](modules.md#az-modules)的一部分提供。

| Cmdlet | 说明 |
|:--- |:--- |
| [Get-AzAutomationSchedule](/powershell/module/Az.Automation/Get-AzAutomationSchedule) |检索计划。 |
| [Get-AzAutomationScheduledRunbook](/powershell/module/az.automation/get-azautomationscheduledrunbook) |检索计划 Runbook。 |
| [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) |创建新计划。 |
| [Register-AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook) |将 Runbook 与计划相关联。 |
| [Remove-AzAutomationSchedule](/powershell/module/Az.Automation/Remove-AzAutomationSchedule) |删除计划。 |
| [Set-AzAutomationSchedule](/powershell/module/Az.Automation/Set-AzAutomationSchedule) |设置现有计划的属性。 |
| [Unregister-AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook) |将 Runbook 与计划取消关联。 |

## <a name="create-a-schedule"></a>创建计划

可以在 Azure 门户中或使用 PowerShell 为 runbook 创建新计划。 为了避免影响 runbook 及其自动化进程，你应该首先测试已将计划与专用于测试的自动化帐户进行链接的 runbook。 测试将验证计划 runbook 是否继续正常工作。 如果出现问题，可以在将更新的 runbook 版本迁移到生产环境之前进行故障排除并应用所需的更改。

> [!NOTE]
> 自动化帐户不会自动获取模块的任何新版本，除非你通过从[模块](../automation-update-azure-modules.md)中选择“更新 Azure 模块”选项来手动更新它们。 当运行新的计划作业时，Azure 自动化将在自动化帐户中使用最新模块。 

### <a name="create-a-new-schedule-in-the-azure-portal"></a>在 Azure 门户中创建新计划

1. 在自动化帐户的左侧窗格中，选择“共享资源”下的“计划” 。
2. 在“计划”页上选择“添加计划”。
3. 在“新建计划”页中，输入新计划的名称，还可以选择输入新计划的说明。

    >[!NOTE]
    >自动化计划目前不支持在计划名称中使用特殊字符。
    >

4. 通过选择“一次”或“定期”来选择该计划是运行一次，还是按计划重复运行。 如果选择“一次”，请指定开始时间，然后选择“创建” 。 如果选择“定期”，请指定开始时间。 对于“重复间隔”，请选择想要 runbook 重复运行的频率。 选择“时”、“天”、“周”或“月”。

    * 如果选择“周”，则会显示一周中可供选择的日期。 根据需要选择天数。 计划的第一次运行将在开始时间之后选择的第一天进行。 例如，若要选择周末计划，请选择“星期六”和“星期日”。

    ![设置周末定期计划](../media/schedules/week-end-weekly-recurrence.png)

    * 如果选择“月”，则会看到不同的选项。 对于“每月进行次数”选项，请选择“每月天数”或“每周天数”  。 如果选择“每月天数”，则会显示一个可根据需要选择天数的日历。 如果选择当月不存在的日期（例如 31 日），则计划将不会运行。 如果希望计划在最后一天运行，请在“在月份的最后一天运行”下选择“是” 。 如果选择“每周天数”，则会显示“重复间隔”选项 。 选择“第一”、“第二”、“第三”、“第四”或“最后”    。 最后选择一天进行重复。

    ![在每月第一天、第十五天和最后一天运行的每月计划](../media/schedules/monthly-first-fifteenth-last.png)

5. 完成后，请选择“创建”。

### <a name="create-a-new-schedule-with-powershell"></a>使用 PowerShell 创建新计划

使用 [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) cmdlet 创建计划。 必须指定计划的开始时间以及运行频率。 以下示例演示如何创建许多不同的计划场景。

>[!NOTE]
>自动化计划目前不支持在计划名称中使用特殊字符。
>

#### <a name="create-a-one-time-schedule"></a>创建一次性计划

以下示例创建一个一次性计划。

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>创建定期计划

以下示例演示如何创建每天下午 1:00 运行且持续一年的定期计划。

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>创建每周重复执行计划

以下示例演示如何创建仅在工作日运行的每周计划。

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>创建每周定期在周末运行的计划

以下示例演示如何创建仅在周末运行的每周计划。

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-the-first-fifteenth-and-last-days-of-the-month"></a>创建在每月的第一天、第十五天和最后一天运行的定期计划

以下示例演示如何创建在每月的第一天、第十五天和最后一天运行的定期计划。

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="link-a-schedule-to-a-runbook"></a>将计划链接到 Runbook

可将一个 Runbook 链接到多个计划，一个计划可以链接多个 Runbook。 如果 Runbook 包含参数，则可以提供这些参数的值。 必须为所有必需参数提供值，还可以为任何可选参数提供值。 此计划每次启动 Runbook 时，都将使用这些值。 可以将同一个 Runbook 附加到另一个计划，并指定不同的参数值。

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>使用 Azure 门户将计划链接到 runbook

1. 在 Azure 门户中，从你的自动化帐户中，在“流程自动化”下选择“Runbook” 。
1. 选择要计划的 runbook 的名称。
1. 如果 runbook 当前未链接到计划，则系统会提供“创建新计划”或“链接到现有计划”选项。
1. 如果 runbook 有参数，可以选择选项“修改运行设置(默认值:Azure)”，此时会显示“参数”窗格 。 可以在其中输入参数信息。

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>使用 PowerShell 将计划链接到 runbook

使用 [Register-AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook) cmdlet 链接计划。 可以使用 Parameters 参数指定 Runbook 参数的值。 有关如何指定参数值的详细信息，请参阅[在 Azure 自动化中启动 Runbook](../start-runbooks.md)。
以下示例演示了如何使用带参数的 Azure 资源管理器 cmdlet 将计划链接到 runbook。

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="schedule-runbooks-to-run-more-frequently"></a>计划 runbook 以使其更频繁地运行

可为 Azure 自动化中的计划配置的最频繁间隔为一小时。 如果你需要运行比这更频繁的计划，有两个选项：

* 为 runbook 创建 [Webhook](../automation-webhooks.md)，并使用 [Azure 逻辑应用](../../logic-apps/logic-apps-overview.md)调用 webhook。 Azure 逻辑应用提供更精细的粒度来定义计划。

* 创建四个计划，每个计划在 15 分钟内开始，每小时运行一次。 此方案使用不同的计划，可让 Runbook 每隔 15 分钟运行一次。

## <a name="disable-a-schedule"></a>禁用计划

禁用计划后，链接到该计划的所有 Runbook 不再按该计划运行。 可以手动禁用计划，也可以在创建带频率的计划时设置其过期时间。 到达过期时间时，会禁用该计划。

### <a name="disable-a-schedule-from-the-azure-portal"></a>从 Azure 门户禁用计划

1. 在自动化帐户的左侧窗格中，选择“共享资源”下的“计划” 。
1. 选择某个计划的名称以打开详细信息窗格。
1. 将“已启用”更改为“否”。

> [!NOTE]
> 如果要禁用开始时间为过去时间的计划，则必须在保存之前将开始日期更改为将来的时间。

### <a name="disable-a-schedule-with-powershell"></a>使用 PowerShell 禁用计划

可使用 [Set-AzAutomationSchedule](/powershell/module/Az.Automation/Set-AzAutomationSchedule) cmdlet 更改现有计划的属性。 若要禁用计划，请为 `IsEnabled` 参数指定 False。

以下示例演示了如何使用 Azure 资源管理器 cmdlet 禁用 runbook 的计划。

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="remove-a-schedule"></a>删除计划

准备删除计划时，可以使用 Azure 门户或 PowerShell。 请记住，只能删除已禁用的计划，如前一部分中所述。

### <a name="remove-a-schedule-using-the-azure-portal"></a>使用 Azure 门户删除计划

1. 在自动化帐户的左侧窗格中，选择“共享资源”下的“计划” 。
2. 选择某个计划的名称以打开详细信息窗格。
3. 单击 **“删除”** 。

### <a name="remove-a-schedule-with-powershell"></a>使用 PowerShell 删除计划

可以按如下所示使用 `Remove-AzAutomationSchedule` cmdlet 来删除现有计划。

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Remove-AzAutomationSchedule -AutomationAccountName $automationAccountName `
-Name $scheduleName -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>后续步骤

* 若要了解有关用于访问计划的 cmdlet 的详细信息，请参阅[在 Azure 自动化中管理模块](modules.md)。
* 有关 runbook 的常规信息，请参阅[在 Azure 自动化中执行 Runbook](../automation-runbook-execution.md)。