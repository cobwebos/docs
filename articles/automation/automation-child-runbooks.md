---
title: Azure 自动化中的子 Runbook
description: 介绍从 Azure 自动化中的一个 Runbook 启动另一个 Runbook 并在它们之间共享信息的不同方法。
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 42362a170f493afd51a5d4ee139620ad25b54e79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367357"
---
# <a name="child-runbooks-in-azure-automation"></a>Azure 自动化中的子 Runbook

在 Azure 自动化中，建议编写具有其他 Runbook 调用的离散函数的可重用模块化 Runbook。 父 Runbook 通常会调用一个或多个子 Runbook 来执行所需的功能。 有两种方法可以调用子 Runbook，并且您应该了解哪些方法最适合您的方案，因此应了解这些差异。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可以使用["如何更新 Azure 自动化 中的 Azure PowerShell"模块](automation-update-azure-modules.md)将模块更新到最新版本。

## <a name="invoking-a-child-runbook-using-inline-execution"></a>使用内联执行调用子 Runbook 

要从另一个 Runbook 调用 Runbook 内联，请使用 Runbook 的名称并为其参数提供值，就像使用活动或 cmdlet 一样。 同一自动化帐户中的所有 Runbook 可按此方式相互使用。 父 Runbook 等待子 Runbook 完成，然后再移动到下一行，并且任何输出都直接返回到父级。

在调用某个内联 Runbook 时，它会在与父 Runbook 所在的同一个作业中运行。 子手册的作业历史记录中没有任何指示。 任何异常和来自子 Runbook 的任何流输出都与父级相关联。 此行为导致作业更少，并使它们更易于跟踪和排除故障。

发布某个 Runbook 时，必须事先发布它所调用的任何子 Runbook。 原因是 Azure 自动化在编译 Runbook 时与任何子 Runbook 建立关联。 如果子 Runbook 尚未发布，则父 Runbook 似乎已正确发布，但在启动时将生成异常。 如果发生这种情况，可以重新发布父 Runbook，以正确引用子 Runbook。 如果由于关联已创建而更改了任何子 Runbook，则无需重新发布父 Runbook。

