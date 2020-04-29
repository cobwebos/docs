---
title: Azure 自动化 Runbook 类型
description: 介绍可以在 Azure 自动化中使用的不同类型的 runbook，以及确定使用哪种类型的注意事项。
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 1ac6347bd8e723f356da4803da54a6ea45a4a71a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81535513"
---
# <a name="azure-automation-runbook-types"></a>Azure 自动化 Runbook 类型

Azure 自动化流程自动化服务支持多种 runbook 类型，如下表中所述。 若要了解有关流程自动化环境的信息，请参阅[在 Azure 自动化中执行 Runbook](automation-runbook-execution.md)。

| 类型 | 说明 |
|:--- |:--- |
| [图形](#graphical-runbooks)|基于 Windows PowerShell 的图形 runbook，完全在 Azure 门户的图形编辑器中创建和编辑。 |
| [图形 PowerShell 工作流](#graphical-runbooks)|基于 Windows PowerShell 工作流的图形 runbook，完全在 Azure 门户的图形编辑器中创建和编辑。 |
| [PowerShell](#powershell-runbooks) |基于 Windows PowerShell 脚本的文本 runbook。 |
| [PowerShell 工作流](#powershell-workflow-runbooks)|基于 Windows PowerShell 工作流脚本的文本 runbook。 |
| [Python](#python-runbooks) |基于 Python 脚本的文本 runbook。 |

确定要用于特定 runbook 的类型时，请考虑以下注意事项。

* 不能将 runbook 从图形转换为文本类型，也不能将其转换为其他方法。
* 将不同类型的 runbook 用作子 runbook 时存在一些限制。 有关详细信息，请参阅 [Azure 自动化中的子 Runbook](automation-child-runbooks.md)。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可参阅[如何更新 Azure 自动化中的 Azure PowerShell 模块](automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="graphical-runbooks"></a>图形 Runbook

可以在 Azure 门户中使用图形编辑器创建和编辑图形 Runbook 与图形 PowerShell 工作流 Runbook。 但是，不能通过其他工具创建或编辑此类型的 runbook。 图形 runbook 的主要功能：

* 可以导出到自动化帐户中的文件，然后导入到另一个自动化帐户。 
* 生成 PowerShell 代码。 
* 可以在导入过程中从图形 PowerShell 工作流 runbook 转换为。 

### <a name="advantages"></a>优点

* 使用视觉的 "插入链接-配置创作模型"。
* 专注于数据在整个过程中的流动方式。
* 直观展示管理流程。
* 包括其他 runbook 作为子 runbook，以创建高级工作流。
* 鼓励模块化编程。

### <a name="limitations"></a>限制

* 无法在 Azure 门户之外创建或编辑。
* 可能需要包含 PowerShell 代码的代码活动，才能执行复杂逻辑。
* 不能转换为其中一种[文本格式](automation-runbook-types.md)，也无法将文本 runbook 转换为图形格式。 
* 无法查看或直接编辑图形工作流创建的 PowerShell 代码。 您可以查看在任何代码活动中创建的代码。
* 无法在 Linux 混合 Runbook 辅助角色上运行 runbook。 请参阅[使用混合 Runbook 辅助角色使数据中心或云中的资源自动化](automation-hybrid-runbook-worker.md)。

## <a name="powershell-runbooks"></a>PowerShell Runbook

基于 Windows PowerShell 的 PowerShell Runbook。 可以在 Azure 门户中使用文本编辑器直接编辑 Runbook 的代码。  还可以使用任何脱机文本编辑器，以便[导入 Runbook](manage-runbooks.md) 到 Azure 自动化中。

### <a name="advantages"></a>优点

* 通过 PowerShell 代码来实现所有复杂的逻辑，没有 PowerShell 工作流的各种额外的复杂操作。
* 比 PowerShell 工作流 runbook 的启动速度更快，因为它们不需要在运行前进行编译。
* 在 Azure 和适用于 Windows 和 Linux 的混合 Runbook 辅助角色上运行。

### <a name="limitations"></a>限制

* 必须熟悉 PowerShell 脚本。
* Runbook 不能使用[并行处理](automation-powershell-workflow.md#parallel-processing)来并行执行多个操作。
* 如果出现错误，runbook 不能使用[检查点](automation-powershell-workflow.md#checkpoints)恢复 runbook。
* 可以使用[AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) cmdlet （创建新作业）将 PowerShell 工作流 runbook 和图形 runbook 仅包含为子 runbook。

### <a name="known-issues"></a>已知问题

以下是 PowerShell runbook 当前已知的问题：

* PowerShell runbook 无法检索具有 null 值的未加密的[变量资产](automation-variables.md)。
* PowerShell Runbook 无法检索名称中包含  的变量资产`*~*`。
* 在 PowerShell runbook 中，循环中的[进程](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-process?view=powershell-7)内操作在大约80次迭代后可能会崩溃。
* 如果 PowerShell runbook 尝试一次性将大量数据写入到输出流，则它可能会失败。 通常，可以通过使 runbook 仅输出处理大型对象所需的信息来解决此问题。 例如，你可以让 cmdlet `Get-Process`只输出所需的参数，而不是使用而没有任何限制`Get-Process | Select ProcessName, CPU`。

## <a name="powershell-workflow-runbooks"></a>PowerShell 工作流 Runbook

PowerShell 工作流 runbook 是基于[Windows PowerShell 工作流](automation-powershell-workflow.md)的文本 runbook。 可以在 Azure 门户中使用文本编辑器直接编辑 Runbook 的代码。 还可以使用任何脱机文本编辑器，以便[导入 Runbook](manage-runbooks.md) 到 Azure 自动化中。

### <a name="advantages"></a>优点

* 通过 PowerShell 工作流代码实现所有复杂的逻辑。
* 如果出现错误，请使用[检查点](automation-powershell-workflow.md#checkpoints)恢复操作。
* 使用[并行处理](automation-powershell-workflow.md#parallel-processing)并行执行多个操作。
* 可以包括其他图形 runbook 和 PowerShell 工作流 runbook 作为子 runbook，以创建高级工作流。

### <a name="limitations"></a>限制

* 必须熟悉 PowerShell 工作流。
* Runbook 必须处理 PowerShell 工作流的额外复杂性，例如[反序列化的对象](automation-powershell-workflow.md#code-changes)。
* Runbook 的启动时间比 PowerShell runbook 长，因为必须先编译 runbook，然后才能运行。
* 使用`Start-AzAutomationRunbook` cmdlet 只能将 PowerShell runbook 作为子 runbook 包括在内。
* Runbook 无法在 Linux 混合 Runbook 辅助角色上运行。

## <a name="python-runbooks"></a>Python Runbook

在 Python 2 下编译 Python runbook。 可以在 Azure 门户中使用文本编辑器直接编辑 Runbook 的代码。 你还可以使用脱机文本编辑器，并将[runbook 导入](manage-runbooks.md)到 Azure 自动化中。

### <a name="advantages"></a>优点

* 使用强大的 Python 库。
* 可在 Azure 或 Linux 混合 Runbook 辅助角色上运行。 在安装了 [python2.7](https://www.python.org/downloads/release/latest/python2) 的情况下，支持 Windows 混合 Runbook 辅助角色。

### <a name="limitations"></a>限制

* 必须熟悉 Python 脚本。
* 目前仅支持 Python 2。 任何 Python 3 特定函数都将失败。
* 若要使用第三方库，必须将[包导入](python-packages.md)到自动化帐户中。

## <a name="next-steps"></a>后续步骤

* 若要详细了解图形 runbook 创作，请参阅[Azure 自动化中的图形创作](automation-graphical-authoring-intro.md)。
* 若要了解 runbook 的 PowerShell 和 PowerShell 工作流之间的差异，请参阅[了解 Windows PowerShell 工作流](automation-powershell-workflow.md)。
* 有关如何创建或导入 runbook 的详细信息，请参阅[在 Azure Automation 中管理 runbook](manage-runbooks.md)。
* 若要了解有关 PowerShell 的详细信息，包括语言参考和学习模块，请参阅[Powershell 文档](https://docs.microsoft.com/powershell/scripting/overview)。
