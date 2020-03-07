---
title: Azure 自动化中的图形创作
description: 图形创作可以让你在不使用代码的情况下，为 Azure 自动化创建 Runbook。 本文介绍了图形创作以及开始创建图形 Runbook 所需的所有详细信息。
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: a50dbe4d1e100032282891ccd15a94330f7fead4
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373380"
---
# <a name="graphical-authoring-in-azure-automation"></a>Azure 自动化中的图形创作

利用图形创作，无需编写基本的 Windows PowerShell 或 PowerShell 工作流代码，就能为 Azure 自动化创建 runbook。 你可以将活动从 cmdlet 和 runbook 的库添加到画布，将它们链接在一起，然后将其配置为形成工作流。 如果你曾经使用过 System Center Orchestrator 或 Service Management Automation （SMA），图形创作应该非常熟悉。 本文介绍了开始创建图形 runbook 所需的概念。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az module 安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于你的自动化帐户，可使用[如何在 Azure 自动化中更新 Azure PowerShell 模块](automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="graphical-runbooks"></a>图形 Runbook

Azure 自动化中的所有 runbook 都是 Windows PowerShell 工作流。 图形 runbook 和图形 PowerShell 工作流 runbook 会生成自动化工作线程运行的 PowerShell 代码，但无法查看或修改这些代码。 可以将图形 runbook 转换为图形 PowerShell 工作流 runbook，反之亦然。 但是，不能将这些 runbook 转换为文本 runbook。 此外，自动化图形编辑器无法导入文本 runbook。

## <a name="overview-of-graphical-editor"></a>图形编辑器概述

通过创建或编辑图形 Runbook，可以在 Azure 门户中打开图形编辑器。

![图形工作区](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

以下各节介绍图形编辑器中的控件。

### <a name="canvas-control"></a>Canvas 控件

可以通过 Canvas 控件设计 runbook。 可以将库控件中节点的活动添加到 runbook，并将其与链接连接以定义 runbook 逻辑。 在画布的底部有一些控件可用于放大和缩小。

### <a name="library-control"></a>库控件

库控件允许选择要添加到 runbook 的[活动](#activities)。 将它们添加到画布，可在其中将它们连接到其他活动。 库控件包含下表中定义的部分。

| 部分 | 说明 |
|:--- |:--- |
| Cmdlet |可在 runbook 中使用的所有 cmdlet。 Cmdlet 按模块组织。 已在自动化帐户中安装的所有模块都可用。 |
| Runbook |自动化帐户中的 runbook。 可以将这些 runbook 添加到要用作子 runbook 的画布上。 仅显示其核心类型与所编辑 runbook 相同的 runbook。 对于图形 runbook，仅显示基于 PowerShell 的 runbook。 对于图形 PowerShell 工作流 runbook，仅显示基于 PowerShell 工作流的 runbook。 |
| 资产 |你的自动化帐户中可在 runbook 中使用的[自动化资产](/previous-versions/azure/dn939988(v=azure.100))。 向 runbook 添加资产将添加用于获取所选资产的工作流活动。 在使用变量资产的情况下，可以选择是否添加用于获取变量或设置变量的活动。 |
| Runbook 控件 |控制可以在当前 runbook 中使用的活动。 接合活动采用多个输入，并等待所有输入完成，然后再继续执行工作流。 代码活动根据图形 runbook 类型运行一行或多行 PowerShell 或 PowerShell 工作流代码。 可以将此活动用于自定义代码或通过其他活动难以实现的功能。 |

### <a name="configuration-control"></a>配置控件

使用配置控件可以为在画布上选择的对象提供详细信息。 此控件中的可用属性取决于所选对象的类型。 当你在配置控件中选择一个选项时，它会打开其他边栏选项卡以提供详细信息。

### <a name="test-control"></a>测试控件

首次启动图形编辑器时，不显示测试控件。 它会在你以交互方式测试图形 Runbook 时打开。

## <a name="activities"></a>活动

活动是 Runbook 的构建基块。 活动可以是 PowerShell cmdlet、子 runbook 或工作流。 您可以向 runbook 添加活动，方法是在库控件中右键单击该活动，然后选择 "**添加到画布**"。 然后，可以通过单击拖放的方式将活动置于画布上喜欢的任何位置。 活动在画布上的位置不会影响 runbook 的操作。 你可以根据你最适合的方式为你的 runbook 布局，以可视化其操作。

![添加到画布](media/automation-graphical-authoring-intro/add-to-canvas-revised20165.png)

在画布上选择一个活动，以便在 "配置" 边栏选项卡中配置其属性和参数。 可以将活动的 "标签" 更改为 "描述性名称"。 Runbook 仍会运行原始 cmdlet。 只需更改图形编辑器使用的显示名称。 请注意，该标签在 runbook 中必须是唯一的。

### <a name="parameter-sets"></a>参数集

参数集用于定义会接受特定 cmdlet 的值的必需参数和可选参数。 所有 cmdlet 都有至少一个参数集，有些都有多个集。 如果某个 cmdlet 有多个参数集，则必须选择要使用的参数集，然后才能配置参数。 您可以通过选择 "**参数集**" 并选择另一个集来更改活动使用的参数集。 在这种情况下，已配置的任何参数值都将丢失。

在下面的示例中， [new-azvm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) cmdlet 有三个参数集。 此示例使用一个名为**ListVirtualMachineInResourceGroupParamSet**的集，其中包含一个可选参数，用于返回资源组中的所有虚拟机。 该示例还使用**GetVirtualMachineInResourceGroupParamSet**参数集来指定要返回的虚拟机。 此集具有两个必需参数和一个可选参数。

![参数集](media/automation-graphical-authoring-intro/get-azurermvm-parameter-sets.png)

#### <a name="parameter-values"></a>参数值

指定某个参数的值时，可以选择一个数据源，以便确定如何指定该值。 可用于特定参数的数据源取决于该参数的有效值。 例如，对于不允许 null 值的参数， **Null**不是可用的选项。

| 数据源 | 说明 |
|:--- |:--- |
| 常量值 |键入参数的值。 此数据源仅适用于以下数据类型： Int32、Int64、字符串、布尔值、DateTime、开关。 |
| 活动输出 |使用工作流中当前活动之前的活动的输出。 将列出所有有效的活动。 对于参数值，只使用生成输出的活动。 如果活动输出的对象具有多个属性，则可以在选择活动之后键入特定属性的名称。 |
| Runbook 输入 |选择一个 runbook 输入作为活动参数的输入。 |
| 变量资产 |选择一个自动化变量作为输入。 |
| 凭据资产 |选择一个自动化凭据作为输入。 |
| 证书资产 |选择一个自动化证书作为输入。 |
| 连接资产 |选择一个自动化连接作为输入。 |
| PowerShell 表达式 |指定简单的[PowerShell 表达式](#powershell-expressions)。 表达式在活动和结果用于参数值之前进行计算。 可以使用变量来引用活动或 Runbook 输入参数的输出。 |
| 未配置 |清除以前配置的任何值。 |

#### <a name="optional-additional-parameters"></a>可选的其他参数

所有 cmdlet 都会有提供其他参数的选项。 这些是 PowerShell 通用参数或其他自定义参数。 图形编辑器提供了一个文本框，可以在其中使用 PowerShell 语法提供参数。 例如，若要使用*Verbose*通用参数，应指定 `-Verbose:$True`。

### <a name="retry-activity"></a>重试活动

活动的重试功能允许在满足特定条件时多次运行，这与循环非常类似。 此功能可用于应该多次运行的活动，容易出错，可能需要多次尝试才能成功，或者测试活动的输出信息中是否存在有效数据。

对活动启用重试时，可以设置延迟和条件。 延迟是 Runbook 再次运行活动之前等待的时间（以秒或分钟为单位）。 如果未指定延迟，则活动在完成之后立即再次运行。

![活动重试延迟](media/automation-graphical-authoring-intro/retry-delay.png)

重试条件是在每次运行活动后评估的 PowerShell 表达式。 如果表达式解析为 True，则活动再次运行。 如果表达式解析为 False，则活动不会再次运行，并且 runbook 会转到下一个活动。

![活动重试延迟](media/automation-graphical-authoring-intro/retry-condition.png)

重试条件可以使用名为*RetryData*的变量，该变量提供对活动重试相关信息的访问。 此变量具有下表中的属性。

| properties | 说明 |
|:--- |:--- |
| NumberOfAttempts |活动已运行的次数。 |
| 输出 |活动上次运行的输出。 |
| TotalDuration |活动首次开始之后的经过时间。 |
| StartedAt |首次启动活动时的时间（UTC 格式）。 |

下面是活动重试条件的示例。

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

配置活动的重试条件以后，该活动将包含两个用于提醒可视提示。 其中一个在活动中显示，另一个在查看活动的配置时显示。

![活动重试可视指示符](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>工作流脚本控件

工作流脚本控件是一种特殊的活动，它接受 PowerShell 或 PowerShell 工作流脚本，具体取决于所创作的图形 runbook 的类型。 此控件提供了可能无法通过其他方式获得的功能。 它不能接受参数，但可以使用针对活动输出和 Runbook 输入参数的变量。 活动的任何输出都将添加到 databus。 异常是没有传出链接的输出，在这种情况下，会将输出添加到 runbook 的输出中。

例如，以下代码使用名为*NumberOfDays*的 runbook 输入变量执行日期计算。 然后，它会将计算的日期时间值作为输出发送，供 runbook 中的后续活动使用。

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="links-and-workflow"></a>链接和工作流

图形 Runbook 中的链接用于连接两个活动。 它作为箭头显示在画布上，从源活动指向目标活动。 活动按箭头的方向运行，源活动完成后才会开始目标活动。

### <a name="link-creation"></a>链接创建

您可以通过选择源活动，然后单击形状底部的圆圈来创建两个活动之间的链接。 将箭头拖到目标活动，然后放开。

![创建链接](media/automation-graphical-authoring-intro/create-link-revised20165.png)

选择可在“配置”边栏选项卡中配置其属性的链接。 属性包括下表中描述的链接类型。

| 链接类型 | 说明 |
|:--- |:--- |
| 管道 |对于源活动中的每个对象输出，目标活动都将运行一次。 如果源活动没有生成任何输出，目标活动将不会运行。 源活动的输出可用作对象。 |
| 序列 |仅当目标活动接收源活动的输出时，才运行一次。 源活动的输出可用作对象数组。 |

### <a name="start-of-activity"></a>活动的开始

图形 Runbook 会通过任何没有传入链接的活动启动。 通常只有一个活动充当 runbook 的启动活动。 如果多个活动没有传入链接，则 runbook 将开始并行运行它们。 然后，它会在每个活动完成时，按链接来运行其他活动。

### <a name="link-conditions"></a>链接条件

在链接上指定条件时，只有当条件解析为 True 时才会运行目标活动。 通常会在条件中使用*ActivityOutput*变量来检索源活动的输出。

对于管道链接，必须为单个对象指定条件。 Runbook 针对源活动的每个对象输出对条件进行评估。 然后，它将为满足条件的每个对象运行目标活动。 例如，对于**new-azvm**的源活动，你可以使用以下条件管道链接语法来仅检索资源组中名为 Group1 的虚拟机。

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

对于序列链接，runbook 只计算一次条件，因为返回包含源活动中所有对象的单个数组。 因此，runbook 不能使用序列链接进行筛选，就像它可以使用管道链接一样。 序列链接可以仅确定是否运行下一个活动。

例如，在 "**启动 VM** " runbook 中执行以下一组活动：

![使用序列的条件链接](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Runbook 使用三个不同的序列链接来验证输入参数*VMName*和*ResourceGroupName*的值，以确定要执行的相应操作。 可能的操作是启动单个 VM、启动资源组中的所有 Vm，或者启动订阅中的所有 Vm。 对于 "**连接到 Azure** " 和 "**获取单个 VM**" 之间的序列链接，以下是条件逻辑：

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

使用条件链接时，将通过条件来筛选该分支中从源活动到其他活动所提供的数据。 如果某个活动是多个链接的源，则每个分支中可供活动使用的数据将取决于连接到该分支的链接中的条件。

例如，下面的 runbook 中的**new-azvm**活动将启动所有虚拟机。 它有两个条件链接。 第一个条件链接使用 expression `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` 筛选**new-azvm**活动是否成功完成。 第二个条件链接使用 expression `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` 筛选**new-azvm**活动是否无法启动虚拟机。

![条件链接示例](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

在第一个链接之后使用的活动**输出的任何活动仅会**检索运行**get-help**时启动的虚拟机。 遵循第二个链接的任何活动仅会获取运行**get-help**时停止的虚拟机。 任何按第三个链接进行的活动都会获取所有虚拟机，不管这些虚拟机的运行状态如何。

### <a name="junctions"></a>交接点

一个交接点是一个特殊活动，它会一直等到所有传入分支完成为止。 这允许 runbook 并行运行多个活动，并确保在继续操作之前全部完成。

尽管接合点可以有无限数量的传入链接，但其中只有一个链接可以是管道。 传入序列链接的数目不受限制。 可以创建具有多个传入管道链接的接合点并保存 runbook，但在运行它时它将失败。

下面的示例是某个 Runbook 的一部分，该 Runbook 可以启动一组虚拟机，同时还会下载要应用到这些虚拟机的修补程序。 它使用交接点来确保在 runbook 继续之前两个进程都已完成。

![交接点](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>Cycles

当目标活动链接回其源活动或最终链接回其源的另一个活动时，将形成循环。 图形创作目前不支持循环。 如果 Runbook 有一个周期，可以正常保存它，但在运行它时会收到一个错误。

![周期](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="data-sharing-between-activities"></a>活动之间的数据共享

带有传出链接的活动输出的任何数据都将写入到此 runbook 的 databus。 Runbook 中的任何活动都可以使用数据总线上的数据来填充参数值或添加脚本代码。 一个活动可以访问工作流中任何以前的活动的输出。

数据写入数据总线的方式取决于活动的链接的类型。 对于管道链接，数据作为多个对象输出。 就序列链接来说，数据是以数组形式输出的。 如果只有一个值，则它作为单元素数组输出。

Runbook 有两种方法可用于在 databus 上访问数据： 
* 使用活动输出数据源。
* 使用 PowerShell 表达式数据源。

第一种机制是使用活动输出数据源来填充另一活动的参数。 如果输出是一个对象，则该 runbook 可以指定单个属性。

![活动输出](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

第二个数据访问机制使用如下所示的语法，在 PowerShell 表达式数据源或包含*ActivityOutput*变量的工作流脚本活动中检索活动的输出。 如果输出是一个对象，则 runbook 可以指定单个属性。

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="checkpoints"></a>检查点

可以通过在任何活动上选择**检查点 runbook** ，在图形 PowerShell 工作流 runbook 中设置[检查点](automation-powershell-workflow.md#checkpoints)。 这会导致在运行活动之后设置检查点。

![Checkpoint](media/automation-graphical-authoring-intro/set-checkpoint.png)

检查点仅在图形 PowerShell 工作流 runbook 中启用，在图形 runbook 中不可用。 如果 runbook 使用 Azure cmdlet，则它应遵循**AzAccount**活动的任何检查点活动。 如果 runbook 已挂起，则使用 connect 操作，并且必须在另一辅助角色上从此检查点重新启动。

## <a name="runbook-input-and-output"></a>Runbook 输入和输出

### Runbook 输入<a name="runbook-input"></a>

Runbook 需要从通过 Azure 门户启动 runbook 的用户或从另一个 runbook （如果当前的 runbook 用作子 runbook）进行输入。 例如，对于创建虚拟机的 runbook，用户可能需要在每次启动 runbook 时提供此类信息作为虚拟机的名称和其他属性。

Runbook 通过定义一个或多个输入参数来接受输入。 每次启动 runbook 时，用户都提供这些参数的值。 当用户使用 Azure 门户启动 runbook 时，系统将提示用户为 runbook 支持的每个输入参数提供值。

创作 runbook 时，可以通过单击 runbook 工具栏上的 "**输入和输出**" 按钮来访问其输入参数。 这将打开输入和输出控件，你可以在其中编辑现有输入参数或通过单击 "**添加输入**" 创建一个新的。

![添加输入](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

按下表中的属性定义每个输入参数：

| properties | 说明 |
|:--- |:--- |
| 名称 | 必需。 参数的名称。 该名称在 runbook 中必须是唯一的。 它必须以字母开头，只能包含字母、数字和下划线。 名称不能包含空格。 |
| 说明 |可选。 输入参数用途说明。 |
| 类型 | 可选。 参数值应有的数据类型。 提示输入时，Azure 门户将针对每个参数的数据类型提供相应的控件。 支持的参数类型包括 String、Int32、Int64、Decimal、Boolean、DateTime 和 Object。 如果未选择数据类型，则默认为字符串。|
| 必需 | 可选。 指定是否必须为参数提供值的设置。 如果选择 **"是"** ，则在启动 runbook 时必须提供一个值。 如果选择 "**否**"，则在启动 runbook 时不需要值，并且可以使用默认值。 如果没有为每个没有定义默认值的必需参数提供值，则无法启动 runbook。 |
| 默认值 | 可选。 如果在 runbook 启动时未传入参数，则为参数使用该值。 若要设置默认值，请选择“自定义”。 如果不想提供任何默认值，请选择 "**无**"。 |

### <a name="runbook-output"></a>Runbook 输出

图形创作保存由任何活动创建的数据，这些活动没有到[runbook 的输出的](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages)传出链接。 输出将与 Runbook 作业一起保存，在该 Runbook 作为子 Runbook 使用的情况下，还可供父 Runbook 使用。

## <a name="powershell-expressions"></a>PowerShell 表达式

图形创作的优点之一是它允许您使用最少的 PowerShell 知识生成 runbook。 但目前，您确实需要知道一些 PowerShell 来填充某些[参数值](#activities)和设置[链接条件](#links-and-workflow)。 本部分提供 PowerShell 表达式的快速简介。 [使用 Windows PowerShell 编写脚本](https://technet.microsoft.com/library/bb978526.aspx)中提供了 PowerShell 的完整详细信息。

### <a name="powershell-expression-data-source"></a>PowerShell 表达式数据源

您可以使用 PowerShell 表达式作为数据源，以使用 PowerShell 代码的结果来填充[活动参数](#activities)的值。 表达式可以是执行简单函数的一行代码，也可以是执行一些复杂逻辑的多行代码。 未分配给变量的任何命令输出都将输出到参数值。

例如，下面的命令输出当前日期。

```powershell-interactive
Get-Date
```

下一个代码段从当前日期生成一个字符串，并将其分配给一个变量。 该代码会将变量的内容发送到输出。

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

以下命令计算当前日期并返回一个字符串，该字符串指示当天是周末还是工作日。

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="activity-output"></a>活动输出

若要在 runbook 中使用上一个活动的输出，请使用具有以下语法的*ActivityOutput*变量。

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

例如，你可以有一个具有属性的活动，该属性需要虚拟机的名称。 在这种情况下，runbook 可以使用以下表达式。

```powershell-interactive
$ActivityOutput['Get-AzureVM'].Name
```

如果属性需要虚拟机对象而不只是名称，runbook 将使用以下语法返回整个对象。

```powershell-interactive
$ActivityOutput['Get-AzureVM']
```

Runbook 可以在更复杂的表达式中使用活动的输出，如下所示。 此表达式将文本连接到虚拟机名称。

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVM'].Name
```

### <a name="conditions"></a>条件

使用[比较运算符](https://technet.microsoft.com/library/hh847759.aspx)来比较值或确定值是否与指定的模式匹配。 比较返回的值为 True 或 False。

例如，以下条件确定名为**get-help**的活动中的虚拟机当前是否已停止。

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

以下条件确定同一虚拟机是否处于停止状态以外的任何状态。

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

可以使用[逻辑运算符](https://technet.microsoft.com/library/hh847789.aspx)（例如 **-and**或 **-or**）在 runbook 中加入多个条件。 例如，以下条件会检查上一示例中的虚拟机是否处于**停止**或**停止**状态。

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="hashtables"></a>哈希表

[哈希表](https://technet.microsoft.com/library/hh847780.aspx)是用于返回一组值的名称/值对。 你可能还会看到一个被称为字典的哈希表。 某些活动的属性应使用哈希表而不是简单值。

使用以下语法创建哈希表。 它可以包含任意数目的条目，但每个条目由一个名称和值定义。

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

例如，下面的表达式创建一个哈希表，该哈希表将用作需要某个 internet 搜索值哈希表的活动参数的数据源。

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

以下示例使用名为 **Get Twitter Connection** 的活动的输出来填充哈希表。

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="authenticating-to-azure-resources"></a>通过 Azure 资源进行身份验证

Azure 自动化中用于管理 Azure 资源的 Runbook 将需要通过 Azure 进行身份验证。 [运行方式帐户](automation-create-runas-account.md)（也称为服务主体）是自动化 runbook 用来访问订阅中的 Azure 资源管理器资源的默认机制。 可以通过将使用 PowerShell [get-automationconnection](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) Cmdlet 的**AzureRunAsConnection**连接资产添加到画布，将此功能添加到图形 runbook。 还可以添加[AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet。 下面的示例阐释了这种情况。

![运行方式身份验证活动](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

使用名为**AzureRunAsConnection**的常量值数据源配置 "**获取运行方式连接" 活动**或 " **get-automationconnection**" 配置。

![运行方式连接配置](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

接下来， **AzAccount**会添加已通过身份验证的运行方式帐户，以便在 runbook 中使用。

![AzAccount 参数集](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

>[!NOTE]
>对于 PowerShell runbook， **AzAccount**和**add-azurermaccount**是**AzAccount**的别名。 请注意，这些别名不适用于图形 runbook。 图形 runbook 只能使用**AzAccount**本身。

对于参数字段、 **APPLICATIONID**、 **CERTIFICATETHUMBPRINT**和**TENANTID**，请指定字段路径的属性的名称，因为该活动输出的对象具有多个属性。 否则，runbook 在执行时失败，并在尝试进行身份验证时失败。 这是你在通过运行方式帐户对 Runbook 进行身份验证时必须满足的最低要求。

某些订阅者使用[Azure AD 的用户帐户](automation-create-aduser-account.md)创建一个自动化帐户，用于管理 azure 经典部署或 azure 资源管理器资源。 若要维护这些订阅服务器的向后兼容性，在 runbook 中使用的身份验证机制是包含[凭据资产](automation-credentials.md)的**add-azureaccount** cmdlet。 资产表示有权访问 Azure 帐户的 Active Directory 用户。

你可以为图形 runbook 启用此功能，方法是将凭据资产添加到画布，然后使用**add-azureaccount**活动，该活动将凭据资产用于输入。 请参阅以下示例。

![身份验证活动](media/automation-graphical-authoring-intro/authentication-activities.png)

Runbook 必须在其开始和每个检查点之后进行身份验证。 因此，在任何**检查点工作流**活动之后，必须使用**add-azureaccount**活动。 不需要使用其他凭据活动。

![活动输出](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="exporting-and-importing-a-graphical-runbook"></a>导出和导入图形 Runbook

可以只导出图形 Runbook 的已发布版本。 如果尚未发布 runbook，则将禁用 "**导出**" 按钮。 单击 "**导出**" 按钮时，runbook 将下载到本地计算机。 文件的名称与 runbook 的名称匹配，扩展名为 **.graphrunbook** 。

添加 runbook 时，可以通过选择 "**导入**" 选项来导入图形或图形 PowerShell 工作流 runbook 文件。 选择要导入的文件时，可以保留相同的名称，也可以提供一个新的名称。 " **Runbook 类型**" 字段会在评估所选文件后显示 runbook 的类型。 如果尝试选择不正确的其他类型，则图形编辑器会显示一条消息，指出存在潜在的冲突，在转换过程中可能会出现语法错误。

![导入 Runbook](media/automation-graphical-authoring-intro/runbook-import-revised20165.png)

## <a name="testing-a-graphical-runbook"></a>测试图形 Runbook

Azure Automation 中的每个图形 runbook 都有草稿版本和已发布的版本。 您只能运行已发布的版本，而您只能编辑草稿版本。 已发布版不受对草稿版所做的任何更改的影响。 当草稿版本可供使用时，可以发布它，从而使用草稿版本覆盖当前发布的版本。

可以在 Azure 门户中测试 runbook 的草稿版本，同时保留发布版本不变。 或者，你可以在发布新的 runbook 之前对其进行测试，以便可以在替换任何版本之前验证 runbook 是否正常工作。 Runbook 的测试会执行草稿版本，并确保其执行的任何操作都已完成。 不会创建任何作业历史记录，但 "测试输出" 窗格会显示输出。

打开用于编辑的 runbook，然后单击 "**测试窗格**"，打开图形 Runbook 的测试控件。 测试控件会提示输入参数，可以通过单击 "**启动**" 来启动 runbook。

## <a name="publishing-a-graphical-runbook"></a>发布图形 Runbook

打开要编辑的 runbook，然后单击 "**发布**"，发布图形 runbook。 Runbook 的可能状态如下：

* 新--尚未发布 runbook。 
* 已发布-已发布 runbook。
* 在编辑中-已发布 runbook 后对其进行编辑，并且草稿版本和已发布版本不同。

![Runbook 状态](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

你可以选择还原到 runbook 的已发布版本。 此操作会丢弃自上次发布 runbook 后所做的任何更改。 它将 runbook 的草稿版替换为已发布的版本。

## <a name="next-steps"></a>后续步骤

* 若要开始使用 PowerShell 工作流 Runbook，请参阅 [我的第一个 PowerShell 工作流 Runbook](automation-first-runbook-textual.md)。
* 若要开始处理图形 runbook，请参阅[我的第一个图形 runbook](automation-first-runbook-graphical.md)。
* 若要了解有关 runbook 类型及其优点和限制的详细信息，请参阅[Azure 自动化 runbook 类型](automation-runbook-types.md)。
* 若要了解如何使用自动化运行方式帐户进行身份验证，请参阅[配置 Azure 运行方式帐户](automation-sec-configure-azure-runas-account.md)。
