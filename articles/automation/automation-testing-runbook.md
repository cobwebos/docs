---
title: 在 Azure 自动化中测试 Runbook
description: 在 Azure 自动化中发布某个 Runbook 之前，可以对它进行测试，以确保它按预期工作。  本文介绍如何测试 Runbook 并查看其输出。
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 780d197943737c3b821cbcec3afeeb60846ddf39
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
---
# <a name="testing-a-runbook-in-azure-automation"></a>在 Azure 自动化中测试 Runbook
测试 Runbook 时，将执行[草稿版](automation-creating-importing-runbook.md#publishing-a-runbook)，并会完成其所执行的任何操作。 不会创建作业历史记录，但会在“测试输出”窗格中显示[“输出”](automation-runbook-output-and-messages.md#output-stream)与[“警告和错误”](automation-runbook-output-and-messages.md#message-streams)。 仅当 [$VerbosePreference 变量](automation-runbook-output-and-messages.md#preference-variables)设置为 Continue 时，才会在“输出”窗格中显示发送到[详细流](automation-runbook-output-and-messages.md#message-streams)的消息。

即使草稿版正在运行，该 Runbook 也仍会正常执行工作流，并针对环境中的资源执行任何操作。 因此，只能在非生产资源中测试 Runbook。

测试每种 [Runbook 类型](automation-runbook-types.md)的过程是相同的，且 Azure 门户中文本编辑器和图形编辑器的测试没有区别。  

## <a name="to-test-a-runbook-in-the-azure-portal"></a>在 Azure 门户中测试 Runbook
在 Azure 门户中，可使用任何 [Runbook 类型](automation-runbook-types.md)。

1. 在[文本编辑器](automation-edit-textual-runbook.md)或[图形编辑器](automation-graphical-authoring-intro.md)中打开 Runbook 的草稿版本。
2. 单击“测试”按钮，打开“测试”边栏选项卡。
3. 如果 Runbook 有参数，参数会在左窗格中列出，可以在其中提供用于测试的值。
4. 如果要在[混合 Runbook 辅助角色](automation-hybrid-runbook-worker.md)上运行测试，则将“运行设置”更改为“混合辅助角色”并选择目标组的名称。  否则，保留默认值 **Azure**，以在云中运行测试。
5. 单击“开始”按钮以开始测试。
6. 如果 Runbook 是 [PowerShell 工作流](automation-runbook-types.md#powershell-workflow-runbooks) Runbook 或[图形](automation-runbook-types.md#graphical-runbooks) Runbook，则在使用“输出”窗格下方的按钮对它进行测试时，可以停止或暂停它。 暂停 Runbook 时，该 Runbook 会完成它在被暂停之前正在进行的活动。 暂停 Runbook 后，可以将它停止或重启。
7. 在输出窗格中检查 Runbook 的输出。

## <a name="next-steps"></a>后续步骤
* 若要了解如何创建或导入 Runbook，请参阅[在 Azure 自动化中创建或导入 Runbook](automation-creating-importing-runbook.md)
* 若要了解有关图形创作的详细信息，请参阅 [Azure 自动化中的图形创作](automation-graphical-authoring-intro.md)
* 若要开始使用 PowerShell 工作流 Runbook，请参阅 [我的第一个 PowerShell 工作流 Runbook](automation-first-runbook-textual.md)
* 要详细了解如何配置 runbook 以返回状态消息和错误（包括建议的做法），请参阅 [Azure 自动化中的 Runbook 输出和消息](automation-runbook-output-and-messages.md)

