<!-- not suitable for Mooncake -->

<properties 
   pageTitle="Azure 自动化 Runbook 类型"
   description="说明你可以在 Azure 自动化中使用的不同 Runbook 类型，以及在确定要使用的具体类型时需要考虑的注意事项。"
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
	ms.service="automation"
	ms.date="11/13/2015"
	wacn.date=""/>

# Azure 自动化 Runbook 类型

Azure 自动化支持三种类型的 Runbook，这些 Runbook 在下表中进行了简要描述。以下各个部分提供了每种类型的详细信息，包括如何选择在何时使用每种类型。


| 类型 | 说明 |
|:---|:---|
| [PowerShell 工作流](#powershell-workflow-runbooks) | 基于 Windows PowerShell 工作流的文本 Runbook。 |

## PowerShell 工作流 Runbook

PowerShell 工作流 Runbook 是基于 [Windows PowerShell 工作流](/documentation/articles/automation-powershell-workflow)的文本 Runbook。你可以在 Azure 管理门户中使用文本编辑器直接编辑 Runbook 的代码。你还可以使用任何脱机文本编辑器，以便[导入 Runbook](/documentation/articles/automation-creating-importing-runbook) 到 Azure 自动化中。

### 优点

- 通过 PowerShell 工作流代码实现所有复杂的逻辑。
- 在发生错误时，使用[检查点](/documentation/articles/automation-powershell-workflow#checkpoints)恢复 Runbook。
- 使用[并行处理](/documentation/articles/automation-powershell-workflow#parallel-processing)并行执行多个操作。
- 可以以子 Runbook 的形式包括其他 PowerShell 工作流 Runbook，以便创建高级工作流。


### 限制

- 作者必须熟悉 PowerShell 工作流。
- Runbook 还必须处理与 PowerShell 工作流相关的其他复杂问题，例如[反序列化的对象](/documentation/articles/automation-powershell-workflow#code-changes)。
- 与 PowerShell Runbook 相比，Runbook 需要更长时间来启动，因为它在运行前需要进行编译。
- 只能通过用于创建新作业的 Start-AzureAutomationRunbook cmdlet 以子 Runbook 的形式包括 PowerShell Runbook。

## 注意事项

在确定特定 Runbook 需要使用哪种类型时，应考虑以下各种额外的注意事项。

- 不能将 Runbook 从一种类型转换为另一种类型。
- 将不同类型的 Runbook 用作子 Runbook 存在各种限制。有关详细信息，请参阅 [Azure 自动化中的子 Runbook](/documentation/articles/automation-child-runbooks)。



  
## 相关文章

- [学习 Windows PowerShell 工作流](/documentation/articles/automation-powershell-workflow)
- [创建或导入 Runbook](/documentation/articles/automation-creating-importing-runbook)
- [创建或导入 Runbook](/documentation/articles/automation-creating-importing-runbook)

<!---HONumber=Mooncake_1207_2015-->