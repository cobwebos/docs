---
title: 非工作时间解决方案期间启动/停止 VM
description: 此 VM 管理解决方案按计划启动和停止 Azure 虚拟机，并从 Azure 监视器日志主动监视。
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: fbcd4ea174d4b6a2a45495c32f178ed1bd01bbe0
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261340"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>在 Azure 自动化中，在非工作时间解决方案期间启动/停止 VM

**在非工作时间解决方案启动/停止 VM**启动或停止 Azure 虚拟机。 它根据用户定义的计划启动或停止计算机，通过 Azure 监视器日志提供见解，并使用[操作组](../azure-monitor/platform/action-groups.md)发送可选电子邮件。 对于大多数方案，该解决方案同时支持 Azure 资源管理器和经典 VM。 

对于想要优化 VM 成本的用户，此解决方案提供分散式的低成本自动化选项。 使用此解决方案可以：

- [计划 VM 启动和停止](automation-solution-vm-management-config.md#schedule)。
- 使用[Azure 标记](automation-solution-vm-management-config.md#tags)（经典 VM 不支持）计划 VM 以升序启动和停止。
- 基于[低 CPU 使用率](automation-solution-vm-management-config.md#cpuutil)的自动停止 VM。

> [!NOTE]
> **已更新非工作时间解决方案期间的启动/停止 VM，** 以支持可用的 Azure 模块的最新版本。

以下是当前解决方案的限制：

- 它管理任何区域中的 VM，但只能在与 Azure 自动化帐户相同的订阅中使用。
- 它在 Azure 和 Azure 政府中可用于支持日志分析工作区、Azure 自动化帐户和警报的任何区域。 Azure 政府区域当前不支持电子邮件功能。

## <a name="solution-prerequisites"></a>解决方案先决条件

此解决方案的 Runbook 使用 [Azure 运行方式帐户](automation-create-runas-account.md)。 运行 As 帐户是首选身份验证方法，因为它使用证书身份验证而不是可能经常过期或更改的密码。

我们建议您在**非工作时间解决方案期间为开始/停止 VM**使用单独的自动化帐户。 Azure 模块版本经常升级，其参数可能会更改。 解决方案不会以相同的节奏进行升级，并且可能无法与它使用的较新版本的 cmdlet 一起使用。 建议您在生产自动化帐户中导入模块更新之前，先在测试自动化帐户中测试这些更新。

## <a name="solution-permissions"></a>解决方案权限

您必须具有某些权限才能**在非工作时间解决方案期间部署开始/停止 VM。** 如果解决方案使用预先创建的自动化帐户和日志分析工作区，则权限因解决方案在部署期间创建新帐户和工作区所需的权限而异。 

如果您是订阅的参与者，并且是 Azure 活动目录租户中的全局管理员，则无需配置权限。 如果您没有这些权限或需要配置自定义角色，请确保具有下面描述的权限。

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>预先存在的自动化帐户和日志分析工作区的权限

要**将非工作时间解决方案的启动/停止 VM**部署到现有的自动化帐户和日志分析工作区，部署该解决方案的用户需要资源组作用域的以下权限。 要了解有关角色的更多信息，请参阅[Azure 资源的自定义角色](../role-based-access-control/custom-roles.md)。

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
| 微软.自动化/自动化会计/作业计划/写入 | 资源组 |
| Microsoft.Automation/automationAccounts/jobs/write | 资源组 |
| Microsoft.Automation/automationAccounts/jobs/read | 资源组 |
| Microsoft.OperationsManagement/solutions/write | 资源组 |
| 微软.操作见解/工作空间/* | 资源组 |
| 微软.见解/诊断设置/写入 | 资源组 |
| Microsoft.Insights/ActionGroups/Write | 资源组 |
| 微软.见解/行动组/阅读 | 资源组 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 资源组 |
| Microsoft.Resources/deployments/* | 资源组 |

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>新自动化帐户和新的日志分析工作区的权限

您可以将**非工作时间解决方案的"开始/停止 VM"** 部署到新的自动化帐户和日志分析工作区。 在这种情况下，部署解决方案的用户需要上一节中定义的权限以及本节中定义的权限。 

部署解决方案的用户需要以下角色：

- 订阅时是共同管理员。 如果要管理经典 VM，则需要此角色来创建"经典运行即用"帐户。 [默认情况下不再创建经典"运行帐户"。](automation-create-standalone-account.md#create-a-classic-run-as-account)
- [Azure 活动目录](../active-directory/users-groups-roles/directory-assign-admin-roles.md)应用程序开发人员角色的成员身份。 有关配置"以帐户身份运行"的详细信息，请参阅[配置"以帐户身份运行"的权限](manage-runas-account.md#permissions)。
- 订阅或以下权限上的参与者。

| 权限 |范围|
| --- | --- |
| 微软.授权/操作/读取 | 订阅|
| Microsoft.Authorization/permissions/read |订阅|
| Microsoft.Authorization/roleAssignments/read | 订阅 |
| Microsoft.Authorization/roleAssignments/write | 订阅 |
| Microsoft.Authorization/roleAssignments/delete | 订阅 |
| Microsoft.Automation/automationAccounts/connections/read | 资源组 |
| Microsoft.Automation/automationAccounts/certificates/read | 资源组 |
| Microsoft.Automation/automationAccounts/write | 资源组 |
| Microsoft.OperationalInsights/workspaces/write | 资源组 |

## <a name="solution-components"></a>解决方案组件

**非工作时间解决方案的启动/停止 VM**包括预配置的 Runbook、计划和与 Azure 监视器日志的集成。 您可以使用这些元素来定制 VM 的启动和关闭，以满足您的业务需求。

### <a name="runbooks"></a>Runbook

下表列出了解决方案部署到自动化帐户的 Runbook。 不要更改 Runbook 代码。 应该对新功能编写自己的 Runbook。

> [!IMPORTANT]
> 不要直接运行任何附加其名称**的子**簿。

所有父运行簿都包含`WhatIf`参数。 当设置为 True 时，参数支持详细说明 Runbook 在没有参数的情况下运行时所采取的确切行为，并验证是否将正确的 VM 作为目标。 Runbook 仅在`WhatIf`参数设置为 False 时执行其定义的操作。

|Runbook | 参数 | 说明|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | 从父 runbook 调用。 此 Runbook 基于每个资源为自动停止方案创建警报。|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf：True 或 False  | 在目标订阅或资源组中的 VM 上创建或更新 Azure 警报规则。 <br> `VMList`是 VM 的逗号分隔列表。 例如，`vm1, vm2, vm3` 。<br> `WhatIf`支持在不执行的情况下验证 Runbook 逻辑。|
|AutoStop_Disable | None | 禁用自动停止警报和默认计划。|
|AutoStop_VM_Child | WebHookData | 从父 runbook 调用。 警报规则调用此 Runbook 以停止经典 VM。|
|AutoStop_VM_Child_ARM | WebHookData |从父 runbook 调用。 警报规则调用此 Runbook 以停止 VM。  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> Action：Start 或 Stop<br> VMList  | 按云服务在经典 VM 组中执行操作启动或停止。 |
|ScheduledStartStop_Child | VMName <br> Action：Start 或 Stop <br> ResourceGroupName | 从父 runbook 调用。 针对计划的停止执行启动或停止操作。|
|ScheduledStartStop_Child_Classic | VMName<br> Action：Start 或 Stop<br> ResourceGroupName | 从父 runbook 调用。 为经典 VM 的计划停止执行开始或停止操作。 |
|ScheduledStartStop_Parent | Action：Start 或 Stop <br>VMList <br> WhatIf：True 或 False | 启动或停止订阅中的所有 VM。 编辑变量`External_Start_ResourceGroupNames`，并`External_Stop_ResourceGroupNames`仅在这些目标资源组上执行。 还可以通过更新`External_ExcludeVMNames`变量来排除特定的 VM。|
|SequencedStartStop_Parent | Action：Start 或 Stop <br> WhatIf：True 或 False<br>VMList| 在要为其排序开始/停止活动的每个 VM 上创建名为**序列启动**和**序列停止**的标记。 这些标记名称区分大小写。 标记的值应为一个正整数（1、2、3），对应于启动或停止的顺序。 <br>**注意**：VM 必须位于 在`External_Start_ResourceGroupNames``External_Stop_ResourceGroupNames`中定义的资源组中`External_ExcludeVMNames`， 和变量中。 它们必须具有相应的标记才能使操作生效。|

### <a name="variables"></a>变量

下表列出了在自动化帐户中创建的变量。 仅修改预缀在`External`的变量。 修改预先固定的`Internal`变量会导致不良效果。

> [!NOTE]
> 对 VM 名称和资源组的限制很大程度上是可变大小造成的。

|变量 | 说明|
|---------|------------|
|External_AutoStop_Condition | 在触发警报之前配置条件时所需的条件运算符。 可接受的值是`GreaterThan` `GreaterThanOrEqual`，`LessThan`和`LessThanOrEqual`。|
|External_AutoStop_Description | CPU 百分比超过阈值时停止 VM 的警报。|
|External_AutoStop_Frequency | 规则的评估频率。 此参数接受 timespan 格式的输入。 可能的值为 5 分钟到 6 小时。 |
|External_AutoStop_MetricName | 为其配置 Azure 警报规则的性能指标的名称。|
|External_AutoStop_Severity | 指标警报的严重性，范围从 0 到 4。 |
|External_AutoStop_Threshold | 变量`External_AutoStop_MetricName`中指定的 Azure 警报规则的阈值。 百分比值范围从 1 到 100。|
|External_AutoStop_TimeAggregationOperator | 应用于所选窗口大小的时间聚合运算符用于评估条件。 可接受的值是`Average` `Minimum` `Maximum`、、`Total`和`Last`。|
|External_AutoStop_TimeWindow | Azure 分析用于触发警报的选定指标的窗口的大小。 此参数接受 timespan 格式的输入。 可能的值为 5 分钟到 6 小时。|
|External_EnableClassicVMs| 指定解决方案是否针对经典 VM 的值。 默认值为 True。 为 Azure 云解决方案提供程序 （CSP） 订阅将此变量设置为 False。 经典 VM 需要[经典运行作为帐户](automation-create-standalone-account.md#create-a-classic-run-as-account)。|
|External_ExcludeVMNames | 要排除的 VM 名称的 Comma 分隔列表，限制为 140 个 VM。 如果将超过 140 个 VM 添加到列表中，则设置为排除的 VM 可能会无意中启动或停止。|
|External_Start_ResourceGroupNames | 面向启动操作的一个或多个资源组的 Comma 分隔列表。|
|External_Stop_ResourceGroupNames | 一个或多个资源组的 Comma 分隔列表，这些资源组针对停止操作。|
|External_WaitTimeForVMRetrySeconds |在**SequencedStartStop_Parent** runbook 的 VM 上执行操作的等待时间（以秒为单位）。 此变量允许 Runbook 等待子操作指定秒数，然后再继续执行下一个操作。 最长等待时间为 10800 或 3 小时。 默认值为 2100 秒。|
|Internal_AutomationAccountName | 指定自动化帐户的名称。|
|Internal_AutoSnooze_ARM_WebhookURI | Webhook URI 要求 VM 的自动停止方案。|
|Internal_AutoSnooze_WebhookUri | Webhook URI 要求为经典 VM 提供自动停止方案。|
|Internal_AzureSubscriptionId | Azure 订阅 ID。|
|Internal_ResourceGroupName | 自动化帐户资源组名称。|

>[!NOTE]
>对于变量`External_WaitTimeForVMRetryInSeconds`，默认值从 600 更新到 2100。 

在所有方案中，`External_Start_ResourceGroupNames`变量 和`External_Stop_ResourceGroupNames``External_ExcludeVMNames`是目标 VM 所必需的，但**AutoStop_CreateAlert_Parent、SequencedStartStop_Parent**和**ScheduledStartStop_Parent** runbook 的逗号分隔**SequencedStartStop_Parent**VM 列表除外。 也就是说，VM 必须属于目标资源组，才能启动和停止执行操作。 此逻辑的工作方式类似于 Azure Policy，因为可以在订阅或资源组中设定目标，并且具有新创建的 VM 继承的操作。 此方法避免了必须为每个 VM 维护一个单独计划和管理规模启动和停止操作。

### <a name="schedules"></a>计划

下表列出了在自动化帐户中创建的每个默认计划。你可以修改默认计划或创建自己的自定义计划。默认情况下，除**Scheduled_StartVM**和**Scheduled_StopVM**计划外，所有计划都已禁用。

不要启用所有计划，因为这样做可能会创建重叠的计划操作。 最好确定要执行哪些优化并相应地修改它们。 请参阅概述部分的示例方案以查看进一步解释。

|计划名称 | 频率 | 说明|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | 每隔 8 小时 | 每 8 小时运行**一次AutoStop_CreateAlert_Parent** runbook，这反过来又会停止`External_Start_ResourceGroupNames`中`External_Stop_ResourceGroupNames`基于 VM`External_ExcludeVMNames`的值， 和变量。 或者，可以使用`VMList`参数指定逗号分隔的 VM 列表。|
|Scheduled_StopVM | 用户定义的每日 | 在指定时间运行具有每天参数`Stop`**的ScheduledStopStart_Parent** runbook。自动停止满足变量资产定义的规则的所有 VM。启用相关计划**计划-启动VM。**|
|Scheduled_StartVM | 用户定义的每日 | 在指定时间运行具有`Start`每天参数值**的ScheduledStopStart_Parent** runbook。 自动启动满足变量资产定义的规则的所有 VM。启用相关**计划计划-停止VM。**|
|Sequenced-StopVM | 凌晨 1:00 (UTC)，每星期五 | 在指定时间运行参数值为每周五的`Stop`Sequenced_Parent runbook。将按顺序（升序）停止具有相应变量定义的 **SequenceStop** 标记的所有 VM。 有关标记值和资产变量的详细信息，请参阅“Runbook”部分。启用相关计划 Sequenced-StartVM****。|
|Sequenced-StartVM | 下午 1:00 (UTC)，每星期一 | **在指定**时间运行SequencedStopStart_Parent运行簿的参数值`Start`为每周一。 按顺序（降序）启动具有相应变量定义的 **SequenceStart** 标记的所有 VM。 有关标记值和变量资产的详细信息，请参阅[Runbook](#runbooks)。 启用相关计划 Sequenced-StopVM****。

## <a name="use-of-the-solution-with-classic-vms"></a>将解决方案与经典 VM 一起使用

如果您在**非工作时间使用针对经典 VM 的启动/停止 VM**解决方案，则自动化将按每个云服务顺序处理所有 VM。 VM 仍在不同的云服务中并行处理。 

要将解决方案与经典 VM 一起使用，您需要一个经典运行作为帐户，默认情况下不创建该帐户。 有关创建经典"作为"帐户的说明，请参阅[创建经典运行作为帐户](automation-create-standalone-account.md#create-a-classic-run-as-account)。

如果每个云服务有超过 20 个 VM，以下是一些建议：

* 使用父 runbook 创建多个计划**ScheduledStartStop_Parent**并为每个计划指定 20 个 VM。 
* 在计划属性中，使用`VMList`参数将 VM 名称指定为逗号分隔列表。 

否则，如果此解决方案的自动化作业运行超过三个小时，则根据[公平共享](automation-runbook-execution.md#fair-share)限制暂时卸载或停止该作业。

Azure CSP 订阅仅支持 Azure 资源管理器模型。 非 Azure 资源管理器服务在程序中不可用。 当**非工作时间解决方案运行时启动/停止 VM**运行时，您可能会收到错误，因为它具有管理经典资源的 cmdlet。 若要了解有关 CSP 的详细信息，请参阅 [CSP 订阅中可用的服务](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)。 如果使用 CSP 订阅，则应在部署后将[External_EnableClassicVMs](#variables)变量设置为 False。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-the-solution"></a>启用此解决方案

要开始使用该解决方案，请按照[启用开始/停止 VM 解决方案](automation-solution-vm-management-enable.md)中的步骤操作。

## <a name="view-the-solution"></a>查看解决方案

启用解决方案后，请使用以下机制之一访问解决方案：

* 从自动化帐户中，选择 **"相关资源**"下的 **"开始/停止 VM"。** 在"开始/停止 VM"页上，选择"从页面右侧**管理解决方案**"，在 **"管理开始/停止 VM 解决方案**"下。

* 导航到链接到自动化帐户的日志分析工作区。 选择工作区后，从左侧窗格中选择 **"解决方案**"。 在"解决方案"页上，从列表中选择 **"开始-停止-VM_工作区]** 解决方案。  

选择该解决方案会显示“Start-Stop-VM[workspace]”解决方案页面。**** 在这里，您可以查看重要详细信息，例如**StartStopVM**磁贴中的信息。 如同在 Log Analytics 工作区中一样，此磁贴显示解决方案中已成功启动和完成的 Runbook 作业计数与图形表示。

![自动化更新管理解决方案页面](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

您可以通过单击圆环磁贴对作业记录执行进一步分析。 解决方案仪表板显示作业历史记录和预定义的日志搜索查询。 切换到日志分析高级门户，以便根据您的搜索查询进行搜索。

## <a name="update-the-solution"></a>更新解决方案

如果您已部署此解决方案的早期版本，请将其从帐户中删除，然后再部署更新的版本。 按照步骤[删除解决方案，](#remove-the-solution)然后按照步骤[部署解决方案](automation-solution-vm-management-enable.md)。

## <a name="remove-the-solution"></a>删除解决方案

如果不再需要使用解决方案，可以从自动化帐户中删除它。 删除解决方案的操作只会删除 Runbook， 而不会删除添加解决方案时创建的计划或变量。 如果不将这些资产与其他 Runbook 一起使用，请手动删除这些资产。

要删除解决方案：

1. 从自动化帐户中，在 **"相关资源**"下选择 **"链接工作区**"。

2. 选择 **"转到工作区**"。

3. 单击 **"常规**"下**的解决方案**。 

4. 在“解决方案”页中，请选择解决方案 Start-Stop-VM[Workspace]****。 

5. 在**VM 管理解决方案[工作区]** 页上，选择"从菜单**中删除**"。<br><br> ![删除 VM 管理解决方案](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. 在 **"删除解决方案"** 窗口中，确认要删除解决方案。

7. 当信息被验证并删除解决方案时，您可以在从菜单中选择**的"通知**"下跟踪进度。 删除解决方案的过程开始后，您将返回到"解决方案"页。

此进程不会删除自动化帐户和 Log Analytics 工作区。 如果不想保留日志分析工作区，则必须手动将其从 Azure 门户中删除：

1. 搜索并选择**日志分析工作区**。

2. 在**日志分析工作区**页面上，选择工作区。

3. 从工作区设置页面上的菜单中选择“删除”。****

4. 如果不想保留 Azure 自动化帐户组件，可以手动删除每个组件。 请参阅[解决方案组件](#solution-components)。

## <a name="next-steps"></a>后续步骤

为 Azure VM[在](automation-solution-vm-management-enable.md)**非工作时间解决方案启用开始/停止 VM。**
