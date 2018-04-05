---
title: Azure 自动化中的 Runbook 设置
description: 介绍 Azure 自动化中 Runbook 的配置设置，以及如何使用 Azure 门户和 Windows PowerShell 更改这些设置。
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 548a168055a63ee8cb5147b56fb77ef0bc801e17
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="runbook-settings"></a>Runbook 设置
Azure 自动化中的每个 Runbook 都提供了多个设置用于帮助标识自身，以及更改它的日志记录行为。 下面会描述其中的每个设置，然后再介绍修改设置的过程。

## <a name="settings"></a>设置
### <a name="name-and-description"></a>名称和说明
创建 Runbook 后，无法更改它的名称。 “说明”是可选的，最多可包含 512 个字符。

### <a name="tags"></a>标记
使用标记可以指定不同的单词和短语用于帮助标识 Runbook。 例如，在向 [PowerShell 库](https://www.powershellgallery.com/)提交 Runbook 时，可以指定特定的标记来标识应将该 Runbook 列入的类别。 可为一个 Runbook 指定多个标记并用逗号分隔各个标记。

### <a name="logging"></a>日志记录
默认情况下，“详细”和“进度”记录不会写入作业历史记录。 可以更改特定 Runbook 的设置以记录这些记录。 有关这些记录的详细信息，请参阅 [Runbook 输出和消息](automation-runbook-output-and-messages.md)。

## <a name="changing-runbook-settings"></a>更改 Runbook 设置

### <a name="changing-runbook-settings-with-the-azure-portal"></a>使用 Azure 门户更改 Runbook 设置
可以在 Azure 门户中通过 Runbook 的“设置”边栏选项卡更改 Runbook 的设置。

1. 在 Azure 门户中，选择“自动化”，并单击自动化帐户的名称。
2. 选择“Runbook”选项卡。
3. 单击 Runbook 的名称，转到 Runbook 的“设置”边栏选项卡。 可在此处指定或修改标记、Runbook 描述，配置日志记录和跟踪设置，以及访问有助于解决问题的支持工具。     

### <a name="changing-runbook-settings-with-windows-powershell"></a>使用 Windows PowerShell 更改 Runbook 设置
可以使用 [Set-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603786.aspx) cmdlet 更改 Runbook 的设置。 如果想要指定多个标记，可以向 Tags 参数提供一个数组，或者一个包含逗号分隔值的字符串。 可以使用 [Get-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603728.aspx) 获取当前标记。

以下示例命令演示了如何设置 Runbook 的属性。 此示例向现有标记添加了三个标记，并指定应该记录详细记录。

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $tags = (Get-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName $automationAccountName –Name $runbookName).Tags
    $tags += "Tag1,Tag2,Tag3"
    Set-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName $automationAccountName –Name $runbookName –LogVerbose $true –Tags $tags

## <a name="next-steps"></a>后续步骤
* 若要学习如何创建输出和错误消息以及从 Runbook 检索此类消息，请参阅 [Runbook 输出和消息](automation-runbook-output-and-messages.md) 
* 若要了解如何添加已由社区或其他源开发的 Runbook，或创建自己的 Runbook，请参阅[创建或导入 Runbook](automation-creating-importing-runbook.md) 

