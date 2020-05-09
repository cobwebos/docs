---
title: 使用 Azure 自动化图形 runbook SDK
description: 本文介绍如何使用 Azure 自动化图形 runbook SDK。
services: automation
ms.subservice: process-automation
ms.date: 07/20/2018
ms.topic: conceptual
ms.openlocfilehash: 886ce03b6e107d871879ff40bdc5de9ceb97c7c3
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690738"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk"></a>使用 Azure 自动化图形 runbook SDK

[图形 runbook](automation-graphical-authoring-intro.md)有助于管理底层 Windows PowerShell 或 PowerShell 工作流代码的复杂性。 Microsoft Azure 自动化图形创作 SDK 使开发人员能够创建和编辑图形 runbook，以便与 Azure 自动化一起使用。 本文介绍了通过代码创建图形 runbook 的基本步骤。

## <a name="prerequisites"></a>先决条件

将`Orchestrator.GraphRunbook.Model.dll`包导入到项目中。

## <a name="create-a-runbook-object-instance"></a>创建 Runbook 对象实例

引用 `Orchestrator.GraphRunbook.Model` 程序集，并创建 `Orchestrator.GraphRunbook.Model.GraphRunbook` 类的实例：

```csharp
using Orchestrator.GraphRunbook.Model;
using Orchestrator.GraphRunbook.Model.ExecutableView;

var runbook = new GraphRunbook();
```

## <a name="add-runbook-parameters"></a>添加 Runbook 参数

实例化 `Orchestrator.GraphRunbook.Model.Parameter` 对象，并将其添加到 Runbook：

```csharp
runbook.AddParameter(
 new Parameter("YourName") {
  TypeName = typeof(string).FullName,
   DefaultValue = "World",
   Optional = true
 });

runbook.AddParameter(
 new Parameter("AnotherParameter") {
  TypeName = typeof(int).FullName, ...
 });
```

## <a name="add-activities-and-links"></a>添加活动和链接

实例化相应类型的活动，并将其添加到 Runbook：

```csharp
var writeOutputActivityType = new CommandActivityType {
 CommandName = "Write-Output",
  ModuleName = "Microsoft.PowerShell.Utility",
 InputParameterSets = new [] {
  new ParameterSet {
   Name = "Default",
    new [] {
     new Parameter("InputObject"), ...
    }
  },
  ...
 }
};

var outputName = runbook.AddActivity(
 new CommandActivity("Output name", writeOutputActivityType) {
  ParameterSetName = "Default",
   Parameters = new ActivityParameters {
    {
     "InputObject",
     new RunbookParameterValueDescriptor("YourName")
    }
   },
   PositionX = 0,
   PositionY = 0
 });

var initializeRunbookVariable = runbook.AddActivity(
 new WorkflowScriptActivity("Initialize variable") {
  Process = "$a = 0",
   PositionX = 0,
   PositionY = 100
 });
```

活动由`Orchestrator.GraphRunbook.Model`命名空间中的以下类实现。

|类  |活动  |
|---------|---------|
|CommandActivity     | 调用 PowerShell 命令（cmdlet、函数等）。        |
|InvokeRunbookActivity     | 调用另一个内联 Runbook。        |
|JunctionActivity     | 等待所有传入的分支完成。        |
|WorkflowScriptActivity     | 在 Runbook 的上下文中执行 PowerShell 或 PowerShell 工作流代码的块（具体取决于 Runbook 类型）。 这是一个强大的工具，但不要滥用：UI 将以文本形式显示此脚本块；执行引擎会将提供的块视为黑盒，并且不会尝试分析其内容，但基本语法检查除外。 如果只需调用单个 PowerShell 命令，请优先使用 CommandActivity。        |

> [!NOTE]
> 不要从提供的类中派生您自己的活动。 Azure Automation 不能将 runbook 与自定义活动类型一起使用。

必须提供`CommandActivity`和`InvokeRunbookActivity`参数作为值描述符，而不是直接值。 值描述符指定如何生成实际参数值。 目前提供以下值描述符：


|描述符  |定义  |
|---------|---------|
|ConstantValueDescriptor     | 引用硬编码的常量值。        |
|RunbookParameterValueDescriptor     | 按名称引用 Runbook 参数。        |
|ActivityOutputValueDescriptor     | 引用上游活动输出，使一个活动能够“订阅”另一个活动生成的数据。        |
|AutomationVariableValueDescriptor     | 按名称引用自动化变量资产。         |
|AutomationCredentialValueDescriptor     | 按名称引用自动化证书资产。        |
|AutomationConnectionValueDescriptor     | 按名称引用自动化连接资产。        |
|PowerShellExpressionValueDescriptor     | 指定自由格式的 PowerShell 表达式，调用活动之后，会紧接着计算该表达式。  <br/>这是一个强大的工具，但不要滥用：UI 将以文本形式显示此表达式；执行引擎会将提供的块视为黑盒，并且不会尝试分析其内容，但基本语法检查除外。 如果可能，请优先使用更具体的值描述符。      |

> [!NOTE]
> 不要从提供的类中派生你自己的值说明符。 Azure Automation 不能将 runbook 与自定义值描述符类型一起使用。

实例化连接活动的链接，并将其添加到 Runbook：

```csharp
runbook.AddLink(new Link(activityA, activityB, LinkType.Sequence));

runbook.AddLink(
 new Link(activityB, activityC, LinkType.Pipeline) {
  Condition = string.Format("$ActivityOutput['{0}'] -gt 0", activityB.Name)
 });
```

## <a name="save-the-runbook-to-a-file"></a>将 Runbook 保存到文件

使用 `Orchestrator.GraphRunbook.Model.Serialization.RunbookSerializer` 将 Runbook 序列化成字符串：

```csharp
var serialized = RunbookSerializer.Serialize(runbook);
```

可以将此字符串保存到扩展名为 **.graphrunbook**的文件中。 相应的 runbook 可以导入到 Azure 自动化中。
序列化格式在的未来版本中可能会`Orchestrator.GraphRunbook.Model.dll`更改。 我们保证后向兼容性：使用旧版 `Orchestrator.GraphRunbook.Model.dll` 序列化的 Runbook 可由新版反序列化。 不保证前向兼容性：使用新版序列化的 Runbook 不一定能够由旧版反序列化。

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 自动化中的图形 runbook 的详细信息，请参阅[图形创作简介](automation-graphical-authoring-intro.md)。