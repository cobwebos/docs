---
title: 在非工作时间解决方案中对启动/停止 VM 进行故障排除
description: 本文提供有关对启动/停止 VM 解决方案进行故障排除的信息。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 003c2c5a2c09957e7a3a4ac0a26b87a9ac43dace
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679162"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>排除“在空闲时间启动/停止 VM”解决方案的故障

本文提供有关在非工作时间使用启动/停止 VM 时出现的疑难解答问题的信息。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可以使用["如何更新 Azure 自动化 中的 Azure PowerShell"模块](../automation-update-azure-modules.md)将模块更新到最新版本。

## <a name="scenario-the-startstop-vms-during-off-hours-solution-fails-to-properly-deploy"></a><a name="deployment-failure"></a>方案：在非工作时间启动/停止 VM 解决方案无法正确部署

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

部署可能会失败，原因如下：

1. 所选区域中已有具有相同名称的自动化帐户。
2. 策略是不允许在非工作时间解决方案期间部署开始/停止 VM。
3. 未`Microsoft.OperationsManagement`注册`Microsoft.Insights`、`Microsoft.Automation`或资源类型。
4. 日志分析工作区已锁定。
5. 您具有 AzureRM 模块的过时版本，或在非工作时间解决方案期间启动/停止 VM。

### <a name="resolution"></a>解决方法

查看以下修补程序，以找到问题的潜在解决方案：

