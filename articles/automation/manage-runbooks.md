---
title: 在 Azure 自动化中管理 Runbook
description: 本文介绍如何在 Azure 自动化中管理 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: ec53c4b2f80fb095f58bee9c15ac5daafb8d59ef
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2020
ms.locfileid: "78226257"
---
# <a name="manage-runbooks-in-azure-automation"></a>在 Azure 自动化中管理 Runbook

可以通过[创建新](#create-a-runbook)的 runbook，或从文件或[runbook 库](automation-runbook-gallery.md)[导入现有](#import-a-runbook)Runbook，将 runbook 添加到 Azure 自动化。 本文介绍如何通过文件创建和导入 Runbook。 可以在[Azure 自动化的 Runbook 和模块库](automation-runbook-gallery.md)中获取访问社区 runbook 和模块的所有详细信息。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az module 安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于你的自动化帐户，可使用[如何在 Azure 自动化中更新 Azure PowerShell 模块](automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="create-a-runbook"></a>创建 runbook

可以使用其中一个 Azure 门户或 Windows PowerShell 在 Azure 自动化中创建一个新的 Runbook。 一旦创建了 Runbook，便可以利用[了解 PowerShell 工作流](automation-powershell-workflow.md)和 [Azure 自动化中的图形创作](automation-graphical-authoring-intro.md)中的信息对其进行编辑。

### <a name="create-a-runbook-in-the-azure-portal"></a>在 Azure 门户中创建 Runbook

1. 在 Azure 门户中，打开自动化帐户。
2. 从中心选择 "**进程自动化**" 下的 " **runbook** "，打开 runbook 的列表。
3. 单击 "**创建 runbook**"。
4. 输入 runbook 的名称，并选择其[类型](automation-runbook-types.md)。 Runbook 名称必须以字母开头，并且可以包含字母、数字、下划线和短划线。
5. 单击“创建”以创建 Runbook 并打开编辑器。

### <a name="create-a-runbook-with-powershell"></a>通过 PowerShell 创建 Runbook

可以使用[AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationrunbook?view=azps-3.5.0) cmdlet 创建空的[PowerShell 工作流 runbook](automation-runbook-types.md#powershell-workflow-runbooks)。 使用*Type*参数指定为**AzAutomationRunbook**定义的 runbook 类型之一。

下面的示例演示如何创建新的空 runbook。

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>导入 Runbook

可以通过导入 PowerShell 脚本或 PowerShell 工作流（**ps1**）、导出的图形 runbook （ **. .Graphrunbook**）或 Python 2 脚本（ **. Py**），在 Azure 自动化中创建新的 runbook。  必须指定在导入期间创建的 [Runbook 类型](automation-runbook-types.md)，并考虑以下注意事项。

* 不包含工作流的**ps1**文件可导入到[Powershell Runbook](automation-runbook-types.md#powershell-runbooks)或[powershell 工作流 runbook](automation-runbook-types.md#powershell-workflow-runbooks)。 如果将其导入到 PowerShell 工作流 runbook 中，则会将其转换为工作流。 在这种情况下，runbook 中将包含注释来描述所做的更改。

* 包含 PowerShell 工作流的**ps1**文件只能导入到[powershell 工作流 runbook](automation-runbook-types.md#powershell-workflow-runbooks)。 如果文件包含多个 PowerShell 工作流，导入将失败。 必须将每个工作流保存到各自的文件中，并分别导入每个工作流。

* 包含 PowerShell 工作流的**ps1**文件不应导入到[powershell runbook](automation-runbook-types.md#powershell-runbooks)中，因为 powershell 脚本引擎无法识别它。

* **.Graphrunbook**文件只能导入到新的[图形 runbook](automation-runbook-types.md#graphical-runbooks)中。 请注意，只能从 **.graphrunbook**文件创建图形 runbook。

### <a name="import-a-runbook-from-a-file-with-the-azure-portal"></a>使用 Azure 门户从文件导入 runbook

可通过以下过程将脚本文件导入 Azure 自动化。

> [!NOTE]
> 只能使用门户将**ps1**文件导入到 PowerShell 工作流 runbook。

1. 在 Azure 门户中，打开自动化帐户。
2. 从中心选择 "**进程自动化**" 下的 " **runbook** "，打开 runbook 的列表。
3. 单击 "**导入 runbook**"。
4. 单击 " **Runbook 文件**"，然后选择要导入的文件。
5. 如果 "**名称**" 字段已启用，则可以选择更改 runbook 名称。 名称必须以字母开头，并且可以包含字母、数字、下划线和短划线。
6. 将自动选择 [Runbook 类型](automation-runbook-types.md)，但可以在考虑适用的限制后更改该类型。
7. 单击“创建”。 新的 runbook 会出现在自动化帐户的 runbook 列表中。
8. 必须先[发布 Runbook](#publish-a-runbook)，才能运行它。

> [!NOTE]
> 导入图形 runbook 或图形 PowerShell 工作流 runbook 后，可以将其转换为其他类型。 但是，不能将其中一种图形 runbook 转换为文本 runbook。

### <a name="import-a-runbook-from-a-script-file-with-windows-powershell"></a>使用 Windows PowerShell 从脚本文件导入 runbook

使用[AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/import-azautomationrunbook?view=azps-3.5.0) cmdlet 将脚本文件导入为 PowerShell 工作流 runbook 草稿。 如果 runbook 已存在，除非将*Force*参数与 cmdlet 一起使用，否则导入将失败。

下面的示例演示如何将脚本文件导入 runbook。

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="test-a-runbook"></a>测试 Runbook

测试 Runbook 时，将执行[草稿版](#publish-a-runbook)，并会完成其所执行的任何操作。 不会创建任何作业历史记录，但会在 "测试输出" 窗格中显示[输出](automation-runbook-output-and-messages.md#output-stream)、[警告和错误](automation-runbook-output-and-messages.md#message-streams)流。 仅当 VerbosePreference 变量] （automation-变量）设置为**Continue**时，才会在输出窗格中显示[详细流](automation-runbook-output-and-messages.md#message-streams)的消息。

即使草稿版正在运行，该 Runbook 也仍会正常执行，并针对环境中的资源执行任何操作。 因此，只能在非生产资源中测试 Runbook。

测试每种[runbook 类型](automation-runbook-types.md)的过程是相同的。 Azure 门户中的文本编辑器和图形编辑器之间的测试没有区别。

1. 在[文本编辑器](automation-edit-textual-runbook.md)或[图形编辑器](automation-graphical-authoring-intro.md)中打开 runbook 的草稿版本。
1. 单击“测试”按钮以打开“测试”页。
1. 如果 runbook 具有参数，则这些参数将列在左侧窗格中，你可以在其中提供用于测试的值。
1. 如果要在[混合 Runbook 辅助角色](automation-hybrid-runbook-worker.md)上运行测试，则将 "**运行设置**" 更改为 "**混合辅助角色**" 并选择目标组的名称。  否则，保留默认值 **Azure**，以在云中运行测试。
1. 单击 "**启动**" 按钮以开始测试。
1. 你可以使用 "输出" 窗格下的按钮来停止或挂起正在测试的[PowerShell 工作流](automation-runbook-types.md#powershell-workflow-runbooks)或[图形](automation-runbook-types.md#graphical-runbooks)runbook。 暂停 Runbook 时，该 Runbook 会完成它在被暂停之前正在进行的活动。 暂停 Runbook 后，可以将它停止或重启。
1. 在输出窗格中检查 runbook 的输出。

## <a name="publish-a-runbook"></a>发布 Runbook

创建或导入新的 Runbook 时，必须先将其发布，然后才能导入。 Azure Automation 中的每个 runbook 都有草稿版本和已发布的版本。 只有已发布版才能用来运行，只有草稿版才能用来编辑。 已发布版不受对草稿版所做的任何更改的影响。 如果应使草稿版本可用，请发布该版本，并使用草稿版本覆盖当前发布的版本。

### <a name="publish-a-runbook-in-the-azure-portal"></a>在 Azure 门户中发布 runbook

1. 在 Azure 门户中打开 Runbook。
2. 单击 **“编辑”** 。
3. 单击 "**发布**"，然后单击 **"是"** 以响应验证消息。

### <a name="publish-a-runbook-using-powershell"></a>使用 PowerShell 发布 runbook

使用[AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Publish-AzAutomationRunbook?view=azps-3.5.0) Cmdlet 通过 Windows PowerShell 发布 runbook。 下面的示例演示如何发布示例 runbook。

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="next-steps"></a>后续步骤

* 若要了解如何从 Runbook 和 PowerShell 模块库中受益，请参阅[Azure 自动化的 runbook 和模块库](automation-runbook-gallery.md)。
* 若要了解有关使用文本编辑器编辑 PowerShell 和 PowerShell 工作流 runbook 的详细信息，请参阅[在 Azure 自动化中编辑文本 runbook](automation-edit-textual-runbook.md)。
* 若要详细了解图形 runbook 创作，请参阅[Azure 自动化中的图形创作](automation-graphical-authoring-intro.md)。
