---
title: Azure 自动化图形 Runbook 中的错误处理
description: 本文介绍如何在 Azure 自动化图形 Runbook 中实现错误处理逻辑。
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 147fbe35a08d78360959d858e756d6b6ec71234d
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
---
# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Azure 自动化图形 Runbook 中的错误处理

需要考虑的 Runbook 设计关键原则是确定 Runbook 可能会遇到的不同情况。 这些情况可能包括：成功、预计的错误状态，以及意外的错误条件。

Runbook 应包含错误处理。 若要通过图形 Runbook 来验证活动的输出或处理错误，可以使用 Windows PowerShell 代码活动、针对活动的输出链接定义条件逻辑，或者应用另一方法。          

通常情况下，如果某个 Runbook 活动出现了非终止性错误，系统会无视错误，继续执行后续的任何活动。 该错误可能会生成异常，但下一活动仍允许运行。 PowerShell 这样处理错误是设计使然。    

在执行期间可能发生的 PowerShell 错误类型包括终止性错误或非终止性错误。 终止性和非终止性错误的区别如下：

* **终止性错误**：执行期间发生的严重错误，会完全停止命令（或脚本执行）。 例子包括：cmdlet 不存在、阻止某个 cmdlet 运行的语法错误，或其他严重错误。

* **非终止性错误**：非严重性错误，尽管失败，但允许继续执行。 例子包括：“找不到文件”之类的操作错误、权限问题。

Azure 自动化图形 Runbook 在改进后可以进行错误处理。 用户现在可以将例外变成非终止性错误，并在活动之间创建错误链接。 通过这样一个过程，Runbook 创作者便可以捕获错误，以及管理意料内或意料外的情况。  

## <a name="when-to-use-error-handling"></a>何时使用错误处理

每当关键活动引发错误或异常时，必须阻止 Runbook 中下一活动的继续执行，并对错误进行相应处理。 当 Runbook 在为业务过程或服务运营过程提供支持时，特别有必要采取此措施。

对于可能会生成错误的每个活动，Runbook 创作者可以添加指向其他任何活动的错误链接。 目标活动可属于任何类型，包括：代码活动、调用 cmdlet、调用另一个 Runbook，等等。

此外，目标活动还可以有传出链接。 这些链接可以是常规链接，也可以是错误链接。 这意味着，Runbook 创作者可以在不求助于代码活动的情况下，实现复杂的错误处理逻辑。 建议的做法是创建具有常用功能的专用错误处理 Runbook，但这不是必需的。 在 PowerShell 代码活动中实现错误处理逻辑不是唯一选项。  

例如，有一个 Runbook 尝试启动 VM 并在其上安装应用程序。 VM 在未正确启动的情况下，会执行两项操作：

1. 发送有关此问题的通知。
2. 改为启动另一个 Runbook 来自动预配新的 VM。

一种解决方案是让一个错误链接指向处理步骤一操作的活动。 例如，可以将 **Write-Warning** cmdlet 连接到步骤二的一个活动，例如 **Start-AzureRmAutomationRunbook** cmdlet。

还可以通用化此行为，使其能够在多个 Runbook 中使用，只需将这两个活动放在单独的错误处理 Runbook 中，并遵循前面提供的指导即可。 在调用此错误处理 Runbook 之前，可以基于原始 Runbook 中的数据构造自定义消息，然后将其作为参数传递给错误处理 Runbook。

## <a name="how-to-use-error-handling"></a>如何使用错误处理

在每个活动中，有一项配置设置可将异常转化为非终止性错误。 此设置默认已禁用。 在需要处理错误的任何活动中，建议启用此设置。  

启用此配置可确保将活动中的终止性和非终止性错误作为非终止性错误进行处理，并可使用错误链接进行处理。  

配置此设置后，请创建用于处理该错误的活动。 如果某个活动生成了任何错误，会遵循传出错误链接，但不遵循常规链接，即使活动同时生成了常规输出。<br><br> ![自动化 Runbook 错误链接示例](media/automation-runbook-graphical-error-handling/error-link-example.png)

在以下示例中，一个 Runbook 检索的变量包含虚拟机的计算机名称。 然后，该 Runbook 尝试使用下一活动启动虚拟机。<br><br> ![自动化 Runbook 错误处理示例](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

**Get-AutomationVariable** 活动和 **Start-AzureRmVm** 已配置为将异常转换为错误。 如果在获取变量或启动 VM 时出现问题，会生成错误。<br><br> ![自动化 Runbook 错误处理活动设置](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)

错误链接从这些活动流向单个“错误管理”活动（代码活动）。 该活动是通过使用 *Throw* 关键字的简单 PowerShell 表达式（用于停止处理）以及 *$Error.Exception.Message*（用于获取描述当前异常的消息）配置的。<br><br> ![自动化 Runbook 错误处理代码示例](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)


## <a name="next-steps"></a>后续步骤

* 若要详细了解图形 Runbook 中的链接和链接类型，请参阅 [Azure 自动化中的图形创作](automation-graphical-authoring-intro.md#links-and-workflow)。

* 若要详细了解 Runbook 执行方式、如何监视 Runbook 作业和其他技术详细信息，请参阅[跟踪 Runbook 作业](automation-runbook-execution.md)。
