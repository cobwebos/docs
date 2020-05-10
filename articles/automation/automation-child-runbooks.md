---
title: Azure 自动化中的子 Runbook
description: 介绍从 Azure 自动化中的一个 Runbook 启动另一个 Runbook 并在它们之间共享信息的不同方法。
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 21dc14362fed2abf80c2c5ecf57f688541c9c639
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82994796"
---
# <a name="child-runbooks-in-azure-automation"></a>Azure 自动化中的子 Runbook

在 Azure 自动化中，建议的做法之一是编写可重用、模块化且提供可由其他 Runbook 调用的离散函数的 Runbook。 父 Runbook 通常会调用一个或多个子 Runbook 来执行所需的功能。 可通过两种方式调用子 Runbook，应了解这些方法的明显差异，以确定哪种方法最适合自己的方案。

## <a name="invoking-a-child-runbook-using-inline-execution"></a>使用内联执行调用子 Runbook

若要从另一个 Runbook 调用某个内联 Runbook，请使用被调用 Runbook 的名称并提供其参数值，就像使用活动或 cmdlet 时一样。 同一自动化帐户中的所有 Runbook 可按此方式相互使用。 父 Runbook 将等待子 Runbook 完成，并转移到下一行，并直接向父级返回任何输出。

在调用某个内联 Runbook 时，它会在与父 Runbook 所在的同一个作业中运行。 子 Runbook 的作业历史记录中不会提供相应的指示。 子 Runbook 发生的任何异常和任何流输出将与父级关联。 此行为减少了作业数并简化了作业的跟踪和故障排除。

发布某个 Runbook 时，必须事先发布它所调用的任何子 Runbook。 原因是在编译 Runbook 时，Azure 自动化会生成与任何子 Runbook 的关联。 如果尚未发布子 Runbook，则父 Runbook 看似发布正常，但在启动时会生成异常。 如果发生这种情况，可以重新发布父 Runbook，以正确引用子 Runbook。 如果由于已创建关联而更改了任何子 Runbook，则不需重新发布父 Runbook。

