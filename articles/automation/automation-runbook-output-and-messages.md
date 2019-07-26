---
title: Azure 自动化中的 Runbook 输出和消息
description: 介绍如何创建和检索 Azure 自动化中 Runbook 的输出和错误消息。
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 12/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c129391c0830e0194c32a041853482f92340bbb9
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405787"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Azure 自动化中的 Runbook 输出和消息
大多数 Azure 自动化 runbook 都有某种形式的输出。 此输出可能是发给用户的错误消息，也可能是你打算用于另一个 runbook 的复杂对象。 Windows PowerShell 提供[多个流](/powershell/module/microsoft.powershell.core/about/about_redirection)，以便从脚本或工作流发送输出。 Azure 自动化以不同方式处理每个流。 在创建 runbook 时，应遵循如何使用每种方法的最佳实践。

下表简要介绍了 Azure 门户中的每个流及其对已发布的 runbook 的行为以及[测试 runbook](automation-testing-runbook.md) 的时间。 后面的部分将提供有关每个流的更多详细信息。

| Stream | 描述 | 已发布 | 测试 |
|:--- |:--- |:--- |:--- |
| Output |对象旨在由其他 Runbook 使用。 |写入作业历史记录。 |显示在测试输出窗格中。 |
| 警告 |面向用户的警告消息。 |写入作业历史记录。 |显示在测试输出窗格中。 |
| Error |面向用户的错误消息。 与发生异常时不同，默认情况下，在出现错误消息后，Runbook 会继续执行。 |写入作业历史记录。 |显示在测试输出窗格中。 |
| 详细 |提供一般信息或调试信息的消息。 |仅当为 Runbook 启用了详细日志记录时，才写入作业历史记录。 |仅当在 Runbook 中将 $VerbosePreference 设置为 Continue 时，才显示在“测试输出”窗格中。 |
| 进度 |完成 Runbook 中每个活动之前和之后自动生成的记录。 由于 runbook 面向交互式用户，因此不应尝试创建自身的进度记录。 |仅当为 Runbook 启用了进度日志记录时，才写入作业历史记录。 |不显示在测试输出窗格中。 |
| 调试 |面向交互式用户的消息。 不应在 Runbook 中使用。 |不会写入作业历史记录。 |不会写入测试输出窗格。 |

