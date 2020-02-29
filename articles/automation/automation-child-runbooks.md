---
title: Azure 自动化中的子 Runbook
description: 介绍从 Azure 自动化中的一个 Runbook 启动另一个 Runbook 并在它们之间共享信息的不同方法。
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 34446f98bc593c8b78cfb4a9ceae2c5e6dc6aef3
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191157"
---
# <a name="child-runbooks-in-azure-automation"></a>Azure 自动化中的子 Runbook

建议在 Azure 自动化中使用由其他 runbook 调用的离散函数编写可重用、模块化 runbook。 父 Runbook 通常会调用一个或多个子 Runbook 来执行所需的功能。 可以通过两种方法来调用子 runbook，并且你应该理解哪些不同的差异才能确定哪种方法最适合你的方案。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az module 安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于你的自动化帐户，可使用[如何在 Azure 自动化中更新 Azure PowerShell 模块](automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="invoking-a-child-runbook-using-inline-execution"></a>使用内联执行调用子 Runbook

若要从另一个 runbook 中内联调用 runbook，请使用 runbook 的名称并提供其参数的值，就像使用活动或 cmdlet 一样。 同一自动化帐户中的所有 Runbook 可按此方式相互使用。 父 runbook 将等待子 runbook 完成，然后再移到下一行，并且任何输出都会直接返回给父级。

在调用某个内联 Runbook 时，它会在与父 Runbook 所在的同一个作业中运行。 子 runbook 的作业历史记录中没有任何指示。 来自子 runbook 的任何异常和任何流输出都与父项关联。 此行为将导致作业较少，并使其更易于跟踪和进行故障排除。

发布某个 Runbook 时，必须事先发布它所调用的任何子 Runbook。 原因在于，在编译 runbook 时，Azure 自动化会生成与任何子 runbook 的关联。 如果尚未发布子 runbook，则父 runbook 似乎已正确发布，但在启动时会生成异常。 如果发生这种情况，可以重新发布父 Runbook，以正确引用子 Runbook。 如果由于已创建关联而更改了任何子 runbook，则无需重新发布父 runbook。

内联调用的子 runbook 的参数可以是任意数据类型（包括复杂对象）。 没有[JSON 序列化](start-runbooks.md#runbook-parameters)，因为使用 Azure 门户或[AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) cmdlet 启动 runbook 时，会发生这种序列化。

### <a name="runbook-types"></a>Runbook 类型

哪些 runbook 类型可以互相调用？

* [Powershell runbook](automation-runbook-types.md#powershell-runbooks)和[图形 runbook](automation-runbook-types.md#graphical-runbooks)可以相互独立地调用，因为两者都基于 powershell。
* [Powershell 工作流 runbook](automation-runbook-types.md#powershell-workflow-runbooks)和图形 PowerShell 工作流 runbook 可以互相调用，因为两者都是基于 PowerShell 工作流的。
* PowerShell 类型和 PowerShell 工作流类型无法相互调用，必须使用**AzAutomationRunbook**。

发布订单重要的时间

Runbook 的发布顺序仅对于 PowerShell 工作流和图形 PowerShell 工作流 Runbook 重要。

当 runbook 使用内联执行调用图形或 PowerShell 工作流子 runbook 时，它将使用 runbook 的名称。 名称必须以 **.\\** 以指定该脚本位于本地目录中。

### <a name="example"></a>示例

下面的示例启动一个测试子 runbook，该 runbook 接受一个复杂对象、一个整数值和一个布尔值。 该子 Runbook 的输出将分配到某个变量。 在本示例中，子 Runbook 是 PowerShell 工作流 Runbook。

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

下面是使用 PowerShell runbook 作为子项的同一示例。

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-a-cmdlet"></a>使用 cmdlet 启动子 runbook

> [!IMPORTANT]
> 如果 runbook 使用带有*Wait*参数的**AzAutomationRunbook** cmdlet 调用子 runbook，并且子 runbook 生成对象结果，则操作可能会遇到错误。 若要解决此错误，请参阅[具有对象输出的子 runbook](troubleshoot/runbooks.md#child-runbook-object) ，了解如何使用[AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) cmdlet 实现逻辑来轮询结果。

可以使用**AzAutomationRunbook**启动 runbook，如[使用 Windows PowerShell 启动 runbook](start-runbooks.md#start-a-runbook-with-powershell)中所述。 使用此 cmdlet 的模式有两种。 在一种模式下，该 cmdlet 会在为子 runbook 创建作业时返回作业 ID。 在您的脚本通过指定*Wait*参数启用的其他模式下，该 cmdlet 会等待子作业完成，并返回子 runbook 的输出。

使用 cmdlet 启动的子 runbook 中的作业独立于父 runbook 作业运行。 此行为会导致更多的作业，而不是以内联方式启动 runbook，并使作业更难以跟踪。父项可以异步启动多个子 runbook，而无需等待每个子 runbook 完成。 对于调用内联子 runbook 的并行执行，父 runbook 必须使用[parallel 关键字](automation-powershell-workflow.md#parallel-processing)。

由于计时，子 runbook 输出不会可靠地返回到父 runbook。 此外，一些变量（例如 *$VerbosePreference*、 *$WarningPreference*和其他）可能不会传播到子 runbook。 若要避免这些问题，可以使用**AzAutomationRunbook**和*Wait*参数作为单独的自动化作业启动子 runbook。 此方法会阻止父 runbook，直到子 runbook 完成。

如果你不希望在等待时阻止父 runbook，则可以使用**AzAutomationRunbook**启动子 runbook，而无需使用*Wait*参数。 在这种情况下，runbook 必须使用[AzAutomationJob](/powershell/module/az.automation/get-azautomationjob)来等待作业完成。 它还必须使用[AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput)和[AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord)来检索结果。

使用 cmdlet 启动的子 runbook 的参数以哈希表形式提供，如[runbook 参数](start-runbooks.md#runbook-parameters)中所述。 只能使用简单数据类型。 如果 Runbook 的参数使用复杂数据类型，则必须内联调用该 Runbook。

将子 Runbook 作为单独作业启动时，可能会丢失订阅上下文。 要使子 runbook 针对特定 Azure 订阅执行 Az module cmdlet，子 runbook 必须独立于父 runbook 向此订阅进行身份验证。

如果同一自动化帐户中的作业与多个订阅一起使用，则在一个作业中选择订阅可能会更改当前所选的其他作业的订阅上下文。 若要避免这种情况，请在每个 runbook 的开头使用 `Disable-AzContextAutosave –Scope Process`。 此操作仅保存该 Runbook 执行的上下文。

### <a name="example"></a>示例

下面的示例启动一个具有参数的子 runbook，然后使用**AzAutomationRunbook** Cmdlet 和*Wait*参数等待其完成。 完成后，该示例从子 runbook 收集 cmdlet 输出。 若要使用**AzAutomationRunbook**，脚本必须在你的 Azure 订阅中进行身份验证。

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

下表总结了从另一个 runbook 调用 runbook 的两种方法之间的差异。

|  | 内联 | Cmdlet |
|:--- |:--- |:--- |
| 作业 |子 Runbook 在父级所在的同一个作业中运行。 |为子 Runbook 创建单独的作业。 |
| 执行 |父 Runbook 等待子 Runbook 完成，然后再继续。 |父 Runbook 会在子 Runbook 启动后立刻继续运行，或父 Runbook 会等待子作业完成。 |
| 输出 |父 Runbook 可以直接从子 Runbook 获取输出。 |父 Runbook 必须检索子 Runbook 作业的输出，或父 Runbook 可以直接从子 Runbook 获取输出。 |
| parameters |子 Runbook 参数的值需单独指定，并且可以使用任意数据类型。 |子 Runbook 参数的值必须组合成单个哈希表。 此哈希表只能包含简单数据类型、数组和利用 JSON 序列化的对象数据类型。 |
| 自动化帐户 |父 runbook 只能使用同一自动化帐户中的子 runbook。 |父 runbook 可以使用来自同一 Azure 订阅的任何自动化帐户中的子 runbook，甚至可以使用具有连接的其他订阅中的子 runbook。 |
| 发布 |在发布父 Runbook 之前必须先发布子 Runbook。 |在启动父 runbook 之前的任何时间发布子 runbook。 |

## <a name="next-steps"></a>后续步骤

* [在 Azure 自动化中启动 Runbook](start-runbooks.md)
* [Azure 自动化中的 Runbook 输出和消息](automation-runbook-output-and-messages.md)