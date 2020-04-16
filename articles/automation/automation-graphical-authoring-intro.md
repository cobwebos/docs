---
title: Azure 自动化中的图形创作
description: 图形创作可以让你在不使用代码的情况下，为 Azure 自动化创建 Runbook。 本文介绍了图形创作以及开始创建图形 Runbook 所需的所有详细信息。
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: cf8ced05066923c94e80628651d8983560601d69
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406052"
---
# <a name="graphical-authoring-in-azure-automation"></a>Azure 自动化中的图形创作

图形创作允许您为 Azure 自动化创建 Runbook，而无需基础 Windows PowerShell 或 PowerShell 工作流代码的复杂性。 可以从 cmdlet 和 Runbook 库中向画布添加活动，将它们链接在一起，并将它们配置为形成工作流。 如果您曾经使用过系统中心协调器或服务管理自动化 （SMA），图形创作应该看起来很熟悉。 本文介绍了开始创建图形 Runbook 所需的概念。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可以使用["如何更新 Azure 自动化 中的 Azure PowerShell"模块](automation-update-azure-modules.md)将模块更新到最新版本。

## <a name="graphical-runbooks"></a>图形 Runbook

Azure 自动化中的所有运行簿都是 Windows PowerShell 工作流。 图形运行簿和图形 PowerShell 工作流运行簿生成了自动化工作人员运行但您无法查看或修改的 PowerShell 代码。 您可以将图形运行簿转换为图形 PowerShell 工作流运行簿，反之亦然。 但是，无法将这些 Runbook 转换为文本 Runbook。 此外，自动化图形编辑器无法导入文本 Runbook。

## <a name="overview-of-graphical-editor"></a>图形编辑器概述

通过创建或编辑图形 Runbook，可以在 Azure 门户中打开图形编辑器。

