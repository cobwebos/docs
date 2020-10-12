---
title: 在 Azure 自动化中编辑文本 runbook
description: 本文介绍如何使用 Azure 自动化文本编辑器来处理 PowerShell 和 PowerShell 工作流 runbook。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c51ef23e27cd63d3706c104d1e39a14bf61c258e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86186344"
---
# <a name="edit-textual-runbooks-in-azure-automation"></a>在 Azure 自动化中编辑文本 runbook

可以使用 Azure 自动化中的文本编辑器来编辑 [PowerShell runbook](automation-runbook-types.md#powershell-runbooks) 和 [PowerShell 工作流 runbook](automation-runbook-types.md#powershell-workflow-runbooks)。 此编辑器具有其他代码编辑器（如 IntelliSense）的典型功能。 它还使用带有其他特殊功能的颜色编码来帮助你访问 runbook 中常用的资源。 

该文本编辑器包含的一项功能是将 cmdlet、资产和子 Runbook 的代码插入 Runbook 中。 不需要亲自键入代码，只需从可用资源列表中进行选择，编辑器就会将相应代码插入 runbook 中。

Azure 自动化中的每个 Runbook 都有两个版本：草稿版和已发布版。 先对 Runbook 的草稿版进行编辑，然后将其发布，这样便可以执行了。 无法编辑已发布版本。 有关详细信息，请参阅[发布 runbook](manage-runbooks.md#publish-a-runbook)。

本文提供了使用该编辑器执行不同功能的详细步骤。 它们不适用于[图形 runbook](automation-runbook-types.md#graphical-runbooks)。 若要使用这些 runbook，请参阅 [Azure 自动化中的图形创作](automation-graphical-authoring-intro.md)。

## <a name="edit-a-runbook-with-the-azure-portal"></a>使用 Azure 门户编辑 runbook

1. 在 Azure 门户中，选择自动化帐户。
2. 在“流程自动化”下选择“Runbook”，打开 Runbook 的列表。 
3. 选择要编辑的 runbook，然后单击“编辑”。
4. 编辑 runbook。
5. 完成编辑后，单击“保存”。
6. 若要发布最新的 runbook 草稿版，请单击“发布”。

### <a name="insert-a-cmdlet-into-a-runbook"></a>将 cmdlet 插入 runbook

1. 在文本编辑器的“画布”中，将光标置于要放置该 cmdlet 的地方。
2. 展开“库”控件中的“Cmdlet”节点。
3. 展开包含要使用的 cmdlet 的模块。
4. 右键单击要插入的 cmdlet 名称，然后选择“添加到画布”。 如果 cmdlet 具有多个参数集，编辑器将添加默认集。 还可以展开 cmdlet 来选择不同的参数集。
5. 请注意，该 cmdlet 的代码在插入时附带其所有参数。
6. 对于任何必需的参数，请提供合适的值来替换尖括号 (<>) 中的值。 请删除所有不需要的参数。

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>将子 runbook 的代码插入到 runbook 中

1. 在文本编辑器的“画布”中，将光标置于要放置[子 runbook](automation-child-runbooks.md) 代码的位置。
2. 展开“库”控件中的“Runbook”节点。
3. 右键单击要插入的 runbook，并选择“添加到画布”。
4. 此时会插入带 Runbook 参数占位符的子 Runbook 的代码。
5. 将占位符替换为每个参数的相应值。

### <a name="insert-an-asset-into-a-runbook"></a>将资产插入 runbook

1. 在文本编辑器的“画布”控件中，将光标置于要放置子 runbook 代码的位置。
2. 展开“库”控件中的“资产”节点。
3. 展开所需资产类型的节点。
4. 右键单击要插入的资产名称，并选择“添加到画布”。 对于[变量资产](./shared-resources/variables.md)，请选择“将‘获取变量’添加到画布”或“将‘设置变量’添加到画布”，具体取决于是要获取变量还是要设置变量 。
5. 请注意，此时会将资产的代码插入到 runbook 中。

## <a name="edit-an-azure-automation-runbook-using-windows-powershell"></a>使用 Windows PowerShell 编辑 Azure 自动化 runbook

若要使用 Windows PowerShell 编辑 runbook，请使用所选编辑器进行操作，然后将其保存到 .ps1 文件。 可使用 [Export-AzAutomationRunbook](/powershell/module/Az.Automation/Export-AzAutomationRunbook) cmdlet 检索 runbook 的内容。 可使用 [Import-AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) cmdlet 将现有的草稿 runbook 替换为已修改的 runbook。

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>使用 Windows PowerShell 检索 runbook 的内容

以下示例命令演示了如何检索 Runbook 的脚本并将其保存到脚本文件。 在此示例中，检索的是草稿版本。 也可以检索 runbook 的已发布版本，不过不能更改此版本。

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>使用 Windows PowerShell 更改 runbook 的内容

以下示例命令演示了如何使用脚本文件的内容替换 Runbook 的现有内容。 

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>后续步骤

* [在 Azure 自动化中管理 runbook](manage-runbooks.md)。
* [了解 PowerShell 工作流](automation-powershell-workflow.md)。
* [Azure 自动化中的图形创作](automation-graphical-authoring-intro.md)。
* [证书](./shared-resources/certificates.md)。
* [连接](automation-connections.md)。
* [凭据](./shared-resources/credentials.md)。
* [计划](./shared-resources/schedules.md)。
* [变量](./shared-resources/variables.md)。
* [PowerShell cmdlet 参考](/powershell/module/az.automation/?view=azps-3.7.0#automation)。
