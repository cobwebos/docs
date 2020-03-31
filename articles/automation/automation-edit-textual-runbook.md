---
title: 在 Azure 自动化中编辑文本 Runbook
description: 本文提供的不同过程适用于在 Azure 自动化中通过文本编辑器来处理 PowerShell Runbook 和 PowerShell 工作流 Runbook。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 72aefb8de57e27718b14dba6a6d82deb8b63466f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367272"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>在 Azure 自动化中编辑文本 Runbook

Azure 自动化中的文本编辑器可以用来编辑 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) 和 [PowerShell 工作流 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)。 此编辑器具有其他代码编辑器的典型功能，如 IntelliSense。 它还具有带有其他特殊功能的颜色编码，可帮助您访问 Runbook 常见的资源。 

该文本编辑器包含的一项功能是将 cmdlet、资产和子 Runbook 的代码插入 Runbook 中。 您可以从可用资源列表中选择，编辑器将适当的代码插入 Runbook，而不是自己键入代码。

Azure 自动化中的每个 Runbook 都有两个版本：草稿版和已发布版。 先对 Runbook 的草稿版进行编辑，然后将其发布，这样便可以执行了。 无法编辑已发布版本。 有关详细信息，请参阅 [发布 runbook](manage-runbooks.md#publish-a-runbook)。

本文提供了使用该编辑器执行不同功能的详细步骤。 这些不适用于[图形运行簿](automation-runbook-types.md#graphical-runbooks)。 要使用这些 Runbook，请参阅[Azure 自动化 中的图形创作](automation-graphical-authoring-intro.md)。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可以使用["如何更新 Azure 自动化 中的 Azure PowerShell"模块](automation-update-azure-modules.md)将模块更新到最新版本。

## <a name="editing-a-runbook-with-the-azure-portal"></a>使用 Azure 门户编辑 Runbook

通过以下过程打开 Runbook 即可在文本编辑器中进行编辑。

1. 在 Azure 门户中，选择自动化帐户。
2. 在 **"进程自动化"** 下，选择**Runbook**以打开 Runbook 列表。
3. 选择要编辑的 Runbook，然后单击"**编辑**"。
4. 编辑 runbook。
5. 完成编辑后，单击“保存”****。
6. 如果要发布 Runbook 的最新草稿版本，请单击 **"发布**"。

### <a name="insert-a-cmdlet-into-a-runbook"></a>将 cmdlet 插入到 Runbook 中

1. 在文本编辑器的画布中，将光标放置在要放置 cmdlet 的位置。
2. 展开“库”控件中的“Cmdlet”**** 节点。
3. 展开包含要使用的 cmdlet 的模块。
4. 右键单击 cmdlet 名称以插入并选择"**添加到画布**"。 如果 cmdlet 具有多个参数集，则编辑器将添加默认集。 还可以展开 cmdlet 来选择不同的参数集。
5. 请注意，cmdlet 的代码将插入其整个参数列表。
6. 为任何必需的参数提供适当的值，以代替由角括号（<>）括起来的值。 删除不需要的任何参数。

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>将子运行簿的代码插入到 Runbook 中

1. 在文本编辑器的画布中，将光标定位到要放置[子 Runbook](automation-child-runbooks.md)的代码的位置。
2. 展开“库”控件中的“Runbook”**** 节点。
3. 右键单击要插入的 runbook，并选择“添加到画布”****。
4. 此时会插入带 Runbook 参数占位符的子 Runbook 的代码。
5. 将占位符替换为每个参数的相应值。

### <a name="insert-an-asset-into-a-runbook"></a>将资产插入到 Runbook 中

1. 在文本编辑器的 Canvas 控件中，将光标定位到要放置子 Runbook 的代码的位置。
2. 展开“库”控件中的“资产”**** 节点。
3. 展开所需资产类型的节点。
4. 右键单击要插入的资产名称，然后选择 **"添加到画布**"。 对于[变量资产](automation-variables.md)，选择 **"获取变量"以画布**或**添加"设置变量"以画布**，具体取决于您是想要获取还是设置变量。
5. 请注意，资产的代码将插入到 Runbook 中。

## <a name="editing-an-azure-automation-runbook-using-windows-powershell"></a>使用 Windows PowerShell 编辑 Azure 自动化运行簿

要使用 Windows PowerShell 编辑 Runbook，请使用您选择的编辑器并将 Runbook 保存到 **.ps1**文件。 您可以使用[导出-AzAutomationRunbook](/powershell/module/Az.Automation/Export-AzAutomationRunbook) cmdlet 检索 Runbook 的内容。 您可以使用[导入-AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) cmdlet 将现有的草稿运行簿替换为修改后的计划运行簿。

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>使用 Windows PowerShell 检索 Runbook 的内容

以下示例命令演示了如何检索 Runbook 的脚本并将其保存到脚本文件。 在此示例中，检索的是草稿版本。 也可以检索 Runbook 的已发布版本，尽管无法更改此版本。

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>使用 Windows PowerShell 更改 Runbook 的内容

以下示例命令演示了如何使用脚本文件的内容替换 Runbook 的现有内容。 此示例过程与[使用 Windows PowerShell 从脚本文件中导入 runbook](manage-runbooks.md#import-a-runbook) 中的相同。

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="related-articles"></a>相关文章

* [在 Azure 自动化中管理 runbook](manage-runbooks.md)
* [学习 PowerShell 工作流](automation-powershell-workflow.md)
* [Azure 自动化中的图形创作](automation-graphical-authoring-intro.md)
* [证书](automation-certificates.md)
* [连接](automation-connections.md)
* [凭据](automation-credentials.md)
* [计划](automation-schedules.md)
* [变量](automation-variables.md)

