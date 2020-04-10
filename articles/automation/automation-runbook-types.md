---
title: Azure 自动化 Runbook 类型
description: 介绍可以在 Azure 自动化中使用的不同 Runbook 类型，以及在确定要使用的具体类型时需要考虑的注意事项。
services: automation
ms.subservice: process-automation
ms.date: 03/05/2019
ms.topic: conceptual
ms.openlocfilehash: 10f9c829207dc17fa39711e273ae4fbfab3ecbcd
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010165"
---
# <a name="azure-automation-runbook-types"></a>Azure 自动化 Runbook 类型

Azure 自动化过程自动化服务支持多种类型的 Runbook。 下表简要定义了这些类型，并在以下各节中进行了更详细的描述。 要了解流程自动化环境，请参阅[Azure 自动化 中的 Runbook 执行](automation-runbook-execution.md)。

| 类型 | 说明 |
|:--- |:--- |
| [图形](#graphical-runbooks)|基于 Windows PowerShell 的图形运行簿，并在 Azure 门户中的图形编辑器中完全创建和编辑。 |
| [图形 PowerShell 工作流](#graphical-runbooks)|基于 Windows PowerShell 工作流的图形运行簿，并在 Azure 门户中的图形编辑器中完全创建和编辑。 |
| [PowerShell](#powershell-runbooks) |基于 Windows PowerShell 脚本的文本运行簿。 |
| [PowerShell 工作流](#powershell-workflow-runbooks)|基于 Windows PowerShell 工作流脚本的文本运行簿。 |
| [Python](#python-runbooks) |基于 Python 脚本的文本运行簿。 |

## <a name="graphical-runbooks"></a>图形 Runbook

您可以使用 Azure 门户中的图形编辑器创建和编辑图形和图形 PowerShell 工作流 Runbook。 但是，您无法使用其他工具创建或编辑这种类型的 Runbook。

图形运行簿具有以下主要功能：

* 可以导出到自动化帐户中的文件，然后导入到另一个自动化帐户。 
* 生成 PowerShell 代码。 
* 可以在导入期间转换为图形 PowerShell 工作流运行簿或从该功能手册转换。 

### <a name="advantages"></a>优点

* 使用可视插入链接配置创作模型。
* 重点介绍数据如何流经流程。
* 直观地表示管理流程。
* 将其他 Runbook 作为子 Runbook，以创建高级工作流。
* 鼓励模块化编程。

### <a name="limitations"></a>限制

* 无法在 Azure 门户之外创建或编辑。
* 可能需要包含 PowerShell 代码的代码活动来执行复杂的逻辑。
* 不能转换为其中一种[文本格式](automation-runbook-types.md)，也不能将文本运行簿转换为图形格式。 
* 不允许查看或直接编辑图形工作流创建的 PowerShell 代码。 您可以查看在任何代码活动中创建的代码。
* 不在 Linux 混合 Runbook 工作线程上运行。 请参阅[通过使用混合 Runbook 辅助角色自动执行数据中心或云中的资源](automation-hybrid-runbook-worker.md)。

## <a name="powershell-runbooks"></a>PowerShell Runbook

基于 Windows PowerShell 的 PowerShell Runbook。 可以在 Azure 门户中使用文本编辑器直接编辑 Runbook 的代码。  还可以使用任何脱机文本编辑器，以便[导入 Runbook](manage-runbooks.md) 到 Azure 自动化中。

### <a name="advantages"></a>优点

* 通过 PowerShell 代码来实现所有复杂的逻辑，没有 PowerShell 工作流的各种额外的复杂操作。
* 与 PowerShell 工作流 Runbook 相比，Runbook 的启动速度更快，因为它在运行前不需要经过编译。
* 可以在 Azure 中运行，也可以在 Linux 和 Windows 混合 Runbook 工作簿上运行

### <a name="limitations"></a>限制

* 必须熟悉 PowerShell 脚本。
* 无法使用[并行处理](automation-powershell-workflow.md#parallel-processing)并行执行多个操作。
* 出现错误时，无法使用[检查点](automation-powershell-workflow.md#checkpoints)恢复 Runbook。
* 只能通过用于创建新作业的 Start-AzureAutomationRunbook cmdlet 以子 Runbook 的形式包括 PowerShell 工作流 Runbook 和图形 Runbook。

### <a name="known-issues"></a>已知问题

以下是当前 PowerShell Runbook 的已知问题。

* PowerShell Runbook 无法检索未加密且值为 null 的[变量资产](automation-variables.md)。
* PowerShell Runbook 无法检索名称中包含 ~ 的[变量资产](automation-variables.md)**。
* 在 PowerShell Runbook 中，处于循环状态的 Get-Process 在经历大约 80 次迭代后可能会崩溃。
* 如果 PowerShell Runbook 尝试一次性将大量数据写入输出流中，则可能会发生故障。   通常情况下，在处理大型对象时，可以只输出所需信息，从而避免出现这种问题。  例如，不需要输出 *Get-Process* 这样的内容，只需通过 *Get-Process | Select ProcessName, CPU* 输出所需字段即可。

## <a name="powershell-workflow-runbooks"></a>PowerShell 工作流 Runbook

PowerShell 工作流 Runbook 是基于 [Windows PowerShell 工作流](automation-powershell-workflow.md)的文本 Runbook。  可以在 Azure 门户中使用文本编辑器直接编辑 Runbook 的代码。  还可以使用任何脱机文本编辑器，以便[导入 Runbook](manage-runbooks.md) 到 Azure 自动化中。

### <a name="advantages"></a>优点

* 通过 PowerShell 工作流代码实现所有复杂的逻辑。
* 出现错误时，使用[检查点](automation-powershell-workflow.md#checkpoints)恢复 Runbook。
* 使用[并行处理](automation-powershell-workflow.md#parallel-processing)并行执行多个操作。
* 能够以子 Runbook 的形式包括其他图形 Runbook 和 PowerShell 工作流 Runbook，以创建高级工作流。

### <a name="limitations"></a>限制

* 作者必须熟悉 PowerShell 工作流。
* Runbook 还必须处理与 PowerShell 工作流相关的其他复杂问题，例如[反序列化的对象](automation-powershell-workflow.md#code-changes)。
* 与 PowerShell Runbook 相比，Runbook 需要更长时间来启动，因为它在运行前需要进行编译。
* 只能通过用于创建新作业的 Start-AzureAutomationRunbook cmdlet 以子 Runbook 的形式包括 PowerShell Runbook。
* 无法在 Linux 混合 Runbook 工作线程上运行。

## <a name="python-runbooks"></a>Python Runbook

在 Python 2 下编译 Python runbook。 可以在 Azure 门户中直接使用文本编辑器编辑 runbook 的代码，也可以使用任何脱机文本编辑器并将 [runbook 导入](manage-runbooks.md)到 Azure 自动化中。

### <a name="advantages"></a>优点

* 利用强大的 Python 库。
* 可以在 Azure 中运行，也可以在两个 Linux 混合 Runbook 工作簿上运行。 在安装了 [python2.7](https://www.python.org/downloads/release/latest/python2) 的情况下，支持 Windows 混合 Runbook 辅助角色。

### <a name="limitations"></a>限制

* 必须熟悉 Python 脚本。
* 目前仅支持 Python 2，这意味着特定于 Python 3 的函数将会失败。
* 要使用第三方库，必须[将包导入](python-packages.md)自动化帐户才能使用。

## <a name="considerations"></a>注意事项

在确定特定 Runbook 需要使用的类型时，请额外注意以下事项。

* 无法将 runbook 从图形转换为文本类型，反之亦然。
* 将不同类型的 Runbook 用作子 Runbook 存在各种限制。 有关详细信息，请参阅 [Azure 自动化中的子 Runbook](automation-child-runbooks.md)。

## <a name="next-steps"></a>后续步骤

* 若要详细了解图形 Runbook 创作，请参阅 [Azure 自动化中的图形创作](automation-graphical-authoring-intro.md)
* 若要了解 Runbook 的 PowerShell 和 PowerShell 工作流之间的差异，请参阅[了解 Windows PowerShell 工作流](automation-powershell-workflow.md)
* 有关如何创建或导入 Runbook 的详细信息，请参阅[创建或导入 Runbook](manage-runbooks.md)
* 有关 PowerShell 的详细信息（包括语言参考和学习模块），请参阅 [PowerShell 文档](https://docs.microsoft.com/powershell/scripting/overview)。
