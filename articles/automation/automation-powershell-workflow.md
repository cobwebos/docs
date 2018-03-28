---
title: 了解 Azure 自动化的 PowerShell 工作流
description: 本文旨在作为熟悉 PowerShell 创作人员的一个速成教程，以便其了解 PowerShell 和 PowerShell 工作流之间的具体差异，并介绍适用于自动化 runbook 的概念。
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.devlang: na
ms.tgt_pltfrm: na
ms.openlocfilehash: b8d78d6dfd258fb51e7b30832b6c214c67ac0fec
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2018
---
# <a name="learning-key-windows-powershell-workflow-concepts-for-automation-runbooks"></a>了解自动化 runbook 的关键 PowerShell 工作流概念 
Azure 自动化中的 Runbook 作为 Windows PowerShell 工作流实现。  Windows PowerShell 工作流类似于 Windows PowerShell 脚本，但包括一些可能会让新用户产生混淆的重大差异。  本文旨在提供有关使用 PowerShell 工作流编写 runbook 的帮助，但我们建议使用 PowerShell 编写 runbook，除非是需要检查点的情况。  编写 PowerShell 工作流 runbook 时存在几个语法差异，这些差异会增加编写有效工作流时所需的工作量。  

工作流是一系列编程的连接步骤，用于执行长时间运行的任务，或者要求跨多个设备或托管节点协调多个步骤。 与标准脚本相比，工作流的好处包括能够同时执行针对多台设备的操作以及自动从故障中恢复的能力。 Windows PowerShell 工作流是使用 Windows Workflow Foundation 的 Windows PowerShell 脚本。 尽管工作流是使用 Windows PowerShell 语法编写的并通过 Windows PowerShell 启动，但它由 Windows Workflow Foundation 进行处理。