称为内联的子 Runbook 的参数可以是任何数据类型，包括复杂对象。 没有[JSON 序列化](start-runbooks.md#runbook-parameters)，就像使用 Azure 门户或使用[开始自动化 Runbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) cmdlet 启动 Runbook 时一样。

### <a name="runbook-types"></a>Runbook 类型

哪些 Runbook 类型可以相互调用？

* [PowerShell 运行簿](automation-runbook-types.md#powershell-runbooks)和[图形运行簿](automation-runbook-types.md#graphical-runbooks)可以相互调用内联，因为两者都基于 PowerShell。
* [PowerShell 工作流运行簿](automation-runbook-types.md#powershell-workflow-runbooks)和图形 PowerShell 工作流运行簿可以相互调用，因为两者都基于 PowerShell 工作流。
* PowerShell 类型和 PowerShell 工作流类型不能相互内联调用，必须使用`Start-AzAutomationRunbook`。

何时发布订单很重要？

Runbook 的发布顺序仅适用于 PowerShell 工作流和图形 PowerShell 工作流运行簿。

当您的 Runbook 使用内联执行调用图形或 PowerShell 工作流子 Runbook 时，它使用 Runbook 的名称。 名称必须从`.\\`开始指定脚本位于本地目录中。

### <a name="example"></a>示例

下面的示例启动一个测试子 Runbook，该运行簿接受复杂对象、整数值和布尔值。 该子 Runbook 的输出将分配到某个变量。 在本示例中，子 Runbook 是 PowerShell 工作流 Runbook。

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

下面是使用 PowerShell 运行簿与子级相同的示例。

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-a-cmdlet"></a>使用 cmdlet 启动子运行簿

> [!IMPORTANT]
> 如果 Runbook 调用具有`Start-AzAutomationRunbook``Wait`参数的 cmdlet 的子 Runbook，并且子 Runbook 生成对象结果，则操作可能会遇到错误。 要解决该错误，请参阅[具有对象输出的子 Runbook，](troubleshoot/runbooks.md#child-runbook-object)了解如何使用[Get-AzAutomationJob输出记录](/powershell/module/az.automation/get-azautomationjoboutputrecord)cmdlet 实现逻辑以轮询结果。

您可以使用 启动`Start-AzAutomationRunbook`Runbook，如[使用 Windows PowerShell 启动 Runbook](start-runbooks.md#start-a-runbook-with-powershell)中所述。 使用此 cmdlet 的模式有两种。 在一种模式下，在为子 Runbook 创建作业时，cmdlet 返回作业 ID。 在其他模式下，脚本通过指定*Wait*参数启用该模式，cmdlet 将等待子作业完成并从子 Runbook 返回输出。

子 runbook 中的作业从 cmdlet 开始与父 Runbook 作业分开运行。 此行为产生的作业比启动 Runbook 内联作业多，并且使作业更难跟踪。父级可以异步启动多个子 Runbook，而无需等待每个子运行簿完成。 对于此并行执行，调用子 Runbook 内联，父 Runbook 必须使用[并行关键字](automation-powershell-workflow.md#parallel-processing)。

子 Runbook 输出由于计时而无法可靠地返回到父 Runbook。 此外，变量（如`$VerbosePreference`、`$WarningPreference`等变量）和其他变量可能不会传播到子 Runbook。 为了避免这些问题，可以使用 参数将子 Runbook 作为单独的自动化作业`Start-AzAutomationRunbook``Wait`启动。 此技术阻止父 Runbook，直到子 Runbook 完成。

如果不希望在等待时阻止父 Runbook，则可以使用`Start-AzAutomationRunbook`没有 参数启动`Wait`子 Runbook。 在这种情况下，您的 Runbook 必须使用[Get-AzAutomationJobJob](/powershell/module/az.automation/get-azautomationjob)来等待作业完成。 它还必须使用[获取-Az自动化作业输出](/powershell/module/az.automation/get-azautomationjoboutput)和[获取阿兹自动化作业输出记录](/powershell/module/az.automation/get-azautomationjoboutputrecord)来检索结果。

以 cmdlet 启动的子 Runbook 的参数作为哈希表提供，如[Runbook 参数](start-runbooks.md#runbook-parameters)中所述。 只能使用简单数据类型。 如果 Runbook 的参数使用复杂数据类型，则必须内联调用该 Runbook。

将子 Runbook 作为单独作业启动时，可能会丢失订阅上下文。 子 Runbook 要针对特定的 Azure 订阅执行 Az 模块 cmdlet，子级必须独立于父运行簿对此订阅进行身份验证。

如果同一自动化帐户中的作业使用多个订阅，则在一个作业中选择订阅可以更改当前为其他作业选择的订阅上下文。 为了避免这种情况，请在每个`Disable-AzContextAutosave –Scope Process`Runbook 的开头使用。 此操作仅保存该 Runbook 执行的上下文。

### <a name="example"></a>示例

下面的示例启动具有参数的子 Runbook，然后等待它使用带有 参数`Start-AzAutomationRunbook``Wait`的 cmdlet 完成。 完成后，该示例将从子 Runbook 收集 cmdlet 输出。 要使用`Start-AzAutomationRunbook`，脚本必须对 Azure 订阅进行身份验证。

```azurepowershell-interactive
# Ensure that the runbook does not inherit an AzContext
Disable-AzContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Get-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    –Parameters $params –Wait
```

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>子 Runbook 调用方法的比较

下表总结了从另一个 Runbook 调用 Runbook 的两种方法之间的差异。

|  | 内联 | Cmdlet |
|:--- |:--- |:--- |
| 作业 |子 Runbook 在父级所在的同一个作业中运行。 |为子 Runbook 创建单独的作业。 |
| 执行 |父 Runbook 等待子 Runbook 完成，然后再继续。 |父 Runbook 会在子 Runbook 启动后立刻继续运行，或** 父 Runbook 会等待子作业完成。 |
| 输出 |父 Runbook 可以直接从子 Runbook 获取输出。 |父 Runbook 必须检索子 Runbook 作业的输出，或** 父 Runbook 可以直接从子 Runbook 获取输出。 |
| 参数 |子 Runbook 参数的值需单独指定，并且可以使用任意数据类型。 |子 Runbook 参数的值必须组合成单个哈希表。 此哈希表只能包含简单数据类型、数组和利用 JSON 序列化的对象数据类型。 |
| 自动化帐户 |父运行簿只能在同一自动化帐户中使用子 Runbook。 |父 Runbook 可以使用来自任何自动化帐户、同一 Azure 订阅的子 Runbook，甚至使用您连接到的不同订阅的子 Runbook。 |
| 发布 |在发布父 Runbook 之前必须先发布子 Runbook。 |子运行簿在启动父运行簿之前随时发布。 |

## <a name="next-steps"></a>后续步骤

* [在 Azure 自动化中启动 Runbook](start-runbooks.md)
* [Azure 自动化中的 Runbook 输出和消息](automation-runbook-output-and-messages.md)