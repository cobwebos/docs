---
title: 使用 Azure 自动化排除 VM 启动和停止故障
description: 本文介绍了如何使用 Azure 自动化排除 VM 启动和停止故障
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/13/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1894fc1823772c27f37829b9b27ec515dcb841b7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57861054"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>排除“在空闲时间启动/停止 VM”解决方案的故障

## <a name="deployment-failure"></a>场景：启动/停止 VM 解决方案未能正确部署

### <a name="issue"></a>问题

部署[空闲时间启动/停止 VM 解决方案](../automation-solution-vm-management.md)时，收到以下错误之一：

```
Account already exists in another resourcegroup in a subscription. ResourceGroupName: [MyResourceGroup].
```

```
Resource 'StartStop_VM_Notification' was disallowed by policy. Policy identifiers: '[{\\\"policyAssignment\\\":{\\\"name\\\":\\\"[MyPolicyName]”.
```

```
The subscription is not registered to use namespace 'Microsoft.OperationsManagement'.
```

```
The subscription is not registered to use namespace 'Microsoft.Insights'.
```

```
The scope '/subscriptions/000000000000-0000-0000-0000-00000000/resourcegroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView' cannot perform write operation because following scope(s) are locked: '/subscriptions/000000000000-0000-0000-0000-00000000/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView'. Please remove the lock and try again
```

### <a name="cause"></a>原因

部署可能会因以下原因之一失败：

1. 所选区域中已存在具有相同名称的自动化帐户。
2. 存在禁止部署启动/停止 VM 解决方案的策略。
3. `Microsoft.OperationsManagement`、`Microsoft.Insights` 或 `Microsoft.Automation` 资源类型未注册。
4. Log Analytics 工作区对其进行锁定。

### <a name="resolution"></a>解决方法

请查看以下可能问题解决方案列表或要检查的地方：

1. 即使自动化帐户位于不同的资源组中，它们在 Azure 区域中也需要是唯一的。 检查目标区域中的现有自动化帐户。
2. 现有策略阻止部署启动/停止 VM 解决方案所需的资源。 转到 Azure 门户中的策略分配，并检查是否有禁止部署此资源的策略分配。 若要了解详细信息，请参阅 [RequestDisallowedByPolicy](../../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)。
3. 若要部署启动/停止 VM 解决方案，订阅需要注册到以下 Azure 资源命名空间：
    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   若要详细了解注册提供程序时出现的错误，请参阅[解决资源提供程序注册错误](../../azure-resource-manager/resource-manager-register-provider-errors.md)。
4. 如果 Log Analytics 工作区中存在锁定，请转到 Azure 门户中的工作区并删除对资源的任何锁定。

## <a name="all-vms-fail-to-startstop"></a>场景：所有 VM 都无法启动/停止

### <a name="issue"></a>问题

你已配置“启动/停止 VM”解决方案，但它无法启动或停止已配置的全部 VM。

### <a name="cause"></a>原因

出现此错误的原因可能是以下之一：

1. 未正确配置日程安排
2. 可能未正确配置 RunAs 帐户
3. runbook 可能遇到错误
4. 可能已排除 VM

### <a name="resolution"></a>解决方法

请查看以下可能问题解决方案列表或要检查的地方：

* 检查是否已正确配置“启动/停止 VM”解决方案的日程安排。 若要了解如何配置日程安排，请参阅[日程安排](../automation-schedules.md)一文。

* 检查 runbook 的作业流是否有任何错误。 在门户中，转到“自动化帐户”，并选择“流程自动化”下的“作业”。 在“作业”页中，查找以下一个 runbook 中的作业：

  * AutoStop_CreateAlert_Child
  * AutoStop_CreateAlert_Parent
  * AutoStop_Disable
  * AutoStop_VM_Child
  * ScheduledStartStop_Base_Classic
  * ScheduledStartStop_Child_Classic
  * ScheduledStartStop_Child
  * ScheduledStartStop_Parent
  * SequencedStartStop_Parent

* 验证 [RunAs 帐户](../manage-runas-account.md)是否对你尝试启动或停止的 VM 拥有适当权限。 若要了解如何检查对资源的权限，请参阅[快速入门：使用 Azure 门户查看分配给用户的角色](../../role-based-access-control/check-access.md)。 需要提供 RunAs 帐户使用的服务主体的应用程序 ID。 若要检索此值，可以在 Azure 门户中转到“自动化帐户”，选择“帐户设置”下的“RunAs 帐户”，再单击相应的 RunAs 帐户。

* 已明确排除的 VM 可能无法启动或停止。 已排除的 VM 是在解决方案部署到的自动化帐户的 External_ExcludeVMNames 变量中进行设置。 下面的示例展示了如何使用 PowerShell 查询此值。

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="some-vms-fail-to-startstop"></a>场景：部分 VM 无法启动或停止

### <a name="issue"></a>问题

你已配置“启动/停止 VM”解决方案，但它无法启动或停止已配置的部分 VM。

### <a name="cause"></a>原因

出现此错误的原因可能是以下之一：

1. 如果使用的是序列方案，标记可能会缺失或不正确
2. 可能已排除 VM
3. RunAs 帐户对 VM 的权限可能不足
4. 可能有一些 VM 设置阻止它启动或停止

### <a name="resolution"></a>解决方法

请查看以下可能问题解决方案列表或要检查的地方：

