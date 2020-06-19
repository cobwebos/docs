---
title: 在 Azure 自动化中监视 Runbook 输出
description: 本文介绍如何监视 Runbook 输出和消息。
services: automation
ms.subservice: process-automation
ms.date: 12/04/2018
ms.topic: conceptual
ms.openlocfilehash: fb7ddce34a32d7108587bf1a3d47be4b31214535
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2020
ms.locfileid: "83832276"
---
# <a name="monitor-runbook-output"></a>监视 runbook 输出

大多数 Azure 自动化 runbook 都有某种形式的输出。 此输出可能是发给用户的错误消息，也可能是你打算用于另一个 runbook 的复杂对象。 Windows PowerShell 提供[多个流](/powershell/module/microsoft.powershell.core/about/about_redirection)，以便从脚本或工作流发送输出。 Azure 自动化以不同方式处理每个流。 在创建 runbook 时，应遵循使用流的最佳实践。

下表简要介绍了发布的 Runbook 以及[测试 Runbook](automation-testing-runbook.md) 期间每个流及其在 Azure 门户的行为。 输出流是用于 Runbook 间通信的主流。 其他流分类为消息流，旨在向用户传递信息。 

| Stream | 说明 | 已发布 | 测试 |
|:--- |:--- |:--- |:--- |
| 错误 |面向用户的错误消息。 与发生异常时不同，默认情况下，在出现错误消息后，Runbook 会继续执行。 |写入作业历史记录 |显示在测试输出窗格中 |
| 调试 |面向交互式用户的消息。 不应在 Runbook 中使用。 |不会写入作业历史记录 |不显示在测试输出窗格中 |
| 输出 |对象旨在由其他 Runbook 使用。 |写入作业历史记录 |显示在测试输出窗格中 |
| 进度 |完成 Runbook 中每个活动之前和之后自动生成的记录。 由于 runbook 面向交互式用户，因此不应尝试创建自身的进度记录。 |仅当为 Runbook 启用了进度日志记录时，才写入作业历史记录 |不显示在测试输出窗格中 |
| “详细” |提供一般信息或调试信息的消息。 |仅当为 Runbook 启用了详细日志记录时，才写入作业历史记录 |仅当在 Runbook 中将 `VerbosePreference` 设置为 Continue 时，才显示在“测试输出”窗格中 |
| 警告 |面向用户的警告消息。 |写入作业历史记录 |显示在测试输出窗格中 |

## <a name="use-the-output-stream"></a>使用输出流

