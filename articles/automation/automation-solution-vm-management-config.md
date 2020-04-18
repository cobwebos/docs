---
title: Azure 自动化 配置非工作时间解决方案的启动/停止 VM
description: 本文介绍如何在非工作时间解决方案期间配置开始/停止 VM 以支持不同的用例或方案。
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 9842a736cf922e0490f2b0c8acb1d2e5833f3d6c
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604760"
---
# <a name="how-to-configure-startstop-vms-during-off-hours-solution"></a>如何在非工作时间配置启动/停止 VM 解决方案

借助**非工作时间启动/停止 VM**解决方案，您可以：

- [计划 VM 启动和停止](#schedule)。
- 使用[Azure 标记](#tags)（经典 VM 不支持）计划 VM 以升序启动和停止。
- 基于[CPU 使用率低](#cpuutil)自动停止 VM。

本文介绍如何成功配置解决方案以支持这些方案。 您还可以了解如何为解决方案执行其他常见配置设置，例如：

* [配置电子邮件通知](#configure-email-notifications)

* [添加 VM](#add-a-vm)

* [排除 VM](#exclude-a-vm)

* [修改启动和关闭计划](#modify-the-startup-and-shutdown-schedules)

## <a name="scenario-1-startstop-vms-on-a-schedule"></a><a name="schedule"></a>方案 1：按计划启动/停止 VM

此方案是首次部署解决方案时的默认配置。 例如，你可以将其配置为在下班时晚上停止订阅中的所有 VM，并在早上回到办公室时启动它们。 在部署期间配置计划 **Scheduled-StartVM** 和 **Scheduled-StopVM** 时，它们启动和停止目标 VM。 支持将此解决方案配置为仅停止 VM，若要了解如何配置自定义计划，请参阅[修改启动和关闭计划](#modify-the-startup-and-shutdown-schedules)。

> [!NOTE]
> 时区是你配置计划时间参数时的当前时区。 但是它以 UTC 格式存储在 Azure 自动化中。 你不必执行任何时区转换，因为这是在部署过程中处理的。

可以通过配置以下变量控制哪些 VM 位于范围中：**External_Start_ResourceGroupNames**、**External_Stop_ResourceGroupNames** 和 **External_ExcludeVMNames**。

可以启用针对订阅和资源组的操作，或者针对特定虚拟机的列表，但不能同时启用两者。

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>针对订阅和资源组确定启动和停止操作的目标

1. 配置`External_Stop_ResourceGroupNames`和`External_ExcludeVMNames`变量以指定目标 VM。

2. 启用并更新 **Scheduled-StartVM** 和 **Scheduled-StopVM** 计划。

3. 运行**ScheduledStartStop_Parent**运行簿，**操作**参数字段设置为**开始****，WHATIF**参数字段设置为 True 以预览更改。

### <a name="target-the-start-and-stop-action-by-vm-list"></a>根据 VM 列表确定启动和停止操作的目标

1. 运行ScheduledStartStop_Parent**运行簿**，操作**ACTION**设置为**启动**，在**VMList**参数字段中添加逗号分隔的 VM 列表，然后将**WHATIF**参数字段设置为 True。 预览更改。

2. 使用逗`External_ExcludeVMNames`号分隔的 VM 列表（VM1、VM2、VM3）配置变量。

3. 此方案不遵循 和`External_Start_ResourceGroupNames``External_Stop_ResourceGroupnames`变量。 对于此方案，需要创建自己的自动化计划。 有关详细信息，请参阅[在 Azure 自动化中计划 Runbook](../automation/automation-schedules.md)。

    > [!NOTE]
    > **目标资源组名称**的值存储为`External_Start_ResourceGroupNames`和`External_Stop_ResourceGroupNames`的值。 如需更细化的配置粒度，可以修改这些变量以面向不同的资源组。 对于启动操作，请使用`External_Start_ResourceGroupNames`和`External_Stop_ResourceGroupNames`用于停止操作。 VM 会自动添加到启动和停止计划中。

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>方案 2：使用标记按顺序启动/停止 VM

在支持分布式工作负荷的多个 VM 上包含两个或更多组件的环境中，支持按顺序启动和停止组件的顺序非常重要。 为了实现此方案，可以执行以下步骤：

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>针对订阅和资源组确定启动和停止操作的目标

1. `sequencestart`将具有正整`sequencestop`数值的标记添加到 针对 的`External_Start_ResourceGroupNames`VM 和`External_Stop_ResourceGroupNames`变量。 按升序执行启动和停止操作。 若要了解如何标记 VM，请参阅[在 Azure 中标记 Windows 虚拟机](../virtual-machines/windows/tag.md)和[在 Azure 中标记 Linux 虚拟机](../virtual-machines/linux/tag.md)。

2. 修改计划 **Sequenced-StartVM** 和 **Sequenced-StopVM** 的日期和时间，以满足要求并启用计划。

3. 运行**SequencedStartStop_Parent**运行簿，**操作**设置为**启动****，WHATIF**设置为 True，以预览更改。

4. 预览操作，并根据需要进行任何更改，然后对生产 VM 执行操作。 准备就绪后，手动执行运行簿，参数设置为**False**，或者让自动化计划`Sequenced-StartVM`按照`Sequenced-StopVM`规定的计划自动运行。

### <a name="target-the-start-and-stop-action-by-vm-list"></a>根据 VM 列表确定启动和停止操作的目标

1. `sequencestart`将具有正整`sequencestop`数值的标记添加到计划添加到参数的 VM 中`VMList`。

2. 运行**SequencedStartStop_Parent**运行**簿，操作**设置为**启动**，在**VMList**参数字段中添加逗号分隔的 VM 列表，然后将**WHATIF**设置为 True。 预览更改。

3. 使用逗`External_ExcludeVMNames`号分隔的 VM 列表（VM1、VM2、VM3）配置变量。

4. 此方案不遵循 和`External_Start_ResourceGroupNames``External_Stop_ResourceGroupnames`变量。 对于此方案，需要创建自己的自动化计划。 有关详细信息，请参阅[在 Azure 自动化中计划 Runbook](../automation/automation-schedules.md)。

5. 预览操作，并根据需要进行任何更改，然后对生产 VM 执行操作。 准备就绪后，手动执行**监视和诊断/监视操作组 runbook，** 参数设置为**False**。 或者，让自动化计划`Sequenced-StartVM`并按`Sequenced-StopVM`您规定的计划自动运行。

## <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>方案 3：基于 CPU 利用率自动启动/停止

此解决方案通过评估非高峰时段（如下班后）未使用的 VM，帮助管理订阅中运行 Azure 资源管理器和经典虚拟机的成本，并在处理器利用率低于指定百分比时自动关闭它们。

默认情况下，解决方案已预配置为评估百分比 CPU 指标，并查看平均利用率是否为 5% 或更低。 此方案由以下变量控制，如果默认值不符合您的要求，则可以对其进行修改：

* `External_AutoStop_MetricName`
* `External_AutoStop_Threshold`
* `External_AutoStop_TimeAggregationOperator`
* `External_AutoStop_TimeWindow`
* `External_AutoStop_Frequency`
* `External_AutoStop_Severity`

您可以针对订阅和资源组启用和定位操作，或针对特定 VM 列表。

运行**AutoStop_CreateAlert_Parent** runbook 时，它会验证目标订阅、资源组和 VM 是否存在。 如果存在 VM，则 Runbook 然后按父运行簿调用每个已验证 VM**的AutoStop_CreateAlert_Child**运行簿。 此子运行簿执行以下操作：

* 为每个已验证的 VM 创建指标警报规则。

* 如果 CPU 在指定的时间间隔内低于配置的阈值，则触发特定 VM**的AutoStop_VM_Child**运行簿。 然后，此 Runbook 会尝试停止 VM。

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-subscription"></a>针对订阅中的所有 VM 执行自动停止操作

1. 确保`External_Stop_ResourceGroupNames`变量为空或设置为 * （通配符）。

2. [可选步骤]如果要从自动关闭中排除某些 VM，则可以向`External_ExcludeVMNames`变量添加由逗号分隔的 VM 名称列表。

3. 启用`Schedule_AutoStop_CreateAlert_Parent`运行计划以为订阅中的所有 VM 创建所需的停止 VM 指标警报规则。 运行此类计划，您可以在将新 VM 添加到订阅时创建新的指标警报规则。

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-resource-group-or-multiple-resource-groups"></a>针对资源组或多个资源组中的所有 VM 执行自动停止操作

1. 向`External_Stop_ResourceGroupNames`变量添加由逗号分隔的资源组名称列表。

2. 如果要从自动关机中排除某些 VM，则可以向`External_ExcludeVMNames`变量添加与逗号分隔的 VM 名称列表。

3. 启用**运行Schedule_AutoStop_CreateAlert_Parent**计划，以便为资源组中的所有 VM 创建所需的停止 VM 指标警报规则。 按计划运行此操作，可以在将新 VM 添加到资源组时创建新的指标警报规则。

### <a name="to-target-the-autostop-action-to-a-list-of-vms"></a>将自动停止操作定位到 VM 列表中

1. 创建新[的计划](shared-resources/schedules.md#creating-a-schedule)并将其链接到**AutoStop_CreateAlert_Parent** runbook，将 VM 名称的逗号分隔列表`VMList`添加到参数中。

2. 或者，如果要从自动关机中排除某些 VM，则可以向`External_ExcludeVMNames`变量添加由逗号分隔的 VM 名称列表。

## <a name="configure-email-notifications"></a>配置电子邮件通知

要在部署解决方案后更改电子邮件通知，请修改部署期间创建的操作组。  

> [!NOTE]
> Azure 政府云中的订阅不支持此解决方案的电子邮件功能。

1. 在 Azure 门户中，导航到**监视器**，然后**导航操作组**。 选择名为**StartStop_VM_Notication**的操作组。

    ![自动化更新管理解决方案页面](media/automation-solution-vm-management/azure-monitor.png)

2. 在“StartStop_VM_Notification”页上，单击“详细信息”下的“编辑详细信息”************。 ****“电子邮件/短信/推送/语音”页面随即打开。 更新电子邮件地址，并单击“确定”以保存更改****。

    ![自动化更新管理解决方案页面](media/automation-solution-vm-management/change-email.png)

    也可以向操作组添加其他操作，若要了解有关操作组的详细信息，请参阅[操作组](../azure-monitor/platform/action-groups.md)

以下是解决方案关闭虚拟机时发送的示例电子邮件。

![自动化更新管理解决方案页面](media/automation-solution-vm-management/email.png)

## <a name="addexclude-vms"></a><a name="add-exclude-vms"></a>添加/排除 VM

借助该解决方案，可添加要作为该解决方案的目标的 VM，或者专门从该解决方案中排除计算机。

### <a name="add-a-vm"></a>添加 VM

有两个选项可用于确保 VM 在运行时包含在"开始/停止"解决方案中。

* 解决方案的每个父[运行簿](automation-solution-vm-management.md#runbooks)都有一个`VMList`参数。 在为情况安排适当的父 Runbook 时，可以将 VM 名称的逗号分隔列表传递给此参数，并且这些 VM 将在解决方案运行时包含。

* 要选择多个 VM，请`External_Start_ResourceGroupNames`设置`External_Stop_ResourceGroupNames`并且使用包含要启动或停止的 VM 的资源组名称。 还可以将变量设置为 的值，`*`以便解决方案针对订阅中的所有资源组运行。

### <a name="exclude-a-vm"></a>排除 VM

要从解决方案中排除 VM，可以将其添加到变量中`External_ExcludeVMNames`。 此变量是要从"开始/停止"解决方案中排除的特定 VM 的逗号分隔列表。 此列表限制为 140 个 VM。 如果将超过 140 个 VM 添加到此逗号分隔列表中，则设置为排除的 VM 可能会无意中启动或停止。

## <a name="modify-the-startup-and-shutdown-schedules"></a>修改启动和关闭计划

按照[在 Azure 自动化中计划 runbook](automation-schedules.md) 中所述的相同步骤来管理此解决方案中的启动和关闭计划。 需要有一个单独的计划来启动和停止 VM。

支持将解决方案配置为在特定的时间仅停止 VM。 在这种情况下，您只需创建一个**停止**计划，并且没有相应的**开始**计划。 若要实现此目的，需要：

1. 确保已添加 VM 在`External_Stop_ResourceGroupNames`变量中关闭的资源组。

2. 针对要关闭 VM 的时间创建你自己的计划。

3. 导航到 **ScheduledStartStop_Parent** runbook，然后单击“计划”。**** 这允许你选择在上一步中创建的计划。

4. 选择**参数并运行设置**，并将 **"操作"** 字段设置为 **"停止**"。

5. 选择“确定”**** 保存更改。

## <a name="next-steps"></a>后续步骤

* 要了解如何在非工作时间对启动/停止 VM 进行故障排除，请参阅 [故障排除启动/停止 VM。](troubleshoot/start-stop-vm.md)

* [查看](automation-solution-vm-management-logs.md)写入 Azure 监视器日志的自动化记录和示例日志搜索查询，以分析从开始/停止 VM 分析自动化运行簿作业的状态。
