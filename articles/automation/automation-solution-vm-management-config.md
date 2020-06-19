---
title: 配置 Azure 自动化“在空闲时间启动/停止 VM”
description: 本文介绍如何配置“在空闲时间启动/停止 VM”功能以支持不同的用例或方案。
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 127c924da44c7e596d93b21d89ff4591a90ba7cf
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2020
ms.locfileid: "83827669"
---
# <a name="configure-startstop-vms-during-off-hours"></a>配置“在空闲时间启动/停止 VM”

本文介绍如何配置[在空闲时间启动/停止 VM](automation-solution-vm-management.md) 功能以支持所描述的方案。 你还可以了解如何执行以下操作：

* [配置电子邮件通知](#configure-email-notifications)
* [添加 VM](#add-a-vm)
* [排除 VM](#exclude-a-vm)
* [修改启动和关闭计划](#modify-the-startup-and-shutdown-schedules)

## <a name="scenario-1-startstop-vms-on-a-schedule"></a><a name="schedule"></a>场景 1：按计划启动/停止 VM

此方案是首次部署“在空闲时间启动/停止 VM”时的默认配置。 例如，你可以将该功能配置为在晚上离开公司时停止订阅中的所有 VM，并在早上回到办公室时启动它们。 在部署期间配置计划 **Scheduled-StartVM** 和 **Scheduled-StopVM** 时，它们启动和停止目标 VM。 

支持将此解决方案配置为仅停止 VM。 若要了解如何配置自定义计划，请参阅[修改启动和关闭计划](#modify-the-startup-and-shutdown-schedules)。

> [!NOTE]
> 该功能使用的时区是你配置计划时间参数时的当前时区。 但是，Azure 自动化以 UTC 格式将其存储在 Azure 自动化中。 你不必执行任何时区转换，因为这是在计算机部署过程中处理的。

若要控制范围内的 VM，请配置变量：`External_Start_ResourceGroupNames`、`External_Stop_ResourceGroupNames` 和 `External_ExcludeVMNames`。

可以启用针对订阅和资源组的操作，或者针对特定虚拟机的列表，但不能同时启用两者。

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>针对订阅和资源组确定启动和停止操作的目标

1. 配置 `External_Stop_ResourceGroupNames` 和 `External_ExcludeVMNames` 变量以指定目标 VM。

2. 启用并更新 **Scheduled-StartVM** 和 **Scheduled-StopVM** 计划。

3. 在将 ACTION 参数字段设置为“start”且将 WHATIF 参数字段设置为“True”的情况下运行 ScheduledStartStop_Parent runbook 来预览更改   。

### <a name="target-the-start-and-stop-action-by-vm-list"></a>根据 VM 列表确定启动和停止操作的目标

1. 在 ACTION 设置为“start”的情况下运行 ScheduledStartStop_Parent runbook，在 VMList 参数字段中添加以逗号分隔的 VM 列表，然后将 WHATIF 参数字段设置为“True”    。 预览更改。

2. 通过 VM 的逗号分隔列表 (VM1, VM2, VM3) 配置 `External_ExcludeVMNames` 参数。

3. 此方案不接受 `External_Start_ResourceGroupNames` 和 `External_Stop_ResourceGroupnames` 变量。 对于此方案，需要创建自己的自动化计划。 有关详细信息，请参阅[在 Azure 自动化中计划 runbook](shared-resources/schedules.md)。

    > [!NOTE]
    > “目标资源组名称”的值存储为 `External_Start_ResourceGroupNames` 和 `External_Stop_ResourceGroupNames` 的值。 如需更细化的配置粒度，可以修改这些变量以面向不同的资源组。 对于启动操作，请使用 `External_Start_ResourceGroupNames`对于停止操作，请使用 `External_Stop_ResourceGroupNames`。 VM 会自动添加到启动和停止计划中。

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>场景 2：使用标记按顺序启动/停止 VM

在支持分布式工作负荷的多个 VM 上包含两个或更多组件的环境中，支持按顺序启动和停止组件的顺序非常重要。 

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>针对订阅和资源组确定启动和停止操作的目标

1. 将具有正整数值的 `sequencestart` 和 `sequencestop` 标记添加到 `External_Start_ResourceGroupNames` 和 `External_Stop_ResourceGroupNames` 变量中的目标 VM。 按升序执行启动和停止操作。 若要了解如何标记 VM，请参阅[在 Azure 中标记 Windows 虚拟机](../virtual-machines/windows/tag.md)和[在 Azure 中标记 Linux 虚拟机](../virtual-machines/linux/tag.md)。

2. 修改计划 **Sequenced-StartVM** 和 **Sequenced-StopVM** 的日期和时间，以满足要求并启用计划。

3. 在将 ACTION 参数设置为“start”且将 WHATIF 设置为“True”的情况下运行 SequencedStartStop_Parent runbook 来预览更改   。

4. 预览操作，并根据需要进行任何更改，然后对生产 VM 执行操作。 准备就绪后，可以手动执行参数设置为“False”的 runbook，或让自动化计划 Sequenced-StartVM 和 Sequenced-StopVM 在规定的计划之后自动运行  。

### <a name="target-the-start-and-stop-actions-by-vm-list"></a>根据 VM 列表确定启动和停止操作的目标

1. 向计划添加到 `VMList` 参数的 VM 添加具有正整数值的 `sequencestart` 和 `sequencestop` 标记。

2. 在 ACTION 设置为“start”的情况下运行 SequencedStartStop_Parent runbook，在 VMList 参数字段中添加以逗号分隔的 VM 列表，然后将 WHATIF 设置为“True”    。 预览更改。

3. 通过 VM 的逗号分隔列表 (VM1, VM2, VM3) 配置 `External_ExcludeVMNames` 参数。

4. 此方案不接受 `External_Start_ResourceGroupNames` 和 `External_Stop_ResourceGroupnames` 变量。 对于此方案，需要创建自己的自动化计划。 有关详细信息，请参阅[在 Azure 自动化中计划 runbook](shared-resources/schedules.md)。

5. 预览操作，并根据需要进行任何更改，然后对生产 VM 执行操作。 准备就绪后，在将参数设置为“False”的情况下手动执行 monitoring-and-diagnostics/monitoring-action-groupsrunbook 。 或者让自动化计划 Sequenced-StartVM 和 Sequenced-StopVM 在规定的计划之后自动运行 。

## <a name="scenario-3-start-or-stop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>场景 3：根据 CPU 利用率自动启动或停止

在空闲时间启动/停止 VM 可通过评估在非高峰时段（如下班之后）未使用的计算机并在处理器利用率小于指定百分比时自动关闭它们来管理订阅中运行 Azure 资源管理器和经典 VM 的成本。

默认情况下，该功能已预配置为评估百分比 CPU 指标，并查看平均利用率是否为 5% 或更低。 此方案由以下变量控制，并且可以在默认值不符合要求时进行更改：

* `External_AutoStop_MetricName`
* `External_AutoStop_Threshold`
* `External_AutoStop_TimeAggregationOperator`
* `External_AutoStop_TimeWindow`
* `External_AutoStop_Frequency`
* `External_AutoStop_Severity`

你可以启用并将操作的目标设置为某个订阅和资源组，或将目标设置为特定的 VM 列表。

运行 AutoStop_CreateAlert_Parent runbook 时，它将验证目标订阅、资源组和 VM 是否存在。 如果 VM 存在，runbook 将为父 runbook 验证的每个 VM 调用 AutoStop_CreateAlert_Child runbook。 该子 runbook：

* 为每个已验证 VM 创建指标警报规则。
* 如果 CPU 在指定时间间隔内降至配置的阈值以下，则将触发特定 VM 的 AutoStop_VM_Child runbook。 
* 尝试停止 VM。

### <a name="target-the-autostop-action-against-all-vms-in-a-subscription"></a>将自动停止操作的目标设置为订阅中的所有 VM

1. 请确保 `External_Stop_ResourceGroupNames` 变量为空或设置为 *（通配符）。

2. （可选）如果要从自动停止操作中排除某些 VM，可以将以逗号分隔的 VM 名称列表添加到 `External_ExcludeVMNames` 变量中。

3. 让 Schedule_AutoStop_CreateAlert_Parent 计划运行，以便为订阅中的所有 VM 创建所需的“停止 VM”指标警报规则。 运行这种类型的计划，让你可以在将新 VM 添加到订阅时创建新的指标警报规则。

### <a name="target-the-autostop-action-against-all-vms-in-a-resource-group-or-multiple-resource-groups"></a>将自动停止操作的目标设置为资源组或多个资源组中的所有 VM

1. 将以逗号分隔的资源组名称列表添加到 `External_Stop_ResourceGroupNames` 变量中。

2. 如果要从自动停止中排除某些 VM，可以将以逗号分隔的 VM 名称列表添加到 `External_ExcludeVMNames` 变量中。

3. 启用 Schedule_AutoStop_CreateAlert_Parent 计划以运行，以便为资源组中的所有 VM 创建所需的“停止 VM”指标警报规则。 通过按计划运行此操作，可以在将新 VM 添加到资源组时创建新的指标警报规则。

### <a name="target-the-autostop-action-to-a-list-of-vms"></a>将自动停止操作目标设置为 VM 列表

1. 创建新的[计划](shared-resources/schedules.md#create-a-schedule)，将其关联到 AutoStop_CreateAlert_Parent runbook，并在 `VMList` 参数中添加一个以逗号分隔的 VM 名称列表。

2. （可选）如果要从自动停止操作中排除某些 VM，可以将以逗号分隔的 VM 名称列表添加到 `External_ExcludeVMNames` 变量中。

## <a name="configure-email-notifications"></a>配置电子邮件通知

若要在部署“在空闲时间启动/停止 VM”之后更改电子邮件通知，可以修改在部署期间创建的操作组。  

> [!NOTE]
> Azure 政府云中的订阅不支持此功能的电子邮件功能。

1. 在 Azure 门户中，依次导航到“监视”和“操作组” 。 选择名为“StartStop_VM_Notication”的操作组。

    ![Azure 更新管理页面](media/automation-solution-vm-management/azure-monitor.png)

2. 在“StartStop_VM_Notification”页上，单击“详细信息”下的“编辑详细信息” 。 “电子邮件/短信/推送/语音”页面随即打开。 更新电子邮件地址，并单击“确定”以保存更改。

    ![Azure 更新管理页面](media/automation-solution-vm-management/change-email.png)

    也可以向操作组添加其他操作，若要了解有关操作组的详细信息，请参阅[操作组](../azure-monitor/platform/action-groups.md)

以下是该功能关闭虚拟机时发送的示例电子邮件。

![Azure 更新管理页面](media/automation-solution-vm-management/email.png)

## <a name="add-or-exclude-vms"></a><a name="add-exclude-vms"></a>添加或排除 VM

通过该功能可添加要设定为目标或排除的 VM。 

### <a name="add-a-vm"></a>添加 VM

你可以通过两种方法来确保在运行该功能时包含 VM：

* 该功能的每个父 [runbook](automation-solution-vm-management.md#runbooks) 都具有 `VMList` 参数。 根据自身情况计划适当的父 runbook 时，可以向此参数传递一个逗号分隔的 VM 名称列表，当该功能运行时，将包含这些 VM。

* 若要选择多个 VM，请将 `External_Start_ResourceGroupNames` 和 `External_Stop_ResourceGroupNames` 设置为包含要启动或停止的 VM 的资源组名称。 还可以将变量设置为 `*` 的值，使该功能针对订阅中的所有资源组运行。

### <a name="exclude-a-vm"></a>排除 VM

若要从“在空闲时间停止/启动 VM”中排除 VM，可将其名称添加到 `External_ExcludeVMNames` 变量。 此变量是要从该功能中排除的特定 VM 的逗号分隔列表。 此列表限制为 140 个 VM。 如果向此列表添加超过 140 个 VM，则设置为要排除的 VM 可能会无意中启动或停止。

## <a name="modify-the-startup-and-shutdown-schedules"></a>修改启动和关闭计划

按照[在 Azure 自动化中计划 runbook](shared-resources/schedules.md) 中所述的相同步骤来管理此功能中的启动和关闭计划。 需要单独的计划来启动和停止 VM。

支持将该功能配置为在特定的时间仅停止 VM。 在此方案中，只需创建一个停止计划，而非相应的启动计划。 

1. 请确保已在 `External_Stop_ResourceGroupNames` 变量中添加了要关闭的 VM 的资源组。

2. 就 VM 的关闭时间创建你自己的计划。

3. 导航到 **ScheduledStartStop_Parent** runbook，然后单击“计划”。 这允许你选择在上一步中创建的计划。

4. 选择“参数和运行设置”并将 ACTION 字段设置为“Stop”  。

5. 选择“确定”保存更改。

## <a name="next-steps"></a>后续步骤

* 若要在操作期间监视该功能，请参阅[从“在空闲时间启动/停止 VM”查询日志](automation-solution-vm-management-logs.md)。
* 若要处理 VM 管理过程中出现的问题，请参阅[排查在空闲时间启动/停止 VM 的问题](troubleshoot/start-stop-vm.md)。
