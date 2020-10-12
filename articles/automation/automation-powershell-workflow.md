---
title: 了解 Azure 自动化的 PowerShell 工作流
description: 本文介绍 PowerShell 工作流和 PowerShell 之间的差异，以及适用于自动化 runbook 的概念。
services: automation
ms.subservice: process-automation
ms.date: 12/14/2018
ms.topic: conceptual
ms.openlocfilehash: f175e495af8e925c0d5a6c61669a5e2f44f73ae7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185988"
---
# <a name="learn-powershell-workflow-for-azure-automation"></a>了解 Azure 自动化的 PowerShell 工作流

Azure 自动化中的 runbook 实现为 Windows PowerShell 工作流，即使用 Windows Workflow Foundation 的 Windows PowerShell 脚本。 工作流是一系列经过编程的连接步骤，会执行长期运行的任务，或是需要在多个设备或托管节点之间协调多个步骤。 

尽管工作流是使用 Windows PowerShell 语法编写的并通过 Windows PowerShell 启动，但它由 Windows Workflow Foundation 进行处理。 与标准脚本相比，工作流的好处包括能够同时执行针对多台设备的操作，以及能够从故障中自动恢复。 

> [!NOTE]
> PowerShell 工作流脚本类似于 Windows PowerShell 脚本，但包括一些可能会让新用户产生混淆的重大差异。 因此，建议仅在需要使用[检查点](#use-checkpoints-in-a-workflow)时，才使用 PowerShell 工作流编写 runbook。 

有关本文中主题的完整详细信息，请参阅 [Windows PowerShell 工作流入门](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134242(v=ws.11))。

## <a name="use-workflow-keyword"></a>使用 Workflow 关键字

将 PowerShell 脚本转换为 PowerShell 工作流的第一步，是使用 `Workflow` 关键字将其括起来。 工作流以 `Workflow` 关键字开头，后接括在大括号中的脚本正文。 工作流名称跟在 `Workflow` 关键字之后，如以下语法中所示：

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

工作流名称与自动化 Runbook 的名称匹配。 如果正在导入某个 Runbook，其文件名必须与工作流名称匹配，并且必须以 .ps1 结尾。

要将参数添加到工作流，请使用 `Param` 关键字，与使用脚本时相同。

## <a name="learn-differences-between-powershell-workflow-code-and-powershell-script-code"></a>了解 PowerShell 工作流代码与 PowerShell 脚本代码之间的差异

除了几个重大更改以外，PowerShell 工作流代码看起来几乎与 PowerShell 脚本代码完全相同。 以下各部分介绍要使 PowerShell 脚本能够在工作流中运行所需进行的更改。

### <a name="activities"></a>活动

活动是按顺序执行的工作流中的特定任务。 Windows PowerShell 工作流在运行工作流时，会自动将许多 Windows PowerShell cmdlet 转换为活动。 在 Runbook 中指定其中的某个 cmdlet 时，相应的活动由 Windows Workflow Foundation 运行。 

如果 cmdlet 没有相应的活动，Windows PowerShell 工作流会自动在 [InlineScript](#use-inlinescript) 活动中运行该 cmdlet。 某些 cmdlet 已被排除，不能在工作流中使用，除非显式将它们包含在 InlineScript 块中。 有关详细信息，请参阅[在脚本工作流中使用活动](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574194(v=ws.11))。

工作流活动共享一组公用参数来配置其操作。 请参阅 [about_WorkflowCommonParameters](/powershell/module/psworkflow/about/about_workflowcommonparameters)。

### <a name="positional-parameters"></a>位置参数

无法针对工作流中的活动和 cmdlet 使用位置参数。 因此，必须使用参数名称。 考虑下面的代码，它可以获取所有正在运行的服务：

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

如果尝试在工作流中运行此代码，将收到一条类似 `Parameter set cannot be resolved using the specified named parameters.` 的消息。要更正此问题，请提供参数名称，如以下示例中所示：

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>反序列化的对象

工作流中的对象已反序列化，这意味着其属性仍然可用，但其方法不可用。  例如，请考虑以下 PowerShell 代码，它使用 `Service` 对象的 `Stop` 方法停止服务。

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

如果尝试在工作流中运行此代码，你将收到错误消息，指出 `Method invocation is not supported in a Windows PowerShell Workflow.`

一个选项是将这两行代码包装在 [InlineScript](#use-inlinescript) 块中。 在这种情况下，`Service` 表示块中的服务对象。

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

另一个选项是使用具有相同功能的另一个 cmdlet（如果存在）。 在我们的示例中，`Stop-Service` cmdlet 提供了与 `Stop` 方法相同的功能，可以将以下代码用于工作流。

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="use-inlinescript"></a>使用 InlineScript

需要将一个或多个命令作为传统 PowerShell 脚本而不是 PowerShell 工作流运行时，`InlineScript` 活动非常有用。  尽管工作流中的命令将发送到 Windows Workflow Foundation 进行处理，但 InlineScript 块中的命令由 Windows PowerShell 处理。

InlineScript 使用如下所示的语法。

```powershell
InlineScript
{
    <Script Block>
} <Common Parameters>
```

可以通过将输出分配到一个变量，以返回来自 InlineScript 的输出。 下面的示例停止一项服务，并输出服务名称。

```powershell
Workflow Stop-MyService
{
    $Output = InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
        $Service
    }

    $Output.Name
}
```

可将值传递到 InlineScript 块，但必须使用 **$Using** 作用域修饰符。  下面的示例与前面的示例相同，只是服务名称由变量提供。

```powershell
Workflow Stop-MyService
{
    $ServiceName = "MyService"

    $Output = InlineScript {
        $Service = Get-Service -Name $Using:ServiceName
        $Service.Stop()
        $Service
    }

    $Output.Name
}
```

虽然 InlineScript 活动可能在某些工作流中非常关键，但它们不支持工作流构造。 出于以下原因，仅当必要时才应使用它们：

* 不能在 InlineScript 块内部使用[检查点](#use-checkpoints-in-a-workflow)。 如果块中发生失败，它必须从块的开头恢复。
* 不能在 InlineScript 块内部使用[并行执行](#use-parallel-processing)。
* 因为 InlineScript 会在 InlineScript 块的整个长度内占有 Windows PowerShell 会话，因此会影响工作流的可伸缩性。

有关使用 InlineScript 的详细信息，请参阅[在工作流中运行 Windows PowerShell 命令](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574197(v=ws.11))和 [about_InlineScript](/powershell/module/psworkflow/about/about_inlinescript)。

## <a name="use-parallel-processing"></a>使用并行处理

Windows PowerShell 工作流的一个优点是能够与典型脚本一样并行而不是按顺序执行一组命令。

可以使用 `Parallel` 关键字创建包含将同时运行的多个命令的脚本块。 此脚本块使用如下所示的语法。 在此示例中，Activity1 和 Activity2 将同时启动。 只有在 Activity1 和 Activity2 完成后，Activity3 才会启动。

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

例如，请注意以下将多个文件复制到网络目标的 PowerShell 命令。 这些命令将依次进行，因此必须完成一个文件的复制，然后才能开始复制下一个文件。

```azurepowershell-interactive
Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt
```

下面的工作流并行运行这些命令，以便它们同时开始复制。  只有在所有复制均已完成之后，才会显示完成消息。

```powershell
Workflow Copy-Files
{
    Parallel
    {
        Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
        Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
        Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
    }

    Write-Output "Files copied."
}
```

可以使用 `ForEach -Parallel` 构造同时处理集合中每个项的命令。 尽管脚本块中的命令按顺序运行，但集合中的项是并行处理的。 此进程使用如下所示的语法。 在此示例中，将同时对集合中的所有项启动 Activity1。 对于每个项，Activity2 会在 Activity1 完成后启动。 只有在对所有项完成 Activity1 和 Activity2 后，Activity3 才会启动。 我们使用 `ThrottleLimit` 参数来限制并行度。 `ThrottleLimit` 太高会导致问题。 `ThrottleLimit` 参数的理想值取决于环境中的许多因素。 尝试从较低的值开始并尝试不同的增加值，直到找到适合你的特定环境的值。

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

下面的示例是类似于前面的示例，用于并行复制文件。  在这种情况下，每个文件复制完成之后都会显示一条消息。  只有在所有复制均已完成之后，才会显示最终完成消息。

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach -Parallel -ThrottleLimit 10 ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
    }

    Write-Output "All files copied."
}
```

> [!NOTE]
> 我们不建议并行运行子 Runbook，这是由于这已被证实将导致不可靠的结果。 子 Runbook 的输出有时不显示，一个子 Runbook 中的设置可能会影响其他并行子 Runbook。 `VerbosePreference`、`WarningPreference` 等变量可能不会传播到子 runbook。 并且，如果子 runbook 更改这些值，则在调用之后可能无法正确还原这些值。

## <a name="use-checkpoints-in-a-workflow"></a>在工作流中使用检查点

检查点是工作流当前状态的快照，其中包括变量的当前值以及到该点为止生成的任何输出。 如果工作流以错误或挂起状态结束，则它下次运行时会从其最后一个检查点开始，而不是从开头开始。 

可以使用 `Checkpoint-Workflow` 活动在工作流中设置一个检查点。 Azure 自动化具有一项名为[公平份额](automation-runbook-execution.md#fair-share)的功能，将卸载运行了三小时的任何 runbook，以允许运行其他 runbook。 最终，将重新加载已卸载的 runbook。 重新加载后，将从该 runbook 中已经过的最后一个检查点继续执行。

为了保证 runbook 最终完成，必须间隔添加在 3 小时内运行的检查点。 如果在每次运行期间添加一个新检查点，并在三小时后由于错误而逐出 runbook，则会无限期地恢复该 runbook。

在以下示例中，在 Activity2 后发生了异常，导致工作流结束。 当工作流再次运行时，它会通过运行 Activity2 来启动，因为此活动刚好在设置的上一个检查点之后。

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

请在可能容易出现异常且在工作流继续时不应重复的活动之后设置检查点。 例如，工作流可能会创建一个虚拟机。 可以在命令之前和之后设置一个检查点以创建虚拟机。 如果创建失败，则当再次启动工作流时会重复命令。 如果创建成功但工作流随后失败，则恢复工作流时不会再次创建虚拟机。

下面的示例将多个文件复制到某个网络位置，并在每个文件复制完成后设置检查点。  如果网络位置丢失，则工作流以错误结束。  重新启动时，它将在上一个检查点恢复。 只会跳过已复制的文件。

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
        Checkpoint-Workflow
    }

    Write-Output "All files copied."
}
```

由于调用 [Suspend-Workflow](/powershell/module/psworkflow/about/about_suspend-workflow) 活动后或在最后一个检查点之后，将不保留用户名凭据，所以需要在调用 `Suspend-Workflow` 或检查点后将凭据设置为 null，然后再从资产存储重新检索凭据。  否则，可能会收到以下错误消息：`The workflow job cannot be resumed, either because persistence data could not be saved completely, or saved persistence data has been corrupted. You must restart the workflow.`

下面的相同代码演示如何在 PowerShell 工作流 runbook 中处理这种情况。

```powershell
workflow CreateTestVms
{
    $Cred = Get-AzAutomationCredential -Name "MyCredential"
    $null = Connect-AzAccount -Credential $Cred

    $VmsToCreate = Get-AzAutomationVariable -Name "VmsToCreate"

    foreach ($VmName in $VmsToCreate)
        {
        # Do work first to create the VM (code not shown)

        # Now add the VM
        New-AzVM -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

        # Checkpoint so that VM creation is not repeated if workflow suspends
        $Cred = $null
        Checkpoint-Workflow
        $Cred = Get-AzAutomationCredential -Name "MyCredential"
        $null = Connect-AzAccount -Credential $Cred
        }
}
```

> [!NOTE]
> 对于非图形 PowerShell runbook，`Add-AzAccount` 和 `Add-AzureRMAccount` 是 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) 的别名。 可以使用这些 cmdlet，也可以在自动化帐户中[将模块更新](automation-update-azure-modules.md)为最新版本。 即使刚刚创建了一个新的自动化帐户，也可能需要更新你的模块。 如果使用配置了服务主体的运行方式帐户进行身份验证，则不一定要使用这些 cmdlet。

关于检查点的详细信息，请参阅[向脚本工作流添加检查点](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574114(v=ws.11))。

## <a name="next-steps"></a>后续步骤

* 要了解 PowerShell 工作流 runbook，请参阅[教程：创建 PowerShell 工作流 runbook](learn/automation-tutorial-runbook-textual.md)。
