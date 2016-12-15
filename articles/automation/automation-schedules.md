---
title: "Azure 自动化中的计划 | Microsoft Docs"
description: "自动化计划用于安排自动启动 Azure 自动化中的 Runbook。 介绍如何创建和管理计划，以便在特定的时间或按重复计划自动启动 Runbook。"
services: automation
documentationcenter: 
author: MGoedtel
manager: jwhit
editor: tysonn
ms.assetid: 1c2da639-ad20-4848-920b-88e471b2e1d9
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: mgoedtel
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 26cbd468e6aa1613bd5f6c9944aedaafbd865a14


---
# <a name="scheduling-a-runbook-in-azure-automation"></a>在 Azure 自动化中计划 Runbook
若要将 Azure 自动化中的 Runbook 计划为在指定的时间启动，可以将它链接到一个或多个计划。 可以在 Azure 经典门户和 Azure 门户中将 Runbook 的计划配置为运行一次或按每小时或每天计划重复运行，此外，还可以将它们计划为每周、每月、每周或每月的特定几天，或者每月的某一天运行。  可将一个 Runbook 链接到多个计划，一个计划可以链接多个 Runbook。

> [!NOTE]
> 目前计划不支持 Azure 自动化 DSC 配置。
> 
> 

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell Cmdlet
下表中的 cmdlet 用于在 Azure 自动化中通过 Windows PowerShell 创建和管理计划。 它们作为 [Azure PowerShell 模块](../powershell-install-configure.md)的一部分提供。

