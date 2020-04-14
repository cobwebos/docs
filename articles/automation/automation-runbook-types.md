---
title: Azure 自动化 Runbook 类型
description: 描述可在 Azure 自动化中使用不同类型的 Runbook 以及确定要使用的类型的注意事项。
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 4e8a5d2d168b8f60b7a32a8af358c6097003de60
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261289"
---
# <a name="azure-automation-runbook-types"></a>Azure 自动化 Runbook 类型

Azure 自动化过程自动化服务支持几种类型的 Runbook，如下表中定义的。 要了解流程自动化环境，请参阅[Azure 自动化 中的 Runbook 执行](automation-runbook-execution.md)。

| 类型 | 说明 |
|:--- |:--- |
| [图形](#graphical-runbooks)|基于 Windows PowerShell 的图形运行簿，并在 Azure 门户中的图形编辑器中完全创建和编辑。 |
| [图形 PowerShell 工作流](#graphical-runbooks)|基于 Windows PowerShell 工作流的图形运行簿，并在 Azure 门户中的图形编辑器中完全创建和编辑。 |
| [PowerShell](#powershell-runbooks) |基于 Windows PowerShell 脚本的文本运行簿。 |
| [PowerShell 工作流](#powershell-workflow-runbooks)|基于 Windows PowerShell 工作流脚本的文本运行簿。 |
| [Python](#python-runbooks) |基于 Python 脚本的文本运行簿。 |

在确定要用于特定 Runbook 的类型时，请考虑以下注意事项。

* 不能将 Runbook 从图形类型转换为文本类型，或者从另一种方式转换。
* 使用不同类型的 Runbook 作为子 Runbook 时存在限制。 有关详细信息，请参阅 [Azure 自动化中的子 Runbook](automation-child-runbooks.md)。

## <a name="graphical-runbooks"></a>图形 Runbook

您可以使用 Azure 门户中的图形编辑器创建和编辑图形和图形 PowerShell 工作流 Runbook。 但是，您无法使用其他工具创建或编辑这种类型的 Runbook。 图形手册的主要功能：

* 可以导出到自动化帐户中的文件，然后导入到另一个自动化帐户。 
* 生成 PowerShell 代码。 
* 可以在导入期间转换为图形 PowerShell 工作流运行簿或从其转换。 

### <a name="advantages"></a>优点

* 使用可视插入链接配置创作模型。
* 关注数据如何流经流程。
* 直观展示管理流程。
* 将其他 Runbook 作为子 Runbook 进行创建高级工作流。
* 鼓励模块化编程。

### <a name="limitations"></a>限制

* 无法在 Azure 门户之外创建或编辑。
* 可能需要包含 PowerShell 代码的代码活动来执行复杂的逻辑。
* 无法转换为其中一种[文本格式](automation-runbook-types.md)，也无法将文本 Runbook 转换为图形格式。 
* 无法查看或直接编辑图形工作流创建的 PowerShell 代码。 您可以查看在任何代码活动中创建的代码。
* 无法在 Linux 混合 Runbook 工作线程上运行 Runbook。 请参阅[通过使用混合 Runbook 辅助角色自动执行数据中心或云中的资源](automation-hybrid-runbook-worker.md)。

## <a name="powershell-runbooks"></a>PowerShell Runbook

基于 Windows PowerShell 的 PowerShell Runbook。 可以在 Azure 门户中使用文本编辑器直接编辑 Runbook 的代码。  还可以使用任何脱机文本编辑器，以便[导入 Runbook](manage-runbooks.md) 到 Azure 自动化中。

### <a name="advantages"></a>优点

* 通过 PowerShell 代码来实现所有复杂的逻辑，没有 PowerShell 工作流的各种额外的复杂操作。
* 比 PowerShell 工作流运行簿更快地启动，因为它们在运行之前不需要编译。
* 在 Azure 和 Windows 和 Linux 的混合 Runbook 工作簿上运行。

### <a name="limitations"></a>限制

* 您必须熟悉 PowerShell 脚本。
* Runbook 不能使用[并行处理](automation-powershell-workflow.md#parallel-processing)并行执行多个操作。
* 如果出现错误，Runbook 不能使用[检查点](automation-powershell-workflow.md#checkpoints)恢复 Runbook。
* 通过使用创建新作业[的"开始-AzAutomationRunbook"cmdlet，](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0)只能将 PowerShell 工作流运行簿和图形运行簿作为子 Runbook。

### <a name="known-issues"></a>已知问题

以下是 PowerShell Runbook 的当前已知问题：

* PowerShell Runbook 无法检索具有空值的未加密[变量资产](automation-variables.md)。
* PowerShell Runbook 无法检索名称中包含  的变量资产`*~*`。
* PowerShell Runbook 中循环中的["获取过程](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-process?view=powershell-7)"操作在大约 80 次迭代后可能会崩溃。
* 如果 PowerShell Runbook 尝试一次向输出流写入大量数据，则该运行簿可能会失败。 通常，可以通过使 Runbook 输出只是处理大型对象所需的信息来解决此问题。 例如，可以让 cmdlet 输出仅具有所需的参数，而不是在 没有限制时使用`Get-Process`cmdlet`Get-Process | Select ProcessName, CPU`输出， 就像 在 中一样。

## <a name="powershell-workflow-runbooks"></a>PowerShell 工作流 Runbook

PowerShell 工作流 Runbook 是基于 [Windows PowerShell 工作流](automation-powershell-workflow.md)的文本 Runbook。 可以在 Azure 门户中使用文本编辑器直接编辑 Runbook 的代码。 还可以使用任何脱机文本编辑器，以便[导入 Runbook](manage-runbooks.md) 到 Azure 自动化中。

### <a name="advantages"></a>优点

* 通过 PowerShell 工作流代码实现所有复杂的逻辑。
* 如果出现错误[，请使用检查点](automation-powershell-workflow.md#checkpoints)恢复操作。
* 使用[并行处理](automation-powershell-workflow.md#parallel-processing)并行执行多个操作。
* 可以包括其他图形运行簿和 PowerShell 工作流运行簿作为子 Runbook 以创建高级工作流。

### <a name="limitations"></a>限制

* 您必须熟悉 PowerShell 工作流。
* Runbook 必须处理 PowerShell 工作流的额外复杂性，例如[反序列化对象](automation-powershell-workflow.md#code-changes)。
* 运行簿的启动时间比 PowerShell Runbook 要长，因为它们必须在运行前进行编译。
* 您只能使用`Start-AzAutomationRunbook`cmdlet 将 PowerShell Runbook 作为子运行簿。
* Runbook 无法在 Linux 混合 Runbook 工作线程上运行。

## <a name="python-runbooks"></a>Python Runbook

在 Python 2 下编译 Python runbook。 您可以使用 Azure 门户中的文本编辑器直接编辑 Runbook 的代码。 您还可以使用脱机文本编辑器并将[Runbook 导入](manage-runbooks.md)Azure 自动化。

### <a name="advantages"></a>优点

* 使用强大的 Python 库。
* 可以在 Azure 或 Linux 混合 Runbook 工作簿 上运行。 在安装了 [python2.7](https://www.python.org/downloads/release/latest/python2) 的情况下，支持 Windows 混合 Runbook 辅助角色。

### <a name="limitations"></a>限制

* 您必须熟悉 Python 脚本。
* 目前仅支持 Python 2。 任何 Python 3 特定的函数都失败。
* 要使用第三方库，必须[将包导入](python-packages.md)自动化帐户。

## <a name="next-steps"></a>后续步骤

* 要了解有关图形运行簿创作的更多信息，请参阅[Azure 自动化 中的图形创作](automation-graphical-authoring-intro.md)。
* 要了解 Runbook 的 PowerShell 工作流和 PowerShell 工作流之间的差异，请参阅[学习 Windows PowerShell 工作流](automation-powershell-workflow.md)。
* 有关如何创建或导入 Runbook 的详细信息，请参阅[在 Azure 自动化 中管理 Runbook。](manage-runbooks.md)
* 要了解有关 PowerShell 的更多信息，包括语言参考和学习模块，请参阅[PowerShell 文档](https://docs.microsoft.com/powershell/scripting/overview)。
