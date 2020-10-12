---
title: 排查 Azure 自动化在空闲时间启动/停止 VM 的问题
description: 本文介绍如何排查并解决在使用“在空闲时间启动/停止 VM”功能时产生的问题。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bb8fa53fa07d666693ae545c193faaf3d6d0a30c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86187143"
---
# <a name="troubleshoot-startstop-vms-during-off-hours-issues"></a>排查在空闲时间启动/停止 VM 的问题

本文介绍了如何排查和解决在 VM 上部署 Azure 自动化“在空闲时间启动/停止 VM”这一功能时出现的问题。 

## <a name="scenario-startstop-vms-during-off-hours-fails-to-properly-deploy"></a><a name="deployment-failure"></a>场景：“在空闲时间启动/停止 VM”无法正确部署

### <a name="issue"></a>问题

部署[在空闲时间启动/停止 VM](../automation-solution-vm-management.md) 时，收到以下错误之一：

```error
Account already exists in another resourcegroup in a subscription. ResourceGroupName: [MyResourceGroup].
```

```error
Resource 'StartStop_VM_Notification' was disallowed by policy. Policy identifiers: '[{\\\"policyAssignment\\\":{\\\"name\\\":\\\"[MyPolicyName]".
```

```error
The subscription is not registered to use namespace 'Microsoft.OperationsManagement'.
```

```error
The subscription is not registered to use namespace 'Microsoft.Insights'.
```

```error
The scope '/subscriptions/000000000000-0000-0000-0000-00000000/resourcegroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView' cannot perform write operation because following scope(s) are locked: '/subscriptions/000000000000-0000-0000-0000-00000000/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView'. Please remove the lock and try again
```

```error
A parameter cannot be found that matches parameter name 'TagName'
```

```error
Start-AzureRmVm : Run Login-AzureRmAccount to login
```

### <a name="cause"></a>原因

由于以下某个原因，部署可能失败：

- 所选区域中已存在具有相同名称的自动化帐户。
- 策略不允许部署“在空闲时间启动/停止 VM”。
- `Microsoft.OperationsManagement`、`Microsoft.Insights` 或 `Microsoft.Automation` 资源类型未注册。
- Log Analytics 工作区已锁定。
- 存在过时版本的 AzureRM 模块或“在空闲时间启动/停止 VM”功能。

### <a name="resolution"></a>解决方法

查看下面的修复方法以找到可能的解决方案：

