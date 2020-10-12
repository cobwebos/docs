---
title: 在 Azure 自动化中创作图形 Runbook
description: 本文介绍了如何在不使用代码的情况下创作图形 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 161272fe35ee9ea1e0880b991273e5d1a79eafb4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90987319"
---
# <a name="author-graphical-runbooks-in-azure-automation"></a>在 Azure 自动化中创作图形 Runbook

Azure 自动化中的所有 Runbook 都是 Windows PowerShell 工作流。 图形 Runbook 和图形 PowerShell 工作流 Runbook 生成 PowerShell 代码，这些代码由自动化辅助角色运行，但你无法查看或修改。 可以将图形 Runbook 转换为图形 PowerShell 工作流 Runbook，反之亦然。 不过，无法将这些 Runbook 转换为文本 Runbook。 此外，自动化图形编辑器也无法导入文本 Runbook。

借助图形创作，可以为 Azure 自动化创建 Runbook，而不需要编写复杂的基础 Windows PowerShell 或 PowerShell 工作流代码。 可以将 cmdlet 和 Runbook 库中的活动添加到画布，将它们链接到一起，并配置它们来形成工作流。 如果你曾经使用过 System Center Orchestrator 或 Service Management Automation (SMA)，图形创作应该看起来很熟悉。 本文介绍了开始创建图形 Runbook 所需掌握的概念。

## <a name="overview-of-graphical-editor"></a>图形编辑器概述

通过创建或编辑图形 Runbook，可以在 Azure 门户中打开图形编辑器。