内联调用的子 Runbook 的参数可以是任意数据类型，包括复杂对象。 没有[JSON 序列化](start-runbooks.md#runbook-parameters)，因为使用 Azure 门户或[AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) cmdlet 启动 runbook 时，会发生这种序列化。

### <a name="runbook-types"></a>Runbook 类型

哪些 Runbook 类型可以互相调用？

* [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) 和[图形 Runbook](automation-runbook-types.md#graphical-runbooks) 可以互相内联调用，两者都基于 PowerShell。
* [PowerShell 工作流 Runbook](automation-runbook-types.md#powershell-workflow-runbooks) 和图形 PowerShell 工作流 Runbook 可以互相内联调用，两者都基于 PowerShell 工作流。
* PowerShell 类型和 PowerShell 工作流类型不能互相内联调用，并且必须使用 `Start-AzAutomationRunbook`。

发布顺序何时重要？

Runbook 的发布顺序仅对于 PowerShell 工作流和图形 PowerShell 工作流 Runbook 重要。

当 Runbook 在通过内联执行调用图形或 PowerShell 工作流子 Runbook 时，它会使用被调用 Runbook 的名称。 该名称必须以 `.\\` 开头，表示脚本位于本地目录中。

### <a name="example"></a>示例

以下示例将启动一个测试子 Runbook，该 Runbook 接受复杂对象、整数值和布尔值。 该子 Runbook 的输出将分配到某个变量。 在本示例中，子 Runbook 属于 PowerShell 工作流 Runbook。

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

下面是使用 PowerShell Runbook 作为子级的同一示例。

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-a-cmdlet"></a>使用 cmdlet 启动子 Runbook

> [!IMPORTANT]
> 如果 Runbook 将 `Start-AzAutomationRunbook` cmdlet与 `Wait` 参数一起使用来调用子 Runbook，并且子 Runbook 生成对象结果，则该操作可能会遇到错误。 若要解决此错误，请参阅[具有对象输出的子 runbook](troubleshoot/runbooks.md#child-runbook-object) ，了解如何使用[AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) cmdlet 实现逻辑来轮询结果。

可以按照[使用 Windows PowerShell 启动 Runbook](start-runbooks.md#start-a-runbook-with-powershell) 中所述，使用 `Start-AzAutomationRunbook` 来启动 Runbook。 使用此 cmdlet 的模式有两种。 在第一种模式下，cmdlet 将在为子 Runbook 创建作业时返回作业 ID。 在第二种模式（由脚本通过指定 *Wait* 参数启用）下，cmdlet 会等待子作业完成，并且会返回子 Runbook 的输出。

使用 cmdlet 启动的子 Runbook 的作业独立于父 Runbook 作业运行。 此行为会导致比启动内联 Runbook 更多的作业，并使这些作业更难以跟踪。不过，父级可以异步启动多个子 Runbook，而无需等待每个子 Runbook 完成。 对于内联调用子 Runbook 的这种并行执行，父 Runbook 必须使用[并行关键字](automation-powershell-workflow.md#parallel-processing)。

由于时间原因，子 Runbook 的输出不会可靠地返回到父 Runbook。 另外，`$VerbosePreference`、`$WarningPreference` 和其他某些变量可能不会传播到子 Runbook。 为避免这些问题，可以将 `Start-AzAutomationRunbook` 与 `Wait` 参数配合使用来将子 Runbook 作为单独的自动化作业调用。 此方法会阻止父 Runbook，直到子 Runbook 完成。

如果不希望父 Runbook 在等待时被阻止，则可以使用不带 `Wait` 参数的 `Start-AzAutomationRunbook` 启动子 Runbook。 在这种情况下，runbook 必须使用[AzAutomationJob](/powershell/module/az.automation/get-azautomationjob)来等待作业完成。 它还必须使用[AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput)和[AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord)来检索结果。

使用 cmdlet 启动的子 Runbook 的参数以哈希表形式提供，如 [Runbook 参数](start-runbooks.md#runbook-parameters)中所述。 只能使用简单数据类型。 如果 Runbook 的参数使用复杂数据类型，则必须内联调用该 Runbook。

将子 Runbook 作为单独作业启动时，可能会丢失订阅上下文。 要使子 runbook 针对特定 Azure 订阅执行 Az module cmdlet，子 runbook 必须独立于父 runbook 向此订阅进行身份验证。

如果同一自动化帐户中的作业使用多个订阅，则选择一个作业中的订阅可能也会更改当前所选的其他作业的订阅上下文。 若要避免这种情况，请在每个 Runbook 的开头使用 `Disable-AzContextAutosave –Scope Process`。 此操作仅保存该 Runbook 执行的上下文。

### <a name="example"></a>示例

以下示例将启动一个包含参数的子 Runbook，并将 `Start-AzAutomationRunbook` cmdlet 与 `Wait` 参数配合使用来等待其完成。 完成后，该示例将从子 Runbook 收集 cmdlet 输出。 若要使用 `Start-AzAutomationRunbook`，脚本必须向 Azure 订阅进行身份验证。

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

下表汇总了从一个 Runbook 调用另一个 Runbook 的两种方式的差异。

|  | 内联 | Cmdlet |
|:--- |:--- |:--- |
| 作业 |子 Runbook 在父级所在的同一个作业中运行。 |为子 Runbook 创建单独的作业。 |
| 执行 |父 Runbook 等待子 Runbook 完成，然后继续。 |父 Runbook 会在子 Runbook 启动后立刻继续运行，或  父 Runbook 会等待子作业完成。 |
| 输出 |父 Runbook 可以直接从子 Runbook 获取输出。 |父 Runbook 必须检索子 Runbook 作业的输出，或  父 Runbook 可以直接从子 Runbook 获取输出。 |
| parameters |子 Runbook 参数的值需单独指定，并且可以使用任意数据类型。 |子 Runbook 参数的值必须组合成单个哈希表。 此哈希表只能包含简单数据类型、数组和利用 JSON 序列化的对象数据类型。 |
| 自动化帐户 |父 Runbook 只能使用同一自动化帐户中的子 Runbook。 |父 Runbook 可以使用任何自动化帐户中、同一 Azure 订阅中，甚至连接到的不同订阅中的子 Runbook。 |
| 发布 |在发布父 Runbook 之前必须先发布子 Runbook。 |在启动父 Runbook 之前随时可以发布子 Runbook。 |

## <a name="next-steps"></a>后续步骤

* [在 Azure 自动化中启动 Runbook](start-runbooks.md)
* [Azure 自动化中的 Runbook 输出和消息](automation-runbook-output-and-messages.md)