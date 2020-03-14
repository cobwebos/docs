---
title: Azure 自动化中的 Runbook 输出和消息
description: 介绍如何在 Azure 自动化中从 runbook 创建和检索输出和错误消息。
services: automation
ms.subservice: process-automation
ms.date: 12/04/2018
ms.topic: conceptual
ms.openlocfilehash: 457b2d2211ea1ba5fa36cec4b7e9a214f5bcad77
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79367085"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Azure 自动化中的 Runbook 输出和消息

大多数 Azure 自动化 runbook 都有某种形式的输出。 此输出可能是用户或旨在与其他 runbook 一起使用的复杂对象的错误消息。 Windows PowerShell 提供[多个流](/powershell/module/microsoft.powershell.core/about/about_redirection)，以便从脚本或工作流发送输出。 Azure 自动化以不同方式处理每个流。 在创建 runbook 时，应该遵循使用流的最佳实践。

下表简要介绍了每个流在发布的 runbook 的 Azure 门户中以及[runbook 的测试](automation-testing-runbook.md)过程中的行为。 输出流是用于 runbook 间通信的主流。 其他流归类为消息流，旨在将信息传递给用户。 

| Stream | 说明 | 已发布 | 测试 |
|:--- |:--- |:--- |:--- |
| 错误 |面向用户的错误消息。 与异常不同，默认情况下，runbook 在错误消息之后继续运行。 |写入作业历史记录 |显示在测试输出窗格中 |
| 调试 |面向交互式用户的消息。 不应在 Runbook 中使用。 |不写入作业历史记录 |不在测试输出窗格中显示 |
| 输出 |对象旨在由其他 Runbook 使用。 |写入作业历史记录 |显示在测试输出窗格中 |
| 进度 |完成 Runbook 中每个活动之前和之后自动生成的记录。 Runbook 不应尝试创建自己的进度记录，因为这些记录面向交互式用户。 |仅当为 runbook 启用了进度日志记录时才写入作业历史记录 |不在测试输出窗格中显示 |
| “详细” |提供一般信息或调试信息的消息。 |仅当为 runbook 启用了详细日志记录时才写入作业历史记录 |仅当在 runbook 中将 `VerbosePreference` 变量设置为 Continue 时，才显示在 "测试输出" 窗格中 |
| 警告 |面向用户的警告消息。 |写入作业历史记录 |显示在测试输出窗格中 |

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az module 安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于你的自动化帐户，可使用[如何在 Azure 自动化中更新 Azure PowerShell 模块](automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="output-stream"></a>输出流

输出流用于脚本或工作流在正常运行时创建的对象的输出。 Azure 自动化主要使用此流来处理调用[当前 runbook](automation-child-runbooks.md)的父 runbook 使用的对象。 当父项[直接调用 runbook](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution)时，子级将数据从输出流返回到父级。 

Runbook 使用输出流将常规信息传递给客户端，仅当另一个 runbook 永远不会调用它。 不过，最佳做法是，runbook 通常应该使用[详细流](#verbose-stream)向用户传递常规信息。

让 runbook 使用[写入输出](https://technet.microsoft.com/library/hh849921.aspx)将数据写入输出流。 或者，您可以在脚本中将对象放置在其自己的行上。

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="handling-output-from-a-function"></a>处理函数的输出

当 runbook 函数写入到输出流时，输出会传递回 runbook。 如果 runbook 将该输出分配给某个变量，则不会将输出写入到输出流中。 从函数内部向其他任何流写入数据会写入到 Runbook 相应的流。 请考虑以下示例 PowerShell 工作流 runbook。

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

Runbook 作业的详细流是：

```output
Verbose outside of function
Verbose inside of function
```

发布 runbook 后，在启动它之前，还必须在 runbook 设置中打开详细日志记录，以获取详细流输出。

### <a name="declaring-output-data-type"></a>声明输出数据类型

下面是输出数据类型的示例：

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>在工作流中声明输出数据类型

工作流使用[OutputType 属性](https://technet.microsoft.com/library/hh847785.aspx)指定其输出的数据类型。 此属性在运行时不起作用，但在设计时，它为你提供了 runbook 的预期输出的指示。 随着 runbook 的工具集继续发展，在设计时声明输出数据类型的重要性也随之增加。 因此，最佳做法是在创建的任何 runbook 中包含此声明。

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

若要在图形 PowerShell 或图形 PowerShell 工作流 runbook 中声明输出类型，可以选择 "**输入和输出**" 菜单选项，然后输入输出类型。 建议使用完整的 .NET 类名称，使类型在父 runbook 引用时可以轻松识别。 使用完整名称会向 runbook 中的 databus 公开该类的所有属性，并在属性用于条件逻辑、日志记录和作为其他 runbook 活动的值引用时提高灵活性。<br> ![Runbook 输入和输出选项](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>在 "输入和输出属性" 窗格的 "**输出类型**" 字段中输入值后，请确保在控件的外部单击以使其能够识别您的条目。

下面的示例演示两个图形 runbook 来演示输入和输出功能。 应用模块化 runbook 设计模型时，可以使用一个 runbook 作为身份验证 Runbook 模板，使用运行方式帐户管理 Azure 身份验证。 第二个 runbook 通常执行核心逻辑来自动执行给定方案，在本例中，执行身份验证 Runbook 模板。 它会将结果显示在测试输出窗格中。 在正常情况下，会使用此 Runbook 针对利用子 Runbook 输出的资源执行某些操作。

下面是 **AuthenticateTo-Azure** Runbook 的基本逻辑。<br> ![身份验证 Runbook 模板示例](media/automation-runbook-output-and-messages/runbook-authentication-template.png)。

Runbook 包括 `Microsoft.Azure.Commands.Profile.Models.PSAzureContext`的输出类型，它将返回身份验证配置文件属性。<br> ![Runbook 输出类型示例](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

尽管此 runbook 非常简单，但这里有一个配置项目需要调用。 最后一个活动执行 `Write-Output` cmdlet，将配置文件数据写入到使用 `Inputobject` 参数的 PowerShell 表达式的变量。 `Write-Output`需要此参数。

在此示例中，名为**test-childoutputtype**的第二个 runbook 仅定义了两个活动。<br> ![示例子输出类型 Runbook](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

第一个活动调用**authenticateto-azure-Azure** runbook。 第二个活动运行 `Write-Verbose` cmdlet，并将**数据源**设置为**活动输出**。 同时，**字段路径**设置为**SubscriptionName**，即**authenticateto-azure-Azure** runbook 的上下文输出。<br> ![写入-详细 Cmdlet 参数数据源](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

生成的输出是订阅名称。<br> ![Test-ChildOutputType Runbook 结果](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="message-streams"></a>消息流

与输出流不同，消息流将信息传递给用户。 有多个消息流可用于不同种类的信息，Azure 自动化以不同方式处理每个流。

### <a name="warning-and-error-streams"></a>警告和错误流

警告和错误流会记录 runbook 中出现的问题。 执行 runbook 时，Azure 自动化会将这些流写入作业历史记录。 在测试 runbook 时，自动化包括 Azure 门户的 "测试输出" 窗格中的流。 

默认情况下，在出现警告或错误后，runbook 将继续执行。 可以通过在创建消息之前让 runbook 设置[首选项变量](#preference-variables)，指定 runbook 应在出现警告或错误时挂起。 例如，若要使 runbook 由于错误而挂起，请 `ErrorActionPreference` 将其设置为 "停止"。

使用 [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) 或 [Write-Error](https://technet.microsoft.com/library/hh849962.aspx) cmdlet 创建警告或错误消息。 活动还可以写入警告和错误流。

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="debug-stream"></a>调试流

Azure Automation 为交互式用户使用调试消息流。 不应在 runbook 中使用它。

### <a name="verbose-stream"></a>详细流

详细消息流支持有关 runbook 操作的常规信息。 由于调试流对于 runbook 不可用，因此 runbook 应将详细消息用于调试信息。 

默认情况下，出于性能方面的原因，作业历史记录不会存储来自已发布 runbook 的详细消息。 若要存储详细消息，请使用 "Azure 门户**配置**" 选项卡和 "**日志详细记录**" 设置，将已发布的 runbook 配置为记录详细消息。 启用此选项的目的只是为了排查 Runbook 的问题或对它进行调试。 在大多数情况下，应该保留默认设置 "不记录详细记录"。

[测试 runbook](automation-testing-runbook.md) 时，即使已将该 runbook 配置为记录详细记录，详细消息也不会显示。 若要在[测试 runbook](automation-testing-runbook.md)时显示详细消息，必须将 `VerbosePreference` 变量设置为 Continue。 设置该变量后，详细消息将显示在 Azure 门户的 "测试输出" 窗格中。

下面的代码使用[编写详细](https://technet.microsoft.com/library/hh849951.aspx)的 cmdlet 创建详细消息。

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

## <a name="progress-records"></a>进度记录

你可以使用 Azure 门户的 "**配置**" 选项卡将 runbook 配置为记录进度记录。 默认设置为不记录记录，以最大程度地提高性能。 在大多数情况下，应该保留默认设置。 启用此选项的目的只是为了排查 Runbook 的问题或对它进行调试。 

如果启用进度记录记录，runbook 会在每个活动运行前后向作业历史记录中写入一条记录。 即使将 runbook 配置为记录进度记录，测试 runbook 也不会显示进度消息。

>[!NOTE]
>[Write-Progress](https://technet.microsoft.com/library/hh849902.aspx) cmdlet 在 runbook 中无效，因为此 cmdlet 旨在供交互式用户使用。

## <a name="preference-variables"></a>Preference 变量

你可以在 runbook 中设置某些 Windows PowerShell[首选项变量](https://technet.microsoft.com/library/hh847796.aspx)，以控制发送到不同输出流的数据的响应。 下表列出了可在 runbook 中使用的首选项变量及其默认值和有效值。 在 Azure 自动化之外的 Windows PowerShell 中使用时，可使用首选项变量的其他值。

| 变量 | 默认值 | 有效值 |
|:--- |:--- |:--- |
| `WarningPreference` |继续 |停止<br>继续<br>SilentlyContinue |
| `ErrorActionPreference` |继续 |停止<br>继续<br>SilentlyContinue |
| `VerbosePreference` |SilentlyContinue |停止<br>继续<br>SilentlyContinue |

下表列出了在 runbook 中有效的首选项变量值的行为。

| 值 | 行为 |
|:--- |:--- |
| 继续 |记录消息并继续执行 Runbook。 |
| SilentlyContinue |继续执行 Runbook 但不记录消息。 该值会导致忽略消息。 |
| 停止 |记录消息并挂起 Runbook。 |

## <a name="runbook-output"></a>检索 runbook 输出和消息

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>检索 Azure 门户中的 runbook 输出和消息

你可以使用 runbook 的 "**作业**" 选项卡在 Azure 门户中查看 runbook 作业的详细信息。 作业摘要除了显示有关作业的一般信息以及发生的任何异常外，还显示输入参数和[输出流](#output-stream)。 作业历史记录包括来自输出流以及[警告和错误流](#warning-and-error-streams)的消息。 如果将 runbook 配置为记录详细记录和进度记录，则它还包括来自[详细流](#verbose-stream)和[进度记录](#progress-records)的消息。

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>在 Windows PowerShell 中检索 runbook 输出和消息

在 Windows PowerShell 中，可以使用[AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) cmdlet 从 runbook 检索输出和消息。 此 cmdlet 需要作业的 ID，并具有一个名为 `Stream` 的参数，用于指定要检索的流。 可以为此参数指定任何值，以检索作业的所有流。

以下示例将启动一个示例 Runbook，然后等待该 Runbook 完成。 Runbook 执行完毕后，该脚本将从作业收集 runbook 输出流。

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

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>检索图形 runbook 中的 runbook 输出和消息

对于图形 runbook，输出和消息的额外日志记录以活动级别跟踪的形式提供。 有两个级别的跟踪：基本和详细。 基本跟踪显示 runbook 中每个活动的开始和结束时间，以及与任何活动重试相关的信息。 例如，尝试次数和活动开始时间。 详细跟踪包括基本跟踪功能，以及每个活动的输入和输出数据的日志记录。 

当前活动级别跟踪使用详细流写入记录。 因此，当启用跟踪时，必须启用详细日志记录。 对于启用了跟踪的图形 runbook，无需记录进度记录。 基本跟踪起着相同的目的，并且提供更多信息。

![“图形创作作业流”视图](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

你可以从映像中看到，为图形 runbook 启用详细日志记录和跟踪会使更多的信息在 "生产**作业流**" 视图中可用。 此额外信息对于解决 runbook 的生产问题可能至关重要。 

但是，除非你需要此信息来跟踪 runbook 的进度以进行故障排除，否则你可能想要将跟踪作为一般做法来关闭。 跟踪记录可能特别多。 对于图形 runbook 跟踪，每个活动可以获取两到四条记录，具体取决于基本或详细跟踪的配置。

**若要启用活动级别跟踪：**

1. 在 Azure 门户中，打开自动化帐户。
2. 在 "**流程自动化**" 部分中选择 " **runbook** "，打开 runbook 的列表。
3. 在 "Runbook" 页上，从 runbook 列表中选择图形 runbook。
4. 在“设置”下，单击“日志记录和跟踪”。
5. 在 "日志记录和跟踪" 页上的 "**日志详细记录**" 下，单击 **"** 启用" 以启用详细日志记录。
6. 在 "**活动级别跟踪**" 下，根据所需的跟踪级别，将跟踪级别更改为 "**基本**" 或 "**详细**"。<br>

   ![“图形创作日志记录和跟踪”页面](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>在 Microsoft Azure 监视日志中检索 runbook 输出和消息

Azure 自动化可以将 runbook 作业状态和作业流发送到 Log Analytics 工作区。 Azure Monitor 支持允许你执行以下操作的日志：

* 获取有关自动化作业的见解。
* 基于 runbook 作业状态（例如失败或暂停）触发电子邮件或警报。
* 跨作业流编写高级查询。
* 跨自动化帐户关联作业。
* 可视化作业历史记录。

有关配置与 Azure Monitor 日志的集成以收集、关联和处理作业数据的详细信息，请参阅[将作业状态和作业流从自动化转发到 Azure Monitor 日志](automation-manage-send-joblogs-log-analytics.md)。

## <a name="next-steps"></a>后续步骤

* 若要详细了解 runbook 执行、runbook 作业的监视和其他技术详细信息，请参阅[跟踪 runbook 作业](automation-runbook-execution.md)。
* 若要了解如何设计和使用子 runbook，请参阅[Azure 自动化中的子 runbook](automation-child-runbooks.md)。
* 有关 PowerShell 的详细信息，包括语言参考和学习模块，请参阅[Powershell 文档](/powershell/scripting/overview)。
