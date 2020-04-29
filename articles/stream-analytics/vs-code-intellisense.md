---
title: 适用于 Visual Studio Code 的 Azure 流分析工具中的 IntelliSense
description: 本文介绍如何使用 Azure 流分析工具中的 IntelliSense 功能进行 Visual Studio Code。
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 4/11/2020
ms.topic: conceptual
ms.openlocfilehash: 25ece3174cd65df9c665888d913cf6b9029904b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81394392"
---
# <a name="intellisense-in-azure-stream-analytics-tools-for-visual-studio-code"></a>适用于 Visual Studio Code 的 Azure 流分析工具中的 IntelliSense

IntelliSense 适用于适用于[VS Code 的 Azure 流分析工具](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa&ssr=false#overview)中的[流分析查询语言](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?toc=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fstream-analytics%2Ftoc.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fbread%2Ftoc.json)。 IntelliSense 是一种代码补全辅助工具，它包含许多功能：成员列表、参数信息、快速信息和完成单词。 IntelliSense 功能有时由其他名称（如 "代码完成"、"内容助手" 和 "代码提示"）调用。

![IntelliSense 演示](./media/vs-code-intellisense/intellisense.gif)

## <a name="intellisense-features"></a>IntelliSense 功能

适用于 VS Code 的流分析工具中的 IntelliSense 功能由语言服务提供支持。 语言服务会分析你的源代码，并基于语言语义提供智能代码完成。 如果语言服务知道可能的完成，则在您键入时，IntelliSense 建议将会弹出。 如果继续键入，则筛选成员列表（如变量和方法），以仅包含包含所键入字符的成员。 当你按`Tab`或`Enter`键时，IntelliSense 将插入你选择的成员。

您可以通过键入一个触发器字符（例如点字符`.`），在任何编辑器窗口中触发 IntelliSense。

![intellisense 自动完成](./media/vs-code-intellisense/auto-completion.gif)

> [!TIP]
> 建议小组件支持 CamelCase 筛选。 可在方法名称中键入大写，以限制建议。 例如，"cra" 将快速显示 "createApplication"。

### <a name="types-of-completions"></a>完成类型

用于 VS Code IntelliSense 的流分析工具提供了不同类型的完成，包括语言服务器建议、代码段和基于 word 的简单文本完成。

|       |         |       |
| ----- | ------- | ----- |
| 关键字 | `keyword`
| 函数 | `build-in function`, `user defined function`  |
| 数据集名称| `input`, `output`, `intermediate result set`|
| 数据集列名|`input`, `intermediate result set`|
|

#### <a name="name-completion"></a>名称完成

除了关键字自动完成，用于 VS Code 的流分析工具还会读取作业输入和输出名称的列表，以及在配置数据源中的列的名称。 扩展会记住此信息以提供名称完成功能，这些功能可用于输入带有几个击键的语句：

编码时，无需让编辑器对作业输入名称、输出名称和列名执行搜索。 您可以保留您的上下文、查找所需的信息、直接在代码中插入元素，并让 IntelliSense 为您完成键入内容。

请注意，需要配置本地输入或实时输入，并保存配置文件才能使用名称完成。

![名称完成](./media/vs-code-intellisense/name-completion.gif)

### <a name="parameter-info"></a>参数信息

IntelliSense**参数信息**选项打开一个参数列表，其中提供了有关函数所需的参数的数量、名称和类型的信息。 以粗体显示的参数指示键入函数时所需的下一个参数。

对于嵌套函数，也会显示这一参数列表。 如果将一个函数键入为另一个函数的参数，则参数列表将显示内部函数的参数。 内部函数参数列表完成后，参数列表会还原为显示外部函数参数。

![参数信息](./media/vs-code-intellisense/parameter-info.gif)

### <a name="quick-info"></a>快速信息

如语言服务所提供的，你可以在代码中查看每个标识符的**快速信息**。 标识符的一些示例包括输入、输出、中间结果集或函数。 将鼠标指针移到标识符上时，它的声明会显示在弹出窗口中。 显示输入的属性和数据架构（如果已配置）和中间数据集。

![快速信息](./media/vs-code-intellisense/quick-info.gif)

## <a name="troubleshoot-intellisense"></a>IntelliSense 故障排除

此问题是由于缺少提供数据的输入配置引起的。 可以检查是否已正确配置[本地输入](visual-studio-code-local-run.md#define-a-local-input)或[实时输入](visual-studio-code-local-run-live-input.md#define-a-live-stream-input)。

## <a name="next-steps"></a>后续步骤

* [快速入门：在 Visual Studio Code 中创建 Azure 流分析作业](quick-create-vs-code.md)
* [通过 Visual Studio Code 使用示例数据在本地测试流分析查询](visual-studio-code-local-run.md)
* [使用 Visual Studio Code 通过实时流输入在本地测试流分析查询](visual-studio-code-local-run-live-input.md)
