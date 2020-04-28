---
title: 在非工作时间启动/停止 Vm 解决方案
description: 此 VM 管理解决方案按计划启动和停止 Azure 虚拟机，并主动监视 Azure Monitor 日志。
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 631c9b37cf1fec0d39c3c362c6bc303a576d6b7c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187324"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>在 Azure 自动化中，在非工作时间启动/停止 Vm 解决方案

在非**工作时间启动/停止 vm**解决方案启动或停止你的 Azure 虚拟机。 它将根据用户定义的计划启动或停止计算机，通过 Azure Monitor 日志提供见解，并通过使用[操作组](../azure-monitor/platform/action-groups.md)发送可选电子邮件。 此解决方案支持 Azure 资源管理器和经典 Vm，适用于大多数情况。 

此解决方案使用[get-azurermvm](https://docs.microsoft.com/powershell/module/azurerm.compute/start-azurermvm?view=azurermps-6.13.0) cmdlet 来启动 vm。 它使用[get-azurermvm](https://docs.microsoft.com/powershell/module/AzureRM.Compute/Stop-AzureRmVM?view=azurermps-6.13.0)停止 vm。

> [!NOTE]
> 在非**工作时间启动/停止 vm**解决方案已更新，以支持可用 Azure 模块的最新版本。

此解决方案为要优化其 VM 成本的用户提供了分散的低成本自动化选项。 使用此解决方案可以：

- [计划要启动和停止的 vm](automation-solution-vm-management-config.md#schedule)。
- [使用 Azure 标记](automation-solution-vm-management-config.md#tags)按升序计划要启动和停止的 vm （不支持经典 vm）。
- 基于[CPU 使用率较低](automation-solution-vm-management-config.md#cpuutil)的 Autostop vm。

下面是对当前解决方案的限制：

- 它管理任何区域中的 Vm，但只能在与 Azure 自动化帐户相同的订阅中使用。
- 它在 Azure 和 Azure 政府版中提供给支持 Log Analytics 工作区、Azure 自动化帐户和警报的任何区域。 Azure 政府地区目前不支持电子邮件功能。

## <a name="solution-prerequisites"></a>解决方案先决条件

此解决方案的 Runbook 使用 [Azure 运行方式帐户](automation-create-runas-account.md)。 运行方式帐户是首选的身份验证方法，因为它使用证书身份验证而不是可能会过期或经常更改的密码。

建议你在非**工作时间解决方案中为启动/停止 vm**使用单独的自动化帐户。 Azure 模块版本经常升级，其参数可能会更改。 解决方案不会在相同节奏上升级，它可能不适用于它所使用的更新版本的 cmdlet。 建议你在测试自动化帐户中测试模块更新，然后再将其导入到生产自动化帐户中。

## <a name="solution-permissions"></a>解决方案权限

你必须具有特定的权限才能在非**工作时间部署启动/停止 vm**解决方案。 如果解决方案使用预先创建的自动化帐户，并在部署过程中创建新帐户和工作区时所需的权限 Log Analytics 工作区，则权限会有所不同。 

如果你是订阅中的参与者和 Azure Active Directory 租户中的全局管理员，则无需配置权限。 如果你没有这些权限或需要配置自定义角色，请确保具有以下所述的权限。

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>预先存在的自动化帐户和 Log Analytics 工作区的权限

若要将非工作**时间启动/停止 vm**解决方案部署到现有自动化帐户并 Log Analytics 工作区，则部署解决方案的用户需要对资源组作用域具有以下权限。 若要了解有关角色的详细信息，请参阅[Azure 资源的自定义角色](../role-based-access-control/custom-roles.md)。

| 权限 | 范围|
| --- | --- |
| Microsoft.Automation/automationAccounts/read | 资源组 |
| Microsoft.Automation/automationAccounts/variables/write | 资源组 |
| Microsoft.Automation/automationAccounts/schedules/write | 资源组 |
| Microsoft.Automation/automationAccounts/runbooks/write | 资源组 |
| Microsoft.Automation/automationAccounts/connections/write | 资源组 |
| Microsoft.Automation/automationAccounts/certificates/write | 资源组 |
| Microsoft.Automation/automationAccounts/modules/write | 资源组 |
| Microsoft.Automation/automationAccounts/modules/read | 资源组 |
| AutomationAccounts/jobSchedules/write | 资源组 |
| Microsoft.Automation/automationAccounts/jobs/write | 资源组 |
| Microsoft.Automation/automationAccounts/jobs/read | 资源组 |
| Microsoft.OperationsManagement/solutions/write | 资源组 |
| Microsoft.operationalinsights/工作区/* | 资源组 |
| DiagnosticSettings/写入 | 资源组 |
| Microsoft.Insights/ActionGroups/Write | 资源组 |
| Microsoft Insights/ActionGroups/read | 资源组 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 资源组 |
| Microsoft.Resources/deployments/* | 资源组 |

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>新自动化帐户和新 Log Analytics 工作区的权限

可以在非工作**时间解决方案中将启动/停止 vm**部署到新的自动化帐户，并 Log Analytics 工作区。 在这种情况下，部署解决方案的用户需要在前面的部分中定义的权限，以及本部分中定义的权限。 

部署解决方案的用户需要下列角色：

- 订阅上的共同管理员。 如果要管理经典 Vm，则需要此角色才能创建经典运行方式帐户。 默认情况下不再创建[经典运行方式帐户](automation-create-standalone-account.md#create-a-classic-run-as-account)。
- [Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md)应用程序开发人员角色的成员身份。 有关配置运行方式帐户的详细信息，请参阅[配置运行方式帐户的权限](manage-runas-account.md#permissions)。
- 订阅上的参与者或以下权限。

| 权限 |范围|
| --- | --- |
| Microsoft. 授权/操作/读取 | 订阅|
| Microsoft.Authorization/permissions/read |订阅|
| Microsoft.Authorization/roleAssignments/read | 订阅 |
| Microsoft.Authorization/roleAssignments/write | 订阅 |
| Microsoft.Authorization/roleAssignments/delete | 订阅 |
| Microsoft.Automation/automationAccounts/connections/read | 资源组 |
| Microsoft.Automation/automationAccounts/certificates/read | 资源组 |
| Microsoft.Automation/automationAccounts/write | 资源组 |
| Microsoft.OperationalInsights/workspaces/write | 资源组 |

## <a name="solution-components"></a>解决方案组件

在非**工作时间启动/停止 vm 解决方案**包括预配置的 runbook、计划以及与 Azure Monitor 日志的集成。 你可以使用这些元素定制 Vm 的启动和关闭以满足你的业务需求。

### <a name="runbooks"></a>Runbook

下表列出了解决方案部署到自动化帐户的 runbook。 不要对 runbook 代码进行更改。 应该对新功能编写自己的 Runbook。

> [!IMPORTANT]
> 不要直接运行其名称后面附加有**子级**的任何 runbook。

所有父 runbook 都包含`WhatIf`参数。 如果设置为 True，则参数支持详细说明 runbook 在没有参数的情况下运行时执行的确切行为，并验证是否针对正确的 Vm。 仅当`WhatIf`参数设置为 False 时，runbook 才执行其定义的操作。

|Runbook | 参数 | 说明|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | 从父 runbook 调用。 对于自动停止方案，此 runbook 针对每个资源创建警报。|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf：True 或 False  | 在目标订阅或资源组中的 VM 上创建或更新 Azure 警报规则。 <br> `VMList`是以逗号分隔的 Vm 列表。 例如，`vm1, vm2, vm3` 。<br> `WhatIf`启用 runbook 逻辑验证而不执行。|
|AutoStop_Disable | 无 | 禁用自动停止警报和默认计划。|
|AutoStop_VM_Child | WebHookData | 从父 runbook 调用。 警报规则调用此 runbook 以停止经典 VM。|
|AutoStop_VM_Child_ARM | WebHookData |从父 runbook 调用。 警报规则调用此 runbook 以停止 VM。  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> Action：Start 或 Stop<br> VMList  | 通过云服务在经典 VM 组中执行操作启动或停止。 |
|ScheduledStartStop_Child | VMName <br> Action：Start 或 Stop <br> ResourceGroupName | 从父 runbook 调用。 针对计划的停止执行启动或停止操作。|
|ScheduledStartStop_Child_Classic | VMName<br> Action：Start 或 Stop<br> ResourceGroupName | 从父 runbook 调用。 针对经典 Vm 的计划停止执行启动或停止操作。 |
|ScheduledStartStop_Parent | Action：Start 或 Stop <br>VMList <br> WhatIf：True 或 False | 启动或停止订阅中的所有 Vm。 编辑变量`External_Start_ResourceGroupNames`并`External_Stop_ResourceGroupNames`仅在这些目标资源组上执行。 还可以通过更新`External_ExcludeVMNames`变量来排除特定的 vm。|
|SequencedStartStop_Parent | Action：Start 或 Stop <br> WhatIf：True 或 False<br>VMList| 在要对其进行序列化启动/停止活动的每个 VM 上创建名为**sequencestart**和**sequencestop**的标记。 这些标记名称区分大小写。 标记的值应为一个正整数（1、2、3），对应于启动或停止的顺序。 <br>**注意**： vm 必须位于、 `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames`和`External_ExcludeVMNames`变量中定义的资源组内。 它们必须具有相应的标记才能使操作生效。|

### <a name="variables"></a>变量

下表列出了在自动化帐户中创建的变量。 仅修改带有前缀的`External`变量。 修改带有前缀的`Internal`变量会导致意外的后果。

> [!NOTE]
> VM 名称和资源组的限制主要是可变大小的结果。 请参阅[Azure 自动化中的变量资产](https://docs.microsoft.com/azure/automation/shared-resources/variables)。

|变量 | 说明|
|---------|------------|
|External_AutoStop_Condition | 在触发警报之前配置条件时所需的条件运算符。 可接受的`GreaterThan`值`GreaterThanOrEqual`为`LessThan`、、 `LessThanOrEqual`和。|
|External_AutoStop_Description | CPU 百分比超过阈值时停止 VM 的警报。|
|External_AutoStop_Frequency | 规则的评估频率。 此参数接受 timespan 格式的输入。 可能的值为 5 分钟到 6 小时。 |
|External_AutoStop_MetricName | 为其配置 Azure 警报规则的性能指标的名称。|
|External_AutoStop_Severity | 指标警报的严重性，范围为0到4。 |
|External_AutoStop_Threshold | 变量`External_AutoStop_MetricName`中指定的 Azure 警报规则的阈值。 百分比值的范围从1到100。|
|External_AutoStop_TimeAggregationOperator | 应用于所选窗口大小以计算条件的时间聚合运算符。 可接受的`Average`值`Minimum`为`Maximum`、 `Total`、、 `Last`和。|
|External_AutoStop_TimeWindow | Azure 在其中分析用于触发警报的选定指标的窗口大小。 此参数接受 timespan 格式的输入。 可能的值为 5 分钟到 6 小时。|
|External_EnableClassicVMs| 指定解决方案是否针对经典 Vm 的值。 默认值为 True。 对于 Azure 云解决方案提供程序（CSP）订阅，将此变量设置为 False。 经典 Vm 需要[经典运行方式帐户](automation-create-standalone-account.md#create-a-classic-run-as-account)。|
|External_ExcludeVMNames | 要排除的 VM 名称的逗号分隔列表，限于 140 Vm。 如果将超过140个 Vm 添加到列表中，则设置为要排除的 Vm 可能会在无意中启动或停止。|
|External_Start_ResourceGroupNames | 以逗号分隔的列表，其中的一个或多个资源组作为启动操作的目标。|
|External_Stop_ResourceGroupNames | 以逗号分隔的列表，其中列出了针对停止操作的一个或多个资源组。|
|External_WaitTimeForVMRetrySeconds |在**SequencedStartStop_Parent** Runbook 的 vm 上执行操作所用的等待时间（以秒为单位）。 此变量允许 runbook 在执行下一个操作之前等待指定秒数的子操作。 最长等待时间为10800或3小时。 默认值为2100秒。|
|Internal_AutomationAccountName | 指定自动化帐户的名称。|
|Internal_AutoSnooze_ARM_WebhookURI | 为 Vm 的 AutoStop 方案调用的 webhook URI。|
|Internal_AutoSnooze_WebhookUri | 为经典 Vm 的 AutoStop 方案调用的 webhook URI。|
|Internal_AzureSubscriptionId | Azure 订阅 ID。|
|Internal_ResourceGroupName | 自动化帐户资源组名称。|

>[!NOTE]
>对于变量`External_WaitTimeForVMRetryInSeconds`，默认值已从600更新为2100。 

在所有方案中，变量`External_Start_ResourceGroupNames`、 `External_Stop_ResourceGroupNames`和`External_ExcludeVMNames`对于目标 vm 都是必需的，但**AutoStop_CreateAlert_Parent**、 **SequencedStartStop_Parent**和**ScheduledStartStop_Parent** runbook 的以逗号分隔的 VM 列表除外。 也就是说，你的 Vm 必须属于目标资源组才能执行启动和停止操作。 此逻辑的工作方式类似于 Azure 策略，因为可以将订阅或资源组作为目标，并具有新创建的 Vm 继承的操作。 此方法避免了必须为每个 VM 维护一个单独计划和管理规模启动和停止操作。

### <a name="schedules"></a>计划

下表列出了在自动化帐户中创建的每个默认计划。你可以修改默认计划或创建自己的自定义计划。默认情况下，除**Scheduled_StartVM**和**Scheduled_StopVM**计划以外，所有计划都处于禁用状态。

不要启用所有计划，因为这样做可能会创建重叠的计划操作。 最好确定要执行的优化，并相应地进行修改。 请参阅概述部分的示例方案以查看进一步解释。

|计划名称 | 频率 | 说明|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | 每隔 8 小时 | 每8小时运行一次**AutoStop_CreateAlert_Parent** runbook，后者将停止、 `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames`和`External_ExcludeVMNames`变量中基于 VM 的值。 或者，可以使用`VMList`参数指定以逗号分隔的 vm 列表。|
|Scheduled_StopVM | 用户定义，每天 | 使用指定**ScheduledStopStart_Parent**时间每天的参数`Stop`运行 ScheduledStopStart_Parent runbook。自动停止满足变量资产定义的规则的所有 Vm。启用相关计划**StartVM**的计划。|
|Scheduled_StartVM | 用户定义，每天 | 使用指定**ScheduledStopStart_Parent**时间每天的`Start`参数值运行 ScheduledStopStart_Parent runbook。 自动启动所有满足变量资产定义的规则的 Vm。启用相关计划**StopVM**的计划。|
|Sequenced-StopVM | 凌晨 1:00 (UTC)，每星期五 | 在指定**Sequenced_StopStop_Parent**时间，使用`Stop`每个星期五的参数值运行 Sequenced_StopStop_Parent runbook。将按顺序（升序）停止具有相应变量定义的 **SequenceStop** 标记的所有 VM。 有关标记值和资产变量的详细信息，请参阅[runbook](#runbooks)。启用相关计划 Sequenced-StartVM****。|
|Sequenced-StartVM | 下午 1:00 (UTC)，每星期一 | 在`Start`每个星期一的指定时间运行**SequencedStopStart_Parent** runbook，其参数值为。 按顺序（降序）启动具有相应变量定义的 **SequenceStart** 标记的所有 VM。 有关标记值和变量资产的详细信息，请参阅[runbook](#runbooks)。 启用相关计划 Sequenced-StopVM****。

## <a name="use-of-the-solution-with-classic-vms"></a>使用经典 Vm 的解决方案

如果你使用的是针对经典 Vm 的非**工作时间启动/停止 vm**解决方案，则自动化会按每个云服务按顺序处理所有 vm。 Vm 仍会在不同的云服务之间并行处理。 

若要在经典 Vm 中使用解决方案，需要使用经典运行方式帐户，默认情况下不会创建该帐户。 有关创建经典运行方式帐户的说明，请参阅[创建经典运行方式帐户](automation-create-standalone-account.md#create-a-classic-run-as-account)。

如果每个云服务有20个以上的 Vm，以下是一些建议：

* 创建具有父 runbook **ScheduledStartStop_Parent**的多个计划，并为每个计划指定20个 vm。 
* 在计划属性中，使用`VMList`参数将 VM 名称指定为以逗号分隔的列表。 

否则，如果此解决方案的自动化作业运行的时间超过三个小时，则会按[公平份额](automation-runbook-execution.md#fair-share)限制对其进行暂时卸载或停止。

Azure CSP 订阅仅支持 Azure 资源管理器模型。 非 Azure 资源管理器服务在程序中不可用。 当 "在非**工作时间启动/停止 vm" 解决方案**运行时，您可能会收到错误，因为它具有用于管理经典资源的 cmdlet。 若要了解有关 CSP 的详细信息，请参阅 [CSP 订阅中可用的服务](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)。 如果使用 CSP 订阅，应在部署后将[External_EnableClassicVMs](#variables)变量设置为 False。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-the-solution"></a>启用此解决方案

若要开始使用解决方案，请按照[启用启动/停止 vm 解决方案](automation-solution-vm-management-enable.md)中的步骤操作。

## <a name="view-the-solution"></a>查看解决方案

启用解决方案后，使用以下机制之一来访问解决方案：

* 在自动化帐户中，选择 "**相关资源**" 下的 "**启动/停止 VM** "。 在 "启动/停止 VM" 页上，选择 "管理**启动/停止 Vm 解决方案**" 页面右侧的 **"管理解决方案**"。

* 导航到已链接到自动化帐户的 "Log Analytics" 工作区。 选择工作区后，请从左窗格中选择 "**解决方案**"。 在 "解决方案" 页上，从列表中选择 "**启动-停止-VM [工作区]** " 解决方案。  

选择该解决方案会显示“Start-Stop-VM[workspace]”解决方案页面。**** 可在此处查看重要详细信息，如**StartStopVM**磁贴中的信息。 如同在 Log Analytics 工作区中一样，此磁贴显示解决方案中已成功启动和完成的 Runbook 作业计数与图形表示。

![自动化更新管理解决方案页面](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

您可以通过单击 "圆环图" 磁贴对作业记录执行进一步的分析。 解决方案仪表板显示作业历史记录和预定义的日志搜索查询。 切换到 log analytics 高级门户，以根据搜索查询进行搜索。

## <a name="update-the-solution"></a>更新解决方案

如果你部署了此解决方案的以前版本，则在部署更新版本之前，请从你的帐户中删除它。 按照步骤[删除解决方案](#remove-the-solution)，然后按照步骤[部署解决方案](automation-solution-vm-management-enable.md)。

## <a name="remove-the-solution"></a>删除解决方案

如果不再需要使用该解决方案，可从自动化帐户中删除它。 删除解决方案的操作只会删除 Runbook， 而不会删除添加解决方案时创建的计划或变量。 如果不将这些资产与其他 runbook 一起使用，请手动删除这些资产。

删除解决方案：

1. 在自动化帐户中，选择 "**相关资源**" 下的 "**链接的工作区**"。

2. 选择 "**移到工作区**"。

3. 单击 "**常规**" 下的 "**解决方案**"。 

4. 在“解决方案”页中，请选择解决方案 Start-Stop-VM[Workspace]****。 

5. 在 " **VMManagementSolution [Workspace]** " 页上，从菜单中选择 "**删除**"。<br><br> ![删除 VM 管理解决方案](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. 在 "**删除解决方案**" 窗口中，确认要删除该解决方案。

7. 验证信息并删除解决方案时，可以从菜单中选择 "**通知**" 下的进度。 删除解决方案的进程启动后，将返回到 "解决方案" 页。

此进程不会删除自动化帐户和 Log Analytics 工作区。 如果不想保留 Log Analytics 工作区，则必须从 Azure 门户中手动删除：

1. 搜索并选择 " **Log Analytics 工作区**"。

2. 在 "Log Analytics 工作区" 页上，选择工作区。

3. 从工作区设置页面上的菜单中选择“删除”。****

4. 如果不想保留 Azure Automation 帐户[解决方案组件](#solution-components)，可以手动删除每个组件。

## <a name="next-steps"></a>后续步骤

为 Azure Vm[启用](automation-solution-vm-management-enable.md)"**在非工作时间启动/停止 vm** " 解决方案。