## <a name="output-stream"></a>输出流
输出流旨在输出脚本或工作流创建的对象（如果该脚本或工作流正常运行）。 在 Azure 自动化中，此流主要用于供[调用当前 Runbook 的父 Runbook](automation-child-runbooks.md) 使用的对象。 从父 Runbook [调用某个内联 Runbook](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution) 时，后者会将输出流中的数据返回给父级。 仅在知道该 runbook 永不会被其他 runbook 调用时，才使用输出流将一般信息传回给用户。 但是，最佳做法通常是使用[详细流](#verbose-stream)向用户传递常规信息。

可以使用 [Write-Output](https://technet.microsoft.com/library/hh849921.aspx)，或者在 Runbook 中将对象放置在其对应行中，向输出流写入数据。

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="output-from-a-function"></a>函数的输出
向 runbook 中包含的函数写入输出流时，输出将传回到 runbook。 如果 Runbook 将该输出分配给某个变量，则不会将数据写入到输出流。 从函数内部向其他任何流写入数据会写入到 Runbook 相应的流。

请考虑以下示例 Runbook：

```powershell
Workflow Test-Runbook
{
  Write-Verbose "Verbose outside of function" -Verbose
  Write-Output "Output outside of function"
  $functionOutput = Test-Function
  $functionOutput

  Function Test-Function
  {
    Write-Verbose "Verbose inside of function" -Verbose
    Write-Output "Output inside of function"
  }
}
```

Runbook 作业的输出流将是：

```output
Output inside of function
Output outside of function
```

Runbook 作业的详细流将是：

```output
Verbose outside of function
Verbose inside of function
```

发布 runbook 后，在启动它之前，还必须在 runbook 设置中打开详细日志记录，以获取详细的流输出。

### <a name="declaring-output-data-type"></a>声明输出数据类型
工作流可以使用 [OutputType 属性](https://technet.microsoft.com/library/hh847785.aspx)指定其输出的数据类型。 此属性在运行时不起作用，但在设计时，它可以向 Runbook 作者指明 Runbook 的预期输出。 随着 Runbook 工具集的持续发展，在设计时声明输出数据类型的重要性也在不断提升。 因此，最佳做法是在创建的所有 runbook 中包含此声明。

以下是示例输出类型列表：

* System.String
* System.Int32
* System.Collections.Hashtable
* Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine

以下示例 Runbook 将输出一个字符串对象，并包含其输出类型的声明。 如果 Runbook 输出了特定类型的数组，则仍应该指定相对于该类型数组的类型。

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

若要在图形或图形 PowerShell 工作流 Runbook 中声明输出类型，可选择“输入和输出”菜单选项，并键入输出类型的名称。 建议使用完整的 .NET 类名，以便从父 runbook 引用该类时可轻松识别它。 这会向 Runbook 中的数据总线公开该类的所有属性，并在将其用于条件逻辑、日志记录和作为 Runbook 中其他活动的值引用时提供很大灵活性。<br> ![Runbook 输入和输出选项](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

以下示例使用两个图形 Runbook 来演示此功能。 如果应用模块式 Runbook 设计模型，则有一个 Runbook，它作为身份验证 Runbook 模板来管理使用运行方式帐户通过 Azure 进行的身份验证。 在此情况下，通常执行核心逻辑以自动实施给定方案的第二个 Runbook 将执行该身份验证 Runbook 模板，并在“测试”输出窗格中显示结果。 在正常情况下，会使用此 Runbook 针对利用子 Runbook 输出的资源执行某些操作。

下面是 **AuthenticateTo-Azure** Runbook 的基本逻辑。<br> ![身份验证 Runbook 模板示例](media/automation-runbook-output-and-messages/runbook-authentication-template.png)。  

它包括 *Microsoft.Azure.Commands.Profile.Models.PSAzureContext* 输出类型，此输出类型将返回身份验证配置文件属性。<br> ![Runbook 输出类型示例](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png) 

尽管此 Runbook 简单明了，但此处仍需要强调一个配置项。 最后的活动执行 **Write-Output** cmdlet，并使用该 cmdlet 所需 **Inputobject** 参数的 PowerShell 表达式将配置文件数据写入 $_ 变量。  

对于本示例中名为 *Test-ChildOutputType* 的第二个 Runbook，仅需提供两项活动。<br> ![示例子输出类型 Runbook](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png) 

第一个活动调用 **AuthenticateTo Azure** Runbook，第二个活动运行包含**活动输出** **数据源**的 **Write-Verbose** cmdlet，并且**字段路径**的值为 **Context.Subscription.SubscriptionName**，用于指定来自 **AuthenticateTo-Azure** Runbook 的上下文输出。<br> ![Write-Verbose cmdlet 参数数据源](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)    

生成的输出是订阅名称。<br> ![Test-ChildOutputType Runbook 结果](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

一条有关输出类型控件行为的注释。 如果在“输入和输出属性”边栏选项卡上的“输出类型”字段中键入值，必须在键入后单击控件外部，以便控件能够识别条目。  

## <a name="message-streams"></a>消息流
与输出流不同，消息流旨在向用户传递信息。 有多个消息流用于传递不同类型的信息，Azure 自动化以不同的方式处理每个消息流。

### <a name="warning-and-error-streams"></a>警告和错误流
警告和错误流旨在记录 Runbook 中出现的问题。 当 Runbook 执行时，这些流将写入作业历史记录；在测试 Runbook 时，它们将包含在 Azure 门户的测试输出窗格中。 默认情况下，在出现警告或错误后，Runbook 将继续执行。 创建消息之前，可以通过在 Runbook 中设置[首选项变量](#preference-variables)，指定应在出现警告或错误时挂起 Runbook。 例如，要使 Runbook 在出现错误时挂起（就像发生异常时那样），请将 **$ErrorActionPreference** 设置为 Stop。

使用 [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) 或 [Write-Error](https://technet.microsoft.com/library/hh849962.aspx) cmdlet 创建警告或错误消息。 活动可能也会向这些流写入数据。

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="verbose-stream"></a>详细流
详细消息流用于传递有关 Runbook 操作的一般信息。 由于[调试流](#debug-stream)在 Runbook 中不可用，因此，应该为调试信息使用详细消息。 默认情况下，来自已发布 runbook 的详细消息不会存储在作业历史记录中。 若要存储详细消息，请在 Azure 门户中 Runbook 的“配置”选项卡上，将已发布的 Runbook 配置为“记录详细记录”。 在大多数情况下，出于性能方面的原因，应该保留默认设置，即，不记录详细记录。 启用此选项的目的只是为了排查 Runbook 的问题或对它进行调试。

[测试 runbook](automation-testing-runbook.md) 时，即使已将该 runbook 配置为记录详细记录，详细消息也不会显示。 要在[测试 Runbook](automation-testing-runbook.md) 时显示详细消息，必须将 $VerbosePreference 变量设置为 Continue。 设置该变量后，Azure 门户的测试输出窗格中会显示详细消息。

使用 [Write-Verbose](https://technet.microsoft.com/library/hh849951.aspx) cmdlet 创建详细消息。

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

### <a name="debug-stream"></a>调试流
调试流旨在供交互式用户使用，不应在 Runbook 中使用。

## <a name="progress-records"></a>进度记录
如果将 Runbook 配置为记录进度记录（在 Azure 门户中 Runbook 的“配置”选项卡上），则在运行每个活动之前和之后，会向作业历史记录中写入一条记录。 在大多数情况下，应该保留默认设置，即，不记录 Runbook 的进度记录，以最大程度地提高性能。 启用此选项的目的只是为了排查 Runbook 的问题或对它进行调试。 在测试 Runbook 时，将不显示进度消息，即使已将该 Runbook 配置为记录进度记录。

[Write-Progress](https://technet.microsoft.com/library/hh849902.aspx) cmdlet 在 runbook 中无效，因为此 cmdlet 旨在供交互式用户使用。

## <a name="preference-variables"></a>Preference 变量
Windows PowerShell 使用[首选项变量](https://technet.microsoft.com/library/hh847796.aspx)来确定如何响应发送到不同输出流的数据。 可以在 Runbook 中设置这些变量以控制此 Runbook 如何响应发送到不同流中的数据。

下表列出了可在 Runbook 中使用的 preference 变量及其有效值和默认值。 此表仅包含在 Runbook 中有效的值。 preference 变量的其他值在 Azure 自动化外部的 Windows PowerShell 中使用时有效。

| 变量 | Default Value | 有效值 |
|:--- |:--- |:--- |
| WarningPreference |继续 |停止<br>继续<br>SilentlyContinue |
| ErrorActionPreference |继续 |停止<br>继续<br>SilentlyContinue |
| VerbosePreference |SilentlyContinue |Stop<br>继续<br>SilentlyContinue |

下表列出了在 Runbook 中有效的 preference 变量值的行为。

| ReplTest1 | 行为 |
|:--- |:--- |
| 继续 |记录消息并继续执行 Runbook。 |
| SilentlyContinue |继续执行 Runbook 但不记录消息。 该值会导致忽略消息。 |
| 停止 |记录消息并挂起 Runbook。 |

## <a name="runbook-output"></a>检索 runbook 输出和消息
### <a name="azure-portal"></a>Azure 门户
可以从 Azure 门户中 Runbook 的“作业”选项卡查看 Runbook 作业的详细信息。 作业的“摘要”可显示输入参数和[输出流](#output-stream)，此外，还显示有关作业以及任何发生的异常的常规信息。 “历史记录”包含来自[输出流](#output-stream)以及[警告和错误流](#warning-and-error-streams)的消息；此外，如果 runbook 已配置为记录详细记录和进度记录，则还包含[详细流](#verbose-stream)和[进度记录](#progress-records)。

### <a name="windows-powershell"></a>Windows PowerShell
在 Windows PowerShell 中，可以使用 [Get-AzureAutomationJobOutput](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjoboutput) cmdlet 检索 Runbook 的输出和消息。 此 cmdlet 需要作业的 ID，如果指定了要返回的流，则它还要使用一个名为 Stream 的参数。 可以指定 **Any** 来返回作业的所有流。

以下示例将启动一个示例 Runbook，然后等待该 Runbook 完成。 完成后，从作业收集该 Runbook 的输出流。

```powershell
$job = Start-AzAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzAutomationJob -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Output

# For more detailed job output, pipe the output of Get-AzAutomationJobOutput to Get-AzAutomationJobOutputRecord
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Any | Get-AzAutomationJobOutputRecord
``` 

### <a name="graphical-authoring"></a>图形创作
对于图形 Runbook，以活动级别跟踪形式提供了额外的日志记录。 有两个级别的跟踪：基本和详细。 在基本跟踪中，可以查看 runbook 中每个活动的开始和结束时间，以及与任何活动重试相关的信息。 一些示例是活动的尝试次数和开始时间。 在详细跟踪中，可获取基本跟踪以及每个活动的输入和输出数据。 目前跟踪记录是使用详细流写入的，因此，必须在启用跟踪时启用详细日志记录。 对于启用了跟踪的图形 runbook，无需记录进度记录。 基本跟踪起着相同的目的，并且提供更多信息。

![“图形创作作业流”视图](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

从前面的屏幕截图可以看出，为图形 Runbook 启用详细日志记录和跟踪时，在“生产作业流”视图中会提供更多信息。 此额外信息对于解决 Runbook 的生产问题是非常必要的，因此应仅为该目的启用它，而不是作为一种常规做法。 “跟踪”记录可以特别大量。 凭借图形 Runbook 跟踪，可获取与每个活动相关的二至四条记录，具体数量取决于配置的是基本跟踪还是详细跟踪。 除非需要此信息来跟踪 Runbook 进度以进行故障排除，否则你可能想要使跟踪保持关闭状态。

**若要启用活动级别跟踪，请执行以下步骤：**

1. 在 Azure 门户中，打开自动化帐户。
2. 在“流程自动化”下选择“Runbook”，打开 Runbook 的列表。
3. 在“Runbook”页上，单击以从 Runbook 列表中选择图形 Runbook。
4. 在“设置”下，单击“日志记录和跟踪”。
5. 在“日志记录和跟踪”页的“日志详细记录”下，单击“启用”以启用详细日志记录；在“活动级别跟踪”下，根据所需的跟踪级别，将跟踪级别更改为“基本”或“详细”。<br>
   
   ![“图形创作日志记录和跟踪”页面](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="microsoft-azure-monitor-logs"></a>Microsoft Azure 监视日志
自动化可以将 Runbook 作业状态和作业流发送到 Log Analytics 工作区。 可以使用 Azure Monitor 日志进行以下操作：

* 获取有关自动化作业的见解 
* 基于 Runbook 作业状态（例如失败或暂停）触发电子邮件或警报 
* 针对所有作业流编写高级查询 
* 跨自动化帐户关联作业 
* 可视化不同时间段的作业历史记录    

有关如何配置与 Azure Monitor 日志的集成以收集、关联和处理作业数据的详细信息，请参阅[将作业状态和作业流从自动化转发到 Azure Monitor 日志](automation-manage-send-joblogs-log-analytics.md)。

## <a name="next-steps"></a>后续步骤
* 若要详细了解 Runbook 执行方式、如何监视 Runbook 作业和其他技术详细信息，请参阅 [Track a runbook job](automation-runbook-execution.md)（跟踪 Runbook 作业）
* 若要了解如何设计和使用子 Runbook，请参阅 [Azure 自动化中的子 Runbook](automation-child-runbooks.md)


