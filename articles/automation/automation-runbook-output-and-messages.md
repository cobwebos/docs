---
title: Azure 自动化中的 Runbook 输出和消息
description: 介绍如何在 Azure 自动化中创建和检索 Runbook 的输出和错误消息。
services: automation
ms.subservice: process-automation
ms.date: 12/04/2018
ms.topic: conceptual
ms.openlocfilehash: 457b2d2211ea1ba5fa36cec4b7e9a214f5bcad77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367085"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Azure 自动化中的 Runbook 输出和消息

大多数 Azure 自动化 runbook 都有某种形式的输出。 此输出可以是一条错误消息给用户或一个复杂对象，用于另一个 Runbook。 Windows PowerShell 提供[多个流](/powershell/module/microsoft.powershell.core/about/about_redirection)，以便从脚本或工作流发送输出。 Azure 自动化以不同方式处理每个流。 在创建 Runbook 时，应遵循使用流的最佳做法。

下表简要描述了每个流及其在 Azure 门户中为已发布的 Runbook 和[Runbook 测试](automation-testing-runbook.md)期间的行为。 输出流是用于在 Runbook 之间通信的主流。 其他流被归类为消息流，旨在向用户传达信息。 

| 流 | 描述 | 已发布 | 测试 |
|:--- |:--- |:--- |:--- |
| 错误 |面向用户的错误消息。 与异常不同，Runbook 在默认情况下出现错误消息后继续。 |写入作业历史记录 |显示在测试输出窗格中 |
| 调试 |面向交互式用户的消息。 不应在 Runbook 中使用。 |未写入作业历史记录 |未显示在测试输出窗格中 |
| 输出 |对象旨在由其他 Runbook 使用。 |写入作业历史记录 |显示在测试输出窗格中 |
| 进度 |完成 Runbook 中每个活动之前和之后自动生成的记录。 Runbook 不应尝试创建自己的进度记录，因为它们是面向交互式用户的。 |仅当为 Runbook 启用进度日志记录时，才写入作业历史记录 |未显示在测试输出窗格中 |
| “详细” |提供一般信息或调试信息的消息。 |仅当为 Runbook 启用详细日志记录时，才写入作业历史记录 |仅当变量设置为"在 Runbook 中继续"时`VerbosePreference`，才在测试输出窗格中显示 |
| 警告 |面向用户的警告消息。 |写入作业历史记录 |显示在测试输出窗格中 |

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可以使用["如何更新 Azure 自动化 中的 Azure PowerShell"模块](automation-update-azure-modules.md)将模块更新到最新版本。

## <a name="output-stream"></a>输出流

输出流用于脚本或工作流正确运行时创建的对象的输出。 Azure 自动化主要使用此流的对象由调用[当前 Runbook](automation-child-runbooks.md)的父 Runbook 使用。 当父项[内联调用 Runbook](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution)时，子级将数据从输出流返回到父项。 