![图形工作区](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

以下各节介绍图形编辑器中的控件。

### <a name="canvas-control"></a>Canvas 控件

"画布"控件允许您设计 Runbook。 您可以将活动从库控件中的节点添加到 Runbook，并将它们与定义 Runbook 逻辑的链接连接。 在画布的底部，有允许您放大和缩小的控件。

### <a name="library-control"></a>库控件

"库"控件允许您选择要添加到 Runbook[的活动](#activities)。 将它们添加到画布中，您可以在其中将它们连接到其他活动。 库控件包括下表中定义的部分。

| 部分 | 说明 |
|:--- |:--- |
| Cmdlet |可在 runbook 中使用的所有 cmdlet。 Cmdlet 按模块组织。 您在自动化帐户中安装的所有模块都可用。 |
| Runbook |自动化帐户中的 Runbook。 您可以将这些 Runbook 添加到画布以用作子 Runbook。 仅显示与正在编辑的 Runbook 相同的核心类型的 Runbook。 对于图形运行簿，仅显示基于 PowerShell 的 Runbook。 对于图形 PowerShell 工作流运行簿，仅显示基于 PowerShell 工作流的 Runbook。 |
| 资产 |可在 Runbook 中使用自动化帐户中的[自动化资产](/previous-versions/azure/dn939988(v=azure.100))。 将资产添加到 Runbook 会添加获取所选资产的工作流活动。 在使用变量资产的情况下，可以选择是否添加用于获取变量或设置变量的活动。 |
| Runbook 控件 |控制可在当前 Runbook 中使用的活动。 交汇点活动需要多个输入，并等待所有完成，然后再继续工作流。 代码活动运行一行或多行 PowerShell 或 PowerShell 工作流代码，具体取决于图形运行簿类型。 可以将此活动用于自定义代码或通过其他活动难以实现的功能。 |

### <a name="configuration-control"></a>配置控件

通过"配置"控件，您可以为在画布上选择的对象提供详细信息。 此控件中可用的属性取决于所选对象的类型。 在"配置"控件中选择一个选项时，它会打开其他边栏选项卡以提供详细信息。

### <a name="test-control"></a>测试控件

首次启动图形编辑器时，不显示测试控件。 它会在你以交互方式测试图形 Runbook 时打开。

## <a name="activities"></a>活动

活动是 Runbook 的构建基块。 活动可以是 PowerShell cmdlet、子运行簿或工作流。 您可以通过在"库"控件中右键单击 Runbook 并选择"**添加到画布**"来将活动添加到 Runbook。 然后，可以通过单击拖放的方式将活动置于画布上喜欢的任何位置。 活动在画布上的位置不会影响 Runbook 的操作。 您可以以任何您认为最适合可视化其操作的方式布局 Runbook。

![添加到画布](media/automation-graphical-authoring-intro/add-to-canvas-cmdlet.png)

在"配置"边栏选项卡中选择画布上的活动以配置其属性和参数。 您可以将活动的标签更改为您找到描述性的名称。 Runbook 仍然运行原始 cmdlet。 您只需更改图形编辑器使用的显示名称。 请注意，标签必须在 Runbook 中是唯一的。

### <a name="parameter-sets"></a>参数集

参数集用于定义会接受特定 cmdlet 的值的必需参数和可选参数。 所有 cmdlet 都至少有一个参数集，有些具有多个集。 如果 cmdlet 具有多个参数集，则必须选择要使用的参数，然后才能配置参数。 您可以通过选择**参数集**并选择另一个集来更改活动使用的参数集。 在这种情况下，您已配置的任何参数值都丢失。

在下面的示例中[，Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) cmdlet 有三个参数集。 该示例使用一组名为**ListVirtualMachineInResourceGroupParamSet，** 以及单个可选参数，用于返回资源组中的所有虚拟机。 该示例还使用**获取虚拟计算机资源组ParamSet**参数集来指定要返回的虚拟机。 此集有两个必需参数和一个可选参数。

![参数集](media/automation-graphical-authoring-intro/get-azvm-parameter-sets.png)

#### <a name="parameter-values"></a>参数值

指定某个参数的值时，可以选择一个数据源，以便确定如何指定该值。 可用于特定参数的数据源取决于该参数的有效值。 例如，对于不允许 Null 值的参数，Null 不会是可用选项。

| 数据源 | 说明 |
|:--- |:--- |
| 常量值 |键入参数的值。 此数据源仅适用于以下数据类型：Int32、Int64、字符串、布尔、日期时间、交换机。 |
| 活动输出 |使用工作流中当前活动之前的活动的输出。 将列出所有有效的活动。 对于参数值，仅使用生成输出的活动。 如果活动输出具有多个属性的对象，则可以在选择活动后键入特定属性的名称。 |
| Runbook 输入 |选择 Runbook 输入作为活动参数的输入。 |
| 变量资产 |选择自动化变量作为输入。 |
| 凭据资产 |选择自动化凭据作为输入。 |
| 证书资产 |选择自动化证书作为输入。 |
| 连接资产 |选择自动化连接作为输入。 |
| PowerShell 表达式 |指定一个简单的[PowerShell 表达式](#powershell-expressions)。 在活动和结果用于参数值之前计算表达式。 可以使用变量来引用活动或 Runbook 输入参数的输出。 |
| 未配置 |清除以前配置的任何值。 |

#### <a name="optional-additional-parameters"></a>可选的其他参数

所有 cmdlet 都会有提供其他参数的选项。 这些是 PowerShell 通用参数或其他自定义参数。 图形编辑器提供一个文本框，您可以在其中使用 PowerShell 语法提供参数。 例如，要使用`Verbose`公共参数，应指定`-Verbose:$True`。

### <a name="retry-activity"></a>重试活动

活动的重试功能允许它多次运行，直到满足特定条件，就像循环一样。 您可以将此功能用于应多次运行、容易出错、可能需要多次尝试才能成功的活动，或者测试活动的输出信息以查找有效数据。

对活动启用重试时，可以设置延迟和条件。 延迟是 Runbook 再次运行活动之前等待的时间（以秒或分钟为单位）。 如果未指定延迟，则活动在完成后立即再次运行。

![活动重试延迟](media/automation-graphical-authoring-intro/retry-delay.png)

重试条件是每次运行活动后计算的 PowerShell 表达式。 如果表达式解析为 True，则活动将再次运行。 如果表达式解析为 False，则活动不会再次运行，并且 Runbook 将移动到下一个活动。

![活动重试延迟](media/automation-graphical-authoring-intro/retry-condition.png)

重试条件可以使用名为 的`RetryData`变量，该变量提供对有关活动重试的信息的访问。 此变量具有下表中的属性。

| properties | 说明 |
|:--- |:--- |
| `NumberOfAttempts` |活动已运行的次数。 |
| `Output` |活动上次运行的输出。 |
| `TotalDuration` |活动首次开始之后的经过时间。 |
| `StartedAt` |首次启动活动的时间（UTC 格式）。 |

以下是活动重试条件的示例。

```powershell-interactive
# Run the activity exactly 10 times.
$RetryData.NumberOfAttempts -ge 10
```

```powershell-interactive
# Run the activity repeatedly until it produces any output.
$RetryData.Output.Count -ge 1
```

```powershell-interactive
# Run the activity repeatedly until 2 minutes has elapsed.
$RetryData.TotalDuration.TotalMinutes -ge 2
```

配置活动的重试条件以后，该活动将包含两个用于提醒可视提示。 一个显示在活动中，另一个显示在您查看活动的配置时。

![活动重试可视指示符](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>工作流脚本控件

工作流脚本控件是一种特殊活动，它接受 PowerShell 或 PowerShell 工作流脚本，具体取决于所创作的图形运行簿的类型。 此控件提供可能通过其他方式不可用的功能。 它不能接受参数，但可以使用针对活动输出和 Runbook 输入参数的变量。 活动的任何输出都添加到数据总线。 一个例外是输出，没有传出链接，在这种情况下，输出将添加到 Runbook 的输出中。

例如，以下代码使用名为 的`NumberOfDays`Runbook 输入变量执行日期计算。 然后，它将计算的 DateTime 值作为输出，用于 Runbook 中的后续活动。

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="links-and-workflow"></a>链接和工作流

图形 Runbook 中的链接用于连接两个活动。 它作为箭头显示在画布上，从源活动指向目标活动。 活动按箭头的方向运行，源活动完成后才会开始目标活动。

### <a name="link-creation"></a>链接创建

您可以通过选择源活动并单击形状底部的圆圈，在两个活动之间创建链接。 将箭头拖到目标活动，然后放开。

![创建链接](media/automation-graphical-authoring-intro/create-link-options.png)

选择可在“配置”边栏选项卡中配置其属性的链接。 属性包括链接类型，下表中介绍。

| 链接类型 | 说明 |
|:--- |:--- |
| 管道 |对于源活动的每个对象输出，目标活动运行一次。 如果源活动没有生成任何输出，目标活动将不会运行。 源活动的输出可用作对象。 |
| 序列 |目标活动在从源活动接收输出时仅运行一次。 源活动的输出可用作对象数组。 |

### <a name="start-of-activity"></a>活动开始

图形 Runbook 会通过任何没有传入链接的活动启动。 通常只有一个活动充当 Runbook 的起始活动。 如果多个活动没有传入链接，则 Runbook 首先并行运行它们。 然后，它会在每个活动完成时，按链接来运行其他活动。

### <a name="link-conditions"></a>链接条件

在链接上指定条件时，仅当条件解析为 True 时，目标活动才会运行。 通常使用条件中的`ActivityOutput`变量从源活动检索输出。

对于管道链接，必须为单个对象指定条件。 Runbook 按源活动计算每个对象输出的条件。 然后，它运行满足条件的每个对象的目标活动。 例如，对于 源活动`Get-AzVM`，可以使用以下语法进行条件管道链接，以仅检索名为 Group1 的资源组中的虚拟机。

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

对于序列链接，Runbook 只计算条件一次，因为返回包含源活动中所有对象的单个数组。 因此，Runbook 不能使用序列链接进行筛选，就像使用管道链接一样。 序列链接可以简单地确定是否运行下一个活动。

例如，在我们的 **"开始"VM**运行簿中获取以下一组活动：

![使用序列的条件链接](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Runbook 使用三个不同的序列链接来验证输入参数`VMName`的值并确定`ResourceGroupName`要执行的适当操作。 可能的操作是启动单个 VM、启动资源组中的所有 VM 或启动订阅中的所有 VM。 对于 和`Get single VM`之间的`Connect to Azure`序列链接，下面是条件逻辑：

```powershell-interactive
<#
Both VMName and ResourceGroupName runbook input parameters have values
#>
(
(($VMName -ne $null) -and ($VMName.Length -gt 0))
) -and (
(($ResourceGroupName -ne $null) -and ($ResourceGroupName.Length -gt 0))
)
```

使用条件链接时，将通过条件来筛选该分支中从源活动到其他活动所提供的数据。 如果活动是多个链接的源，则每个分支中活动可用的数据取决于连接到该分支的链接中的条件。

例如，`Start-AzVM`下面的 Runbook 中的活动将启动所有虚拟机。 它有两个条件链接。 如果`Start-AzVM`活动成功完成，第一`$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`个条件链接使用表达式进行筛选。 如果活动无法启动虚拟机，`$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true``Start-AzVm`则第二个条件链接使用表达式进行筛选。

![条件链接示例](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

第一个链接之后使用活动输出的任何活动`Get-AzureVM`将仅检索运行时启动`Get-AzureVM`的虚拟机。 第二个链接之后的任何活动仅获取运行时停止`Get-AzureVM`的虚拟机。 任何按第三个链接进行的活动都会获取所有虚拟机，不管这些虚拟机的运行状态如何。

### <a name="junctions"></a>交接点

一个交接点是一个特殊活动，它会一直等到所有传入分支完成为止。 这允许 Runbook 并行运行多个活动，并确保所有活动在继续之前都已完成。

虽然交汇点可以具有无限数量的传入链路，但这些链路中只有一个可以是管道。 传入序列链接的数目不受限制。 您可以创建具有多个传入管道链接的交汇点并保存 Runbook，但在运行时它将失败。

下面的示例是某个 Runbook 的一部分，该 Runbook 可以启动一组虚拟机，同时还会下载要应用到这些虚拟机的修补程序。 它使用一个交汇点来确保在 Runbook 继续之前完成这两个进程。

![交接点](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Cycles

当目标活动链接回其源活动或最终链接回其源的另一个活动时，将形成一个循环。 图形创作当前不支持周期。 如果 Runbook 有一个周期，可以正常保存它，但在运行它时会收到一个错误。

![周期](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="data-sharing-between-activities"></a>活动之间的数据共享

具有传出链接的活动输出的任何数据都写入 Runbook 的数据总线。 Runbook 中的任何活动都可以使用数据总线上的数据来填充参数值或添加脚本代码。 一个活动可以访问工作流中任何以前的活动的输出。

数据写入数据总线的方式取决于活动的链接的类型。 对于管道链接，数据作为多个对象输出。 就序列链接来说，数据是以数组形式输出的。 如果只有一个值，则将其输出为单元素数组。

Runbook 有两种方法可以访问数据总线上的数据： 
* 使用活动输出数据源。
* 使用 PowerShell 表达式数据源。

第一个机制使用活动输出数据源填充另一个活动的参数。 如果输出是对象，则 Runbook 可以指定单个属性。

![活动输出](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

第二个数据访问机制使用如下所示的语法检索 PowerShell 表达式数据源中活动的输出或具有`ActivityOutput`变量的工作流脚本活动的输出。 如果输出是对象，则 Runbook 可以指定单个属性。

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="checkpoints"></a>检查点

您可以通过选择任何活动的**检查点运行簿**，在图形 PowerShell 工作流 Runbook 中设置[检查点](automation-powershell-workflow.md#checkpoints)。 这会导致在运行活动之后设置检查点。

![检查点](media/automation-graphical-authoring-intro/set-checkpoint.png)

检查点仅在图形 PowerShell 工作流运行簿中启用，在图形运行簿中不可用。 如果 Runbook 使用 Azure cmdlet，它应遵循具有`Connect-AzAccount`活动的任何检查点活动。 连接操作用于运行簿挂起且必须从其他工作人员上的此检查点重新启动。

## <a name="runbook-input-and-output"></a>Runbook 输入和输出

### <a name="runbook-input"></a>运行簿输入<a name="runbook-input"></a>

Runbook 需要从通过 Azure 门户启动 Runbook 的用户或从另一个 Runbook（如果当前运行簿用作子簿）输入。 例如，对于创建虚拟机的 Runbook，用户可能需要在每次 Runbook 启动时提供虚拟机的名称和其他属性等信息。

Runbook 通过定义一个或多个输入参数来接受输入。 每次 Runbook 启动时，用户都会提供这些参数的值。 当用户使用 Azure 门户启动 Runbook 时，系统会提示用户为 Runbook 支持的每个输入参数提供值。

创作 Runbook 时，可以通过单击 Runbook 工具栏上的 **"输入"和"输出**"来访问其输入参数。 这种情况下将打开输入和输出控件，可以通过单击“添加输入”**** 在该控件中编辑现有输入参数或创建新的参数。

![添加输入](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

按下表中的属性定义每个输入参数：

| properties | 说明 |
|:--- |:--- |
| 名称 | 必需。 参数的名称。 该名称在 Runbook 中必须是唯一的。 它必须以字母开头，并且只能包含字母、数字和下划线。 名称不能包含空格。 |
| 说明 |可选。 输入参数的用途说明。 |
| 类型 | 可选。 参数值应有的数据类型。 提示输入时，Azure 门户将针对每个参数的数据类型提供相应的控件。 支持的参数类型包括 String、Int32、Int64、Decimal、Boolean、DateTime 和 Object。 如果未选择数据类型，将默认为 String。|
| 必需 | 可选。 指定是否必须为参数提供值的设置。 如果选择`yes`，则必须在启动 Runbook 时提供值。 如果选择`no`，则在启动 Runbook 时不需要值，并且可以使用默认值。 如果不为每个未定义默认值的必需参数提供值，则 Runbook 无法启动。 |
| 默认值 | 可选。 在启动 Runbook 时未传入参数时用于参数的值。 要设置默认值，请选择`Custom`。 如果`None`不想提供任何默认值，请选择该选项。 |

### <a name="runbook-output"></a>Runbook 输出

图形创作保存由没有传出链接到[Runbook 输出](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages)的任何活动创建的数据。 输出将与 Runbook 作业一起保存，在该 Runbook 作为子 Runbook 使用的情况下，还可供父 Runbook 使用。

## <a name="powershell-expressions"></a>PowerShell 表达式

图形创作的优点之一是，它允许您构建一个运行簿，对 PowerShell 的了解最少。 不过，目前，您确实需要了解一些 PowerShell 来填充某些[参数值](#activities)并设置[链接条件](#links-and-workflow)。 本节简要介绍 PowerShell 表达式。 [使用 Windows PowerShell 编写脚本](https://technet.microsoft.com/library/bb978526.aspx)中提供了 PowerShell 的完整详细信息。

### <a name="powershell-expression-data-source"></a>PowerShell 表达式数据源

您可以使用 PowerShell 表达式作为数据源，使用 PowerShell 代码的结果填充[活动参数](#activities)的值。 表达式可以是执行简单函数的单行代码，也可以是执行某些复杂逻辑的多行。 未分配给变量的任何命令输出都将输出到参数值。

例如，以下命令输出当前日期。

```powershell-interactive
Get-Date
```

下一个代码段从当前日期生成字符串并将其分配给变量。 代码将变量的内容发送到输出。

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

以下命令评估当前日期并返回一个字符串，指示当前日期是周末还是工作日。

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="activity-output"></a>活动输出

要使用 Runbook 中以前活动的输出，请使用具有以下语法`ActivityOutput`的变量。

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

例如，可以具有需要虚拟机名称的属性的活动。 在这种情况下，runbook 可以使用以下表达式。

```powershell-interactive
$ActivityOutput['Get-AzureVM'].Name
```

如果属性需要虚拟机对象，而不是仅名称，则 Runbook 使用以下语法返回整个对象。

```powershell-interactive
$ActivityOutput['Get-AzureVM']
```

Runbook 可以在更复杂的表达式中使用活动的输出，如下所示。 此表达式将文本串联到虚拟机名称。

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVM'].Name
```

### <a name="conditions"></a>条件

使用[比较运算符](https://technet.microsoft.com/library/hh847759.aspx)来比较值或确定值是否与指定的模式匹配。 比较返回"真"或"真"的值。

例如，以下条件确定来自命名`Get-AzureVM`活动的虚拟机当前是否停止。

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

以下条件确定同一虚拟机是否处于停止以外的任何状态。

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

您可以使用[逻辑运算符](https://technet.microsoft.com/library/hh847789.aspx)（如`-and`）`-or`联接 Runbook 中的多个条件。 例如，以下条件检查上例中的虚拟机是否处于"停止"或"停止"状态。

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="hashtables"></a>哈希表

[哈希表](https://technet.microsoft.com/library/hh847780.aspx)是名称值对，可用于返回一组值。 您可能还会看到称为字典的哈希表。 某些活动的属性需要可哈希值而不是简单值。

使用以下语法创建哈希表。 它可以包含任意数量的条目，但每个条目由名称和值定义。

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

例如，以下表达式创建一个哈希表，用作活动参数的数据源，该参数需要 Internet 搜索的值可哈希。

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

下面的示例使用调用`Get Twitter Connection`的活动的输出来填充哈希表。

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="authenticating-to-azure-resources"></a>通过 Azure 资源进行身份验证

Azure 自动化中用于管理 Azure 资源的 Runbook 将需要通过 Azure 进行身份验证。 [运行 As 帐户](automation-create-runas-account.md)（也称为服务主体）是自动化 Runbook 用于访问订阅中的 Azure 资源管理器资源的默认机制。 通过将使用 PowerShell`AzureRunAsConnection`[获取-自动化连接](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx)cmdlet 的连接资产添加到画布中，可以将此功能添加到图形 Runbook。 您还可以添加[连接-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet。 以下示例说明了此方案。

![运行方式身份验证活动](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

活动`Get Run As Connection`或`Get-AutomationConnection`配置了名为 的`AzureRunAsConnection`常量值数据源。

![运行方式连接配置](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

下一个活动`Connect-AzAccount`， 添加经过身份验证的 Run As 帐户，用于 runbook 中。

![连接-Azaccount 参数集](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

>[!NOTE]
>对于 PowerShell 运行`Add-AzAccount`簿`Add-AzureRMAccount`，并且是`Connect-AzAccount`的别名。 请注意，这些别名不适用于图形运行簿。 图形运行簿只能使用`Connect-AzAccount`自身。

对于参数字段 **"应用ID"、"****证书"** 和 **"TENANTID"，** 指定字段路径的属性的名称，因为活动输出具有多个属性的对象。 否则，当 Runbook 执行时，它尝试进行身份验证时失败。 这是你在通过运行方式帐户对 Runbook 进行身份验证时必须满足的最低要求。

某些订阅者使用 Azure AD[用户帐户](automation-create-aduser-account.md)创建自动化帐户来管理 Azure 经典部署或 Azure 资源管理器资源。 为了维护这些订阅者的向后兼容性，runbook 中使用的身份验证机制是具有`Add-AzureAccount`[凭据资产](automation-credentials.md)的 cmdlet。 该资产表示具有 Azure 帐户访问权限的活动目录用户。

您可以通过向画布添加凭据资产，然后将凭据资产用于其输入`Add-AzureAccount`的活动，为图形 Runbook 启用此功能。 请参阅以下示例。

![身份验证活动](media/automation-graphical-authoring-intro/authentication-activities.png)

Runbook 必须在其开始和之后在每个检查点后进行身份验证。 因此，您必须在任何`Add-AzureAccount``Checkpoint-Workflow`活动之后使用活动。 您无需使用其他凭据活动。

![活动输出](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="exporting-and-importing-a-graphical-runbook"></a>导出和导入图形 Runbook

可以只导出图形 Runbook 的已发布版本。 如果运行簿尚未发布，则禁用 **"导出"** 按钮。 单击"**导出"** 按钮时，Runbook 将下载到本地计算机。 文件的名称将 runbook 的名称与 **.graphrunbook**扩展名匹配。

您可以通过在添加 Runbook 时选择 **"导入**"选项来导入图形或图形 PowerShell 工作流运行簿文件。 选择要导入的文件时，可以保留相同的名称或提供新名称。 **Runbook 类型**字段在评估所选文件后显示 Runbook 的类型。 如果尝试选择不正确的其他类型，图形编辑器将显示一条消息，指出存在潜在冲突，并且转换期间可能存在语法错误。

![导入 Runbook](media/automation-graphical-authoring-intro/runbook-import.png)

## <a name="testing-a-graphical-runbook"></a>测试图形 Runbook

Azure 自动化中的每个图形运行簿都有草稿版本和已发布版本。 您只能运行已发布版本，而只能编辑草稿版本。 已发布版不受对草稿版所做的任何更改的影响。 当草稿版本准备好使用时，您可以发布它，这将用草稿版本覆盖当前发布的版本。

您可以在 Azure 门户中测试 Runbook 的草稿版本，同时保持已发布版本不变。 或者，您可以在新 Runbook 发布之前对其进行测试，以便在任何版本替换之前验证 Runbook 是否正常工作。 Runbook 的测试将执行草稿版本，并确保完成它执行的任何操作。 未创建作业历史记录，但"测试输出"窗格显示输出。

通过打开 Runbook 进行编辑，然后单击"测试"窗格，打开图形运行簿的 **"测试"控件**。 "测试"控件提示输入参数，您可以通过单击 **"开始"** 启动 Runbook 来启动 Runbook。

## <a name="publishing-a-graphical-runbook"></a>发布图形 Runbook

通过打开 Runbook 进行编辑，然后单击 **"发布**"来发布图形运行簿。 Runbook 的可能状态为：

* 新的 - 运行手册尚未发布。 
* 已发布 -- 运行簿已发布。
* 在编辑中 - Runbook 在发布后进行了编辑，草稿和已发布版本也不同。

![Runbook 状态](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

您可以选择还原到 Runbook 的已发布版本。 此操作将放弃自上次发布 Runbook 以来所做的任何更改。 它将 Runbook 的草稿版本替换为已发布版本。

## <a name="next-steps"></a>后续步骤

* 要开始使用 PowerShell 工作流运行簿，请参阅[我的第一个 PowerShell 工作流运行簿](automation-first-runbook-textual.md)。
* 要开始使用图形运行簿，请参阅[我的第一个图形运行簿](automation-first-runbook-graphical.md)。
* 要了解有关 Runbook 类型及其优点和限制的更多信息，请参阅[Azure 自动化 Runbook 类型](automation-runbook-types.md)。
* 要了解如何使用"作为帐户"进行自动化运行进行身份验证，请参阅[配置 Azure 运行作为帐户](automation-sec-configure-azure-runas-account.md)。
* 有关 PowerShell cmdlet 引用，请参阅[Az.自动化](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。
