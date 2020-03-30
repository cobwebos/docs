---
title: Azure 自动化图形 Runbook 中的错误处理
description: 本文介绍如何在 Azure 自动化图形 Runbook 中实现错误处理逻辑。
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: f1aa605b3e6f32b260ea4a9eee9c056277fcd12d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367064"
---
# <a name="error-handling-in-azure-automation-graphical-runbooks"></a>Azure 自动化图形 Runbook 中的错误处理

Azure 自动化图形运行簿需要考虑的一个关键设计原则是确定 Runbook 在执行过程中可能会遇到的问题。 这些情况可能包括：成功、预计的错误状态，以及意外的错误条件。

通常，如果 Runbook 活动发生非终止错误，Windows PowerShell 会通过处理以下任何活动来处理活动，而不管错误如何。 该错误可能会生成异常，但下一活动仍允许运行。

图形运行簿应包括错误处理代码，以处理执行问题。 要验证活动的输出或处理错误，可以使用 PowerShell 代码活动、在活动的输出链接上定义条件逻辑或应用其他方法。

Azure 自动化图形 Runbook 在改进后可以进行错误处理。 用户现在可以将例外变成非终止性错误，并在活动之间创建错误链接。 改进后的过程允许您的 Runbook 捕获错误并管理已实现或意外的情况。 

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可以使用["如何更新 Azure 自动化 中的 Azure PowerShell"模块](automation-update-azure-modules.md)将模块更新到最新版本。

## <a name="powershell-error-types"></a>电源壳错误类型

运行簿执行期间可能发生的 PowerShell 错误类型包括终止错误和非终止错误。
 
### <a name="terminating-error"></a>终止错误

终止错误是执行期间完全停止命令或脚本执行的严重错误。 示例包括不存在的 cmdlet、阻止 cmdlet 运行的语法错误以及其他致命错误。

### <a name="non-terminating-error"></a>非终止错误

非终止错误是非严重错误，允许执行继续，尽管错误条件。 示例包括操作错误，例如未发现文件错误和权限问题。

## <a name="when-to-use-error-handling"></a>何时使用错误处理

当关键活动引发错误或异常时，在 Runbook 中使用错误处理。 请务必防止 Runbook 中的下一个活动处理并适当地处理错误。 当 Runbook 支持业务或服务操作流程时，处理该错误尤为重要。

对于每个可能产生错误的活动，可以添加指向任何其他活动的错误链接。 目标活动可以是任何类型的活动，包括代码活动、cmdlet 的调用、另一个 Runbook 的调用等。 目标活动还可以具有传出链接，无论是常规链接还是错误链接。 这些链接允许 Runbook 实现复杂的错误处理逻辑，而无需诉诸代码活动。

建议的做法是创建具有常见功能的专用错误处理 Runbook，但这种做法不是强制性的。 例如，考虑尝试启动虚拟机并在其上安装应用程序的 Runbook。 如果 VM 启动不正确，它将：

1. 发送有关此问题的通知。
2. 启动另一个运行簿，自动配置新的 VM。

一个解决方案是在 Runbook 中具有指向处理步骤 1 的活动的错误链接。 例如，runbook 可以将`Write-Warning`cmdlet 连接到步骤 2 的活动，例如[启动-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0) cmdlet。

还可以通过将这两个活动放在单独的错误处理 Runbook 中来概括此行为，以便在许多 Runbook 中使用。 在原始 Runbook 调用此错误处理 Runbook 之前，它可以从其数据构造自定义消息，然后将其作为参数传递给错误处理 Runbook。

## <a name="how-to-use-error-handling"></a>如何使用错误处理

Runbook 中的每个活动都有一个配置设置，可将异常转换为非终止错误。 此设置默认已禁用。 我们建议在 Runbook 处理错误的任何活动上启用此设置。 此设置可确保 Runbook 使用错误链接将活动中的终止错误和非终止错误作为非终止错误处理。  

启用配置设置后，让 Runbook 创建处理错误的活动。 如果活动产生任何错误，则遵循传出错误链接。 即使活动也生成常规输出，也不会遵循常规链接。<br><br> ![自动化 Runbook 错误链接示例](media/automation-runbook-graphical-error-handling/error-link-example.png)

在下面的示例中，Runbook 检索包含 VM 计算机名称的变量。 然后，它尝试使用下一个活动启动 VM。<br><br> ![自动化运行簿错误处理示例](media/automation-runbook-graphical-error-handling/runbook-example-error-handling.png)<br><br>      

活动和`Get-AutomationVariable`[启动-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0) cmdlet 配置为将异常转换为错误。 如果在获取变量或启动 VM 时出现问题，代码将生成错误。<br><br> ![自动化运行簿错误处理活动设置](media/automation-runbook-graphical-error-handling/activity-blade-convertexception-option.png)。

错误链接从这些活动流向单个`error management`代码活动。 此活动配置了一个简单的 PowerShell 表达式，`throw`该表达式使用 关键字停止处理，`$Error.Exception.Message`以及获取描述当前异常的消息。<br><br> ![自动化运行簿错误处理代码示例](media/automation-runbook-graphical-error-handling/runbook-example-error-handling-code.png)

## <a name="next-steps"></a>后续步骤

* 若要详细了解图形 Runbook 中的链接和链接类型，请参阅 [Azure 自动化中的图形创作](automation-graphical-authoring-intro.md#links-and-workflow)。

* 要了解有关 Runbook 执行、监视 Runbook 作业和其他技术详细信息的详细信息，请参阅[Azure 自动化 中的 Runbook 执行](automation-runbook-execution.md)。