有关本文中主题的完整详细信息，请参阅 [Windows PowerShell 工作流简介](http://technet.microsoft.com/library/jj134242.aspx)。

## <a name="basic-structure-of-a-workflow"></a>工作流的基本结构
将 PowerShell 脚本转换为 PowerShell 工作流的第一步是使用 **Workflow** 关键字将其括起来。  工作流以 **Workflow** 关键字开头，后接括在大括号中的脚本正文。 工作流名称跟在 **Workflow** 关键字之后，如以下语法中所示：

    Workflow Test-Workflow
    {
       <Commands>
    }

工作流名称与自动化 Runbook 的名称匹配。 如果正在导入某个 Runbook，其文件名必须与工作流名称匹配，并且必须以 *.ps1* 结尾。

要将参数添加到工作流，请使用 **Param** 关键字，与使用脚本时相同。

## <a name="code-changes"></a>代码更改
除了几个重大更改以外，PowerShell 工作流代码看起来几乎与 PowerShell 脚本代码完全相同。  以下各部分介绍要使 PowerShell 脚本能够在工作流中运行所需进行的更改。

### <a name="activities"></a>活动
活动是工作流中的特定任务。 就像脚本由一个或多个命令构成一样，工作流由一个或多个按顺序执行的活动构成。 Windows PowerShell 工作流在运行工作流时，会自动将许多 Windows PowerShell cmdlet 转换为活动。 在 Runbook 中指定其中的某个 cmdlet 时，相应的活动由 Windows Workflow Foundation 运行。 对于这些没有相应活动的 cmdlet，Windows PowerShell 工作流会自动在 [InlineScript](#inlinescript) 活动中运行该 cmdlet。 有一组 cmdlet 已被排除，不能包含在工作流中，除非显式将它们包含在 InlineScript 块中。 有关这些概念的更多详细信息，请参阅[在脚本工作流中使用活动](http://technet.microsoft.com/library/jj574194.aspx)。

工作流活动共享一组公用参数来配置其操作。 有关工作流通用参数的详细信息，请参阅 [about_WorkflowCommonParameters](http://technet.microsoft.com/library/jj129719.aspx)。

### <a name="positional-parameters"></a>位置参数
无法针对工作流中的活动和 cmdlet 使用位置参数。  这意味着必须使用参数名称。

例如，请注意下面的代码，用于获取所有正在运行的服务。

     Get-Service | Where-Object {$_.Status -eq "Running"}

如果尝试在工作流中运行此代码，将收到一条消息，类似于“无法使用指定的命名参数解析参数集”。  若要更正此问题，请提供如以下所示的参数名称。

    Workflow Get-RunningServices
    {
        Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
    }

### <a name="deserialized-objects"></a>反序列化的对象
工作流中的对象已反序列化。  这意味着其属性仍然可用，但其方法不再可用。  例如，请注意以下 PowerShell 代码，使用服务对象的 Stop 方法停止一项服务。

    $Service = Get-Service -Name MyService
    $Service.Stop()

如果尝试在工作流中运行此代码，将收到错误消息，指出“在 Windows PowerShell 工作流中不支持方法调用”。  

一种选择是将这两行代码包括在 [InlineScript](#inlinescript) 代码块中，在这种情况下，$Service 是该代码块中的服务对象。

    Workflow Stop-Service
    {
        InlineScript {
            $Service = Get-Service -Name MyService
            $Service.Stop()
        }
    }

另一个选项是使用执行相同功能的另一个 cmdlet（如果可用）。  在我们的示例中，Stop-Service cmdlet 提供了与 Stop 方法相同的功能，可以将以下代码用于工作流。

    Workflow Stop-MyService
    {
        $Service = Get-Service -Name MyService
        Stop-Service -Name $Service.Name
    }


## <a name="inlinescript"></a>InlineScript
需要将一个或多个命令作为传统 PowerShell 脚本而不是 PowerShell 工作流运行时，**InlineScript** 活动非常有用。  尽管工作流中的命令将发送到 Windows Workflow Foundation 进行处理，但 InlineScript 块中的命令由 Windows PowerShell 处理。

InlineScript 使用如下所示的语法。

    InlineScript
    {
      <Script Block>
    } <Common Parameters>

可以通过将输出分配到一个变量，以返回来自 InlineScript 的输出。 下面的示例停止一项服务，并输出服务名称。

    Workflow Stop-MyService
    {
        $Output = InlineScript {
            $Service = Get-Service -Name MyService
            $Service.Stop()
            $Service
        }

        $Output.Name
    }


可将值传递到 InlineScript 块，但必须使用 **$Using** 作用域修饰符。  下面的示例与前面的示例相同，只是服务名称由变量提供。

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


尽管 InlineScript 活动可能在某些工作流中非常关键，但它们不支持工作流构造，并且只能出于以下原因才使用：

* 不能在 InlineScript 块内部使用[检查点](#checkpoints)。 如果块中发生失败，它必须从块的开头恢复。
* 不能在 InlineScriptBlock 内部使用[并行执行](#parallel-processing)。
* 因为 InlineScript 会在 InlineScript 块的整个长度内占有 Windows PowerShell 会话，因此会影响工作流的可伸缩性。

有关使用 InlineScript 的详细信息，请参阅[在工作流中运行 Windows PowerShell 命令](http://technet.microsoft.com/library/jj574197.aspx)和 [about_InlineScript](http://technet.microsoft.com/library/jj649082.aspx)。

## <a name="parallel-processing"></a>并行处理
Windows PowerShell 工作流的一个优点是能够与典型脚本一样并行而不是按顺序执行一组命令。

可以使用 **Parallel** 关键字创建包含将同时运行的多个命令的脚本块。 此脚本块使用如下所示的语法。 在此示例中，Activity1 和 Activity2 将同时启动。 只有在 Activity1 和 Activity2 完成后，Activity3 才会启动。

    Parallel
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>


例如，请注意以下将多个文件复制到网络目标的 PowerShell 命令。  这些命令将依次进行，因此必须完成一个文件的复制，然后才能开始复制下一个文件。     

    Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
    Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
    Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt

下面的工作流并行运行这些命令，以便它们同时开始复制。  只有在所有复制均已完成之后，才会显示完成消息。

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


可以使用 **ForEach-Parallel** 构造同时处理集合中每个项的命令。 尽管脚本块中的命令按顺序运行，但集合中的项是并行处理的。 此脚本块使用如下所示的语法。 在此示例中，将同时对集合中的所有项启动 Activity1。 对于每个项，Activity2 会在 Activity1 完成后启动。 只有在对所有项完成 Activity1 和 Activity2 后，Activity3 才会启动。

    ForEach -Parallel ($<item> in $<collection>)
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

下面的示例是类似于前面的示例，用于并行复制文件。  在这种情况下，每个文件复制完成之后都会显示一条消息。  只有在所有文件均复制完成之后，才会显示最终的完成消息。

    Workflow Copy-Files
    {
        $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

        ForEach -Parallel ($File in $Files)
        {
            Copy-Item -Path $File -Destination \\NetworkPath
            Write-Output "$File copied."
        }

        Write-Output "All files copied."
    }

> [!NOTE]
> 我们不建议并行运行子 Runbook，这是由于这已被证实将导致不可靠的结果。  子 Runbook 的输出有时不显示，一个子 Runbook 中的设置可能会影响其他并行子 Runbook
>

## <a name="checkpoints"></a>检查点
“检查点”是工作流当前状态的快照，其中包括变量的当前值以及到该点为止生成的任何输出。 如果工作流以错误结束或暂停，则其下次运行时会从其上一个检查点开始，而不是从工作流的起点开始。  可以使用 **Checkpoint-Workflow** 活动在工作流中设置一个检查点。

在以下示例代码中，Activity2 后发生的异常导致工作流结束。 当工作流再次运行时，它会通过运行 Activity2 来启动，因为此活动刚好在设置的上一个检查点之后。

    <Activity1>
    Checkpoint-Workflow
    <Activity2>
    <Exception>
    <Activity3>

应在可能容易出现异常并且在工作流继续时不应重复进行的活动之后设置检查点。 例如，工作流可能会创建一个虚拟机。 可以在命令之前和之后设置一个检查点以创建虚拟机。 如果创建失败，则当再次启动工作流时会重复命令。 如果创建成功但工作流随后失败，则恢复工作流时不会再次创建虚拟机。

下面的示例将多个文件复制到某个网络位置，并在每个文件复制完成后设置检查点。  如果网络位置丢失，则工作流以错误结束。  重新启动工作流时，它将从上一个检查点处继续，这意味着只会跳过已复制的文件。

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

由于调用 [Suspend-Workflow](https://technet.microsoft.com/library/jj733586.aspx) 活动后或在最后一个检查点之后，将不保留用户名凭据，所以需要在调用 **Suspend-Workflow** 或检查点后将凭据设置为 null，然后再从资产存储重新检索凭据。  否则，可能会收到以下错误消息：*无法继续执行工作流作业，因为无法完整保存持久性数据或保存的持久性数据已损坏。必须重启工作流。*

下面的相同代码演示如何在 PowerShell 工作流 Runbook 中处理此问题。

    workflow CreateTestVms
    {
       $Cred = Get-AzureAutomationCredential -Name "MyCredential"
       $null = Add-AzureRmAccount -Credential $Cred

       $VmsToCreate = Get-AzureAutomationVariable -Name "VmsToCreate"

       foreach ($VmName in $VmsToCreate)
         {
          # Do work first to create the VM (code not shown)

          # Now add the VM
          New-AzureRmVm -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

          # Checkpoint so that VM creation is not repeated if workflow suspends
          $Cred = $null
          Checkpoint-Workflow
          $Cred = Get-AzureAutomationCredential -Name "MyCredential"
          $null = Add-AzureRmAccount -Credential $Cred
         }
     }


此外，如果使用配置了服务主体的运行方式帐户进行身份验证，则不需要此处理。  

关于检查点的详细信息，请参阅[向脚本工作流添加检查点](http://technet.microsoft.com/library/jj574114.aspx)。

## <a name="next-steps"></a>后续步骤
* 若要开始使用 PowerShell 工作流 Runbook，请参阅 [我的第一个 PowerShell 工作流 Runbook](automation-first-runbook-textual.md)
