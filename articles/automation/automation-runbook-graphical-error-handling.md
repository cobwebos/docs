---
title: "Azure 自动化图形 Runbook 中的错误处理 | Microsoft 文档"
description: "本文介绍如何在 Azure 自动化图形 Runbook 中实现错误处理逻辑。"
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/26/2016
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: f9b359691122da9e5d93e51f3085cad51e55d8f2
ms.openlocfilehash: 13c3e1693badcf4148738cb63666f34546d1696c

---

# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Azure 自动化图形 Runbook 中的错误处理

需要考虑的一个关键 Runbook 设计原则是识别 Runbook 可能遇到的不同问题，例如成功、意料中的错误状态和意外的错误状态。  应在 Runbook 中包含错误处理逻辑来进行相应的检测。  使用图形 Runbook 时，如果想要验证活动的输出或以适当的方式处理错误，你有可能会跟踪 PowerShell 代码的活动，针对活动的输出链接定义条件逻辑，或运用其他方法。          

通常，在执行 Runbook 时，如果某个活动发生非终止性错误，则其后的任何活动会继续处理。  当然，这种状态可能会生成异常，但问题在于，后续的活动可继续执行。 此行为幕后的原因在于 PowerShell 语言处理错误的设计方式。    

在执行期间可能发生的 PowerShell 错误类型包括终止性错误或非终止性错误。  两者的差别如下：

* 终止性错误：执行期间发生的严重错误，会完全停止命令（或脚本执行）。 例子包括 cmdlet 不存在、阻止某个 cmdlet 运行的语法错误，或其他严重错误。

* 非终止性错误：非严重性错误，尽管发生失败，但允许继续执行。 例子包括“找不到此类文件”操作错误、权限问题，等等。

Azure 自动化图形 Runbook 已得到改进，能够包含错误处理逻辑，让用户将异常转化为非终止性错误，并在活动之间创建错误链接。 这样，Runbook 创作者便可以捕获错误，通过某种途径来管理意料内的或意外的状态。  

## <a name="when-to-use"></a>使用时机

控制工作流的执行非常重要，这样可以确保每当关键活动引发错误或异常时，你都可以阻止继续执行 Runbook 中的下一个活动，并相应地处理错误。  当 Runbook 在为业务过程或服务运营过程提供支持时，特别有必要采取此措施。

对于可能会生成错误的每个活动，Runbook 创作者可以添加指向其他任何活动的错误链接。  目标活动可属于任何类型：代码活动、调用 cmdlet、调用另一个 Runbook，或其他任何活动。 

此外，目标活动还可以包含传出链接，可以是常规链接或错误链接，因此，Runbook 创作者可以实现复杂的错误处理逻辑，无需退而求其次包含代码活动。  尽管建议的做法是创建一个具有通用功能的专用错误处理 Runbook，但不一定非要在 PowerShell 代码活动中包含错误处理逻辑，这并不是唯一的选择。  

例如，假设某个 Runbook 尝试启动 VM 并在其上安装一个应用程序，但如果该 VM 无法正常启动，该 Runbook 将执行两个操作： 

1. 发送有关此问题的通知。
2. 启动另一个 Runbook 来自动预配新的 VM。 

一种解决方法是创建一个指向活动的错误链接用于处理步骤 1 中的操作（例如，使用 **Write-Warning** cmdlet），并创建一个连接到活动的链接（例如，使用 **Start-AzureRmAutomationRunbook** cmdlet）用于处理步骤 2 中的操作。 

还可以遵循前面所述的建议指导，通用化此行为以便在多个 Runbook 中使用，并将这两个活动放在单独的错误处理 Runbook 中。  在调用此错误处理 Runbook 之前，可以基于原始 Runbook 中的数据构造自定义消息，然后将此消息作为参数传递给错误处理 Runbook。 

## <a name="how-to-use"></a>如何使用

在每个活动中，有一项配置可将异常转化为非终止性错误。 此配置默认已禁用。  在想要处理错误的任何活动中，应该启用此配置。  启用此配置可确保将活动中的终止性和非终止性错误作为非终止性错误进行处理，然后使用错误链接进行处理。  配置此设置后，请创建用于处理该错误的活动。  如果某个活动生成了任何错误，将会遵循传出错误链接，但不遵循常规链接，即使活动同时生成了常规输出。<br><br> ![自动化 Runbook 错误链接示例](media/automation-runbook-graphical-error-handling/error-link-example.png)

在下面的简单示例中，某个 Runbook 将会检索包含虚拟机名称的变量，然后尝试使用下一个活动启动该虚拟机。<br><br> ![自动化 Runbook 错误处理示例](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

**Get-AutomationVariable** 活动和 **Start-AzureRmVm** 已配置为将异常转换为错误。  如果获取变量或启动 VM 时出现问题，将会生成错误。<br><br> ![自动化 Runbook 错误处理活动设置](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)

错误链接从这些活动流向单个“错误管理”活动（代码活动），该活动是在一个简单的 PowerShell 表达式中使用 *Throw* 关键字（用于停止处理）以及 *$Error.Exception.Message*（用于获取描述当前异常的消息）配置的。<br><br> ![自动化 Runbook 错误处理代码示例](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)


## <a name="next-steps"></a>后续步骤

* 若要详细了解图形 Runbook 中的链接和链接类型，请参阅 [Azure 自动化中的图形创作](automation-graphical-authoring-intro.md#links-and-workflow)。

* 若要详细了解 Runbook 执行方式、如何监视 Runbook 作业和其他技术详细信息，请参阅 [Track a runbook job](automation-runbook-execution.md)（跟踪 Runbook 作业） 


<!--HONumber=Jan17_HO1-->