* 即使自动化帐户位于不同的资源组中，它们在 Azure 区域中也需要是唯一的。 检查目标区域中的现有自动化帐户。
* 现有策略可防止部署"开始/停止 VM"在非工作时间解决方案期间所需的资源。 转到 Azure 门户中的策略分配，并检查是否有禁止部署此资源的策略分配。 若要了解详细信息，请参阅 [RequestDisallowedByPolicy](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md)。
* 要部署"开始/停止 VM"解决方案，需要将订阅注册到以下 Azure 资源命名空间：

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   请参阅[解决资源提供程序注册的错误](../../azure-resource-manager/templates/error-register-resource-provider.md)，以了解有关注册提供程序时的错误的更多信息。
* 如果 Log Analytics 工作区中存在锁定，请转到 Azure 门户中的工作区并删除对资源的任何锁定。
* 如果上述解决方案不能解决问题，请按照["更新解决方案"](../automation-solution-vm-management.md#update-the-solution)下的说明重新部署"开始/停止"解决方案。

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>方案：所有 VM 无法启动或停止

### <a name="issue"></a>问题

您已配置了在非工作时间解决方案中的启动/停止 VM，但它不会启动或停止所有 VM。

### <a name="cause"></a>原因

出现此错误的原因可能是以下之一：

1. 计划配置不正确。
2. "运行为"帐户可能未正确配置。
3. Runbook 可能遇到错误。
4. VM 可能已被排除。

### <a name="resolution"></a>解决方法

查看以下列表，查看您的问题的潜在解决方案：

* 检查您是否在非工作时间解决方案期间正确配置了"开始/停止 VM"的计划。 若要了解如何配置日程安排，请参阅[日程安排](../automation-schedules.md)一文。

* 检查[作业流](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal)以查找任何错误。 从以下运行簿之一查找作业：

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* 验证您的["运行为"帐户](../manage-runas-account.md)对尝试启动或停止的 VM 具有适当的权限。 要了解如何检查资源的权限，请参阅[快速入门：查看使用 Azure 门户分配给用户的角色](../../role-based-access-control/check-access.md)。 您需要为运行 As 帐户所使用的服务主体提供应用程序 ID。 您可以通过访问 Azure 门户中的自动化帐户、选择"在**帐户设置****下作为帐户运行"** 以及单击相应的"以"帐户运行"来检索此值。

* 已明确排除的 VM 可能无法启动或停止。 排除的 VM 设置在`External_ExcludeVMNames`部署解决方案的自动化帐户中的变量中。 下面的示例演示如何使用 PowerShell 查询该值。

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>方案：我的某些 VM 无法启动或停止

### <a name="issue"></a>问题

您已配置了在非工作时间解决方案中的开始/停止 VM，但它不会启动或停止配置的某些 VM。

### <a name="cause"></a>原因

出现此错误的原因可能是以下之一：

1. 在序列方案中，标记可能丢失或不正确。
2. 可能会排除 VM。
3. "运行为帐户"可能没有足够的权限。
4. VM 可能有阻止其启动或停止的问题。

### <a name="resolution"></a>解决方法

请查看以下可能问题解决方案列表或要检查的地方：

* 在非工作时间解决方案中使用"开始/停止 VM"[的序列方案](../automation-solution-vm-management.md)时，必须确保要启动或停止的每个 VM 都具有正确的标记。 请确保要启动的 VM 有 `sequencestart` 标记，要停止的 VM 有 `sequencestop` 标记。 两个标记都必须有正整数值。 可使用下面示例的查询来查找所有 VM 及其标记和值。

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* 如果 VM 被显式排除，则可能无法启动或停止它们。 排除的 VM 设置在`External_ExcludeVMNames`部署解决方案的自动化帐户中的变量中。 下面的示例演示如何使用 PowerShell 查询该值。

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* 要启动和停止 VM，自动化帐户的"运行为帐户"必须对 VM 具有适当的权限。 要了解如何检查资源的权限，请参阅[快速入门：查看使用 Azure 门户分配给用户的角色](../../role-based-access-control/check-access.md)。 您需要为运行 As 帐户所使用的服务主体提供应用程序 ID。 您可以通过访问 Azure 门户中的自动化帐户、在**帐户设置**下选择 **"作为帐户运行"** 并单击相应的"以"身份运行帐户"来检索此值。

* 如果 VM 在启动或取消分配时出现问题，则 VM 本身可能存在问题。 例如，当 VM 尝试关闭、服务挂起等时，将应用更新。 请转到 VM 资源，并检查“活动日志”****，以确定日志中是否有任何错误。 您可能还会尝试登录到 VM 以查看事件日志中是否存在任何错误。 要了解有关 VM 故障排除的信息，请参阅[对 Azure 虚拟机进行故障排除](../../virtual-machines/troubleshooting/index.yml)

* 检查[作业流](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal)以查找任何错误。 在门户中，转到您的自动化帐户，并在 **"过程自动化**"下选择**作业**。

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>方案：我的自定义 Runbook 无法启动或停止 VM

### <a name="issue"></a>问题

你已创作自定义 runbook 或已从 PowerShell 库下载了一个，但它无法正常运行。

### <a name="cause"></a>原因

失败的原因可能有很多。 转到 Azure 门户中的自动化帐户，并在 **"过程自动化**"下选择**作业**。 在“作业”页上，查找 runbook 中的作业，以确定是否有任何失败的作业。

### <a name="resolution"></a>解决方法

建议：

* 在[非工作时间使用"开始/停止 VM"解决方案](../automation-solution-vm-management.md)在 Azure 自动化中启动和停止 VM。 此解决方案由 Microsoft 创作。 

* 请注意，Microsoft 不支持自定义 Runbook。 您可能会从 Runbook 故障排除中找到自定义[Runbook 的解决方案](runbooks.md)。 检查[作业流](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal)以查找任何错误。 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>方案：VM 不会以正确的顺序启动或停止

### <a name="issue"></a>问题

在解决方案中配置的 VM 无法按正确顺序启动或停止。

### <a name="cause"></a>原因

此问题是由 VM 上的标记不正确引起的。

### <a name="resolution"></a>解决方法

请按照以下步骤操作，以确保正确配置解决方案。

1. 请确保所有要启动或停止的 VM 都有 `sequencestart` 或 `sequencestop` 标记，具体视情况而定。 这些标记必须有正整数作为值。 VM 根据此值按升序顺序进行处理。
2. 请确保要启动或停止的 VM 的资源组位于 `External_Start_ResourceGroupNames` 或 `External_Stop_ResourceGroupNames` 变量中，具体视情况而定。
3. 通过将`WHATIF`参数设置为 True 的执行`SequencedStartStop_Parent`Runbook 来预览更改，即可测试更改。
4. 有关使用解决方案按顺序启动和停止 VM 的更多信息，请参阅[按顺序开始/停止 VM。](../automation-solution-vm-management.md)

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>方案：在下班时间作业期间启动/停止 VM 失败，出现 403 个禁止错误

### <a name="issue"></a>问题

在非工作时间解决方案运行簿期间`403 forbidden`，您会发现"开始/停止 VM"出错的作业。

### <a name="cause"></a>原因

此问题可能由配置不当或过期的"运行作为"帐户引起。 可能还因为运行为帐户对 VM 资源的权限不足。

### <a name="resolution"></a>解决方法

要验证"运行为"帐户是否正确配置，请转到 Azure 门户中的自动化帐户，然后选择"在**帐户设置**下**作为帐户运行"。** 如果"运行"帐户配置不正确或已过期，则状态将显示此情况。

如果"运行作为"帐户配置错误，则应删除并重新创建"运行为"帐户。 请参阅[管理 Azure 自动化作为帐户运行](../manage-runas-account.md)。

如果您的"运行即"帐户的证书已过期，请参阅[自签名证书续订](../manage-runas-account.md#cert-renewal)中的步骤以续订证书。

如果缺少权限，请参阅[快速入门：查看使用 Azure 门户分配给用户的角色](../../role-based-access-control/check-access.md)。 您必须为运行 As 帐户所使用的服务主体提供应用程序 ID。 您可以通过访问 Azure 门户中的自动化帐户、选择"在**帐户设置****下作为帐户运行"** 以及单击相应的"以"帐户运行"来检索此值。

## <a name="scenario-my-problem-isnt-listed-above"></a><a name="other"></a>方案：上面未列出我的问题

### <a name="issue"></a>问题

在非工作时间使用未在此页面上列出的"开始/停止 VM"解决方案时，您会遇到问题或意外结果。

### <a name="cause"></a>原因

很多时候，错误可能是由于使用的解决方案过旧和过时所致。

> [!NOTE]
> 在非工作时间启动/停止 VM 解决方案已使用部署解决方案时导入到自动化帐户中的 Azure 模块进行了测试。 该解决方案当前不适用于 Azure 模块的较新版本。 这仅影响用于在非工作时间解决方案期间运行开始/停止 VM 的自动化帐户。 您仍然可以在其他自动化帐户中使用较新版本的 Azure 模块，如如何在 Azure[自动化中更新 Azure PowerShell 模块](../automation-update-azure-modules.md)中所述

### <a name="resolution"></a>解决方法

为了解决许多错误，建议在[非工作时间删除并更新开始/停止 VM 解决方案](../automation-solution-vm-management.md#update-the-solution)。 此外，您可以检查[作业流](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal)以查找任何错误。 

## <a name="next-steps"></a>后续步骤

如果您在上面看不到问题或无法解决问题，请尝试以下渠道之一以获取其他支持：

* 通过[Azure 论坛](https://azure.microsoft.com/support/forums/)从 Azure 专家那里获得答案。
* 与[@AzureSupport](https://twitter.com/azuresupport)连接 ，官方 Microsoft Azure 帐户通过将 Azure 社区连接到正确的资源（答案、支持和专家）来改善客户体验。
* 提出 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **"获取支持**"。