![图形工作区](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

以下各节介绍图形编辑器中的控件。

### <a name="canvas-control"></a>“画布”控件

使用“画布”控件，可以设计 Runbook。 可以将“库”控件中节点内的活动添加到 Runbook，并通过链接将它们连接起来，以定义 Runbook 逻辑。 在画布底部，有一些可用于放大和缩小的控件。

### <a name="library-control"></a>库控件

使用“库”控件，可以选择要添加到 Runbook 的[活动](#use-activities)。 可以将活动添加到画布中，在其中能够将它们连接到其他活动。 “库”控件包含下表中定义的部分。

| 部分 | 说明 |
|:--- |:--- |
| Cmdlet |可以在 Runbook 中使用的所有 cmdlet。 Cmdlet 按模块组织。 你在自动化帐户中安装的所有模块都是可用的。 |
| Runbook |自动化帐户中的 Runbook。 可以将这些 Runbook 添加到画布中，以用作子 Runbook。 只显示核心类型与所编辑的 Runbook 相同的 Runbook。 对于图形 Runbook，只显示基于 PowerShell 的 Runbook。 对于图形 PowerShell 工作流 Runbook，只显示基于 PowerShell 工作流的 Runbook。 |
| 资产 |自动化帐户中能够在 Runbook 中使用的[自动化资产](/previous-versions/azure/dn939988(v=azure.100))。 向 Runbook 添加资产会添加获取所选资产的工作流活动。 在使用变量资产的情况下，可以选择是否添加用于获取变量或设置变量的活动。 |
| Runbook 控件 |可以在当前 Runbook 中使用的控件活动。 “接合”活动需要使用多个输入，并等到所有输入都完成后再继续执行工作流。 “代码”活动运行一行或多行 PowerShell 或 PowerShell 工作流代码，具体视图形 Runbook 类型而定。 可以将此活动用于自定义代码或通过其他活动难以实现的功能。 |

### <a name="configuration-control"></a>配置控件

使用“配置”控件，可以提供在画布上选择的对象的详细信息。 此控件中的可用属性取决于所选对象的类型。 当你在“配置”控件中选择一个选项时，它会打开其他边栏选项卡来提供更多信息。

### <a name="test-control"></a>测试控件

首次启动图形编辑器时，不显示测试控件。 当你以交互方式测试图形 Runbook 时，它会打开。

## <a name="use-activities"></a>使用活动

活动是 Runbook 的构建基块。 活动可以是 PowerShell cmdlet、子 Runbook 或工作流。 通过在“库”控件中右键单击活动并选择“添加到画布”，可以向 Runbook 添加活动。 然后，可以通过单击拖放的方式将活动置于画布上喜欢的任何位置。 活动在画布上的位置不会影响 Runbook 的操作。 你可以用任何你认为最适合可视化 Runbook 操作的方式来对 Runbook 进行布局。

![添加到画布](media/automation-graphical-authoring-intro/add-to-canvas-cmdlet.png)

在画布上选择一个活动，以在“配置”边栏选项卡中配置它的属性和参数。 可以将活动的标签更改为更具描述性的名称。 Runbook 仍运行原始 cmdlet。 只需更改图形编辑器使用的显示名称。 请注意，标签在 Runbook 中必须是唯一的。

### <a name="parameter-sets"></a>参数集

参数集用于定义会接受特定 cmdlet 的值的必需参数和可选参数。 所有 cmdlet 都至少有一个参数集，有些有多个参数集。 如果 cmdlet 有多个参数集，必须先选择要使用的参数集，然后才能配置参数。 通过依次选择“参数集”和另一个参数集，可以更改活动使用的参数集。 在这种情况下，你已经配置的任何参数值都会丢失。

在下面的示例中，[Get-AzVM](/powershell/module/az.compute/get-azvm?view=azps-3.5.0&preserve-view=true) cmdlet 有三个参数集。 此示例使用一个名为 ListVirtualMachineInResourceGroupParamSet 的参数集（其中带有一个可选参数），用于返回资源组中的所有虚拟机。 此示例还使用 GetVirtualMachineInResourceGroupParamSet 参数集，用于指定要返回的虚拟机。 此参数集有两个强制参数和一个可选参数。

![参数集](media/automation-graphical-authoring-intro/get-azvm-parameter-sets.png)

#### <a name="parameter-values"></a>参数值

指定某个参数的值时，可以选择一个数据源，以便确定如何指定该值。 可用于特定参数的数据源取决于相应参数的有效值。 例如，对于不允许 Null 值的参数，Null 不会是可用选项。

| 数据源 | 说明 |
|:--- |:--- |
| 常量值 |键入参数的值。 此数据源只适用于以下数据类型：Int32、Int64、String、Boolean、DateTime、Switch。 |
| 活动输出 |使用工作流中当前活动之前的活动的输出。 将列出所有有效的活动。 对于参数值，只使用生成输出的活动。 如果活动输出的是包含多个属性的对象，可以在选择活动后键入特定属性的名称。 |
| Runbook 输入 |选择 Runbook 输入作为活动参数的输入。 |
| 变量资产 |选择自动化变量作为输入。 |
| 凭据资产 |选择自动化凭据作为输入。 |
| 证书资产 |选择自动化证书作为输入。 |
| 连接资产 |选择自动化连接作为输入。 |
| PowerShell 表达式 |指定简单的 [PowerShell 表达式](#work-with-powershell-expressions)。 表达式计算先于活动，且结果用于参数值。 可以使用变量来引用活动或 Runbook 输入参数的输出。 |
| 未配置 |清除以前配置的任何值。 |

#### <a name="optional-additional-parameters"></a>可选的其他参数

所有 cmdlet 都会有提供其他参数的选项。 这些是 PowerShell 通用参数或其他自定义参数。 图形编辑器提供了一个文本框，可以在其中使用 PowerShell 语法提供参数。 例如，若要使用 `Verbose` 通用参数，应指定 `-Verbose:$True`。

### <a name="retry-activity"></a>重试活动

活动的重试功能可以让活动运行多次，直到满足特定的条件，这很像循环。 可以将此功能用于以下活动：应该运行多次的活动、容易出错的活动、可能需要尝试多次才成功的活动，或测试活动输出信息中是否存在有效数据的活动。

对活动启用重试时，可以设置延迟和条件。 延迟是 Runbook 再次运行活动之前等待的时间（以秒或分钟为单位）。 如果没有指定延迟，则活动在完成后立即再次运行。

:::image type="content" source="media/automation-graphical-authoring-intro/retry-delay.png" alt-text="启用重试功能设置的屏幕截图。":::

重试条件是在每次活动运行后计算的 PowerShell 表达式。 如果表达式解析为 True，则活动再次运行。 如果表达式解析为 False，则活动不会再次运行，且 Runbook 转移到下一个活动。

:::image type="content" source="media/automation-graphical-authoring-intro/retry-condition.png" alt-text="启用重试功能设置的屏幕截图。":::

重试条件可以使用名为 `RetryData` 的变量，此变量提供对活动重试相关信息的访问权限。 此变量具有下表中的属性。

| properties | 说明 |
|:--- |:--- |
| `NumberOfAttempts` |活动已运行的次数。 |
| `Output` |活动上次运行的输出。 |
| `TotalDuration` |活动首次开始之后的经过时间。 |
| `StartedAt` |活动第一次开始时的时间（采用 UTC 格式）。 |

下面是活动重试条件示例。

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

配置活动的重试条件以后，该活动将包含两个用于提醒可视提示。 一个提示在活动中显示，另一个提示在你审阅活动配置时显示。

![活动重试可视指示符](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>工作流脚本控件

“工作流脚本”控件是一种特殊的活动，它接受 PowerShell 或 PowerShell 工作流脚本，具体视要创作的图形 Runbook 的类型而定。 此控件提供了其他方式无法提供的功能。 它不能接受参数，但可以使用针对活动输出和 Runbook 输入参数的变量。 此活动的任何输出都会添加到数据总线。 异常是没有传出链接的输出；在这种情况下，输出被添加到 Runbook 输出中。

例如，以下代码使用名为 `NumberOfDays` 的 Runbook 输入变量来执行日期计算。 然后，它将计算出的 DateTime 值作为输出发送给 Runbook 中的后续活动使用。

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="use-links-for-workflow"></a>对工作流使用链接

图形 Runbook 中的链接用于连接两个活动。 它作为箭头显示在画布上，从源活动指向目标活动。 活动按箭头的方向运行，源活动完成后才会开始目标活动。

### <a name="create-a-link"></a>创建链接

通过选择源活动并单击形状底部的圆圈，可以在两个活动之间创建链接。 将箭头拖到目标活动，然后放开。

![创建链接](media/automation-graphical-authoring-intro/create-link-options.png)

选择可在“配置”边栏选项卡中配置其属性的链接。 属性包括链接类型，下表对此进行了描述。

| 链接类型 | 说明 |
|:--- |:--- |
| 管道 |对于源活动的每个对象输出，目标活动运行一次。 如果源活动没有生成任何输出，目标活动将不会运行。 源活动的输出可用作对象。 |
| 序列 |目标活动只在收到源活动的输出时运行一次。 源活动的输出可用作对象数组。 |

### <a name="start-runbook-activity"></a>Runbook 启动活动

图形 Runbook 会通过任何没有传入链接的活动启动。 通常只有一个活动作为 Runbook 启动活动。 如果多个活动没有传入链接，Runbook 在启动时并行运行这些活动。 然后，它会在每个活动完成时，按链接来运行其他活动。

### <a name="specify-link-conditions"></a>指定链接条件

如果你指定链接条件，那么仅当条件解析为 True 时，目标活动才运行。 通常在条件中使用 `ActivityOutput` 变量来检索源活动的输出。

对于管道链接，必须为单个对象指定条件。 Runbook 对源活动输出的每个对象进行条件计算。 然后，它会为符合条件的每个对象运行目标活动。 例如，对于源活动 `Get-AzVM`，可以对条件管道链接使用以下语法，只检索名为 Group1 的资源组中的虚拟机。

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

对于序列链接，Runbook 只计算一次条件，因为会返回一个包含源活动中所有对象的数组。 因此，Runbook 不能像使用管道链接那样使用序列链接进行筛选。 序列链接可以直接确定是否运行下一个活动。

例如，以“启动 VM”Runbook 中的以下活动集为例：

![使用序列的条件链接](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Runbook 使用三个不同的序列链接来验证输入参数 `VMName` 和 `ResourceGroupName` 的值，以确定要执行的适当操作。 可能会执行的操作有启动一个 VM、启动资源组中的所有 VM 或启动订阅中的所有 VM。 对于 `Connect to Azure` 和 `Get single VM` 之间的序列链接，条件逻辑如下：

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

使用条件链接时，将通过条件来筛选该分支中从源活动到其他活动所提供的数据。 如果某个活动是多个链接的源，那么每个分支中的活动可用的数据取决于连接到相应分支的链接中的条件。

例如，下面 Runbook 中的 `Start-AzVM` 活动启动所有虚拟机。 它有两个条件链接。 第一个条件链接使用表达式 `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true` 来筛选 `Start-AzVM` 活动是否已成功完成。 第二个条件链接使用表达式 `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true` 来筛选 `Start-AzVm` 活动是否无法启动虚拟机。

![条件链接示例](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

任何遵循第一个链接并使用 `Get-AzureVM` 的活动输出的活动只检索在 `Get-AzureVM` 运行时启动的虚拟机。 任何遵循第二个链接的活动只获取在 `Get-AzureVM` 运行时停止的虚拟机。 任何按第三个链接进行的活动都会获取所有虚拟机，不管这些虚拟机的运行状态如何。

### <a name="use-junctions"></a>使用接合

一个交接点是一个特殊活动，它会一直等到所有传入分支完成为止。 这样，Runbook 可以并行运行多个活动，并确保在继续之前所有活动都已完成。

虽然接合可以有无限数量的传入链接，但这些链接中只有一个可以是管道。 传入序列链接的数目不受限制。 你可以创建包含多个传入管道链接的接合，并保存 Runbook，但它在运行时会失败。

下面的示例是某个 Runbook 的一部分，该 Runbook 可以启动一组虚拟机，同时还会下载要应用到这些虚拟机的修补程序。 它使用接合来确保两个进程在 Runbook 继续之前都已完成。

![交接点](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="work-with-cycles"></a>使用循环

如果目标活动链接回其源活动，或链接回另一个最终会链接回其源的活动，则形成循环。 图形创作目前不支持循环。 如果 Runbook 有一个周期，可以正常保存它，但在运行它时会收到一个错误。

![周期](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="share-data-between-activities"></a>在活动之间共享数据

活动使用传出链接输出的任何数据都被写入到 Runbook 的数据总线。 Runbook 中的任何活动都可以使用数据总线上的数据来填充参数值或添加脚本代码。 一个活动可以访问工作流中任何以前的活动的输出。

数据写入数据总线的方式取决于活动的链接的类型。 对于管道链接，数据以多个对象的形式输出。 对于序列链接，数据以数组的形式输出。 如果只有一个值，数据以单元素数组的形式输出。

Runbook 有两种方式来访问数据总线中的数据： 
* 使用活动输出数据源。
* 使用 PowerShell 表达式数据源。

第一种机制使用活动输出数据源来填充另一活动的参数。 如果输出是对象，Runbook 可以指定单个属性。

![活动输出](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

第二种数据访问机制使用 `ActivityOutput` 变量，检索 PowerShell 表达式数据源中活动的输出或工作流脚本活动的输出，语法如下所示。 如果输出是对象，Runbook 可以指定单个属性。

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="use-checkpoints"></a>使用检查点

通过在任何活动上选择“检查点 Runbook”，可以在图形 PowerShell 工作流 Runbook 中设置[检查点](automation-powershell-workflow.md#use-checkpoints-in-a-workflow)。 这会导致在运行活动之后设置检查点。

![检查点](media/automation-graphical-authoring-intro/set-checkpoint.png)

检查点只在图形 PowerShell 工作流 Runbook 中启用，在图形 Runbook 中不可用。 如果 Runbook 使用 Azure cmdlet，它应使用 `Connect-AzAccount` 活动遵循任何设置了检查点的活动。 连接操作用于以下情况：Runbook 已暂停，且必须从此检查点在另一个辅助角色上重启。

## <a name="handle-runbook-input"></a>处理 Runbook 输入

Runbook 需要输入，要么来自通过 Azure 门户启动 Runbook 的用户，要么来自另一个 Runbook（如果当前 Runbook 用作子 Runbook 的话）。 例如，对于创建虚拟机的 Runbook，用户可能需要在 Runbook 每次启动时都提供虚拟机名称和其他属性等信息。

Runbook 通过定义一个或多个输入参数来接受输入。 用户在 Runbook 每次启动时提供这些参数的值。 当用户使用 Azure 门户启动 Runbook 时，系统会提示用户为 Runbook 支持的每个输入参数提供值。

创作 Runbook 时，可以通过单击 Runbook 工具栏上的“输入和输出”来访问它的输入参数。 这会打开“输入和输出”控件，在其中可以编辑现有输入参数，也可以通过单击“添加输入”来新建输入参数。

![添加输入](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

按下表中的属性定义每个输入参数：

| properties | 说明 |
|:--- |:--- |
| 名称 | 必需。 参数的名称。 此名称在 Runbook 中必须是唯一的。 它必须以字母开头，且只能包含字母、数字和下划线字符。 此名称不得包含空格。 |
| 说明 |可选。 有关输入参数用途的说明。 |
| 类型 | 可选。 参数值应有的数据类型。 提示输入时，Azure 门户将针对每个参数的数据类型提供相应的控件。 支持的参数类型是 String、Int32、Int64、Decimal、Boolean、DateTime 和 Object。 如果没有选择数据类型，则默认为 String。|
| 必需 | 可选。 指定是否必须为参数提供值的设置。 如果选择 `yes`，则必须在 Runbook 启动时提供值。 如果选择 `no`，则不需要在 Runbook 启动时提供值，可以使用默认值。 如果不为没有定义默认值的每个强制参数提供值，Runbook 就无法启动。 |
| 默认值 | 可选。 用于在 Runbook 启动时没有值传入的参数的值。 若要设置默认值，请选择 `Custom`。 如果不想提供任何默认值，请选择 `None`。 |

## <a name="handle-runbook-output"></a>处理 Runbook 输出

图形创作将由任何没有传出链接的活动所创建的数据都保存到 [Runbook 输出](./automation-runbook-output-and-messages.md)。 输出将与 Runbook 作业一起保存，在该 Runbook 作为子 Runbook 使用的情况下，还可供父 Runbook 使用。

## <a name="work-with-powershell-expressions"></a>使用 PowerShell 表达式

图形创作的一个优点是，你可以在对 PowerShell 了解最少的情况下创建 Runbook。 但目前，确实需要了解一点 PowerShell，以便填充某些[参数值](#use-activities)和设置[链接条件](#use-links-for-workflow)。 此部分快速介绍了 PowerShell 表达式。 [使用 Windows PowerShell 编写脚本](/powershell/scripting/overview)中提供了 PowerShell 的完整详细信息。

### <a name="use-a-powershell-expression-as-a-data-source"></a>使用 PowerShell 表达式作为数据源

可以使用 PowerShell 表达式作为数据源，以使用 PowerShell 代码的结果来填充[活动参数](#use-activities)的值。 表达式可以是执行简单函数的一行代码，也可以是执行复杂逻辑的多行代码。 未分配给变量的任何命令输出都将输出到参数值。

例如，以下命令输出当前日期。

```powershell-interactive
Get-Date
```

下一个代码片段通过当前日期生成字符串，并将它分配给变量。 此代码将变量的内容发送到输出。

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

以下命令计算当前日期，并返回指明当天是工作日还是周末的字符串。

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="use-activity-output"></a>使用活动输出

若要在 Runbook 中使用上一个活动的输出，请通过以下语法使用 `ActivityOutput` 变量。

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

例如，可以有一个活动，它的属性需要使用虚拟机名称。 在这种情况下，Runbook 可以使用以下表达式。

```powershell-interactive
$ActivityOutput['Get-AzureVM'].Name
```

如果属性需要使用虚拟机对象（而不仅仅是名称），Runbook 使用以下语法返回整个对象。

```powershell-interactive
$ActivityOutput['Get-AzureVM']
```

Runbook 可以在更复杂的表达式中使用活动输出，如下所示。 此表达式将文本连接到虚拟机名称。

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVM'].Name
```

### <a name="compare-values"></a>比较值

使用[比较运算符](/powershell/module/microsoft.powershell.core/about/about_comparison_operators)来比较值或确定值是否与指定的模式匹配。 比较操作返回 True 或 False 值。

例如，以下条件确定活动 `Get-AzureVM` 中的虚拟机目前是否已停止。

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"
```

以下条件确定同一虚拟机是否处于除“已停止”以外的其他任何状态。

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"
```

可以使用[逻辑运算符](/powershell/module/microsoft.powershell.core/about/about_logical_operators)（如 `-and` 或 `-or`）在 Runbook 中联接多个条件。 例如，以下条件检查上一示例中的虚拟机是处于“已停止”状态，还是处于“正在停止”状态。

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")
```

### <a name="use-hashtables"></a>使用哈希表

[哈希表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)是名称/值对，对于返回一组值很有用。 你可能还会发现，哈希表有时亦称为“字典”。 某些活动的属性需要使用哈希表，而不是简单的值。

使用以下语法创建哈希表。 它可以包含任意数量的条目，但每个条目都由名称和值定义。

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

例如，以下表达式创建要用作活动参数的数据源的哈希表，此活动参数需要使用包含值的哈希表进行 Internet 搜索。

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

以下示例使用活动 `Get Twitter Connection` 的输出来填充哈希表。

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="authenticate-to-azure-resources"></a>访问 Azure 资源所需的身份验证

Azure 自动化中用于管理 Azure 资源的 Runbook 将需要通过 Azure 进行身份验证。 [运行方式帐户](./manage-runas-account.md)（亦称为“服务主体”）是自动化 Runbook 用于访问订阅中的 Azure 资源管理器资源的默认机制。 通过向画布添加使用 PowerShell [Get-AutomationConnection](/system-center/sma/manage-global-assets) cmdlet 的 `AzureRunAsConnection` 连接资产，可以将此功能添加到图形 Runbook。 还可以添加 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet。 以下示例展示了此方案。

![运行方式身份验证活动](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

`Get Run As Connection` 活动（或 `Get-AutomationConnection`）配置了名为 `AzureRunAsConnection` 的常量值数据源。

![运行方式连接配置](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

下一个活动（即 `Connect-AzAccount`）添加经过身份验证的运行方式帐户，以供在 Runbook 中使用。

![Connect-AzAccount 参数集](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

>[!NOTE]
>对于 PowerShell Runbook，`Add-AzAccount` 和 `Add-AzureRMAccount` 是 `Connect-AzAccount` 的别名。 请注意，这些别名不适用于图形 Runbook。 图形 Runbook 只能使用 `Connect-AzAccount` 本身。

对于参数字段 APPLICATIONID、CERTIFICATETHUMBPRINT 和 TENANTID，指定字段路径的属性名，因为活动输出的是包含多个属性的对象。 否则，当 Runbook 执行时，它会在尝试进行身份验证时失败。 这是你在通过运行方式帐户对 Runbook 进行身份验证时必须满足的最低要求。

一些订阅者使用 [Azure AD 用户帐户](./shared-resources/credentials.md)创建自动化帐户，用于管理 Azure 经典部署，或用于 Azure 资源管理器资源。 为了保持这些订阅者的向后兼容性，Runbook 中使用的身份验证机制是包含[凭据资产](./shared-resources/credentials.md)的 `Add-AzureAccount` cmdlet。 资产表示有权访问 Azure 帐户的 Active Directory 用户。

可以为图形 Runbook 启用此功能，具体方法是将凭据资产添加到画布中，后加使用凭据资产作为输入的 `Add-AzureAccount` 活动。 请参阅以下示例。

![身份验证活动](media/automation-graphical-authoring-intro/authentication-activities.png)

Runbook 必须在启动时和每个检查点之后进行身份验证。 因此，必须在任何 `Checkpoint-Workflow` 活动之后使用 `Add-AzureAccount` 活动。 不需要使用额外的凭据活动。

![活动输出](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="export-a-graphical-runbook"></a>导出图形 Runbook

可以只导出图形 Runbook 的已发布版本。 如果 Runbook 还没有发布，“导出”按钮处于禁用状态。 在你单击“导出”按钮后，Runbook 下载到本地计算机。 文件名与扩展名为 .graphrunbook 的 Runbook 的名称一致。

## <a name="import-a-graphical-runbook"></a>导入图形 Runbook

通过在添加 Runbook 时选择“导入”选项，可以导入图形 Runbook 或图形 PowerShell 工作流 Runbook 文件。 选择要导入的文件时，可以保留相同的名称，也可以提供新名称。 “Runbook 类型”字段在评估所选文件后显示 Runbook 的类型。 如果你尝试选择不正确的其他类型，图形编辑器会显示一条消息，指明存在潜在冲突，并且在转换过程中可能会出现语法错误。

![导入 Runbook](media/automation-graphical-authoring-intro/runbook-import.png)

## <a name="test-a-graphical-runbook"></a>测试图形 Runbook

Azure 自动化中的每个图形 Runbook 都有草稿版和发布版。 只能运行发布版，同时只能编辑草稿版。 已发布版不受对草稿版所做的任何更改的影响。 当草稿版可以使用时，发布它，这样草稿版就会覆盖当前的发布版。

可以在 Azure 门户中测试草稿版 Runbook，同时保持发布版 Runbook 不变。 或者，也可以在发布新的 Runbook 之前测试它，这样可以在替换任何版本之前验证 Runbook 是否正常运行。 Runbook 测试执行的是草稿版，并确保它执行的所有操作都已完成。 不会创建作业历史记录，但“测试输出”窗格中会显示输出。

打开要编辑的 Runbook，然后单击“测试窗格”，以打开图形 Runbook 的“测试”控件。 “测试”控件会提示提供输入参数，可以通过单击“启动”来启动 Runbook。

## <a name="publish-a-graphical-runbook"></a>发布图形 Runbook

打开要编辑的 Runbook，然后单击“发布”，以发布图形 Runbook。 可能的 Runbook 状态包括：

* 新 - Runbook 尚未发布。 
* 已发布 - Runbook 已发布。
* 正在编辑 - Runbook 在发布后进行了编辑，草稿版和发布版是不同的。

![Runbook 状态](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

可以视需要选择还原到发布版 Runbook。 此操作会丢弃自上次发布 Runbook 以来所做的任何更改。 它将草稿版 Runbook 替换为发布版。

## <a name="next-steps"></a>后续步骤

* 若要开始使用图形 Runbook，请参阅[教程：创建图形 Runbook](learn/automation-tutorial-runbook-graphical.md)。
* 若要了解有关 Runbook 类型、其优点和限制的详细信息，请参阅 [Azure 自动化 Runbook 类型](automation-runbook-types.md)。
* 若要了解如何使用自动化运行方式帐户进行身份验证，请参阅[运行方式帐户](automation-security-overview.md#run-as-account)。
* 有关 PowerShell cmdlet 参考，请参阅 [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0&preserve-view=true#automation)。
