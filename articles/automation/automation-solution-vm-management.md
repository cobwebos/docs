---
title: Azure 自动化“在空闲时间启动/停止 VM”的概述
description: 本文介绍“在空闲时间启动/停止 VM”功能，该功能按计划启动或停止 VM 并主动通过 Azure Monitor 日志监视这些 VM。
services: automation
ms.subservice: process-automation
ms.date: 06/04/2020
ms.topic: conceptual
ms.openlocfilehash: 3b4358651b811ba5c1e7644333a1e9f5a8da2990
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84424068"
---
# <a name="startstop-vms-during-off-hours-overview"></a>“在空闲时间启动/停止 VM”概述

在空闲时间启动/停止 VM 功能启动或停止启用的 Azure Vm。 它根据用户定义的计划启动或停止计算机、通过 Azure Monitor 日志提供见解，并通过使用[操作组](../azure-monitor/platform/action-groups.md)发送可选的电子邮件。 在大多数情况下，可同时在 Azure 资源管理器和经典 VM 上启用此功能。 

此功能使用[new-azvm](https://docs.microsoft.com/powershell/module/az.compute/start-azvm) cmdlet 来启动 vm。 它使用[new-azvm](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm)停止 vm。

> [!NOTE]
> 当 runbook 已更新为使用新的 Azure Az module cmdlet 时，它们使用 AzureRM 前缀别名。

> [!NOTE]
> “在空闲时间启动/停止 VM”已进行了更新，可支持可用的最新版本的 Azure 模块。 此功能更新后的版本（Azure 市场提供）不支持 AzureRM 模块，因为我们已从 AzureRM 迁移到 Az 模块。

对于想要优化 VM 成本的用户，此功能提供分散式的低成本自动化选项。 可以使用此功能执行以下操作：

- [计划要启动/停止的 VM](automation-solution-vm-management-config.md#schedule)。
- [使用 Azure 标记](automation-solution-vm-management-config.md#tags)按升序计划要启动和停止的 VM。 经典虚拟机不支持此活动。
- 根据 [CPU 低使用率](automation-solution-vm-management-config.md#cpuutil)自动停止 VM。

当前功能存在以下限制：

- 它可管理任何区域中的 VM，但只能在 Azure 自动化帐户所在的同一订阅中使用。
- 它可在支持 Log Analytics 工作区、Azure 自动化帐户和警报的任何 Azure 和 Azure 政府区域中使用。 Azure 政府区域目前不支持电子邮件功能。

## <a name="prerequisites"></a>先决条件

“在空闲时间启动/停止 VM”功能的 runbook 使用 [Azure 运行方式帐户](automation-create-runas-account.md)。 运行方式帐户是首选的身份验证方法，因为它使用证书身份验证，而不是可能会过期或经常更改的密码。

对于为“在空闲时间启动/停止 VM”功能启用的 VM，建议使用单独的自动化帐户。 Azure 模块版本经常升级，其参数可能会更改。 此功能不按照与之相同的频率升级，所以可能不适用于它所使用的较新版本的 cmdlet。 建议先在测试自动化帐户中测试模块更新，再将其导入生产自动化帐户。

## <a name="permissions"></a>权限

需要具有特定权限才能为“在空闲时间启动/停止 VM”功能启用 VM。 所需权限会有所不同，具体取决于此功能是使用预创建的自动化帐户和 Log Analytics 工作区，还是会创建一个新帐户和一个工作区。 

如果你是订阅的参与者和 Azure Active Directory (AD) 租户中的全局管理员，则无需配置相关权限。 如果你没有这些权限或需要配置自定义角色，请确保具有下述权限。

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>适用于预先存在的自动化帐户和 Log Analytics 工作区的权限

若要使用现有的自动化帐户和 Log Analytics 工作区为“在空闲时间启动/停止 VM”功能启用 VM，需要对资源组作用域具有以下权限。 若要详细了解角色，请参阅 [Azure 资源的自定义角色](../role-based-access-control/custom-roles.md)。

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
| Microsoft.automation/automationAccounts/jobSchedules/write | 资源组 |
| Microsoft.Automation/automationAccounts/jobs/write | 资源组 |
| Microsoft.Automation/automationAccounts/jobs/read | 资源组 |
| Microsoft.OperationsManagement/solutions/write | 资源组 |
| Microsoft.OperationalInsights/workspaces/* | 资源组 |
| Microsoft.Insights/diagnosticSettings/write | 资源组 |
| Microsoft.Insights/ActionGroups/Write | 资源组 |
| Microsoft.Insights/ActionGroups/read | 资源组 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 资源组 |
| Microsoft.Resources/deployments/* | 资源组 |

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>适用于新的自动化帐户和新的 Log Analytics 工作区的权限

可以使用新的自动化帐户和 Log Analytics 工作区为“在空闲时间启动/停止 VM”功能启用 VM。 在这种情况下，需要具有上一部分中定义的权限以及本部分中定义的权限。 还需要以下角色：

- 订阅上的共同管理员。 如果要管理经典 VM，则需要此角色才能创建“经典运行方式帐户”。 默认情况下，不再创建[经典运行方式帐户](automation-create-standalone-account.md#create-a-classic-run-as-account)。
- [Azure AD](../active-directory/users-groups-roles/directory-assign-admin-roles.md) 应用程序开发人员角色中的成员身份。 有关配置运行方式帐户的详细信息，请参阅[用于配置运行方式帐户的权限](manage-runas-account.md#permissions)。
- 订阅的参与者或以下权限。

| 权限 |范围|
| --- | --- |
| Microsoft.Authorization/Operations/read | 订阅|
| Microsoft.Authorization/permissions/read |订阅|
| Microsoft.Authorization/roleAssignments/read | 订阅 |
| Microsoft.Authorization/roleAssignments/write | 订阅 |
| Microsoft.Authorization/roleAssignments/delete | 订阅 || Microsoft.Automation/automationAccounts/connections/read | 资源组 |
| Microsoft.Automation/automationAccounts/certificates/read | 资源组 |
| Microsoft.Automation/automationAccounts/write | 资源组 |
| Microsoft.OperationalInsights/workspaces/write | 资源组 |

## <a name="components"></a>组件

在空闲时间启动/停止 VM 功能包括预配置的 runbook、计划以及与 Azure Monitor 日志的集成。 可以使用这些元素，按业务需求精确设置 VM 的启动和关闭。

### <a name="runbooks"></a>Runbook

下表列出了此功能部署到自动化帐户的 runbook。 请勿对 runbook 代码进行更改。 应该对新功能编写自己的 Runbook。

> [!IMPORTANT]
> 请勿直接运行在名称末尾追加了“child”的任何 runbook。

所有父 runbook 都包含 `WhatIf` 参数。 设置为 True 时，此参数支持详细说明在无参数的情况下运行时 runbook 的确切行为，并验证是否以正确 VM 为目标。 仅当 `WhatIf` 参数设置为 False 时，runbook 才执行其定义的操作。

|Runbook | 参数 | 说明|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | 从父 runbook 调用。 此 runbook 为 Auto-Stop 方案按每个资源创建警报。|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf：是或否  | 在目标订阅或资源组中的 VM 上创建或更新 Azure 警报规则。 <br> `VMList`以逗号分隔的 Vm 列表（不含空格），例如 `vm1,vm2,vm3` 。<br> `WhatIf` 可实现对 runbook 逻辑进行验证但不执行。|
|AutoStop_Disable | 无 | 禁用 Auto-Stop 警报和默认计划。|
|AutoStop_VM_Child | WebHookData | 从父 runbook 调用。 警报规则调用此 runbook 以停止经典 VM。|
|AutoStop_VM_Child_ARM | WebHookData |从父 runbook 调用。 警报规则调用此 runbook 以停止 VM。  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> 操作：启动或停止<br> VMList  | 通过云服务在经典 VM 组中执行启动或停止操作。 |
|ScheduledStartStop_Child | VMName <br> 操作：启动或停止 <br> ResourceGroupName | 从父 runbook 调用。 针对计划的停止执行启动或停止操作。|
|ScheduledStartStop_Child_Classic | VMName<br> 操作：启动或停止<br> ResourceGroupName | 从父 runbook 调用。 根据经典 VM 的停止计划执行启动或停止操作。 |
|ScheduledStartStop_Parent | 操作：启动或停止 <br>VMList <br> WhatIf：是或否 | 启动或停止订阅中的所有 VM。 编辑变量 `External_Start_ResourceGroupNames` 和 `External_Stop_ResourceGroupNames`，以实现仅在这些目标资源组上执行操作。 还可以通过更新 `External_ExcludeVMNames` 变量排除特定 VM。|
|SequencedStartStop_Parent | 操作：启动或停止 <br> WhatIf：是或否<br>VMList| 在要确定启动/停止活动序列的每个 VM 上，创建名为 sequencestart 和 sequencestop 的标记 。 这些标记名称区分大小写。 标记的值应为正整数的列表，例如， `1,2,3` 对应于你想要启动或停止的顺序。 <br>**注意**：VM 必须位于 `External_Start_ResourceGroupNames`、`External_Stop_ResourceGroupNames` 和 `External_ExcludeVMNames` 变量中定义的资源组中。 它们必须具有相应的标记才能使操作生效。|

### <a name="variables"></a>变量

下表列出了在自动化帐户中创建的变量。 仅修改以 `External` 为前缀的变量。 修改以 `Internal` 为前缀的变量将造成不利影响。

> [!NOTE]
> VM 名称和资源组的限制主要是由于变量的大小造成的。 请参阅 [Azure 自动化中的变量资产](https://docs.microsoft.com/azure/automation/shared-resources/variables)。

|变量 | 说明|
|---------|------------|
|External_AutoStop_Condition | 在触发警报之前配置条件时所需的条件运算符。 可接受的值为 `GreaterThan`、`GreaterThanOrEqual`、`LessThan` 和 `LessThanOrEqual`。|
|External_AutoStop_Description | CPU 百分比超过阈值时停止 VM 的警报。|
|External_AutoStop_Frequency | 规则的评估频率。 此参数接受 timespan 格式的输入。 可能的值为 5 分钟到 6 小时。 |
|External_AutoStop_MetricName | 为其配置 Azure 警报规则的性能指标的名称。|
|External_AutoStop_Severity | 指标警报严重性，范围从 0 到 4。 |
|External_AutoStop_Threshold | 在变量 `External_AutoStop_MetricName` 中指定的 Azure 警报规则的阈值。 百分比值的范围是从 1 到 100。|
|External_AutoStop_TimeAggregationOperator | 应用到所选窗口大小以计算条件的时间聚合运算符。 可接受的值为 `Average`、`Minimum`、`Maximum`、`Total` 和 `Last`。|
|External_AutoStop_TimeWindow | Azure 将分析用于触发警报的选定指标的窗口大小。 此参数接受 timespan 格式的输入。 可能的值为 5 分钟到 6 小时。|
|External_EnableClassicVMs| 其值用于指定此功能是否以经典 VM 为目标。 默认值为 True。 对于 Azure 云解决方案提供商 (CSP) 订阅，将此变量设置为 False。 经典 VM 需要一个[经典运行方式帐户](automation-create-standalone-account.md#create-a-classic-run-as-account)。|
|External_ExcludeVMNames | 要排除的 VM 名称的列表，以逗号分隔，限于 140 个 VM。 如果将超过140个 Vm 添加到列表中，则为排除指定的 Vm 可能会在无意中启动或停止。|
|External_Start_ResourceGroupNames | 一个或多个以启动操作为目标的资源组的以逗号分隔的列表。|
|External_Stop_ResourceGroupNames | 一个或多个以停止操作为目标的资源组的以逗号分隔的列表。|
|External_WaitTimeForVMRetrySeconds |要在 SequencedStartStop_Parent runbook 的 VM 上执行的操作的等待时间（以秒为单位）。 此变量允许 runbook 在继续执行下一步操作之前，等待子操作运行，等待时间为指定的秒数。 最长等待时间是 10800 秒，即三个小时。 默认值为 2100 秒。|
|Internal_AutomationAccountName | 指定自动化帐户的名称。|
|Internal_AutoSnooze_ARM_WebhookURI | 为 VM 的 AutoStop 方案调用的 Webhook URI。|
|Internal_AutoSnooze_WebhookUri | 为经典 VM 的 AutoStop 方案调用的 Webhook URI。|
|Internal_AzureSubscriptionId | Azure 订阅 ID。|
|Internal_ResourceGroupName | 自动化帐户资源组名称。|

>[!NOTE]
>对于变量 `External_WaitTimeForVMRetryInSeconds`，默认值已从 600 更新为 2100。 

在所有方案中，变量 `External_Start_ResourceGroupNames`、`External_Stop_ResourceGroupNames` 和 `External_ExcludeVMNames` 对于目标 VM 来说是必需的，但 AutoStop_CreateAlert_Parent、SequencedStartStop_Parent 和 ScheduledStartStop_Parent runbook 的以逗号分隔的 VM 列表除外  。 也就是说，你的 VM 必须属于目标资源组，才能执行启动和停止操作。 此逻辑的工作方式类似于 Azure Policy，因为可以在订阅或资源组中设定目标，并且具有新创建的 VM 继承的操作。 此方法避免了必须为每个 VM 维护一个单独计划和管理规模启动和停止操作。

### <a name="schedules"></a>计划

下表列出了在自动化帐户中创建的每个默认计划。 你可以修改默认计划或创建自己的自定义计划。 默认情况下，除了 Scheduled_StartVM 和 Scheduled-StopVM 计划，所有计划都是禁用的 。

请勿启用所有计划，因为此操作可能会创建重叠的计划操作。 最好确定希望执行哪些优化，然后再进行相应的修改。 请参阅概述部分的示例方案以查看进一步解释。

|计划名称 | 频率 | 说明|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | 每隔 8 小时 | 每隔 8 小时运行一次 AutoStop_CreateAlert_Parent runbook，这反过来会停止 `External_Start_ResourceGroupNames`、`External_Stop_ResourceGroupNames` 和 `External_ExcludeVMNames` 变量中基于 VM 的值。 或者，可以使用 `VMList` 参数指定以逗号分隔的 VM 列表。|
|Scheduled_StopVM | 用户定义，每天 | 每天在指定的时间运行带有 `Stop` 参数的 ScheduledStopStart_Parent runbook。 自动停止所有满足通过变量资产定义的规则的 VM。 启用相关计划 Scheduled-StartVM。|
|Scheduled_StartVM | 用户定义，每天 | 每天在指定的时间运行带有 `Start` 参数值的 ScheduledStopStart_Parent runbook。 自动启动所有满足通过变量资产定义的规则的 VM。 启用相关计划 Scheduled-StopVM。|
|Sequenced-StopVM | 凌晨 1:00 (UTC)，每星期五 | 每个星期五在指定的时间运行带有 `Stop` 参数值的 Sequenced_StopStop_Parent runbook。 将按顺序（升序）停止具有相应变量定义的 **SequenceStop** 标记的所有 VM。 有关标记值和资产变量的详细信息，请参阅 [Runbook](#runbooks)。 启用相关计划 Sequenced-StartVM。|
|Sequenced-StartVM | 下午 1:00 (UTC)，每星期一 | 每个星期一在指定的时间运行带有 `Start` 参数值的 SequencedStopStart_Parent runbook。 按顺序（降序）启动具有相应变量定义的 **SequenceStart** 标记的所有 VM。 有关标记值和变量资产的详细信息，请参阅 [Runbook](#runbooks)。 启用相关计划 Sequenced-StopVM。

## <a name="use-the-feature-with-classic-vms"></a>将该功能与经典 VM 配合使用

如果对经典 VM 使用“在空闲时间启动/停止 VM”功能，则自动化功能将按顺序处理每个云服务的所有 VM。 VM 仍然可以跨不同的云服务并行处理。 

若要将此功能用于经典 VM，需要一个非默认创建的经典运行方式帐户。 有关如何创建经典运行方式帐户的说明，请参阅[创建 Azure 运行方式帐户](automation-create-standalone-account.md#create-a-classic-run-as-account)。

如果每个云服务有超过 20 个 VM，请参考以下建议：

* 使用父 runbook ScheduledStartStop_Parent 创建多个计划，并为每个计划指定 20 个 VM。 
* 在计划属性中，使用 `VMList` 参数将 VM 名称指定为以逗号分隔的列表（无空格）。 

否则，如果此功能的自动化作业运行超过三个小时，将根据[公平份额](automation-runbook-execution.md#fair-share)限制暂时将其卸载或停止。

Azure CSP 订阅仅支持 Azure 资源管理器模型。 非 Azure 资源管理器服务在计划中不可用。 当“在空闲时间启动/停止 VM”功能运行时，可能会收到错误，因为它具有用于管理经典资源的 cmdlet。 若要了解有关 CSP 的详细信息，请参阅 [CSP 订阅中可用的服务](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)。 如果使用 CSP 订阅，则应在部署之后将 [External_EnableClassicVMs](#variables) 变量设置为 False。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-the-feature"></a>启用功能

若要开始使用此功能，请按照[启用“在空闲时间启动/停止 VM”](automation-solution-vm-management-enable.md)中的步骤操作。

## <a name="view-the-feature"></a>查看功能

使用下列机制之一来访问已启用的功能：

* 在自动化帐户中，选择“相关资源”下的“启动/停止 VM” 。 在“启动/停止 VM”页上，选择“管理‘启动/停止 VM’解决方案”下的“管理解决方案” 。

* 导航到链接到自动化帐户的 Log Analytics 工作区。 选择工作区后，请从左窗格中选择“解决方案”。 在“解决方案”页上，从列表中选择“Start-Stop-VM[workspace]”。  

选择该功能会显示 Start-Stop-VM[workspace] 页面。 在此处可以查看重要详细信息，例如 StartStopVM 磁贴中的信息。 如同在 Log Analytics 工作区中一样，此磁贴显示功能中已成功启动和完成的 runbook 作业计数与图形表示。

![Azure 更新管理页面](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

可以通过单击圆环磁贴对作业记录执行进一步的分析。 仪表板显示作业历史记录和预定义的日志搜索查询。 切换到日志分析高级门户可以根据搜索查询进行搜索。

## <a name="update-the-feature"></a>更新功能

如果你部署了“在空闲时间启动/停止 VM”的以前版本，则在部署更新的版本之前，必须先从帐户中将其删除。 按照以下步骤[删除此功能](#remove-the-feature)，然后按照步骤[启用它](automation-solution-vm-management-enable.md)。

## <a name="remove-the-feature"></a>删除功能

如果不再需要使用此功能，可从自动化帐户中删除它。 删除此功能只会删除关联的 runbook， 而不会删除添加此功能时创建的计划或变量。 

删除“在空闲时间启动/停止 VM”：

1. 在自动化帐户中，选择“相关资源”下的“链接的工作区” 。

2. 选择“转到工作区”。

3. 单击“常规”下的“解决方案” 。 

4. 在“解决方案”页上，选择“Start-Stop-VM[Workspace]”。 

5. 在“VMManagementSolution[Workspace]”页上，从菜单中选择“删除”。<br><br> ![删除 VM 管理功能](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. 在“删除解决方案”窗口中，确认要删除此功能。

7. 在验证信息和删除此功能时，可以在菜单中的“通知”下面跟踪操作进度。 删除后，将返回到“解决方案”页。

8. 此进程不会删除自动化帐户和 Log Analytics 工作区。 如果不想保留 Log Analytics 工作区，则必须从 Azure 门户中手动删除它：

    1. 搜索并选择“Log Analytics 工作区”。

    2. 在“Log Analytics 工作区”页面上，选择工作区。

    3. 从菜单中选择“删除”。

    4. 如果不想保留 Azure 自动化帐户[功能组件](#components)，可以手动删除每个组件。

## <a name="next-steps"></a>后续步骤

若要在环境中启用 VM 上的功能，请参阅[启用“在空闲时间启动/停止 VM”](automation-solution-vm-management-enable.md)。
