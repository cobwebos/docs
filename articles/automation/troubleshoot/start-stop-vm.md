---
title: 在非工作时间启动/停止 Vm 解决方案解决问题
description: 本文介绍了有关启动/停止 VM 解决方案疑难解答的信息。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 003c2c5a2c09957e7a3a4ac0a26b87a9ac43dace
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679162"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>排除“在空闲时间启动/停止 VM”解决方案的故障

本文介绍了在下班休息解决方案中使用启动/停止 Vm 时出现的问题的疑难解答信息。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可参阅[如何更新 Azure 自动化中的 Azure PowerShell 模块](../automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="scenario-the-startstop-vms-during-off-hours-solution-fails-to-properly-deploy"></a><a name="deployment-failure"></a>方案：在非工作时间启动/停止 Vm 解决方案无法正确部署

### <a name="issue"></a>问题

部署[空闲时间启动/停止 VM 解决方案](../automation-solution-vm-management.md)时，收到以下错误之一：

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

部署可能会因为以下原因之一而失败：

1. 所选区域中已存在具有相同名称的自动化帐户。
2. 策略不允许在休息时间解决方案中部署启动/停止 Vm。
3. 未`Microsoft.OperationsManagement`注册`Microsoft.Insights`、或`Microsoft.Automation`资源类型。
4. Log Analytics 工作区已锁定。
5. 你的 AzureRM 模块版本已过时，或在非工作时间启动/停止 Vm 解决方案。

### <a name="resolution"></a>解决方法

查看以下修补程序，以获得可能的问题解决方案：

* 即使自动化帐户位于不同的资源组中，它们在 Azure 区域中也需要是唯一的。 检查目标区域中的现有自动化帐户。
* 现有策略会阻止部署启动/停止 Vm 时所需的资源。 转到 Azure 门户中的策略分配，并检查是否有禁止部署此资源的策略分配。 若要了解详细信息，请参阅 [RequestDisallowedByPolicy](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md)。
* 若要部署启动/停止 Vm 解决方案，需要将你的订阅注册到以下 Azure 资源命名空间：

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   请参阅[解决资源提供程序注册的错误](../../azure-resource-manager/templates/error-register-resource-provider.md)以了解有关注册提供程序时的错误的详细信息。
* 如果 Log Analytics 工作区中存在锁定，请转到 Azure 门户中的工作区并删除对资源的任何锁定。
* 如果上述解决方法不能解决问题，请按照[更新解决方案](../automation-solution-vm-management.md#update-the-solution)中的说明来重新部署启动/停止解决方案。

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>方案：所有 Vm 均无法启动或停止

### <a name="issue"></a>问题

你已配置 "在非工作时间启动/停止 Vm" 解决方案，但它不会启动或停止所有 Vm。

### <a name="cause"></a>原因

出现此错误的原因可能是以下之一：

1. 未正确配置计划。
2. 运行方式帐户可能配置不正确。
3. Runbook 可能已遇到错误。
4. 可能已排除 Vm。

### <a name="resolution"></a>解决方法

查看以下列表，了解可能存在的问题解决方案：

* 检查是否已正确配置 "在非工作时间启动/停止 Vm" 解决方案计划。 若要了解如何配置日程安排，请参阅[日程安排](../automation-schedules.md)一文。

* 检查[作业流](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal)以查找是否有任何错误。 从以下 runbook 之一查找作业：

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* 验证[运行方式帐户](../manage-runas-account.md)对于尝试启动或停止的 vm 是否具有适当的权限。 若要了解如何检查资源的权限，请参阅[快速入门：使用 Azure 门户查看分配给用户的角色](../../role-based-access-control/check-access.md)。 你需要提供运行方式帐户所使用的服务主体的应用程序 ID。 可以通过转到 Azure 门户中的自动化帐户，选择 "**帐户设置**" 下的 "**运行方式帐户**"，然后单击相应的运行方式帐户来检索此值。

* 已明确排除的 VM 可能无法启动或停止。 已排除的 Vm 是在`External_ExcludeVMNames`解决方案所部署到的自动化帐户中的变量中设置的。 下面的示例演示如何通过 PowerShell 查询该值。

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>方案：我的某些 Vm 无法启动或停止

### <a name="issue"></a>问题

已配置 "在非工作时间启动/停止 Vm" 解决方案，但不会启动或停止一些配置的 Vm。

### <a name="cause"></a>原因

出现此错误的原因可能是以下之一：

1. 在序列方案中，标记可能丢失或不正确。
2. VM 可能被排除。
3. 运行方式帐户可能没有足够的权限访问 VM。
4. VM 可能有一个停止启动或停止的问题。

### <a name="resolution"></a>解决方法

请查看以下可能问题解决方案列表或要检查的地方：

* 使用 "在非工作时间启动/停止 Vm" 解决方案中的[序列方案](../automation-solution-vm-management.md)时，必须确保要启动或停止的每个 vm 都有正确的标记。 请确保要启动的 VM 有 `sequencestart` 标记，要停止的 VM 有 `sequencestop` 标记。 两个标记都必须有正整数值。 可使用下面示例的查询来查找所有 VM 及其标记和值。

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* 如果要显式排除 Vm，则可能无法启动或停止 Vm。 已排除的 Vm 是在`External_ExcludeVMNames`解决方案所部署到的自动化帐户中的变量中设置的。 下面的示例演示如何通过 PowerShell 查询该值。

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* 若要启动和停止 Vm，自动化帐户的运行方式帐户必须具有 VM 的相应权限。 若要了解如何检查资源的权限，请参阅[快速入门：使用 Azure 门户查看分配给用户的角色](../../role-based-access-control/check-access.md)。 你需要提供运行方式帐户所使用的服务主体的应用程序 ID。 可以通过转到 Azure 门户中的自动化帐户，选择 "**帐户设置**" 下的 "**运行方式帐户**"，然后单击相应的运行方式帐户来检索此值。

* 如果 VM 启动或解除分配时出现问题，则可能是 VM 本身存在问题。 例如，当 VM 尝试关闭、服务挂起等时，将应用更新。 请转到 VM 资源，并检查“活动日志”****，以确定日志中是否有任何错误。 你还可以尝试登录到 VM，以查看事件日志中是否存在任何错误。 若要详细了解如何对 VM 进行故障排除，请参阅[Azure 虚拟机故障排除](../../virtual-machines/troubleshooting/index.yml)

* 检查[作业流](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal)以查找是否有任何错误。 在门户中，切换到自动化帐户，并选择 "**流程自动化**" 下的 "**作业**"。

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>方案：我的自定义 runbook 无法启动或停止我的 Vm

### <a name="issue"></a>问题

你已创作自定义 runbook 或已从 PowerShell 库下载了一个，但它无法正常运行。

### <a name="cause"></a>原因

出现失败的原因可能有很多。 在 Azure 门户中转到自动化帐户，并选择 "**流程自动化**" 下的 "**作业**"。 在“作业”页上，查找 runbook 中的作业，以确定是否有任何失败的作业。

### <a name="resolution"></a>解决方法

建议执行以下操作：

* 使用 "[在非工作时间启动/停止 vm" 解决方案](../automation-solution-vm-management.md)在 Azure 自动化中启动和停止 vm。 此解决方案由 Microsoft 创作。 

* 请注意，Microsoft 不支持自定义 runbook。 你可能会从[runbook 疑难解答](runbooks.md)中找到自定义 runbook 的解决方案。 检查[作业流](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal)以查找是否有任何错误。 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>方案： Vm 不会按正确的顺序启动或停止

### <a name="issue"></a>问题

在解决方案中配置的 VM 无法按正确顺序启动或停止。

### <a name="cause"></a>原因

此问题是由 Vm 上的标记错误引起的。

### <a name="resolution"></a>解决方法

请按照以下步骤操作，以确保正确配置解决方案。

1. 请确保所有要启动或停止的 VM 都有 `sequencestart` 或 `sequencestop` 标记，具体视情况而定。 这些标记必须有正整数作为值。 VM 根据此值按升序顺序进行处理。
2. 请确保要启动或停止的 VM 的资源组位于 `External_Start_ResourceGroupNames` 或 `External_Stop_ResourceGroupNames` 变量中，具体视情况而定。
3. 通过执行将`WHATIF`参数设置为`SequencedStartStop_Parent` True 的 runbook 来测试更改，以预览所做的更改。
4. 有关使用解决方案按顺序启动和停止 Vm 的详细信息，请参阅[按顺序启动/停止 vm](../automation-solution-vm-management.md)。

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>方案：在非工作时间启动/停止 Vm 作业失败，出现403禁止的错误

### <a name="issue"></a>问题

在非工作时间解决方案 runbook 中`403 forbidden`发现失败并出现 "启动/停止 vm" 错误的作业。

### <a name="cause"></a>原因

此问题的原因可能是配置不正确或运行方式帐户已过期。 这也可能是由于运行方式帐户对 VM 资源权限不足。

### <a name="resolution"></a>解决方法

若要验证是否正确配置了运行方式帐户，请在 Azure 门户中，找到你的自动化帐户，然后在 "**帐户设置**" 下选择 "**运行方式帐户**"。 如果运行方式帐户配置不正确或已过期，状态将显示条件。

如果运行方式帐户配置错误，则应删除并重新创建运行方式帐户。 请参阅[管理 Azure 自动化运行方式帐户](../manage-runas-account.md)。

如果证书对于运行方式帐户已过期，请参阅[自行签署证书续订](../manage-runas-account.md#cert-renewal)中的步骤来续订证书。

如果缺少权限，请参阅[快速入门：使用 Azure 门户查看分配给用户的角色](../../role-based-access-control/check-access.md)。 你必须提供运行方式帐户所使用的服务主体的应用程序 ID。 可以通过转到 Azure 门户中的自动化帐户，选择 "**帐户设置**" 下的 "**运行方式帐户**"，然后单击相应的运行方式帐户来检索此值。

## <a name="scenario-my-problem-isnt-listed-above"></a><a name="other"></a>方案：上面未列出我的问题

### <a name="issue"></a>问题

使用在非工作时间启动/停止 Vm 解决方案时遇到问题或意外的结果。

### <a name="cause"></a>原因

很多时候，错误可能是由于使用的解决方案过旧和过时所致。

> [!NOTE]
> 在非工作时间启动/停止 Vm 解决方案经过测试，可在部署解决方案时导入到自动化帐户中的 Azure 模块。 此解决方案目前不适用于较新版本的 Azure 模块。 这只会影响在休息时间解决方案中用于运行启动/停止 Vm 的自动化帐户。 你仍可以在其他自动化帐户中使用较新版本的 Azure 模块，如[如何在 Azure 自动化中更新 Azure PowerShell 模块](../automation-update-azure-modules.md)中所述。

### <a name="resolution"></a>解决方法

若要解决许多错误，建议在[休息时间解决方案中删除和更新启动/停止 vm](../automation-solution-vm-management.md#update-the-solution)。 此外，还可以检查[作业流](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal)以查找任何错误。 

## <a name="next-steps"></a>后续步骤

如果你没有看到你的问题或无法解决你的问题，请尝试以下通道之一以获取其他支持：

* 通过[Azure 论坛](https://azure.microsoft.com/support/forums/)获取 azure 专家的解答。
* 与[@AzureSupport](https://twitter.com/azuresupport)官方 Microsoft Azure 帐户联系，通过将 Azure 社区连接到适当的资源来改进客户体验：答案、支持和专家。
* 提出 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 "**获取支持**"。