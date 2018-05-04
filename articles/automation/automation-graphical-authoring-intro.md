---
title: Azure 自动化中的图形创作
description: 图形创作可以让你在不使用代码的情况下，为 Azure 自动化创建 Runbook。 本文介绍了图形创作以及开始创建图形 Runbook 所需的所有详细信息。
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 42187def32ea0f0605b50e0e25b7f78ebf0c10ac
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="graphical-authoring-in-azure-automation"></a>Azure 自动化中的图形创作
## <a name="introduction"></a>介绍
图形创作可以用来为 Azure 自动化创建 Runbook，不需要编写复杂的基础性的 Windows PowerShell 或 PowerShell 工作流代码。 可以将活动从 cmdlet 和 Runbook 的库添加到画布，将它们链接到一起，并通过配置来形成工作流。 如果曾经使用过 System Center Orchestrator 或 Service Management Automation (SMA)，则此操作对你来说应该很熟悉。

本文提供了图形创作简介，以及着手创建图形 Runbook 所需的概念。

## <a name="graphical-runbooks"></a>图形 Runbook
Azure 自动化中的所有 Runbook 都是 Windows PowerShell 工作流。 图形 Runbook 和图形 PowerShell 工作流 Runbook 会生成由自动化辅助角色运行的 PowerShell 代码，但你无法看到它，也不能直接修改它。 图形 Runbook 可以转换为图形 PowerShell 工作流 Runbook，反之亦然，但它们不能转换为文本 Runbook。 不能将现有文本 Runbook 导入图形编辑器。

## <a name="overview-of-graphical-editor"></a>图形编辑器概述
通过创建或编辑图形 Runbook，可以在 Azure 门户中打开图形编辑器。