* 自动化帐户在 Azure 区域内必须是唯一的，即使它们位于不同的资源组中也是如此。 检查目标区域中的现有自动化帐户。
* 现有策略会阻止部署“在空闲时间启动/停止 VM”所需的资源。 在 Azure 门户中转到策略分配，检查是否有不允许部署该资源的策略分配。 要了解更多信息，请参阅 [RequestDisallowedByPolicy error](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md)。
* 要部署“在空闲时间启动/停止 VM”，需要将订阅注册到以下 Azure 资源命名空间：

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   若要详细了解注册提供程序时出现的错误，请参阅[解决资源提供程序注册的错误](../../azure-resource-manager/templates/error-register-resource-provider.md)。
* 如果 Log Analytics 工作区中存在锁定，请转到 Azure 门户中的工作区并删除对资源的任何锁定。
* 如果这些解决方法不能解决你的问题，请按照[更新此功能](../automation-solution-vm-management.md#update-the-feature)下的说明重新部署“在空闲时间启动/停止 VM”。

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>场景：所有 VM 都无法启动或停止

### <a name="issue"></a>问题

已配置“在空闲时间启动/停止 VM”，但是该功能并没有启动或停止所有 VM。

### <a name="cause"></a>原因

出现此错误的原因可能是以下之一：

- 未正确配置计划。
- 可能未正确配置运行方式帐户。
- Runbook 可能遇到错误。
- VM 可能已被排除。

### <a name="resolution"></a>解决方法

查看下面的列表以找到可能的解决方案：

* 检查是否为“在空闲时间启动/停止 VM”正确配置了计划。 要了解如何配置计划，请参阅[计划](../shared-resources/schedules.md)。

* 检查[作业流](../automation-runbook-execution.md#job-statuses)以查看是否有任何错误。 从以下某个 Runbook 中查找作业：

  * AutoStop_CreateAlert_Child
  * AutoStop_CreateAlert_Parent
  * AutoStop_Disable
  * AutoStop_VM_Child
  * ScheduledStartStop_Base_Classic
  * ScheduledStartStop_Child_Classic
  * ScheduledStartStop_Child
  * ScheduledStartStop_Parent
  * SequencedStartStop_Parent

* 验证[运行方式帐户](../manage-runas-account.md)对要尝试启动或停止的 VM 是否拥有适当的权限。 若要了解如何检查对资源的权限，请参阅[快速入门：使用 Azure 门户查看分配给用户的角色](../../role-based-access-control/check-access.md)。 需要提供运行方式帐户所使用的服务主体的应用程序 ID。 可以通过转到 Azure 门户中的自动化帐户来检索该值。 在“帐户设置”下选择“运行方式帐户”，然后选择适当的运行方式帐户。

* 如果已显式排除 VM，则这些 VM可能不会启动或停止。 排除的 VM 在该功能部署到的自动化帐户的 `External_ExcludeVMNames` 变量中设置。 下例显示了如何使用 PowerShell 查询该值。

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>场景：部分 VM 无法启动或停止

### <a name="issue"></a>问题

已配置“在空闲时间启动/停止 VM”，但该功能不会启动或停止某些已配置的 VM。

### <a name="cause"></a>原因

出现此错误的原因可能是以下之一：

- 在序列场景中，标记可能丢失或不正确。
- VM 可能会被排除。
- 运行方式帐户可能在 VM 上没有足够的权限。
- VM 可能存在阻止其启动或停止的问题。

### <a name="resolution"></a>解决方法

查看下面的列表以找到可能的解决方案：

* 使用“在空闲时间启动/停止 VM”的[序列场景](../automation-solution-vm-management.md)时，必须确保要启动或停止的每个 VM 都具有正确的标记。 请确保要启动的 VM 有 `sequencestart` 标记，要停止的 VM 有 `sequencestop` 标记。 两个标记都必须有正整数值。 可使用下面示例的查询来查找所有 VM 及其标记和值。

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* 如果已显式排除 VM，则这些 VM可能不会启动或停止。 排除的 VM 在该功能部署到的自动化帐户的 `External_ExcludeVMNames` 变量中设置。 下例显示了如何使用 PowerShell 查询该值。

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* 要启动和停止 VM，自动化帐户的运行方式帐户必须拥有 VM 的适当权限。 若要了解如何检查对资源的权限，请参阅[快速入门：使用 Azure 门户查看分配给用户的角色](../../role-based-access-control/check-access.md)。 需要提供运行方式帐户所使用的服务主体的应用程序 ID。 可以通过转到 Azure 门户中的自动化帐户来检索该值。 在“帐户设置”下选择“运行方式帐户”，然后选择适当的运行方式帐户。
* 如果 VM 在启动或解除分配时遇到问题，则 VM 本身可能存在问题。 例如，当 VM 尝试关闭时正在应用的更新、挂起的服务等。 转到 VM 资源，然后检查“活动日志”以查看日志中是否有任何错误。 也可以尝试登录到 VM，以查看事件日志中是否有任何错误。 若要详细了解 VM 故障排除，请参阅[排查 Azure 虚拟机问题](../../virtual-machines/troubleshooting/index.yml)。
* 检查[作业流](../automation-runbook-execution.md#job-statuses)以查看是否有任何错误。 在门户中，转到“自动化帐户”，然后选择“流程自动化”下的“作业”。

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>场景：我的自定义 runbook 无法启动或停止我的 VM

### <a name="issue"></a>问题

你创作了一个自定义 runbook 或从 PowerShell 库下载了一个，但它无法正常运行。

### <a name="cause"></a>原因

问题的原因可能有很多。 转到 Azure 门户中的自动化帐户，然后选择“流程自动化”下的“作业”。 在“作业”页上，查找 runbook 中的作业，以确定是否有任何失败的作业。

### <a name="resolution"></a>解决方法

建议：

* 使用[在空闲时间启动/停止 VM](../automation-solution-vm-management.md) 功能在 Azure 自动化中启动和停止 VM。 
* 请注意，Microsoft 不支持自定义 runbook。 可能会在[排查 runbook 问题](runbooks.md)中找到自定义 runbook 的解决方法。 检查[作业流](../automation-runbook-execution.md#job-statuses)以查看是否有任何错误。 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>场景：VM 无法按正确顺序启动或停止

### <a name="issue"></a>问题

为该功能启用的 VM 无法按正确顺序启动或停止。

### <a name="cause"></a>原因

该问题是由于 VM 上的标记不正确所致。

### <a name="resolution"></a>解决方法

请按照以下步骤确保已正确启用该功能：

1. 请确保所有要启动或停止的 VM 都有 `sequencestart` 或 `sequencestop` 标记，具体视情况而定。 这些标记必须有正整数作为值。 VM 根据此值按升序顺序进行处理。
1. 请确保要启动或停止的 VM 的资源组位于 `External_Start_ResourceGroupNames` 或 `External_Stop_ResourceGroupNames` 变量中，具体视情况而定。
1. 通过执行 SequencedStartStop_Parent runbook 来测试更改（其中，将 `WHATIF` 参数设置为 True，以便于预览更改）。

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>场景：“在空闲时间启动/停止 VM”作业失败，出现 403 禁止错误

### <a name="issue"></a>问题

你发现“在空闲时间启动/停止 VM”runbook 作业失败，且出现 `403 forbidden`错误。

### <a name="cause"></a>原因

此问题可能是由于运行方式帐户未正确配置或已过期所致。 也可能是因为运行方式帐户对 VM 资源的权限不足。

### <a name="resolution"></a>解决方法

若要验证是否已正确配置运行方式帐户，请转到 Azure 门户中的自动化帐户，然后在“帐户设置”下选择“运行方式帐户”。 如果运行方式帐户配置不正确或已过期，状态将显示相应的条件。

如果运行方式帐户未正确配置，应删除并重新创建运行方式帐户。 有关详细信息，请参阅[管理 Azure 自动化运行方式帐户](../manage-runas-account.md)。

如果运行方式帐户的证书已过期，请按照[自签名证书续订](../manage-runas-account.md#cert-renewal)中列出的步骤操作，以续订证书。

如果缺少权限，请参阅[快速入门：使用 Azure 门户查看分配给用户的角色](../../role-based-access-control/check-access.md)。 必须提供运行方式帐户所使用的服务主体的应用程序 ID。 可以通过转到 Azure 门户中的自动化帐户来检索该值。 在“帐户设置”下选择“运行方式帐户”，然后选择适当的运行方式帐户。

## <a name="scenario-my-problem-isnt-listed-here"></a><a name="other"></a>场景：此处未列出我的问题

### <a name="issue"></a>问题

在使用“在空闲时间启动/停止 VM”功能时，遇到了本页中未列出的问题或异常结果。

### <a name="cause"></a>原因

很多时候，错误可能是由于使用的功能过旧和过时所致。

> [!NOTE]
> 在 VM 上部署“在空闲时间启动/停止 VM”功能时，已通过导入到自动化帐户中的 Azure 模块测试了该功能。 该功能当前不适用于较新版本的 Azure 模块。 此限制仅影响用于运行“在空闲时间启动/停止 VM”的自动化帐户。 仍可以在其他自动化帐户中使用较新版本的 Azure 模块，如[更新 Azure PowerShell 模块](../automation-update-azure-modules.md)中所述。

### <a name="resolution"></a>解决方法

若要解决许多错误，请删除并[更新在空闲时间启动/停止 VM](../automation-solution-vm-management.md#update-the-feature)。 还可以检查[作业流](../automation-runbook-execution.md#job-statuses)以查看是否有任何错误。 

## <a name="next-steps"></a>后续步骤

如果未在此处看到你遇到的问题，或者无法解决你遇到的问题，请尝试以下途径之一以获取其他支持：

* 通过 [Azure 论坛](https://azure.microsoft.com/support/forums/)获取 Azure 专家的解答。
* 连接到 [@AzureSupport](https://twitter.com/azuresupport)，这是用于改进客户体验的官方 Microsoft Azure 帐户。 Azure 支持将 Azure 社区引导至解答、支持和专业化服务。
* 提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。
