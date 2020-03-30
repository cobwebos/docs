---
title: 在 Azure 自动化中管理 Runbook
description: 本文介绍如何在 Azure 自动化中管理 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: ad8c05b3347ed4741d574a5e6bcc1d928db08411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366830"
---
# <a name="manage-runbooks-in-azure-automation"></a>在 Azure 自动化中管理 Runbook

您可以通过[创建新](#create-a-runbook)Runbook 或从文件或[Runbook 库](automation-runbook-gallery.md)[导入现有](#import-a-runbook)Runbook 来将 Runbook 添加到 Azure 自动化。 本文介绍如何通过文件创建和导入 Runbook。 您可以在[Runbook 和 Azure 自动化的模块库中](automation-runbook-gallery.md)获取访问社区 Runbook 和模块的所有详细信息。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可以使用["如何更新 Azure 自动化 中的 Azure PowerShell"模块](automation-update-azure-modules.md)将模块更新到最新版本。

## <a name="create-a-runbook"></a>创建 runbook

可以使用其中一个 Azure 门户或 Windows PowerShell 在 Azure 自动化中创建一个新的 Runbook。 一旦创建了 Runbook，便可以利用[了解 PowerShell 工作流](automation-powershell-workflow.md)和 [Azure 自动化中的图形创作](automation-graphical-authoring-intro.md)中的信息对其进行编辑。

### <a name="create-a-runbook-in-the-azure-portal"></a>在 Azure 门户中创建 Runbook

1. 在 Azure 门户中，打开自动化帐户。
2. 从中心，在 **"进程自动化**"下选择**Runbook**以打开 Runbook 列表。
3. 单击 **"创建运行簿**"。
4. 输入 Runbook 的名称并选择其[类型](automation-runbook-types.md)。 Runbook 名称必须以字母开头，并可以包含字母、数字、下划线和破折号。
5. 单击“创建”**** 以创建 Runbook 并打开编辑器。

### <a name="create-a-runbook-with-powershell"></a>通过 PowerShell 创建 Runbook

您可以使用[新阿兹自动化 Runbook](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationrunbook?view=azps-3.5.0) cmdlet 创建一个空[的 PowerShell 工作流运行簿](automation-runbook-types.md#powershell-workflow-runbooks)。 使用`Type`参数指定为`New-AzAutomationRunbook`定义的 Runbook 类型之一。

下面的示例演示如何创建新的空 Runbook。

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>导入 Runbook

您可以通过导入 PowerShell 脚本或 PowerShell 工作流 **（.ps1）、** 导出的图形运行**簿 （.graphrunbook）** 或 Python 2 脚本 **（.py）** 在 Azure 自动化中创建新的 Runbook。  必须指定在导入期间创建的 [Runbook 类型](automation-runbook-types.md)，并考虑以下注意事项。

* 不包含工作流的 **.ps1**文件可以导入到[PowerShell 运行簿](automation-runbook-types.md#powershell-runbooks)或[PowerShell 工作流运行簿](automation-runbook-types.md#powershell-workflow-runbooks)中。 如果将其导入 PowerShell 工作流运行簿，则将其转换为工作流。 在这种情况下，注释包含在 Runbook 中，以描述已进行的更改。

* 包含 PowerShell 工作流的 **.ps1**文件只能导入到[PowerShell 工作流运行簿](automation-runbook-types.md#powershell-workflow-runbooks)中。 如果文件包含多个 PowerShell 工作流，则导入将失败。 必须将每个工作流保存到各自的文件中，并分别导入每个工作流。

* 包含 PowerShell 工作流的 **.ps1**文件不应导入[到 PowerShell 运行簿](automation-runbook-types.md#powershell-runbooks)中，因为 PowerShell 脚本引擎无法识别它。

* **.graphrunbook**文件只能导入到新的[图形运行簿](automation-runbook-types.md#graphical-runbooks)中。 请注意，您只能从 **.graphrunbook**文件创建图形运行簿。

### <a name="import-a-runbook-from-a-file-with-the-azure-portal"></a>使用 Azure 门户从文件导入 Runbook

可通过以下过程将脚本文件导入 Azure 自动化。

> [!NOTE]
> 您只能使用门户将 **.ps1**文件导入 PowerShell 工作流运行簿。

1. 在 Azure 门户中，打开自动化帐户。
2. 从中心，在 **"进程自动化**"下选择**Runbook**以打开 Runbook 列表。
3. 单击 **"导入运行簿**"。
4. 单击**Runbook 文件**并选择要导入的文件。
5. 如果启用了 **"名称"** 字段，则可以选择更改 Runbook 名称。 名称必须以字母开头，并可以包含字母、数字、下划线和破折号。
6. 将自动选择 [Runbook 类型](automation-runbook-types.md)，但可以在考虑适用的限制后更改该类型。
7. 单击 **“创建”**。 新的 runbook 会出现在自动化帐户的 runbook 列表中。
8. 必须先[发布 Runbook](#publish-a-runbook)，才能运行它。

> [!NOTE]
> 导入图形运行簿或图形 PowerShell 工作流运行簿后，可以将其转换为其他类型。 但是，无法将这些图形运行簿之一转换为文本 Runbook。

### <a name="import-a-runbook-from-a-script-file-with-windows-powershell"></a>使用 Windows PowerShell 从脚本文件导入运行簿

使用[导入-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/import-azautomationrunbook?view=azps-3.5.0) cmdlet 将脚本文件导入为 PowerShell 工作流运行簿草稿。 如果 Runbook 已存在，则导入将失败，`Force`除非您将参数与 cmdlet 一起使用。

下面的示例演示如何将脚本文件导入 Runbook。

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

测试 Runbook 时，将执行[草稿版](#publish-a-runbook)，并会完成其所执行的任何操作。 未创建作业历史记录，但"测试输出"窗格中将显示["输出](automation-runbook-output-and-messages.md#output-stream)"和["警告和错误](automation-runbook-output-and-messages.md#message-streams)"流。 仅当`VerbosePreference`变量*（自动化运行簿-输出和消息.md_首选项变量）设置为"继续"时，发送到["详细"流](automation-runbook-output-and-messages.md#message-streams)的消息才会显示在"输出"窗格中。

即使草稿版正在运行，该 Runbook 也仍会正常执行，并针对环境中的资源执行任何操作。 因此，只能在非生产资源中测试 Runbook。

测试每[种类型的 Runbook](automation-runbook-types.md)的过程是相同的。 在 Azure 门户中的文本编辑器和图形编辑器之间的测试没有区别。

1. 在[文本编辑器](automation-edit-textual-runbook.md)或[图形编辑器](automation-graphical-authoring-intro.md)中打开 Runbook 的草稿版本。
1. 单击“测试”按钮以打开“测试”页****。
1. 如果 Runbook 具有参数，则它们列在左侧窗格中，您可以在其中提供用于测试的值。
1. 如果要在[混合 Runbook 工作线程](automation-hybrid-runbook-worker.md)上运行测试，请将 **"运行设置"** 更改为 **"混合辅助角色**"，然后选择目标组的名称。  否则，保留默认值 **Azure**，以在云中运行测试。
1. 单击"**开始"** 按钮开始测试。
1. 您可以在"输出"窗格下使用按钮在测试时停止或挂起[PowerShell 工作流](automation-runbook-types.md#powershell-workflow-runbooks)或[图形](automation-runbook-types.md#graphical-runbooks)运行簿。 暂停 Runbook 时，该 Runbook 会完成它在被暂停之前正在进行的活动。 暂停 Runbook 后，可以将它停止或重启。
1. 检查"输出"窗格中的 Runbook 中的输出。

## <a name="publish-a-runbook"></a>发布 Runbook

创建或导入新的 Runbook 时，必须先将其发布，然后才能导入。 Azure 自动化中的每个 Runbook 都有草稿版本和已发布版本。 只有已发布版才能用来运行，只有草稿版才能用来编辑。 已发布版不受对草稿版所做的任何更改的影响。 当草稿版本应可用时，您可以发布它，用草稿版本覆盖当前发布的版本。

### <a name="publish-a-runbook-in-the-azure-portal"></a>在 Azure 门户中发布 Runbook

1. 在 Azure 门户中打开 Runbook。
2. 单击 **“编辑”**。
3. 单击 **"发布"，** 然后单击 **"是**"以响应验证消息。

### <a name="publish-a-runbook-using-powershell"></a>使用 PowerShell 发布运行簿

使用[发布-阿兹自动化 Runbook](https://docs.microsoft.com/powershell/module/Az.Automation/Publish-AzAutomationRunbook?view=azps-3.5.0) cmdlet 使用 Windows PowerShell 发布 Runbook。 下面的示例演示如何发布示例 Runbook。

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="next-steps"></a>后续步骤

* 要了解如何从 Runbook 和 PowerShell 模块库中获益，请参阅[Azure 自动化的 Runbook 和模块库](automation-runbook-gallery.md)。
* 要了解有关使用文本编辑器编辑 PowerShell 和 PowerShell 工作流 Runbook 的详细信息，请参阅[在 Azure 自动化中编辑文本 Runbook](automation-edit-textual-runbook.md)。
* 要了解有关图形运行簿创作的更多信息，请参阅[Azure 自动化 中的图形创作](automation-graphical-authoring-intro.md)。
