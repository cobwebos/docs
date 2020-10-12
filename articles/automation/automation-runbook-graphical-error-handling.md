---
title: 处理 Azure 自动化图形 Runbook 中的错误
description: 本文介绍如何在图形 Runbook 中实现错误处理逻辑。
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 8af64f2189625bcff5271855d6c0102551d1a535
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185953"
---
# <a name="handle-errors-in-graphical-runbooks"></a>处理图形 Runbook 中的错误

对于 Azure 自动化图形 Runbook，需要考虑的一个关键设计原则是识别 Runbook 在执行期间可能会遇到的问题。 这些情况可能包括：成功、预计的错误状态，以及意外的错误条件。

通常情况下，如果 Runbook 活动出现的是非终止错误，则 Windows PowerShell 会无视该错误，并继续处理该活动以及后续的任何活动。 该错误可能会生成异常，但下一活动仍允许运行。

图形 Runbook 应包含错误处理代码来处理执行问题。 若要验证某个活动的输出或对错误进行处理，可以使用 PowerShell 代码活动，对活动的输出链接定义条件逻辑，或者应用其他方法。

Azure 自动化图形 Runbook 在改进后可以进行错误处理。 用户现在可以将例外变成非终止性错误，并在活动之间创建错误链接。 这一经过改进的过程使 Runbook 可以捕获错误，并管理意料内或意料外的情况。 

## <a name="powershell-error-types"></a>PowerShell 错误类型

Runbook 执行期间可能会发生的 PowerShell 错误的类型为终止性错误和非终止性错误。
 
### <a name="terminating-error"></a>终止性错误

终止性错误是执行期间发生的严重错误，该错误会导致命令或脚本的执行完全停止。 示例包括：cmdlet 不存在、阻止某个 cmdlet 运行的语法错误，以及其他灾难性错误。

### <a name="non-terminating-error"></a>非终止性错误

非终止性错误是不严重的错误，尽管出现错误状况，但允许继续执行。 示例包括：“找不到文件”之类的操作错误、权限问题。

## <a name="when-to-use-error-handling"></a>何时使用错误处理

当关键活动引发错误或异常时，可使用 Runbook 中的错误处理。 必须禁止处理 Runbook 中的下一个活动，并适当地处理错误。 当 Runbook 在为业务过程或服务运营过程提供支持时，处理错误尤其重要。

## <a name="add-error-links"></a>添加错误链接

对于可能会生成错误的每个活动，可以添加指向其他任何活动的错误链接。 目标活动可属于任何类型，包括代码活动、调用 cmdlet、调用另一个 Runbook，等等。 目标活动还可以包含传出链接（常规链接或错误链接）。 这些链接可让 Runbook 在不求助于代码活动的情况下，实现复杂的错误处理逻辑。

建议的做法是创建具有常用功能的专用错误处理 Runbook，但这种做法不是必需的。 例如，有一个 Runbook 尝试启动虚拟机并在其上安装应用程序。 如果 VM 无法正常启动，将会：

1. 发送有关此问题的通知。
2. 改为启动另一个 Runbook 来自动预配新的 VM。

一种解决方案是让 Runbook 中的一个错误链接指向处理步骤 1 的活动。 例如，Runbook 可以将 `Write-Warning` cmdlet 连接到步骤 2 的活动（如 [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0) cmdlet）。

还可以通过将这两个活动放在单独的错误处理 Runbook 中，来通用化此行为，以便在许多 Runbook 中使用。 在原始 Runbook 调用此错误处理 Runbook 之前，原始 Runbook 可以基于其数据构造自定义消息，然后将其作为参数传递给错误处理 Runbook。

## <a name="turn-exceptions-into-non-terminating-errors"></a>将异常转换为非终止性错误

Runbook 中的每个活动都有一项可将异常转化为非终止性错误的配置设置。 此设置默认已禁用。 对于 Runbook 要为其处理错误的任何活动，我们均建议启用此设置。 此设置可确保 Runbook 使用错误链接将活动中的终止性错误和非终止性错误作为非终止性错误进行处理。  

启用该配置设置后，请让 Runbook 创建一个处理错误的活动。 如果该活动生成了任何错误，将会打开传出错误链接。 不会打开常规链接，即使活动也生成了常规输出。<br><br> ![自动化 Runbook 错误链接示例](media/automation-runbook-graphical-error-handling/error-link-example.png)

在以下示例中，一个 Runbook 检索了一个包含 VM 的计算机名称的变量。 然后，它尝试使用下一个活动启动该 VM。<br><br> ![自动化 Runbook 错误处理示例](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

`Get-AutomationVariable` 活动和 [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) cmdlet 已配置为将异常转换为错误。 如果获取变量或启动 VM 时出现问题，代码会生成错误。<br><br> ![自动化 Runbook 错误处理活动设置](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)。

错误链接从这些活动流向单个 `error management` 代码活动。 此活动是通过使用 `throw` 关键字的简单 PowerShell 表达式（用于停止处理）以及 `$Error.Exception.Message`（用于获取描述当前异常的消息）配置的。<br><br> ![自动化 Runbook 错误处理代码示例](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)

## <a name="next-steps"></a>后续步骤

* 有关解决图形 Runbook 错误的信息，请参阅[排查 Runbook 问题](troubleshoot/runbooks.md)。
