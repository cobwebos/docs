---
title: Azure 自动化图形 Runbook SDK 概述
description: 本文介绍如何使用 Azure 自动化图形 Runbook SDK
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 07/20/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 702af8311887afc94e7127704d3377e944503324
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240339"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk"></a>使用 Azure 自动化图形 Runbook SDK

[图形 Runbook](automation-graphical-authoring-intro.md) 是帮助管理基础 Windows PowerShell 或 PowerShell 工作流代码复杂性的 Runbook。 Microsoft Azure 自动化图形创作 SDK 可让开发人员创建和编辑用于 Azure 自动化服务的图形 Runbook。 以下代码片段演示通过代码创建图形 Runbook 的基本流程。

## <a name="pre-requisites"></a>先决条件

若要开始，请将 `Microsoft.Azure.Automation.GraphicalRunbook.Model` 包导入到项目中。

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

活动由 `Orchestrator.GraphRunbook.Model` 命名空间中的以下类实现：

|类  |活动  |
|---------|---------|
|CommandActivity     | 调用 PowerShell 命令（cmdlet、函数等）。        |
|InvokeRunbookActivity     | 调用另一个内联 Runbook。        |
|JunctionActivity     | 等待所有传入的分支完成。        |
|WorkflowScriptActivity     | 在 Runbook 的上下文中执行 PowerShell 或 PowerShell 工作流代码的块（具体取决于 Runbook 类型）。 这是一个强大的工具，但不要滥用：UI 将以文本形式显示此脚本块；执行引擎会将提供的块视为黑盒，并且不会尝试分析其内容，但基本语法检查除外。 如果只需调用单个 PowerShell 命令，请优先使用 CommandActivity。        |

> [!NOTE]
> 不要从提供的类派生自己的活动：Azure 自动化无法对自定义活动类型使用 Runbook。

必须以值描述符而不是直接值的形式提供 CommandActivity 和 InvokeRunbookActivity 参数。 值描述符指定如何生成实际参数值。 目前提供以下值描述符：


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
> 不要从提供的类派生自己的值描述符：Azure 自动化无法对自定义值描述符类型使用 Runbook。

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

可将此字符串保存到扩展名为 **.graphrunbook** 的文件，并可将此文件导入到 Azure 自动化中。
可在将来的 `Orchestrator.GraphRunbook.Model.dll` 版本中更改序列化格式。 我们保证后向兼容性：使用旧版 `Orchestrator.GraphRunbook.Model.dll` 序列化的 Runbook 可由新版反序列化。 不保证前向兼容性：使用新版序列化的 Runbook 不一定能够由旧版反序列化。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure 自动化中的图形 Runbook，请参阅[图形创作简介](automation-graphical-authoring-intro.md)