输出流用于输出脚本或工作流创建的对象（如果该脚本或工作流正常运行）。 Azure 自动化将此流主要用于供调用[当前 Runbook](automation-child-runbooks.md) 的父 Runbook 使用的对象。 父 Runbook [调用某个内联 Runbook](automation-child-runbooks.md#invoke-a-child-runbook-using-inline-execution) 时，子 Runbook 会将输出流中的数据返回给父 Runbook。 

仅当某个 runbook 永不会被其他 runbook 调用时，该 runbook 才使用输出流向客户端传递常规信息。 但是，最佳做法是 Runbook 通常使用[详细流](#monitor-verbose-stream)向用户传递常规信息。

让 runbook 使用 [Write-Output](https://technet.microsoft.com/library/hh849921.aspx) 将数据写入输出流。 或者，你可以在脚本中将对象放置在其自己的行上。

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="handle-output-from-a-function"></a>处理函数输出

当 runbook 函数写入到输出流时，输出会传递回 runbook。 如果 Runbook 将该输出分配给某个变量，则不会将输出写入到输出流。 从函数内部向其他任何流写入数据会写入到 Runbook 相应的流。 请考虑以下示例 PowerShell 工作流 runbook。

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

Runbook 作业的输出流是：

```output
Output inside of function
Output outside of function
```

Runbook 作业的详细流是：

```output
Verbose outside of function
Verbose inside of function
```

发布 runbook 后，在启动它之前，还必须在 runbook 设置中打开详细日志记录，以获取详细的流输出。

### <a name="declare-output-data-type"></a>声明输出数据类型

下面是输出数据类型的示例：

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>在工作流中声明输出数据类型

工作流使用 [OutputType 属性](https://technet.microsoft.com/library/hh847785.aspx)指定其输出的数据类型。 此属性在运行时不起作用，但在设计时，它可以指明 Runbook 的预期输出。 随着 Runbook 工具集的持续发展，在设计时声明输出数据类型的重要性也在不断提升。 因此，最佳做法是在创建的所有 runbook 中包含此声明。

以下示例 Runbook 将输出一个字符串对象，并包含其输出类型的声明。 如果 Runbook 输出了特定类型的数组，则仍应该指定相对于该类型数组的类型。

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>在图形 runbook 中声明输出数据类型

若要在图形或图形 PowerShell 工作流 Runbook 中声明输出类型，可选择“输入和输出”菜单选项，并输入输出类型。 建议使用完整的 .NET 类名，以便父 runbook 引用该类型时可轻松识别它。 使用全名会向 Runbook 中的数据总线公开该类的所有属性，并在将这些属性用于条件逻辑、日志记录和作为其他 Runbook 活动的值引用时提高灵活性。<br> ![Runbook 输入和输出选项](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>在“输入和输出”属性窗格中的“输出类型”字段中输入值后，请务必在控件的外部单击，以使其能够识别你的输入。

以下示例展示两个图形 Runbook 来演示输入和输出功能。 如果应用模块式 Runbook 设计模型，则有一个 Runbook，它作为身份验证 Runbook 模板来管理使用运行方式帐户通过 Azure 进行的身份验证。 第二个 Runbook 通常执行核心逻辑来自动执行给定方案，在本例中，执行身份验证 Runbook 模板。 它会将结果显示在测试输出窗格中。 在正常情况下，会使用此 Runbook 针对利用子 Runbook 输出的资源执行某些操作。

下面是 **AuthenticateTo-Azure** Runbook 的基本逻辑。<br> ![身份验证 Runbook 模板示例](media/automation-runbook-output-and-messages/runbook-authentication-template.png)。

Runbook 包括输出类型 `Microsoft.Azure.Commands.Profile.Models.PSAzureContext`，它将返回身份验证配置文件属性。<br> ![Runbook 输出类型示例](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

尽管此 Runbook 简单明了，但此处仍需要强调一个配置项。 最后一个活动执行 `Write-Output` cmdlet，将配置文件数据写入到对 `Inputobject` 参数使用 PowerShell 表达式的变量。 此参数是 `Write-Output` 必需的。

本示例中名为 Test-ChildOutputType 的第二个 Runbook 仅定义两项活动。<br> ![示例子输出类型 Runbook](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

第一个活动调用 AuthenticateTo-Azure Runbook。 第二个活动运行 `Write-Verbose` cmdlet，其中“数据源”设置为“活动输出”。 此外，“字段路径”设置为 Context.Subscription.SubscriptionName，上下文输出来自 AuthenticateTo-Azure Runbook。<br> ![Write-Verbose Cmdlet 参数数据源](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

生成的输出是订阅名称。<br> ![Test-ChildOutputType Runbook 结果](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="monitor-message-streams"></a>监视消息流

与输出流不同，消息流向用户传递信息。 有多个消息流用于传递不同类型的信息，且 Azure 自动化以不同的方式处理每个流。

### <a name="monitor-warning-and-error-streams"></a>监视警告和错误流

警告和错误流记录 Runbook 中出现的问题。 执行 Runbook 时，Azure 自动化会将这些流写入作业历史记录。 在测试 Runbook 时，自动化包括 Azure 门户的“测试输出”窗格中的流。 

默认情况下，在出现警告或错误后，Runbook 将继续执行。 创建消息之前，可以通过在 Runbook 中设置[首选项变量](#work-with-preference-variables)，指定应在出现警告或错误时挂起 Runbook。 例如，要使 Runbook 在出现错误时挂起（就像发生异常时那样），请将 `ErrorActionPreference` 变量设置为 Stop。

使用 [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) 或 [Write-Error](https://technet.microsoft.com/library/hh849962.aspx) cmdlet 创建警告或错误消息。 活动还可以写入警告和错误流。

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="monitor-debug-stream"></a>监视调试流

Azure 自动化对交互式用户使用调试消息流。 不应在 Runbook 中使用。

### <a name="monitor-verbose-stream"></a>监视详细流

详细消息流支持有关 Runbook 操作的一般信息。 由于调试流在 Runbook 中不可用，因此，应该为调试信息使用详细消息。 

出于性能方面的原因，默认情况下，作业历史记录不会存储来自已发布 Runbook 的详细消息。 若要存储详细消息，请使用 Azure 门户“配置”选项卡和“日志详细记录”设置，将已发布的 Runbook 配置为记录详细消息。 启用此选项的目的只是为了排查 Runbook 的问题或对它进行调试。 在大多数情况下，应该保留默认设置，即，不记录详细记录。

[测试 runbook](automation-testing-runbook.md) 时，即使已将该 runbook 配置为记录详细记录，详细消息也不会显示。 若要在[测试 Runbook](automation-testing-runbook.md) 时显示详细消息，必须将 `VerbosePreference` 变量设置为 Continue。 设置该变量后，Azure 门户的测试输出窗格中会显示详细消息。

以下代码使用 [Write-Verbose](https://technet.microsoft.com/library/hh849951.aspx) cmdlet 创建详细消息。

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

## <a name="handle-progress-records"></a>处理进度记录

你可以使用 Azure 门户的“配置”选项卡将 Runbook 配置为记录进度记录。 默认设置为不记录这些记录，以最大程度地提高性能。 在大多数情况下，应该保留默认设置。 启用此选项的目的只是为了排查 Runbook 的问题或对它进行调试。 

如果启用进度记录的日志记录，Runbook 会在每个活动运行前后向作业历史记录中写入一条记录。 测试 Runbook 不会显示进度消息，即使已将该 Runbook 配置为记录进度记录也不显示。

>[!NOTE]
>[Write-Progress](https://technet.microsoft.com/library/hh849902.aspx) cmdlet 在 runbook 中无效，因为此 cmdlet 旨在供交互式用户使用。

## <a name="work-with-preference-variables"></a>使用首选项变量

你可以在 runbook 中设置某些 Windows PowerShell [首选项变量](https://technet.microsoft.com/library/hh847796.aspx)，以控制发送到不同输出流的数据的响应。 下表列出了可在 Runbook 中使用的首选项变量及其默认值和有效值。 在 Azure 自动化外部的 Windows PowerShell 中使用时，首选项变量可使用其他值。

| 变量 | 默认值 | 有效值 |
|:--- |:--- |:--- |
| `WarningPreference` |继续 |停止<br>继续<br>SilentlyContinue |
| `ErrorActionPreference` |继续 |停止<br>继续<br>SilentlyContinue |
| `VerbosePreference` |SilentlyContinue |停止<br>继续<br>SilentlyContinue |

下表列出了在 Runbook 中有效的首选项变量值的行为。

| 值 | 行为 |
|:--- |:--- |
| 继续 |记录消息并继续执行 Runbook。 |
| SilentlyContinue |继续执行 Runbook 但不记录消息。 该值会导致忽略消息。 |
| 停止 |记录消息并挂起 Runbook。 |

## <a name="retrieve-runbook-output-and-messages"></a><a name="runbook-output"></a>检索 runbook 输出和消息

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>检索 Azure 门户中的 Runbook 输出和消息

可以在 Azure 门户中使用 Runbook 的“作业”选项卡查看 Runbook 作业的详细信息。 作业摘要显示输入参数和[输出流](#use-the-output-stream)，此外，还显示有关作业的一般信息以及发生的任何异常。 “作业历史记录”包括来自输出流以及[警告和错误流](#monitor-warning-and-error-streams)的消息。 如果 runbook 已配置为记录详细记录和进度记录，其还包含来自[详细流](#monitor-verbose-stream)以及[进度记录](#handle-progress-records)的消息。

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>在 Windows PowerShell 中检索 Runbook 输出和消息

在 Windows PowerShell 中，可以使用 [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) cmdlet 检索 Runbook 的输出和消息。 此 cmdlet 需要作业的 ID，还有一个名为 `Stream` 的参数（用于指定要检索的流）。 可以为此参数指定 Any 值，以检索作业的所有流。

以下示例将启动一个示例 Runbook，然后等待该 Runbook 完成。 Runbook 执行完毕后，该脚本将从作业收集 Runbook 输出流。

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

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>检索图形 Runbook 中的 Runbook 输出和消息

对于图形 Runbook，以活动级别跟踪形式提供了输出和消息的额外日志记录。 有两个级别的跟踪：基本和详细。 基本跟踪显示 runbook 中每个活动的开始和结束时间，以及与任何活动重试相关的信息。 一些示例是活动的尝试次数和开始时间。 详细跟踪包括基本跟踪功能以及每个活动的输入和输出数据日志记录。 

当前活动级别跟踪使用详细流写入记录。 因此，当启用跟踪时，必须启用详细日志记录。 对于启用了跟踪的图形 runbook，无需记录进度记录。 基本跟踪起着相同的目的，并且提供更多信息。

![“图形创作作业流”视图](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

从图像中可以看出，为图形 Runbook 启用详细日志记录和跟踪时，在生产“作业流”视图中会提供更多信息。 此额外信息对于故障排除 Runbook 的生产问题可能至关重要。 

但是，除非需要此信息来跟踪 Runbook 进度以进行故障排除，否则你可能想要按一般做法使跟踪保持关闭状态。 “跟踪”记录可以特别大量。 凭借图形 Runbook 跟踪，可获取与每个活动相关的二至四条记录，具体数量取决于配置的是基本跟踪还是详细跟踪。

若要启用活动级别跟踪：

1. 在 Azure 门户中，打开自动化帐户。
2. 在“流程自动化”下选择“Runbook”，以打开 Runbook 的列表。 
3. 在“Runbook”页上，从 Runbook 列表中选择图形 Runbook。
4. 在“设置”下，单击“日志记录和跟踪”。
5. 在“日志记录和跟踪”页上的“日志详细记录”下，单击“打开”以启用详细日志记录。
6. 在“活动级别跟踪”下，根据所需的跟踪级别，将跟踪级别更改为“基本”或“详细”。<br>

   ![“图形创作日志记录和跟踪”页面](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>在 Microsoft Azure Monitor 日志中检索 Runbook 输出和消息

Azure 自动化可以将 Runbook 作业状态和作业流发送到 Log Analytics 工作区。 Azure Monitor 支持允许你执行以下操作的日志：

* 获取有关自动化作业的见解。
* 基于 Runbook 作业状态（例如失败或暂停）触发电子邮件或警报。
* 编写跨作业流的高级查询。
* 跨自动化帐户关联作业。
* 可视化作业历史记录。

有关配置与 Azure Monitor 日志的集成以收集、关联和处理作业数据的详细信息，请参阅[将作业状态和作业流从自动化转发到 Azure Monitor 日志](automation-manage-send-joblogs-log-analytics.md)。

## <a name="next-steps"></a>后续步骤

* 若要使用 runbook，请参阅[在 Azure 自动化中管理 runbook](manage-runbooks.md)。
* 有关 PowerShell 的详细信息，请参阅 [PowerShell 文档](https://docs.microsoft.com/powershell/scripting/overview)。
* * 有关 PowerShell cmdlet 参考，请参阅 [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。