Runbook 使用输出流仅在另一个 Runbook 从未调用客户端时才能将常规信息传达给客户端。 但是，最佳做法是 Runbook 通常应使用[详细流](#verbose-stream)向用户传达常规信息。

让 Runbook 使用[写入输出](https://technet.microsoft.com/library/hh849921.aspx)将数据写入输出流。 或者，您可以将对象放在脚本中自己的行上。

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="handling-output-from-a-function"></a>处理来自函数的输出

当 Runbook 函数写入输出流时，输出将传回 Runbook。 如果 Runbook 将输出分配给变量，则输出不会写入输出流。 从函数内部向其他任何流写入数据会写入到 Runbook 相应的流。 请考虑以下示例 PowerShell 工作流运行簿。

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

Runbook 作业的输出流为：

```output
Output inside of function
Output outside of function
```

Runbook 作业的详细流为：

```output
Verbose outside of function
Verbose inside of function
```

发布 Runbook 后，在启动 Runbook 之前，还必须在 Runbook 设置中打开详细日志记录，以获得详细流输出。

### <a name="declaring-output-data-type"></a>声明输出数据类型

以下是输出数据类型的示例：

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>在工作流中声明输出数据类型

工作流使用[OutputType 属性](https://technet.microsoft.com/library/hh847785.aspx)指定其输出的数据类型。 此属性在运行时不起作用，但它在设计时提供 Runbook 预期输出的指示。 随着 Runbook 工具集的不断发展，在设计时声明输出数据类型的重要性也随之增加。 因此，最佳做法是在此声明包含在您创建的任何 Runbook 中。

以下示例 Runbook 将输出一个字符串对象，并包含其输出类型的声明。 如果 Runbook 输出了特定类型的数组，则仍应该指定相对于该类型数组的类型。

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>在图形运行簿中声明输出数据类型

要在图形或图形 PowerShell 工作流运行簿中声明输出类型，可以选择 **"输入和输出"** 菜单选项并输入输出类型。 建议使用 full .NET 类名称，使该类型在父 Runbook 引用时易于识别。 使用全名会将类的所有属性公开到 Runbook 中的 databus，并在属性用作条件逻辑、日志记录和引用作为其他 Runbook 活动的值时增加灵活性。<br> ![Runbook 输入和输出选项](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>在"输入和输出属性"窗格中的 **"输出类型"** 字段中输入值后，请确保单击控件外部，以便它识别您的条目。

下面的示例显示了两个图形运行簿，用于演示输入和输出功能。 应用模块化 Runbook 设计模型，有一个 Runbook 作为身份验证 Runbook 模板，使用"运行即"帐户使用 Azure 管理身份验证。 第二个 Runbook 通常执行核心逻辑以自动执行给定的方案，在这种情况下，将执行身份验证 Runbook 模板。 它将结果显示到测试输出窗格。 在正常情况下，会使用此 Runbook 针对利用子 Runbook 输出的资源执行某些操作。

下面是 **AuthenticateTo-Azure** Runbook 的基本逻辑。<br> ![身份验证 Runbook 模板示例](media/automation-runbook-output-and-messages/runbook-authentication-template.png)。

Runbook 包括返回身份验证配置文件`Microsoft.Azure.Commands.Profile.Models.PSAzureContext`属性的输出类型 。<br> ![Runbook 输出类型示例](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

虽然此 Runbook 非常简单，但此处有一个配置项需要调用。 最后一个活动执行`Write-Output`cmdlet，使用`Inputobject`参数的 PowerShell 表达式将配置文件数据写入变量。 此参数是 需要的`Write-Output`。

此示例中的第二个运行簿名为 **"测试-子输出类型**"，它仅定义两个活动。<br> ![示例子输出类型 Runbook](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

第一个活动调用**身份验证到 Azure**运行簿。 第二个活动运行`Write-Verbose`cmdlet，**数据源**设置为**活动输出**。 此外，**字段路径**设置为**上下文.订阅.订阅名称**，从**身份验证到 Azure**运行簿的上下文输出。<br> ![写入详细 Cmdlet 参数数据源](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

生成的输出是订阅名称。<br> ![Test-ChildOutputType Runbook 结果](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="message-streams"></a>消息流

与输出流不同，消息流将信息传达给用户。 不同类型的信息有多个消息流，Azure 自动化以不同的方式处理每个流。

### <a name="warning-and-error-streams"></a>警告和错误流

警告和错误流记录运行簿中出现的问题。 Azure 自动化在执行 Runbook 时将这些流写入作业历史记录。 在测试 Runbook 时，自动化包括 Azure 门户中的"测试输出"窗格中的流。 

默认情况下，Runbook 在警告或错误后继续执行。 您可以在创建消息之前让 Runbook 设置[首选项变量](#preference-variables)，从而指定 Runbook 应暂停警告或错误。 例如，要使 Runbook 在错误时挂起，就像在异常时那样，将`ErrorActionPreference`变量设置为 Stop。

使用 [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) 或 [Write-Error](https://technet.microsoft.com/library/hh849962.aspx) cmdlet 创建警告或错误消息。 活动也可以写入警告和错误流。

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="debug-stream"></a>调试流

Azure 自动化使用交互式用户的调试消息流。 它不应在 Runbook 中使用。

### <a name="verbose-stream"></a>详细流

详细消息流支持有关 Runbook 操作的一般信息。 由于调试流不适用于 Runbook，因此 Runbook 应使用详细消息来获取调试信息。 

默认情况下，出于性能原因，作业历史记录不会存储来自已发布的 Runbook 的详细消息。 要存储详细邮件，请使用 Azure 门户 **"配置**"选项卡与 **"日志详细记录"** 设置一起配置已发布的 Runbook 以记录详细消息。 启用此选项的目的只是为了排查 Runbook 的问题或对它进行调试。 在大多数情况下，应保留不记录详细记录的默认设置。

[测试 runbook](automation-testing-runbook.md) 时，即使已将该 runbook 配置为记录详细记录，详细消息也不会显示。 要[在测试 Runbook](automation-testing-runbook.md)时显示详细消息，必须将`VerbosePreference`变量设置为"继续"。 使用该变量集，详细消息将显示在 Azure 门户的"测试"输出窗格中。

以下代码使用[Write-Verbose](https://technet.microsoft.com/library/hh849951.aspx) cmdlet 创建详细消息。

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

## <a name="progress-records"></a>进度记录

可以使用 Azure 门户的 **"配置"** 选项卡配置运行簿以记录进度记录。 默认设置是不记录记录，以最大限度地提高性能。 在大多数情况下，应保留默认设置。 启用此选项的目的只是为了排查 Runbook 的问题或对它进行调试。 

如果启用进度记录日志记录，则 Runbook 在每次活动运行之前和之后将记录写入作业历史记录。 即使 Runbook 配置为记录进度记录，测试 Runbook 也不会显示进度消息。

>[!NOTE]
>[Write-Progress](https://technet.microsoft.com/library/hh849902.aspx) cmdlet 在 runbook 中无效，因为此 cmdlet 旨在供交互式用户使用。

## <a name="preference-variables"></a>Preference 变量

您可以在 Runbook 中设置某些 Windows PowerShell[首选项变量](https://technet.microsoft.com/library/hh847796.aspx)，以控制对发送到不同输出流的数据的响应。 下表列出了可在 Runbook 中使用的首选项变量及其默认值和有效值。 在 Azure 自动化之外的 Windows PowerShell 中使用首选项变量时，可以使用其他值。

| 变量 | 默认值 | 有效值 |
|:--- |:--- |:--- |
| `WarningPreference` |继续 |停止<br>继续<br>SilentlyContinue |
| `ErrorActionPreference` |继续 |停止<br>继续<br>SilentlyContinue |
| `VerbosePreference` |SilentlyContinue |停止<br>继续<br>SilentlyContinue |

下一个表列出了在 Runbook 中有效的首选项变量值的行为。

| “值” | 行为 |
|:--- |:--- |
| 继续 |记录消息并继续执行 Runbook。 |
| SilentlyContinue |继续执行 Runbook 但不记录消息。 该值会导致忽略消息。 |
| 停止 |记录消息并挂起 Runbook。 |

## <a name="retrieving-runbook-output-and-messages"></a><a name="runbook-output"></a>检索 runbook 输出和消息

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>在 Azure 门户中检索 Runbook 输出和消息

您可以使用 Runbook 的 **"作业"** 选项卡在 Azure 门户中查看 Runbook 作业的详细信息。 作业摘要除了显示作业和已发生的任何异常的一般信息外，还显示输入参数和[输出流](#output-stream)。 作业历史记录包括来自输出流和[警告和错误流](#warning-and-error-streams)的消息。 它还包括来自[详细流](#verbose-stream)的消息和[进度记录](#progress-records)（如果 Runbook 配置为记录详细记录和进度记录）。

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>检索 Windows PowerShell 中的 Runbook 输出和消息

在 Windows PowerShell 中，您可以使用[获取-AzAutomationJob输出](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0)cmdlet 从 Runbook 检索输出和消息。 此 cmdlet 需要作业的 ID，并且具有一`Stream`个参数，该参数名为，用于指定要检索的流。 您可以为此参数指定"Any"的值，以检索作业的所有流。

以下示例将启动一个示例 Runbook，然后等待该 Runbook 完成。 运行簿完成执行后，脚本将从作业收集 Runbook 输出流。

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

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>检索图形运行簿中的 Runbook 输出和消息

对于图形 Runbook，输出和消息的额外日志记录以活动级跟踪的形式提供。 有两个级别的跟踪：基本和详细。 基本跟踪显示 Runbook 中每个活动的开始和结束时间，以及与任何活动重试相关的信息。 一些示例包括尝试次数和活动开始时间。 详细跟踪包括基本跟踪功能以及每个活动的输入和输出数据的日志记录。 

当前活动级跟踪使用详细流写入记录。 因此，在启用跟踪时，必须启用详细日志记录。 对于启用了跟踪的图形 runbook，无需记录进度记录。 基本跟踪起着相同的目的，并且提供更多信息。

![“图形创作作业流”视图](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

从图像中可以看到，为图形 Runbook 启用详细日志记录和跟踪可在生产**作业流**视图中提供更多信息。 对于使用 Runbook 解决生产问题，这些额外的信息可能是必不可少的。 

但是，除非需要此信息来跟踪 Runbook 的故障排除进度，否则您可能希望作为一般做法关闭跟踪。 跟踪记录可能特别多。 使用图形 Runbook 跟踪，您可以根据基本或详细跟踪的配置，每个活动获取 2 到 4 条记录。

**要启用活动级跟踪：**

1. 在 Azure 门户中，打开自动化帐户。
2. 在 **"流程自动化**"部分中选择**Runbook**以打开 Runbook 列表。
3. 在 Runbook 页上，从 Runbook 列表中选择图形运行簿。
4. 在“设置”**** 下，单击“日志记录和跟踪”****。
5. 在"日志记录和跟踪"页上，在 **"记录详细记录**"下，单击"**打开**"以启用详细日志记录。
6. 在**活动级别跟踪**下，根据所需的跟踪级别将跟踪级别更改为 **"基本**"或 **"详细**"。<br>

   ![“图形创作日志记录和跟踪”页面](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>检索 Microsoft Azure 监视器日志中的 Runbook 输出和消息

Azure 自动化可以将 Runbook 作业状态和作业流发送到日志分析工作区。 Azure 监视器支持允许您执行的日志：

* 获取有关自动化作业的见解。
* 根据 runbook 作业状态触发电子邮件或警报，例如，失败或挂起。
* 跨作业流编写高级查询。
* 跨自动化帐户关联作业。
* 可视化作业历史记录。

有关配置与 Azure 监视器日志的集成以收集、关联和操作作业数据的详细信息，请参阅[将作业状态和作业流从自动化转发到 Azure 监视器日志](automation-manage-send-joblogs-log-analytics.md)。

## <a name="next-steps"></a>后续步骤

* 要了解有关 Runbook 执行、监视 Runbook 作业和其他技术详细信息的详细信息，请参阅[跟踪 Runbook 作业](automation-runbook-execution.md)。
* 要了解如何设计和使用子 Runbook，请参阅[Azure 自动化 中的子 Runbook。](automation-child-runbooks.md)
* 有关 PowerShell 的详细信息（包括语言参考和学习模块），请参阅[PowerShell 文档](/powershell/scripting/overview)。