| Cmdlet | 说明 |
|:--- |:--- |
| **Azure Resource Manager cmdlets** | |
| [Get-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603733.aspx) |检索计划。 |
| [New-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603577.aspx) |创建新计划。 |
| [Remove-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603691.aspx) |删除计划。 |
| [Set-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603566.aspx) |设置现有计划的属性。 |
| [Get-AzureRmAutomationScheduledRunbook](https://msdn.microsoft.com/library/mt619406.aspx) |检索计划 Runbook。 |
| [Register-AzureRmAutomationScheduledRunbook](https://msdn.microsoft.com/library/mt603575.aspx) |将 Runbook 与计划相关联。 |
| [Unregister-AzureRmAutomationScheduledRunbook](https://msdn.microsoft.com/library/mt603844.aspx) |将 Runbook 与计划取消关联。 |
| **Azure 服务管理 cmdlet** | |
| [Get-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690274.aspx) |检索计划。 |
| [New-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690271.aspx) |创建新计划。 |
| [Remove-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690279.aspx) |删除计划。 |
| [Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690270.aspx) |设置现有计划的属性。 |
| [Get-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn913778.aspx) |检索计划 Runbook。 |
| [Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn690265.aspx) |将 Runbook 与计划相关联。 |
| [Unregister-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn690273.aspx) |将 Runbook 与计划取消关联。 |

## <a name="creating-a-schedule"></a>创建计划
可以使用 Azure 门户、经典门户或 Windows PowerShell 为 Runbook 创建新计划。 也可以在使用 Azure 经典门户或 Azure 门户将 Runbook 链接到计划时，选择创建新计划。

> [!NOTE]
> 将计划与 Runbook 相关联时，自动化会将模块的当前版本存储在你的帐户中，并将模块链接到该计划。  这意味着，如果在创建计划时你的帐户中有 1.0 版的模块，然后将模块更新到版本 2.0，该计划将继续使用 1.0 版。  若要使用更新后的模块版本，必须创建新计划。 
> 
> 

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>在 Azure 门户中创建新计划
1. 在 Azure 门户中，从自动化帐户单击“资产”磁贴，打开“资产”边栏选项卡。
2. 单击“计划”磁贴，打开“计划”边栏选项卡。
3. 单击边栏选项卡顶部的“添加计划”。
4. 在“新建计划”边栏选项卡中，键入新计划的“名称”和（可选）“说明”。
5. 通过选择“一次”或“定期”来选择该计划是运行一次，还是按计划重复运行。  如果选择“一次”，请指定“开始时间”，然后单击“创建”。  如果选择“定期”，请指定“开始时间”以及想要 Runbook 重复运行的频率（按“小时”、按“天”、按“周”或按“月”）。  如果从下拉列表中选择了“周”或“月”，则“定期选项”将出现在边栏选项卡中，选择它后，会显示“定期选项”边栏选项卡，如果已选择“周”，则可以选择星期几。  如果选择了“月”，则可以在日历上按“工作日”或每月的特定日进行选择，并最后选择是否希望在每月的最后一天运行它，然后单击“确定”。   

### <a name="to-create-a-new-schedule-in-the-azure-classic-portal"></a>在 Azure 经典门户中创建新计划
1. 在 Azure 经典门户中，选择“自动化”，然后选择自动化帐户的名称。
2. 选择“资产”选项卡。
3. 在窗口底部，单击“添加设置”。
4. 单击“添加计划”。
5. 为新计划键入“名称”和（可选）“说明”。计划将运行“一次”，或者“每小时”、“每日”、“每周”或“每月”运行。
6. 指定“开始时间”，并根据选择的计划类型指定其他选项。

### <a name="to-create-a-new-schedule-with-windows-powershell"></a>使用 Windows PowerShell 创建新计划
可以使用 [New-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690271.aspx) cmdlet 在 Azure 自动化中为经典 Runbook 创建新计划，或使用 [New-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603577.aspx) cmdlet 在 Azure 门户中为 Runbook 创建新计划。 必须指定计划的开始时间以及它应运行的频率。

以下示例命令演示了如何使用 Azure Resource Manager cmdlet 创建每月 15 日和 30 日运行的计划。

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
    -DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"

以下示例命令演示了如何使用 Azure 服务管理 cmdlet 创建一个从 2015 年 1 月 20 日开始在每天下午 3:30 运行的新计划。

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName –StartTime "1/20/2016 15:30:00" –DayInterval 1

## <a name="linking-a-schedule-to-a-runbook"></a>将计划链接到 Runbook
可将一个 Runbook 链接到多个计划，一个计划可以链接多个 Runbook。 如果 Runbook 包含参数，则你可以提供这些参数的值。 必须为所有必需参数提供值，并可以为任何可选参数提供值。  此计划每次启动 Runbook 时，将使用这些值。  你可以将同一个 Runbook 附加到另一个计划，并指定不同的参数值。

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>使用 Azure 门户将计划链接到 Runbook
1. 在 Azure 门户中，从自动化帐户单击“Runbook”磁贴，打开“Runbook”边栏选项卡。
2. 单击要计划的 Runbook 的名称。
3. 如果 Runbook 当前未链接到计划，则系统会提供“创建新计划”或“链接到现有计划”选项。  
4. 如果 Runbook 有参数，可以选择选项“修改运行设置(默认值:Azure)”，此时将显示“参数”边栏选项卡，可以在其中相应地输入信息。  

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-classic-portal"></a>使用 Azure 经典门户将计划链接到 Runbook
1. 在 Azure 经典门户中，选择“自动化”，然后单击自动化帐户的名称。
2. 选择“Runbook”选项卡。
3. 单击要计划的 Runbook 的名称。
4. 单击“**计划**”选项卡。
5. 如果 Runbook 当前未链接到计划，则系统会提供“链接到新计划”或“链接到现有计划”选项。  如果 Runbook 当前已链接到计划，请单击窗口底部的“链接”来访问这些选项。
6. 如果 Runbook 包含参数，系统将提示你输入其值。  

### <a name="to-link-a-schedule-to-a-runbook-with-windows-powershell"></a>使用 Windows PowerShell 将计划链接到 Runbook
可以使用 [Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/azure/dn690265.aspx) 将计划链接到经典 Runbook，或者使用 [Register-AzureRmAutomationScheduledRunbook](https://msdn.microsoft.com/library/mt603575.aspx) cmdlet（适用于 Azure 门户中的 Runbook）。  可以使用 Parameters 参数指定 Runbook 参数的值。 有关指定参数值的详细信息，请参阅[在 Azure 自动化中启动 Runbook](automation-starting-a-runbook.md)。

以下示例命令演示了如何使用带参数的 Azure Resource Manager cmdlet 将计划链接到 Runbook。

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params `
    -ResourceGroupName "ResourceGroup01"
以下示例命令演示了如何使用带参数的 Azure 服务管理 cmdlet 链接计划。

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params

## <a name="disabling-a-schedule"></a>禁用计划
禁用某个计划后，链接到该计划的所有 Runbook 将不再按该计划运行。 可以手动禁用计划，也可以在创建带频率的计划时设置其过期时间。 到达过期时间时，将会禁用该计划。

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>从 Azure 门户禁用计划
1. 在 Azure 门户中，从自动化帐户单击“资产”磁贴，打开“资产”边栏选项卡。
2. 单击“计划”磁贴，打开“计划”边栏选项卡。
3. 单击某个计划的名称以打开“详细信息”边栏选项卡。
4. 将“已启用”更改为“否”。

### <a name="to-disable-a-schedule-from-the-azure-classic-portal"></a>从 Azure 经典门户禁用计划
可以在 Azure 经典门户中通过计划的“计划详细信息”页禁用该计划。

1. 在 Azure 经典门户中，选择“自动化”，然后单击自动化帐户的名称。
2. 选择“资产”选项卡。
3. 单击某个计划的名称以打开该计划的详细信息页。
4. 将“已启用”更改为“否”。

### <a name="to-disable-a-schedule-with-windows-powershell"></a>使用 Windows PowerShell 禁用计划
可以使用 [Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690270.aspx) cmdlet 更改经典 Runbook 的现有计划的属性，或者使用[Set-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603566.aspx) cmdlet（适用于 Azure 门户中的 Runbook）。 若要禁用计划，请为 **IsEnabled** 参数指定 **false**。

以下示例命令演示了如何使用 Azure Resource Manager cmdlet 禁用 Runbook 的计划。

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"

以下示例命令演示了如何使用 Azure 服务管理 cmdlet 禁用计划。

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    Set-AzureAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false

## <a name="next-steps"></a>后续步骤
* 若要在 Azure 自动化中开始使用 Runbook，请参阅[在 Azure 自动化中启动 Runbook](automation-starting-a-runbook.md) 




<!--HONumber=Nov16_HO3-->