* 如果使用“在空闲时间启动/停止 VM”解决方案的[序列方案](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags)，必须确保要启动或停止的每个 VM 都有正确标记。 请确保要启动的 VM 有 `sequencestart` 标记，要停止的 VM 有 `sequencestop` 标记。 两个标记都必须有正整数值。 可使用下面示例的查询来查找所有 VM 及其标记和值。

  ```powershell-interactive
  Get-AzureRmResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* 已明确排除的 VM 可能无法启动或停止。 已排除的 VM 是在解决方案部署到的自动化帐户的 External_ExcludeVMNames 变量中进行设置。 下面的示例展示了如何使用 PowerShell 查询此值。

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* 自动化帐户的 RunAs 帐户必须对 VM 拥有适当权限，才能启动和停止 VM。 若要了解如何检查对资源的权限，请参阅[快速入门：使用 Azure 门户查看分配给用户的角色](../../role-based-access-control/check-access.md)。 需要提供 RunAs 帐户使用的服务主体的应用程序 ID。 若要检索此值，可以在 Azure 门户中转到“自动化帐户”，选择“帐户设置”下的“RunAs 帐户”，再单击相应的 RunAs 帐户。

* 如果 VM 无法启动或解除分配，此行为可能是由 VM 本身问题所致。 一些示例或潜在问题包括，正在尝试关闭、服务挂起等情况下应用更新。 请转到 VM 资源，并检查“活动日志”，以确定日志中是否有任何错误。 也可以尝试登录 VM，以确定“事件日志”中是否有任何错误。 若要了解有关故障排除 VM 的详细信息，请参阅[排查 Azure 虚拟机](../../virtual-machines/troubleshooting/index.md)

## <a name="custom-runbook"></a>场景：我的自定义 runbook 无法启动或停止我的 VM

### <a name="issue"></a>问题

你已创作自定义 runbook 或已从 PowerShell 库下载了一个，但它无法正常运行。

### <a name="cause"></a>原因

导致此故障出现的原因可能有很多种。 在 Azure 门户中，转到“自动化帐户”，并选择“流程自动化”下的“作业”。 在“作业”页上，查找 runbook 中的作业，以确定是否有任何失败的作业。

### <a name="resolution"></a>解决方法

建议使用[“在空闲时间启动/停止 VM”解决方案](../automation-solution-vm-management.md)，在 Azure 自动化中启动和停止 VM。 此解决方案由 Microsoft 创作。 Microsoft 不支持自定义 runbook。 若要查找适用于自定义 runbook 的解决方案，可以参阅 [runbook 故障排除](runbooks.md)一文。 这篇文章介绍了所有类型 runbook 的一般指导和故障排除。

## <a name="dont-start-stop-in-sequence"></a>场景：VM 无法按正确顺序启动或停止

### <a name="issue"></a>问题

在解决方案中配置的 VM 无法按正确顺序启动或停止。

### <a name="cause"></a>原因

这是由于 VM 上的标记不正确所致。

### <a name="resolution"></a>解决方法

请按照以下步骤操作，以确保正确配置解决方案。

1. 请确保所有要启动或停止的 VM 都有 `sequencestart` 或 `sequencestop` 标记，具体视情况而定。 这些标记必须有正整数作为值。 VM 根据此值按升序顺序进行处理。
2. 请确保要启动或停止的 VM 的资源组位于 `External_Start_ResourceGroupNames` 或 `External_Stop_ResourceGroupNames` 变量中，具体视情况而定。
3. 通过执行 `SequencedStartStop_Parent` runbook 来测试更改（其中，将 WHATIF 参数设置为 True，以便于预览更改）。

若要详细了解如何使用解决方案按顺序启动和停止 VM，请参阅[按顺序启动和停止 VM](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags)。

## <a name="403"></a>场景：“启动/停止 VM”作业失败，且状态为“403 forbidden” 

### <a name="issue"></a>问题

你发现，“在空闲时间启动/停止 VM”解决方案 runbook 作业失败，且显示 `403 forbidden` 错误。

### <a name="cause"></a>原因

此问题可能是由于 RunAs 帐户未正确配置或已过期所致。 也可能是因为自动化帐户 RunAs 帐户对 VM 资源的权限不足。

### <a name="resolution"></a>解决方法

若要检查 RunAs 帐户是否已正确配置，请在 Azure 门户中转到“自动化帐户”，并选择“帐户设置”下的“RunAs 帐户”。 其中显示 RunAs 帐户的状态，如果 RunAs 帐户未正确配置或已过期，状态会体现出这一点。

如果 RunAs 帐户[未正确配置](../manage-runas-account.md#misconfiguration)，应删除并重新创建 RunAs 帐户。

如果 RunAs 帐户的证书已过期，请按照[自签名证书续订](../manage-runas-account.md#cert-renewal)中列出的步骤操作，以续订证书。

此问题可能是由于缺少权限所致。 若要了解如何检查对资源的权限，请参阅[快速入门：使用 Azure 门户查看分配给用户的角色](../../role-based-access-control/check-access.md)。 需要提供 RunAs 帐户使用的服务主体的应用程序 ID。 若要检索此值，可以在 Azure 门户中转到“自动化帐户”，选择“帐户设置”下的“RunAs 帐户”，再单击相应的 RunAs 帐户。

## <a name="other"></a>场景：上面未列出我的问题

### <a name="issue"></a>问题

你在使用“在空闲时间启动/停止 VM”解决方案时，遇到了本页中未列出的错误或异常结果。

### <a name="cause"></a>原因

很多时候，错误可能是由于使用的解决方案过旧和过时所致。

### <a name="resolution"></a>解决方法

若要修复许多错误，建议删除并更新解决方案。 若要了解如何更新解决方案，请参阅[更新“在空闲时间启动/停止 VM”解决方案](../automation-solution-vm-management.md#update-the-solution)。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过 [Azure 论坛](https://azure.microsoft.com/support/forums/)获取 Azure 专家的解答
* 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。
* 如需更多帮助，可以提交 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。
