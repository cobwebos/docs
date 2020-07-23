---
title: 适用于 Visual Studio Code 的 Azure 流分析工具中的 IntelliSense
description: 本文介绍如何使用适用于 Visual Studio Code 的 Azure 流分析工具中的 IntelliSense 功能。
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 4/11/2020
ms.topic: how-to
ms.openlocfilehash: df6e7435d072cc88e82f3dc9a323a5b21145c432
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86037250"
---
# <a name="intellisense-in-azure-stream-analytics-tools-for-visual-studio-code"></a>适用于 Visual Studio Code 的 Azure 流分析工具中的 IntelliSense

IntelliSense 可用于[适用于 VS Code 的 Azure 流分析工具](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa&ssr=false#overview)中的[流分析查询语言](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?toc=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fstream-analytics%2Ftoc.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fbread%2Ftoc.json)。 IntelliSense 是一个代码完成辅助插件，其中包括许多功能：“列出成员”、“参数信息”、“快速信息”和“完成单词”。 IntelliSense 功能有时也称作“代码完成”、“内容辅助”和“代码提示”等。

![IntelliSense 演示](./media/vs-code-intellisense/intellisense.gif)

## <a name="intellisense-features"></a>IntelliSense 功能

适用于 VS Code 的流分析工具中的 IntelliSense 功能由语言服务提供支持。 语言服务会分析源代码，并基于语言语义提供智能代码完成。 如果语言服务知道可以补全内容，则在你键入时，会弹出 IntelliSense 建议。 如果你继续键入，则会筛选成员列表（例如变量和方法），以便仅显示包含所键入字符的成员。 按 `Tab` 或 `Enter` 键时，IntelliSense 会插入你选择的成员。

可以通过键入触发器字符（例如点字符 `.`）在任何编辑器窗口中触发 IntelliSense。

![IntelliSense 自动完成](./media/vs-code-intellisense/auto-completion.gif)

> [!TIP]
> “建议”小组件支持 CamelCase 筛选。 可以在方法名称中键入大写字母以限制建议。 例如，键入“cra”会快速显示“createApplication”。

### <a name="types-of-completions"></a>完成类型

适用于 VS Code 的流分析工具中的 IntelliSense 提供不同类型的完成，包括语言服务器建议、代码片段和基于单词的简单文本完成。

|Completion     |  类型       |
| ----- | ------- |
| 关键字 | `keyword`
| 函数 | `build-in function`, `user defined function`  |
| 数据集名称| `input`, `output`, `intermediate result set`|
| 数据集列名称|`input`, `intermediate result set`|

#### <a name="name-completion"></a>名称完成

除了关键字自动完成以外，适用于 VS Code 的流分析工具还会读取作业输入和输出名称的列表，以及在配置数据源时从中读取列名称。 扩展会记住此信息以提供名称完成功能，使用这些功能只需敲击几下按键就能输入语句：

编写代码时，无需退出编辑器即可按作业输入名称、输出名称和列名称执行搜索。 可以保留上下文，查找所需的信息，直接在代码中插入元素，然后让 IntelliSense 自动完成键入内容。

请注意，需要配置本地输入或实时输入并保存配置文件，才能使用名称完成。

![名称完成](./media/vs-code-intellisense/name-completion.gif)

### <a name="parameter-info"></a>参数信息

使用 IntelliSense 的“参数信息”选项可打开一个参数列表，其中提供了有关函数所需参数数目、参数名称和参数类型的信息  。 以粗体显示的参数指示键入某个函数时所需的下一个参数。

对于嵌套函数，也会显示这一参数列表。 如果将一个函数键入为另一个函数的参数，则参数列表将显示内部函数的参数。 内部函数参数列表完成后，参数列表会还原为显示外部函数参数。

![参数信息](./media/vs-code-intellisense/parameter-info.gif)

### <a name="quick-info"></a>快速信息

此功能由语言服务提供。可以查看代码中每个标识符的“快速信息”  。 标识符的一些示例为输入、输出、中间结果集或函数。 将鼠标指针移到标识符上时，该标识符的声明便会显示在一个弹出窗口中。 将会显示输入的属性和数据架构（如果已配置）及中间数据集。

![快速信息](./media/vs-code-intellisense/quick-info.gif)

## <a name="troubleshoot-intellisense"></a>排查 IntelliSense 问题

此问题的原因是缺少提供数据的输入配置。 可以检查是否已正确配置[本地输入](visual-studio-code-local-run.md#define-a-local-input)或[实时输入](visual-studio-code-local-run-live-input.md#define-a-live-stream-input)。

## <a name="next-steps"></a>后续步骤

* [快速入门：在 Visual Studio Code 中创建 Azure 流分析作业](quick-create-vs-code.md)
* [通过 Visual Studio Code 使用示例数据在本地测试流分析查询](visual-studio-code-local-run.md)
* [使用 Visual Studio Code 针对实时传送流输入在本地测试流分析查询](visual-studio-code-local-run-live-input.md)