![图形工作区](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

以下各节介绍图形编辑器中的控件。

### <a name="canvas"></a>画布
画布是设计 Runbook 的地方。 可以将库控件中节点的活动添加到 Runbook，并将其通过链接进行连接，以便定义 Runbook 的逻辑。

可以使用画布底部的控件来放大和缩小。

### <a name="library-control"></a>库控件
库控件是你选择要添加到 Runbook 的[活动](#activities)的地方。 可以将活动添加到画布，再将它们连接到其他活动。 它包括下表中描述的四个部分：

| 部分 | 说明 |
|:--- |:--- |
| Cmdlet |包括可以在 Runbook 中使用的所有 cmdlet。 Cmdlet 按模块组织。 所有安装在自动化帐户中的模块都可用。 |
| Runbook |包括你自动化帐户中的 Runbook。 这些 Runbook 可以添加到画布中用作子 Runbook。 仅显示核心类型与所编辑 Runbook 相同的 Runbook；对于图形 Runbook，仅显示基于 PowerShell 的 Runbook，而对于图形 PowerShell 工作流 Runbook，则仅显示基于 PowerShell 工作流的 Runbook。 |
| 资产 |包括自动化帐户中能够在 Runbook 中使用的[自动化资产](http://msdn.microsoft.com/library/dn939988.aspx)。 将资产添加到 Runbook 中时，它会添加一个可以获取所选资产的工作流活动。 在使用变量资产的情况下，可以选择是否添加用于获取变量或设置变量的活动。 |
| Runbook 控件 |包括可以在当前 Runbook 中使用的 Runbook 控件活动。 *交接点*采用多个输入，并会等到所有输入完成后才会继续执行工作流。 *Code* 活动运行一行或多行 PowerShell 或 PowerShell 工作流代码，具体取决于图形 Runbook 类型。 可以将此活动用于自定义代码或通过其他活动难以实现的功能。 |

### <a name="configuration-control"></a>配置控件
可以在配置控件中提供在画布上选择的对象的详细信息。 此控件中的可用属性将取决于所选对象的类型。 在配置控件中选择一个选项时，它会打开用于提供额外信息的其他边栏选项卡。

### <a name="test-control"></a>测试控件
首次启动图形编辑器时，不显示测试控件。 它会在以交互方式[测试图形 Runbook](#graphical-runbook-procedures) 时打开。

## <a name="graphical-runbook-procedures"></a>图形 Runbook 过程
### <a name="exporting-and-importing-a-graphical-runbook"></a>导出和导入图形 Runbook
可以只导出图形 Runbook 的已发布版本。 如果尚未发布 Runbook，则将禁用“导出”按钮。 单击“导出”按钮时，Runbook 将下载到本地计算机。 文件名与带有 *graphrunbook* 扩展名的 Runbook 名称匹配。

可以在添加 Runbook 时选择“导入”选项，将图形 Runbook 或图形 PowerShell 工作流 Runbook 文件导入。 选择要导入的文件时，可以保留同一个**名称**，或提供一个新名称。 “Runbook 类型”字段会在评估所选文件后显示 Runbook 的类型。如果尝试选择其他不正确的类型，则会显示一条消息，告知你可能存在冲突，并且可能会在转换过程中出现语法错误。

![导入 Runbook](media/automation-graphical-authoring-intro/runbook-import-revised20165.png)

### <a name="testing-a-graphical-runbook"></a>测试图形 Runbook
可以在 Azure 门户中测试 Runbook 的草稿版，对 Runbook 的已发布版不做任何更改，也可以在新的 Runbook 发布前对其进行测试。 这样便可以确保新的 Runbook 在替代已发布版之前各项功能运行正常。 测试 Runbook 时，将执行草稿版 Runbook，并会完成其所执行的任何操作。 不会创建作业历史记录，但会在“测试输出窗格”中显示输出。

要打开 Runbook 的测试控件，可先打开要编辑的该 Runbook，然后单击“测试窗格”按钮。

测试控件会提示输入参数，可以通过单击“启动”按钮来启动 Runbook。

### <a name="publishing-a-graphical-runbook"></a>发布图形 Runbook
Azure 自动化中的每个 Runbook 都有草稿版和已发布版。 只有已发布版才能用来运行，只有草稿版才能用来编辑。 已发布版不受对草稿版所做的任何更改的影响。 当草稿版可以使用时，可以发布草稿版，这样草稿版就会覆盖已发布版。

发布图形 Runbook 时，可以先打开要编辑的 Runbook，然后单击“发布”按钮即可。

当 Runbook 尚未发布时，其状态为“新建”。 当 Runbook 发布后，其状态为“已发布”。 如果对已发布的 Runbook 进行编辑，而草稿版不同于已发布版，则 Runbook 的状态为“正在编辑”。

![Runbook 状态](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

还可以选择还原到 Runbook 的已发布版。 这会抛弃自上次发布 Runbook 以来所做的任何更改，并会将 Runbook 的草稿版替换为已发布版。

## <a name="activities"></a>活动
活动是 Runbook 的构建基块。 活动可以是一个 PowerShell cmdlet，一个子 Runbook，也可以是一个工作流活动。 将活动添加到 Runbook 时，可以右键单击库控件中的该活动，并选择“添加到画布”。 然后，可以通过单击拖放的方式将活动置于画布上喜欢的任何位置。 活动在画布上的位置不会以任何方式影响 Runbook 的运行。 可以对 Runbook 进行任何形式的布局，只要觉得该布局最适合实现 Runbook 操作的可视化即可。

![添加到画布](media/automation-graphical-authoring-intro/add-to-canvas-revised20165.png)

在画布上选择需要在“配置”边栏选项卡中配置其属性和参数的活动。 可以将活动的“标签”更改成觉得更具描述性的内容。 原始 cmdlet 仍在运行，只是更改其要在图形编辑器中使用的显示名称。 该标签在 Runbook 中必须是唯一的。

### <a name="parameter-sets"></a>参数集
参数集用于定义会接受特定 cmdlet 的值的必需参数和可选参数。 所有 cmdlet 都有至少一个参数集，一些 cmdlet 会有多个参数集。 如果某个 cmdlet 有多个参数集，则必须选择要使用的那个参数集，才能配置参数。 能够配置的参数将取决于所选择的参数集。 可以更改某个活动使用的参数集，只需选中“参数集”，然后选择另一参数集即可。 在这种情况下，配置的任何参数值都将丢失。

在下面的示例中，Get-AzureRmVM cmdlet 有三个参数集。 在选择其中一个参数集之前，将无法配置参数值。 ListVirtualMachineInResourceGroupParamSet 参数集用于返回资源组中的所有虚拟机，有一个可选参数。 **GetVirtualMachineInResourceGroupParamSet** 用于指定要返回的虚拟机，有两个必需参数和一个可选参数。

![参数集](media/automation-graphical-authoring-intro/get-azurermvm-parameter-sets.png)

#### <a name="parameter-values"></a>参数值
指定某个参数的值时，可以选择一个数据源，以便确定如何指定该值。 可用于特定参数的数据源将取决于该参数的有效值。 例如，对于不允许 Null 值的参数，Null 不会是可用选项。

| 数据源 | 说明 |
|:--- |:--- |
| 常量值 |键入参数的值。 这仅适用于以下数据类型：Int32、Int64、字符串、布尔值、DateTime、开关。 |
| 活动输出 |工作流中某个位于当前活动前面的活动的输出。 将列出所有有效的活动。 只选择要将其输出用于参数值的活动。 如果该活动输出的对象具有多个属性，可以在选择活动之后键入属性的名称。 |
| Runbook 输入 |选择一个 Runbook 输入参数作为活动参数的输入。 |
| 变量资产 |选择一个自动化变量作为输入。 |
| 凭据资产 |选择一个自动化凭据作为输入。 |
| 证书资产 |选择一个自动化证书作为输入。 |
| 连接资产 |选择一个自动化连接作为输入。 |
| PowerShell 表达式 |指定简单的 [PowerShell 表达式](#powershell-expressions)。 对表达式的计算将先于用于参数值的活动和结果之前。 可以使用变量来引用活动或 Runbook 输入参数的输出。 |
| 未配置 |清除以前配置的任何值。 |

#### <a name="optional-additional-parameters"></a>可选的其他参数
所有 cmdlet 都会有提供其他参数的选项。 这些是 PowerShell 通用参数或其他自定义参数。 系统会显示一个文本框，可以在其中使用 PowerShell 语法提供参数。 例如，要使用 **Verbose** 通用参数，可以指定 **"-Verbose:$True"**。

### <a name="retry-activity"></a>重试活动
**重试行为**可让活动运行多次，直到满足特定的条件为止，很像一个循环。 可以针对应该运行多次的活动或者容易出错和可能需要尝试一次以上才成功的活动使用此功能，或者测试活动的输出信息中是否存在有效数据。

对活动启用重试时，可以设置延迟和条件。 延迟是 Runbook 再次运行活动之前等待的时间（以秒或分钟为单位）。 如果未指定延迟，则活动在完成之后立即再次运行。

![活动重试延迟](media/automation-graphical-authoring-intro/retry-delay.png)

重试条件每次活动运行之后评估的 PowerShell 表达式。 如果表达式求值为 True，则活动再次运行。 如果表达式求值为 False，则活动不再次运行，且 Runbook 转到下一个活动。

![活动重试延迟](media/automation-graphical-authoring-intro/retry-condition.png)

重试条件可以使用名为 $RetryData 的变量提供活动重试相关信息的访问权限。 此变量具有下表中的属性。

| 属性 | 说明 |
|:--- |:--- |
| NumberOfAttempts |活动已运行的次数。 |
| 输出 |活动上次运行的输出。 |
| TotalDuration |活动首次开始之后的经过时间。 |
| StartedAt |活动首次开始的时间（UTC 格式）。 |

下面是活动重试条件的示例。

    # Run the activity exactly 10 times.
    $RetryData.NumberOfAttempts -ge 10

    # Run the activity repeatedly until it produces any output.
    $RetryData.Output.Count -ge 1

    # Run the activity repeatedly until 2 minutes has elapsed.
    $RetryData.TotalDuration.TotalMinutes -ge 2

配置活动的重试条件以后，该活动将包含两个用于提醒可视提示。 一个提示出现在活动中，另一个提示在查看活动的配置时出现。

![活动重试可视指示符](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>工作流脚本控件
代码控件是一种特殊的活动，该活动根据图形 Runbook 的类型来接受 PowerShell 或 PowerShell 工作流脚本，而图形 Runbook 在经过授权后可以提供不授权就无法提供的功能。 它不能接受参数，但可以使用针对活动输出和 Runbook 输入参数的变量。 活动的任何输出都将添加到数据总线中，除非它没有传出链接，这种情况下，会将它添加到 Runbook 的输出中。

例如，以下代码使用称为 $NumberOfDays 的 Runbook 输入变量执行日期计算。 然后，它会将计算所得的日期时间作为输出发送，供 Runbook 中的后续活动使用。

    $DateTimeNow = (Get-Date).ToUniversalTime()
    $DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
    $DateTimeStart


## <a name="links-and-workflow"></a>链接和工作流
图形 Runbook 中的**链接**用于连接两个活动。 它作为箭头显示在画布上，从源活动指向目标活动。 活动按箭头的方向运行，源活动完成后才会开始目标活动。

### <a name="create-a-link"></a>创建链接
在两个活动之间创建链接，方法是：先选择源活动，然后单击图形底部的圆圈。 将箭头拖到目标活动，然后放开。

![创建链接](media/automation-graphical-authoring-intro/create-link-revised20165.png)

选择可在“配置”边栏选项卡中配置其属性的链接。 这种情况下，会包括下表中描述的链接类型。

| 链接类型 | 说明 |
|:--- |:--- |
| 管道 |对于源活动中的每个对象输出，目标活动都将运行一次。 如果源活动没有生成任何输出，目标活动将不会运行。 源活动的输出可用作对象。 |
| 序列 |目标活动只运行一次。 它会接收来自源活动的对象数组。 源活动的输出可用作对象数组。 |

### <a name="starting-activity"></a>启动活动
图形 Runbook 会通过任何没有传入链接的活动启动。 这通常只是一个可充当 Runbook 启动活动的活动。 如果多个活动没有传入链接，则 Runbook 在启动时，将并行运行这些活动。 然后，它会在每个活动完成时，按链接来运行其他活动。

### <a name="conditions"></a>条件
指定链接的条件时，目标活动仅在该条件解析为 true 的情况下运行。 通常会在条件中使用 $ActivityOutput 变量来检索源活动的输出。

对于管道链接，可以为一个对象指定一个条件，并针对源活动的每个对象输出对条件进行评估。 然后会针对符合条件的每个对象运行目标活动。 例如，通过 Get-AzureRmVm 源活动，可以将以下语法用于条件管道链接，以便只检索名为 *Group1* 的资源组中的虚拟机。

    $ActivityOutput['Get Azure VMs'].Name -match "Group1"

对于序列链接，只会对条件评估一次，因为会返回单个包含源活动中所有对象输出的数组。 因此，不能使用序列链接来进行管道链接这样的筛选，但可以通过该链接来直接确定是否会运行下一活动。 以下面的活动集（位于 Start VM Runbook 中）为例。<br> ![使用序列的条件链接](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)<br>
共有三个不同的序列链接，用于确保已将值提供给两个代表 VM 名称和资源组名称的 Runbook 输入参数，以便确定所要采取的适当操作：启动单个 VM、启动资源组中的所有 VM，或者启动订阅中的所有 VM。 对于“连接到 Azure”和“获取单个 VM”之间的序列链接，下面是条件逻辑：

    <#
    Both VMName and ResourceGroupName runbook input parameters have values
    #>
    (
    (($VMName -ne $null) -and ($VMName.Length -gt 0))
    ) -and (
    (($ResourceGroupName -ne $null) -and ($ResourceGroupName.Length -gt 0))
    )

使用条件链接时，将通过条件来筛选该分支中从源活动到其他活动所提供的数据。 如果某个活动是多个链接的源，则每个分支中可供活动使用的数据将取决于连接到该分支的链接中的条件。

例如，下面的 Runbook 中的 **Start-AzureRmVm** 活动可启动所有虚拟机。 它有两个条件链接。 第一个条件链接使用表达式 *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -eq $true* 来筛选 Start-AzureRmVm 活动已成功完成的情况。 第二个条件链接使用表达式 *$ActivityOutput['Start-AzureRmVM'].IsSuccessStatusCode -ne $true* 来筛选 Start-AzureRmVm 活动无法启动虚拟机的情况。

![条件链接示例](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

任何按第一个链接进行并使用 Get-AzureVM 提供的活动输出的活动都只会获取在 Get-AzureVM 运行时已启动的虚拟机。 任何按第二个链接进行的活动都只会获取在 Get-AzureVM 运行时已停止的虚拟机。 任何按第三个链接进行的活动都会获取所有虚拟机，不管这些虚拟机的运行状态如何。

### <a name="junctions"></a>交接点
一个交接点是一个特殊活动，它会一直等到所有传入分支完成为止。 这可以让你并行运行多个活动，并确保在所有活动完成后再继续。

尽管一个接合点可以有无限数量的传入链接，但这些链接中可以成为管道的链接不能超过一个。 传入序列链接的数目不受限制。 系统允许创建具有多个传入管道链接的结合点并保存 Runbook，但在运行时会失败。

下面的示例是某个 Runbook 的一部分，该 Runbook 可以启动一组虚拟机，同时还会下载要应用到这些虚拟机的修补程序。 使用接合点是为了确保在 Runbook 继续之前两个进程都已完成。

![交接点](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="cycles"></a>周期
一个周期是指目标活动需要多长时间才能通过链接回到其源活动，或者回到最终会通过链接回到其源活动的其他活动。 目前不允许使用周期来进行图形创作。 如果 Runbook 有一个周期，可以正常保存它，但在运行它时会收到一个错误。

![周期](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="sharing-data-between-activities"></a>在活动之间共享数据
由活动通过传出链接输出的任何数据都会写入 Runbook 的*数据总线*。 Runbook 中的任何活动都可以使用数据总线上的数据来填充参数值或添加脚本代码。 一个活动可以访问工作流中任何以前的活动的输出。

数据写入数据总线的方式取决于活动的链接的类型。 就**管道**来说，数据是作为多个对象输出的。 就**序列**链接来说，数据是以数组形式输出的。 如果只有一个值，数据将作为单个元素数组输出。

可以使用两种方法之一访问数据总线上的数据。 第一种方法是使用**活动输出**数据源来填充另一活动的参数。 如果输出是一个对象，可以指定单个属性。

![活动输出](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

还可以在 **PowerShell 表达式**数据源中检索活动的输出，或者使用 ActivityOutput 变量通过**工作流脚本**活动这样做。 如果输出是一个对象，可以指定单个属性。 ActivityOutput 变量使用以下语法。

    $ActivityOutput['Activity Label']
    $ActivityOutput['Activity Label'].PropertyName

### <a name="checkpoints"></a>检查点
可以通过在任何活动上选择*检查点 Runbook*，在图形 PowerShell 工作流 Runbook 中设置[检查点](automation-powershell-workflow.md#checkpoints)。 这会导致在运行活动之后设置检查点。

![检查点](media/automation-graphical-authoring-intro/set-checkpoint.png)

检查点仅在图形 PowerShell 工作流 Runbook 中启用，在图形 Runbook 中不可用。 如果 Runbook 使用 Azure cmdlet，当 Runbook 暂停并且在不同的辅助角色上从此检查点重新开始时，应使用 Connect-AzureRmAccount 遵循任何检查点活动。

## <a name="authenticating-to-azure-resources"></a>通过 Azure 资源进行身份验证
Azure 自动化中用于管理 Azure 资源的 Runbook 将需要通过 Azure 进行身份验证。 [运行方式帐户](automation-offering-get-started.md#creating-an-automation-account)（也称为服务主体）是在订阅中使用自动化 runbook 来访问 Azure 资源管理器资源的默认方法。 可以将此功能添加到图形 Runbook，只需向画布添加 AzureRunAsConnection 连接资产即可，该资产使用 PowerShell [Get-AutomationConnection](https://technet.microsoft.com/library/dn919922%28v=sc.16%29.aspx) cmdlet 和 [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) cmdlet。 以下示例对此进行了演示：<br>![运行方式身份验证活动](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)<br>
“获取运行方式连接”活动（即 Get-AutomationConnection）配置了名为 AzureRunAsConnection 的常量值数据源。<br>![运行方式连接配置](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)<br>
下一个活动（即 Connect-AzureRmAccount）添加经过身份验证的可在 Runbook 中使用的运行方式帐户。<br>
![Connect-AzureRmAccount 参数集](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)<br>
对于参数 **APPLICATIONID**、**CERTIFICATETHUMBPRINT** 和 **TENANTID**，需指定字段路径的属性的名称，因为该活动所输出的对象具有多个属性。 否则，执行 Runbook 时，将无法进行身份验证。 这是你在通过运行方式帐户对 Runbook 进行身份验证时必须满足的最低要求。

为了确保后向兼容性，以便使用 [Azure AD 用户帐户](automation-create-aduser-account.md)创建了自动化帐户的订户能够管理 Azure 经典部署或 Azure 资源管理器资源，身份验证的方法是带有[凭据资产](automation-credentials.md)的 Add-AzureAccount cmdlet，该资产代表具有 Azure 帐户访问权限的 Active Directory 用户。

可以将此功能添加到图形 Runbook，只需将凭据资产添加到画布，并完成 Add-AzureAccount 活动即可。 Add-AzureAccount 使用凭据活动作为其输入。 以下示例对此进行了演示：

![身份验证活动](media/automation-graphical-authoring-intro/authentication-activities.png)

必须在 Runbook 启动时以及每个检查点之后进行身份验证。 这意味着在每个 Checkpoint-Workflow 活动之后添加一个表示添加凭据的 Add-AzureAccount 活动。 不需要添加凭据活动，因为可以使用相同的

![活动输出](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="runbook-input-and-output"></a>Runbook 输入和输出
### <a name="runbook-input"></a>Runbook 输入
Runbook 可能会要求用户提供输入（如果该用户是通过 Azure 门户启动的该 Runbook），或者会要求另一 Runbook 提供输入（如果当前的 Runbook 是小孩在用）。
例如，如果使用 Runbook 创建虚拟机，则每次启动 Runbook 时，可能需要提供虚拟机名称、其他属性等信息。

可以通过定义一个或多个输入参数来接受 Runbook 的输入。 每次启动 Runbook 时，都需要为这些参数提供值。 通过 Azure 门户启动 Runbook 时，该门户会提示为每个 Runbook 的输入参数提供值。

可以通过单击 Runbook 工具栏上的“输入和输出”按钮来访问 Runbook 的输入参数。

这种情况下将打开**输入和输出**控件，可以通过单击“添加输入”在该控件中编辑现有输入参数或创建新的参数。

![添加输入](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

按下表中的属性定义每个输入参数：

| 属性 | 说明 |
|:--- |:--- |
| 名称 |参数的唯一名称。 此项只能包含字母数字字符，不能包含空格。 |
| 说明 |针对输入参数的可选说明。 |
| Type |参数值应有的数据类型。 提示输入时，Azure 门户将针对每个参数的数据类型提供相应的控件。 |
| 必需 |指定是否必须为该参数提供值。 如果没有为每个没有定义默认值的必需参数提供值，将无法启动 Runbook。 |
| 默认值 |指定在未提供值的情况下，对参数使用什么值。 此项可以为 Null 或特定值。 |

### <a name="runbook-output"></a>Runbook 输出
由任何没有传出链接的活动创建的数据将添加到 [Runbook 的输出](http://msdn.microsoft.com/library/azure/dn879148.aspx)。 输出将与 Runbook 作业一起保存，在该 Runbook 作为子 Runbook 使用的情况下，还可供父 Runbook 使用。

## <a name="powershell-expressions"></a>PowerShell 表达式
图形编写的优点之一是提供以 PowerShell 的基本知识创建 Runbook 的能力。 目前，确实需要熟悉一下 PowerShell 才能填充某些[参数值](#activities)和设置[链接条件](#links-and-workflow)。 本部分提供 PowerShell 表达式的快速简介供不熟悉的用户参考。 [使用 Windows PowerShell 编写脚本](http://technet.microsoft.com/library/bb978526.aspx)中提供了 PowerShell 的完整详细信息。

### <a name="powershell-expression-data-source"></a>PowerShell 表达式数据源
可以使用 PowerShell 表达式作为数据源，使用一些 PowerShell 代码的结果来填充[活动参数](#activities)的值。 这可以是执行某个简单函数的单行代码，或执行某个复杂逻辑的多行代码。 未分配给变量的任何命令输出都将输出到参数值。

例如，以下命令将输出当前日期。

    Get-Date

以下命令将从当前日期创建字符串并将它分配给变量。 然后将变量的内容发送到输出

    $string = "The current date is " + (Get-Date)
    $string

以下命令计算当前日期并返回表示当天是工作日还是周末的字符串。

    $date = Get-Date
    if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
    else { "Weekday" }


### <a name="activity-output"></a>活动输出
若要在 Runbook 中使用上一个活动的输出，请按以下语法使用 $ActivityOutput 变量。

    $ActivityOutput['Activity Label'].PropertyName

例如，可能某个活动具有需要虚拟机名称的属性，在此情况下可以使用以下表达式：

    $ActivityOutput['Get-AzureVm'].Name

如果是需要虚拟机对象的属性而不只是单纯的属性，则要使用以下语法返回整个对象。

    $ActivityOutput['Get-AzureVm']

也可以在更复杂的表达式中使用活动的输出，例如将文本串联到虚拟机名称的以下表达式。

    "The computer name is " + $ActivityOutput['Get-AzureVm'].Name


### <a name="conditions"></a>条件
使用[比较运算符](https://technet.microsoft.com/library/hh847759.aspx)来比较值或确定值是否与指定的模式匹配。 比较将返回 $true 或 $false 值。

例如，以下条件用于确定名为 *Get-AzureVM* 的活动中的虚拟机目前是否处于“已停止”状态。

    $ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"

以下条件检查同一虚拟机是否处于“已停止”以外的任何状态。

    $ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"

可以使用[逻辑运算符](https://technet.microsoft.com/library/hh847789.aspx)（例如 **-and** 或 **-or**）添加多个条件。 例如，以下条件会检查上述示例中同一虚拟机的状态是“已停止”还是“正在停止”。

    ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping")


### <a name="hashtables"></a>哈希表
[哈希表](http://technet.microsoft.com/library/hh847780.aspx)是返回一组值时很有用的名称/值对。 某些活动的属性可能是哈希表而不是简单值。 哈希表有时也称为字典。

使用以下语法创建哈希表。 哈希表可以包含任意数目的条目，但每个条目由一个名称和值定义。

    @{ <name> = <value>; [<name> = <value> ] ...}

例如，以下表达式创建要在活动参数的数据源中使用的哈希表，此哈希表的值用于 Internet 搜索。

    $query = "Azure Automation"
    $count = 10
    $h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
    $h

以下示例使用名为 *Get Twitter Connection* 的活动的输出来填充哈希表。

    @{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
      'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
      'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
      'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}



## <a name="next-steps"></a>后续步骤
* 若要开始使用 PowerShell 工作流 Runbook，请参阅 [我的第一个 PowerShell 工作流 Runbook](automation-first-runbook-textual.md)
* 若要开始使用图形 Runbook，请参阅 [我的第一个图形 Runbook](automation-first-runbook-graphical.md)
* 若要了解有关 Runbook 类型、其优点和限制的详细信息，请参阅 [Azure 自动化 Runbook 类型](automation-runbook-types.md)
* 若要了解如何使用自动化运行方式帐户进行身份验证，请参阅[配置 Azure 运行方式帐户](automation-sec-configure-azure-runas-account.md)

