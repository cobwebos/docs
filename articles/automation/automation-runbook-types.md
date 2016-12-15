---
title: "Azure 自动化 Runbook 类型 | Microsoft Docs"
description: "介绍可以在 Azure 自动化中使用的不同 Runbook 类型，以及在确定要使用的具体类型时需要考虑的注意事项。 "
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: 9265c975-4281-4819-a84f-d86641277f36
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/11/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: dd6b2fa2a1ca003578eaee82ef42741b9b1bf2a4
ms.openlocfilehash: b509629ba0d6bbaea3de979a4e67f5c444e5e9e0


---
# <a name="azure-automation-runbook-types"></a>Azure 自动化 Runbook 类型
Azure 自动化支持四种类型的 Runbook，这些 Runbook 在下表中进行了简要描述。  以下各个部分提供了每种类型的详细信息，包括如何选择在何时使用每种类型。

| 类型 | 说明 |
|:--- |:--- |
| [图形](#graphical-runbooks) |基于 Windows PowerShell，只能在 Azure 门户上的图形编辑器中创建和编辑。 |
| [图形 PowerShell 工作流](#graphical-runbooks) |基于 Windows PowerShell 工作流，只能在 Azure 门户上的图形编辑器中创建和编辑。 |
| [PowerShell](#powershell-runbooks) |基于 Windows PowerShell 脚本的文本 Runbook。 |
| [PowerShell 工作流](#powershell-workflow-runbooks) |基于 Windows PowerShell 工作流的文本 Runbook。 |

## <a name="graphical-runbooks"></a>图形 Runbook
使用 Azure 门户中的图形编辑器创建和编辑[图形](automation-runbook-types.md#graphical-runbooks) Runbook 和图形 PowerShell 工作流 Runbook。  你可以将其导出到某个文件，然后将其导入另一个自动化帐户，但无法使用其他工具来创建或编辑图形 Runbook。  图形 Runbook 会生成 PowerShell 代码，但你无法直接查看或修改这些代码。 无法将图形 Runbook 转换成某种[文本格式](automation-runbook-types.md)，也无法将文本 Runbook 转换成图形格式。 在导入过程中，可以将图形 runbook 转换为图形 PowerShell 工作流 runbook，反之亦然。

### <a name="advantages"></a>优点
* 直观的插入-链接-配置创作模型  
* 注重数据在执行流程期间的流动方式  
* 直观展示管理流程  
* 可以以子 Runbook 的形式包括其他 Runbook，以便创建高级工作流  
* 鼓励模块化编程  


### <a name="limitations"></a>限制
* 不能在 Azure 门户以外编辑 Runbook。
* 可能需要包含 PowerShell 代码的代码活动，才能执行复杂逻辑。
* 不能查看或直接编辑由图形工作流创建的 PowerShell 代码。 请注意，你可以查看在任何代码活动中创建的代码。

## <a name="powershell-runbooks"></a>PowerShell Runbook
基于 Windows PowerShell 的 PowerShell Runbook。  你可以在 Azure 门户中使用文本编辑器直接编辑 Runbook 的代码。  还可以使用任何脱机文本编辑器，以便[导入 Runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) 到 Azure 自动化中。

### <a name="advantages"></a>优点
* 通过 PowerShell 代码来实现所有复杂的逻辑，没有 PowerShell 工作流的各种额外的复杂操作。 
* 与图形 Runbook 或 PowerShell 工作流 Runbook 相比，Runbook 的启动速度更快，因为它在运行前不需要经过编译。

### <a name="limitations"></a>限制
* 必须熟悉 PowerShell 脚本。
* 无法使用[并行处理](automation-powershell-workflow.md#parallel-processing)来并行执行多个操作。
* 在发生错误时，无法使用[检查点](automation-powershell-workflow.md#checkpoints)恢复 Runbook。
* 只能通过用于创建新作业的 Start-AzureAutomationRunbook cmdlet 以子 Runbook 的形式包括 PowerShell 工作流 Runbook 和图形 Runbook。

### <a name="known-issues"></a>已知问题
以下是当前 PowerShell Runbook 的已知问题。

* PowerShell Runbook 无法检索未加密且值为 null 的[变量资产](automation-variables.md)。
* PowerShell Runbook 无法检索名称中包含 *~* 的[变量资产](automation-variables.md)。
* 在 PowerShell Runbook 中，处于循环状态的 Get-Process 在经历大约 80 次迭代后可能会崩溃。 
* 如果 PowerShell Runbook 尝试一次性将极大量的数据写入输出流中，则可能会发生故障。   通常情况下，在处理大型对象时，你可以只输出所需信息，从而避免出现这种问题。  例如，你不需要输出 *Get-Process* 这样的内容，只需通过 *Get-Process | Select ProcessName, CPU* 输出所需字段即可。

## <a name="powershell-workflow-runbooks"></a>PowerShell 工作流 Runbook
PowerShell 工作流 Runbook 是基于 [Windows PowerShell 工作流](automation-powershell-workflow.md)的文本 Runbook。  你可以在 Azure 门户中使用文本编辑器直接编辑 Runbook 的代码。  还可以使用任何脱机文本编辑器，以便[导入 Runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) 到 Azure 自动化中。

### <a name="advantages"></a>优点
* 通过 PowerShell 工作流代码实现所有复杂的逻辑。
* 在发生错误时，使用[检查点](automation-powershell-workflow.md#checkpoints)恢复 Runbook。
* 使用[并行处理](automation-powershell-workflow.md#parallel-processing)并行执行多个操作。
* 能够以子 Runbook 的形式包括其他图形 Runbook 和 PowerShell 工作流 Runbook，以创建高级工作流。

### <a name="limitations"></a>限制
* 作者必须熟悉 PowerShell 工作流。
* Runbook 还必须处理与 PowerShell 工作流相关的其他复杂问题，例如[反序列化的对象](automation-powershell-workflow.md#code-changes)。
* 与 PowerShell Runbook 相比，Runbook 需要更长时间来启动，因为它在运行前需要进行编译。
* 只能通过用于创建新作业的 Start-AzureAutomationRunbook cmdlet 以子 Runbook 的形式包括 PowerShell Runbook。

## <a name="considerations"></a>注意事项
在确定特定 Runbook 需要使用哪种类型时，应考虑以下各种额外的注意事项。

* 无法将 runbook 从图形转换为文本类型，反之亦然。
* 将不同类型的 Runbook 用作子 Runbook 存在各种限制。  有关详细信息，请参阅 [Azure 自动化中的子 Runbook](automation-child-runbooks.md)。

## <a name="next-steps"></a>后续步骤
* 若要详细了解图形 Runbook 创作，请参阅 [Azure 自动化中的图形创作](automation-graphical-authoring-intro.md)
* 若要了解 Runbook 的 PowerShell 和 PowerShell 工作流之间的差异，请参阅[了解 Windows PowerShell 工作流](automation-powershell-workflow.md)
* 有关如何创建或导入 Runbook 的详细信息，请参阅[创建或导入 Runbook](automation-creating-importing-runbook.md)




<!--HONumber=Nov16_HO3-->


