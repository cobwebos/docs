---
title: 在空闲时间启动/停止 VM 解决方案
description: 此 VM 管理解决方案按计划启动和停止 Azure 虚拟机，并从 Azure 监视器日志主动监视。
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 906c7728365cc902549bd46c57972e1c90af979c
ms.sourcegitcommit: 515482c6348d5bef78bb5def9b71c01bb469ed80
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80607479"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Azure 自动化中的在空闲时间启动/停止 VM 解决方案

在非工作时间启动/停止 VM 解决方案启动并停止 Azure 虚拟机的用户定义计划，通过 Azure 监视器日志提供见解，并使用[操作组](../azure-monitor/platform/action-groups.md)发送可选电子邮件。 它在大多数情况下都支持 Azure 资源管理器和经典 VM。 

要将此解决方案与经典 VM 一起使用，您需要一个经典 RunAs 帐户，默认情况下不创建该帐户。 有关创建经典 RunAs 帐户的说明，请参阅[经典运行帐户](automation-create-standalone-account.md#classic-run-as-accounts)。

> [!NOTE]
> 已更新非工作时间解决方案期间的启动/停止 VM，以支持可用的 Azure 模块的最新版本。

对于想要优化 VM 成本的用户，此解决方案提供分散式的低成本自动化选项。 使用此解决方案可以：

- [计划 VM 启动和停止](automation-solution-vm-management-config.md#schedule)。
- 使用[Azure 标记](automation-solution-vm-management-config.md#tags)（经典 VM 不支持）计划 VM 以升序启动和停止。
- 基于[低 CPU 使用率](automation-solution-vm-management-config.md#cpuutil)的自动停止 VM。

以下是当前解决方案的限制：

- 此解决方案可管理任何区域中的 VM，但只能在 Azure 自动化帐户所在的同一订阅中使用。
- 此解决方案在 Azure 和 Azure 政府中可用于支持日志分析工作区、Azure 自动化帐户和警报的任何区域。 Azure 政府区域当前不支持电子邮件功能。

> [!NOTE]
> 如果使用的是经典 VM 解决方案，则每个云服务将按顺序处理所有 VM。 虚拟机仍然可以跨不同的云服务并行处理。 如果每个云服务的 VM 超过 20 个，我们建议使用父 runbook 创建多个计划**ScheduledStartStop_Parent**并指定每个计划 20 个 VM。 在计划属性中，在**VMList**参数中指定为逗号分隔列表的 VM 名称。 否则，如果此解决方案的自动化作业运行超过三个小时，则根据[公平共享](automation-runbook-execution.md#fair-share)限制暂时卸载或停止该作业。
>
> Azure 云解决方案提供商 (Azure CSP) 订阅仅支持 Azure 资源管理器模型，因此非 Azure 资源管理器服务在计划中不可用。 启动/停止解决方案运行时可能会出现错误，因为它使用 cmdlet 来管理经典资源。 若要了解有关 CSP 的详细信息，请参阅 [CSP 订阅中可用的服务](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)。 如果使用 CSP 订阅，则应在部署之后将 [External_EnableClassicVMs](#variables)**** 变量修改为 False****。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>先决条件

此解决方案的 Runbook 使用 [Azure 运行方式帐户](automation-create-runas-account.md)。 运行方式帐户是首选的身份验证方法，因为它使用证书身份验证，而不是可能会过期或经常更改的密码。

我们建议您对启动/停止 VM 解决方案使用单独的自动化帐户。 这是因为 Azure 模块版本经常升级，并且其参数可能会更改。 启动/停止 VM 解决方案不会以相同的节奏升级，因此它可能无法使用它使用的较新版本的 cmdlet。 我们还建议您在在生产自动化帐户中导入模块更新之前，在测试自动化帐户中测试这些更新。

### <a name="permissions"></a>权限

用户必须在非工作时间解决方案期间部署"开始/停止 VM"的某些权限。 如果使用预先创建的自动化帐户和日志分析工作区或在部署期间创建新权限，则这些权限是不同的。 如果您是订阅的参与者，并且是 Azure 活动目录租户中的全局管理员，则无需配置以下权限。 如果您没有这些权限或需要配置自定义角色，请参阅下面的权限。

#### <a name="pre-existing-automation-account-and-log-analytics-workspace"></a>预先存在的自动化帐户和日志分析工作区

要将"开始/停止 VM"解决方案在非工作时间将解决方案部署到现有的自动化帐户和日志分析工作区，部署该解决方案的用户需要在**资源组**上具有以下权限。 要了解有关角色的更多信息，请参阅[Azure 资源的自定义角色](../role-based-access-control/custom-roles.md)。

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

#### <a name="new-automation-account-and-a-new-log-analytics-workspace"></a>新的自动化帐户和新的日志分析工作区

要将非工作时间启动/停止 VM 解决方案部署到新的自动化帐户和日志分析工作区，部署解决方案的用户需要上一节中定义的权限以及以下权限：

- 订阅时的共同管理员 - 仅当您要管理经典 VM 时，才需要创建经典运行帐户。 默认情况下不再创建[经典 RunAs 帐户](automation-create-standalone-account.md#classic-run-as-accounts)。
- [Azure 活动目录](../active-directory/users-groups-roles/directory-assign-admin-roles.md)**应用程序开发人员**角色的成员。 有关配置"以帐户身份运行"的详细信息，请参阅[配置"以帐户身份运行"的权限](manage-runas-account.md#permissions)。
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

此解决方案包括预配置的 Runbook、计划和与 Azure 监视器日志的集成，以便您可以定制虚拟机的启动和关闭以满足业务需求。

### <a name="runbooks"></a>Runbook

下表列出了此解决方案部署到你的自动化帐户的 Runbook。 请勿对 Runbook 代码进行更改。 应该对新功能编写自己的 Runbook。

> [!IMPORTANT]
> 不要直接运行任何附加其名称*的子*簿。

所有父 Runbook 都包含 _WhatIf_ 参数。 设置为 **True** 时，_WhatIf_ 支持详细说明在无 _WhatIf_ 参数的情况下运行时 Runbook 的确切行为，并验证是否以正确 VM 为目标。 仅当 _WhatIf_ 参数设置为 **False** 时，Runbook 才执行其定义的操作。

|Runbook | 参数 | 描述|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | 从父 runbook 调用。 此 runbook 为 AutoStop 方案按每个资源创建警报。|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf：True 或 False  | 在目标订阅或资源组中的 VM 上创建或更新 Azure 警报规则。 <br> VMList：以逗号分隔的 VM 列表。 例如“vm1, vm2, vm3”__。<br> *WhatIf* 对 runbook 逻辑进行验证但不执行。|
|AutoStop_Disable | none | 禁用 AutoStop 警报和默认计划。|
|AutoStop_VM_Child | WebHookData | 从父 runbook 调用。 警报规则调用此 Runbook 以停止经典 VM。|
|AutoStop_VM_Child_ARM | WebHookData |从父 runbook 调用。 警报规则调用此 Runbook 以停止 VM。  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> Action：Start 或 Stop<br> VMList  | 此 Runbook 用于在云服务的经典 VM 组中执行操作启动或停止。<br> VMList：以逗号分隔的 VM 列表。 例如“vm1, vm2, vm3”__。 |
|ScheduledStartStop_Child | VMName <br> Action：Start 或 Stop <br> ResourceGroupName | 从父 runbook 调用。 针对计划的停止执行启动或停止操作。|
|ScheduledStartStop_Child_Classic | VMName<br> Action：Start 或 Stop<br> ResourceGroupName | 从父 runbook 调用。 为经典 VM 的计划停止执行开始或停止操作。 |
|ScheduledStartStop_Parent | Action：Start 或 Stop <br>VMList <br> WhatIf：True 或 False | 此设置会影响订阅中的所有 VM。 将 **External_Start_ResourceGroupNames** 和 **External_Stop_ResourceGroupNames** 编辑为仅在这些目标资源组上执行。 此外，还可以通过更新 **** External_ExcludeVMNames 变量排除特定 VM。<br> VMList：以逗号分隔的 VM 列表。 例如“vm1, vm2, vm3”__。<br> _WhatIf_ 对 runbook 逻辑进行验证但不执行。|
|SequencedStartStop_Parent | Action：Start 或 Stop <br> WhatIf：True 或 False<br>VMList| 在要为其排序开始/停止活动的每个 VM 上创建名为**序列启动**和**序列停止**的标记。 这些标记名称区分大小写。 标记的值应为一个正整数（1、2、3），对应于启动或停止的顺序。 <br> VMList：以逗号分隔的 VM 列表。 例如“vm1, vm2, vm3”__。 <br> _WhatIf_ 对 runbook 逻辑进行验证但不执行。 <br> **注意**：VM 必须位于 Azure 自动化变量中定义的 External_Start_ResourceGroupNames、External_Stop_ResourceGroupNames 和 External_ExcludeVMNames 资源组中。 它们必须具有相应的标记才能使操作生效。|

### <a name="variables"></a>变量

下表列出了在自动化帐户中创建的变量。 仅修改以 External 为前缀的变量****。 修改以 **Internal** 为前缀的变量将导致不利影响。

|变量 | 描述|
|---------|------------|
|External_AutoStop_Condition | 在触发警报之前配置条件时所需的条件运算符。 可接受的值包括：**GreaterThan**、**GreaterThanOrEqual**、**LessThan** 和 **LessThanOrEqual**。|
|External_AutoStop_Description | CPU 百分比超过阈值时停止 VM 的警报。|
|External_AutoStop_Frequency | 规则的评估频率。 此参数接受 timespan 格式的输入。 可能的值为 5 分钟到 6 小时。 |
|External_AutoStop_MetricName | 为其配置 Azure 警报规则的性能指标的名称。|
|External_AutoStop_Severity | 指标警报的严重性，范围从 0 到 4。 |
|External_AutoStop_Threshold | 在变量 _External_AutoStop_MetricName_ 中指定的 Azure 警报规则的阈值。 百分比值的范围是从 1 到 100。|
|External_AutoStop_TimeAggregationOperator | 将应用到所选窗口大小以计算条件的时间聚合运算符。 可接受的值包括：**Average**、**Minimum**、**Maximum**、**Total** 和 **Last**。|
|External_AutoStop_TimeWindow | Azure 将分析用于触发警报的选定指标的窗口大小。 此参数接受 timespan 格式的输入。 可能的值为 5 分钟到 6 小时。|
|External_EnableClassicVMs| 指定解决方案是否针对经典 VM。 默认值为 True。 对于 CSP 订阅，这应设置为 False。 经典 VM 需要[经典运行帐户](automation-create-standalone-account.md#classic-run-as-accounts)。|
|External_ExcludeVMNames | 输入要排除的 VM 名称，使用逗号分隔名称，不带空格。 限制为 140 个 VM。 如果将超过 140 个 VM 添加到此逗号分隔列表中，则设置为排除的 VM 可能会无意中启动或停止。|
|External_Start_ResourceGroupNames | 指定针对启动操作的一个或多个资源组，使用逗号分隔值。|
|External_Stop_ResourceGroupNames | 指定针对停止操作的一个或多个资源组，使用逗号分隔值。|
|External_WaitTimeForVMRetrySeconds |在序列启动/停止运行簿的 VM 上执行操作的等待时间（以秒为单位）。<br> 默认值为 2100 秒，支持配置为最大值 10800 或 3 小时。|
|Internal_AutomationAccountName | 指定自动化帐户的名称。|
|Internal_AutoSnooze_ARM_WebhookURI | 为 VM 的自动停止方案指定 Webhook URI。|
|Internal_AutoSnooze_WebhookUri | 为经典 VM 的自动停止方案指定 Webhook URI。|
|Internal_AzureSubscriptionId | 指定 Azure 订阅 ID。|
|Internal_ResourceGroupName | 指定自动化帐户资源组名称。|

>[!NOTE]
>对于变量**External_WaitTimeForVMRetryInSeconds**，默认值已从 600 更新到 2100。 此变量允许**序列启动/停止方案**运行簿等待子操作指定秒数，然后再继续执行下一个操作。
>

在所有方案中，除了为 **AutoStop_CreateAlert_Parent**、**SequencedStartStop_Parent** 和 **ScheduledStartStop_Parent** Runbook 提供以逗号分隔的 VM 列表之外，**External_Start_ResourceGroupNames**、**External_Stop_ResourceGroupNames** 和 **External_ExcludeVMNames** 变量都是确定目标 VM 时所必需的。 也就是说，你的 VM 必须驻留在目标资源组中才能进行启动和停止操作。 此逻辑的工作方式类似于 Azure Policy，因为可以在订阅或资源组中设定目标，并且具有新创建的 VM 继承的操作。 此方法避免了必须为每个 VM 维护一个单独计划和管理规模启动和停止操作。

### <a name="schedules"></a>计划

下表列出了在自动化帐户中创建的每个默认计划。你可以修改默认计划或创建自己的自定义计划。默认情况下，除了 Scheduled_StartVM 和 Scheduled-StopVM，所有计划都是禁用的********。

不应启用所有计划，因为这可能会创建重叠的计划操作。 最好确定希望执行哪些优化，然后再进行相应的修改。 请参阅概述部分的示例方案以查看进一步解释。

|计划名称 | 频率 | 描述|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | 每隔 8 小时 | 每隔 8 小时运行一次 AutoStop_CreateAlert_Parent Runbook，它将基于 Azure 自动化变量中的 External_Start_ResourceGroupNames、External_Stop_ResourceGroupNames 和 External_ExcludeVMNames 的值依次停止 VM。 或者，可以使用 VMList 参数指定用逗号分隔的 VM 列表。|
|Scheduled_StopVM | 用户定义，每天 | 每天在指定的时间运行带有 _Stop_ 参数的 Scheduled_Parent Runbook。自动停止所有满足通过资产变量定义的规则 VM。启用相关计划 Scheduled-StartVM****。|
|Scheduled_StartVM | 用户定义，每天 | 每天在给定的时间运行带有 _Start_ 参数的 Scheduled_Parent Runbook。 自动启动所有满足相应变量定义的规则的 VM。启用相关计划 Scheduled-StopVM****。|
|Sequenced-StopVM | 凌晨 1:00 (UTC)，每星期五 | 每星期五在指定的时间运行带有 _Stop_ 参数的 Sequenced_Parent Runbook。将按顺序（升序）停止具有相应变量定义的 **SequenceStop** 标记的所有 VM。 有关标记值和资产变量的详细信息，请参阅“Runbook”部分。启用相关计划 Sequenced-StartVM****。|
|Sequenced-StartVM | 下午 1:00 (UTC)，每星期一 | 每星期一在给定的时间运行带有 Start__ 参数的 Sequenced_Parent runbook。 按顺序（降序）启动具有相应变量定义的 **SequenceStart** 标记的所有 VM。 有关标记值和资产变量的详细信息，请参阅“Runbook”部分。 启用相关计划 Sequenced-StopVM****。|

## <a name="enable-the-solution"></a>启用此解决方案

要开始使用该解决方案，请使用[启用开始/停止 VM 解决方案](automation-solution-vm-management-enable.md)中的步骤。

## <a name="viewing-the-solution"></a>查看解决方案

在通过以下方式之一启用解决方案后，可以访问该解决方案：

* 从自动化帐户中，选择 **"相关资源**"下的 **"开始/停止 VM"。** 在 **"开始/停止 VM"** 页上，选择"从页面右侧**管理解决方案**"，在 **"管理开始/停止 VM 解决方案**"一节中。

* 导航到链接到自动化帐户的日志分析工作区，选择工作区后，从左侧窗格中选择 **"解决方案**"。 在 **"解决方案**"页上，从列表中选择 **"开始-停止-VM_工作区]** 解决方案。  

选择该解决方案会显示“Start-Stop-VM[workspace]”解决方案页面。**** 在此处可以查看重要详细信息，例如 **StartStopVM** 磁贴。 如同在 Log Analytics 工作区中一样，此磁贴显示解决方案中已成功启动和完成的 Runbook 作业计数与图形表示。

![自动化更新管理解决方案页面](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

在此处，可以通过单击圆环磁贴对作业记录执行进一步的分析。 解决方案仪表板显示作业历史记录和预定义的日志搜索查询。 切换到日志分析高级门户，以便根据您的搜索查询进行搜索。

## <a name="update-the-solution"></a>更新解决方案

如果你部署了此解决方案的以前版本，则在部署更新的版本之前，必须先从帐户中将其删除。 按照步骤[删除解决方案，](#remove-the-solution)然后按照步骤[部署解决方案](automation-solution-vm-management-enable.md)。

## <a name="remove-the-solution"></a>删除解决方案

如果决定不再需要使用解决方案，可从自动化帐户中删除它。 删除解决方案的操作只会删除 Runbook， 而不会删除添加解决方案时创建的计划或变量。 如果不会将它们与其他 Runbook 配合使用，则需要手动删除这些资产。

若要删除解决方案，请执行以下步骤：

1. 从您的自动化帐户中，在 **"相关资源**"下，选择 **"链接工作区**"。

2. 选择 **"转到工作区**"。

3. 在 **"常规"** 下，选择 **"解决方案**"。 

4. 在“解决方案”页中，请选择解决方案 Start-Stop-VM[Workspace]********。 在“VMManagementSolution[Workspace]”页上，从菜单中选择“删除”。********<br><br> ![删除 VM 管理解决方案](media/automation-solution-vm-management/vm-management-solution-delete.png)

5. 在“删除解决方案”**** 窗口中，确认要删除该解决方案。

6. 在验证信息和删除解决方案期间，可以在菜单中的“通知”**** 下面跟踪操作进度。 删除解决方案的进程启动后，系统会返回“解决方案”页****。

此进程不会删除自动化帐户和 Log Analytics 工作区。 如果不想保留 Log Analytics 工作区，则需要手动删除。 可以通过 Azure 门户完成此操作。

1. 在 Azure 门户中，搜索并选择**日志分析工作区**。

2. 在**日志分析工作区**页面上，选择工作区。

3. 从工作区设置页面上的菜单中选择“删除”。****

如果不想要保留 Azure 自动化帐户组件，可以手动删除每个组件。 有关该解决方案创建的 Runbook、变量和计划的列表，请参阅[解决方案组件](#solution-components)。

## <a name="next-steps"></a>后续步骤

为 Azure VM[启用](automation-solution-vm-management-enable.md)在非工作时间开始/停止解决方案。
