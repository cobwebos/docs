---
title: 用于可视化工作室代码的 Azure 流分析工具中的智能感知
description: 本文介绍如何在 Azure 流分析工具中使用 Visual Studio 代码的 IntelliSense 功能。
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 4/11/2020
ms.topic: conceptual
ms.openlocfilehash: 25ece3174cd65df9c665888d913cf6b9029904b1
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394392"
---
# <a name="intellisense-in-azure-stream-analytics-tools-for-visual-studio-code"></a>用于可视化工作室代码的 Azure 流分析工具中的智能感知

IntelliSense 可用于[用于用于 VS 代码的 Azure 流分析工具](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa&ssr=false#overview)中的[流分析查询语言](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?toc=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fstream-analytics%2Ftoc.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fbread%2Ftoc.json)。 IntelliSense 是一种代码补全辅助工具，它包含许多功能：成员列表、参数信息、快速信息和完成单词。 IntelliSense 功能有时被其他名称（如"代码完成"、"内容辅助"和"代码提示"）调用。

![IntelliSense 演示](./media/vs-code-intellisense/intellisense.gif)

## <a name="intellisense-features"></a>IntelliSense 功能

VS 代码流分析工具中的 IntelliSense 功能由语言服务提供支持。 语言服务分析源代码，并根据语言语义提供智能代码完成。 如果语言服务知道可能的完成情况，则在键入时弹出 IntelliSense 建议。 如果继续键入，将筛选成员列表（如变量和方法），以仅包括包含键入的字符的成员。 当您按 或`Tab``Enter`键时，IntelliSense 将插入您选择的成员。

您可以通过键入触发器字符（如点字符`.`）在任何编辑器窗口中触发 IntelliSense。

![对内感知自动完成](./media/vs-code-intellisense/auto-completion.gif)

> [!TIP]
> 建议小部件支持 CamelCase 筛选。 您可以在方法名称中键入大写字母以限制建议。 例如，"cra"会迅速提出"创建应用程序"。

### <a name="types-of-completions"></a>完成类型

VS 代码 IntelliSense 的流分析工具提供不同类型的完成，包括语言服务器建议、代码段和简单的基于单词的文本完成。

|       |         |       |
| ----- | ------- | ----- |
| Keywords | `keyword`
| 函数 | `build-in function`, `user defined function`  |
| 数据集名称| `input`, `output`, `intermediate result set`|
| 数据集列名称|`input`, `intermediate result set`|
|

#### <a name="name-completion"></a>名称完成

除了关键字自动完成外，VS Code 的流分析工具还读取作业输入和输出名称的列表，以及数据源中配置列的名称。 扩展记住此信息以提供可用于输入几个击键的语句的名称完成功能：

编码时，无需离开编辑器即可对作业输入名称、输出名称和列名称执行搜索。 您可以保留上下文、查找所需的信息、将元素直接插入到代码中，以及让 IntelliSense 为您完成键入。

请注意，您需要配置本地输入或实时输入，并保存配置文件才能使用名称完成。

![名称完成](./media/vs-code-intellisense/name-completion.gif)

### <a name="parameter-info"></a>参数信息

IntelliSense**参数信息**选项将打开一个参数列表，该列表提供有关函数所需的参数的数量、名称和类型的信息。 粗体参数表示键入函数时所需的下一个参数。

对于嵌套函数，也会显示这一参数列表。 如果将一个函数键入为另一个函数的参数，则参数列表将显示内部函数的参数。 内部函数参数列表完成后，参数列表会还原为显示外部函数参数。

![参数信息](./media/vs-code-intellisense/parameter-info.gif)

### <a name="quick-info"></a>快速信息

根据语言服务提供，您可以在代码中查看每个标识符的快速**信息**。 标识符的一些示例是输入、输出、中间结果集或函数。 将鼠标指针移到标识符上时，其声明将显示在弹出窗口中。 将显示输入的属性和数据架构（如果已配置）和中间数据集。

![快速信息](./media/vs-code-intellisense/quick-info.gif)

## <a name="troubleshoot-intellisense"></a>IntelliSense 故障排除

此问题是由缺少提供数据的输入配置引起的。 您可以检查[本地输入](visual-studio-code-local-run.md#define-a-local-input)或[实时输入](visual-studio-code-local-run-live-input.md#define-a-live-stream-input)是否配置正确。

## <a name="next-steps"></a>后续步骤

* [快速入门：在可视化工作室代码中创建 Azure 流分析作业](quick-create-vs-code.md)
* [使用 Visual Studio Code 通过示例数据在本地测试流分析查询](visual-studio-code-local-run.md)
* [使用 Visual Studio Code 通过实时流输入在本地测试流分析查询](visual-studio-code-local-run-live-input.md)
