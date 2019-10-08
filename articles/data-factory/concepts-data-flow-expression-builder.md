---
title: Azure 数据工厂映射数据流表达式生成器
description: Azure 数据工厂映射数据流的表达式生成器
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: 9862866d5cddb227d9417ac15db6b8ea851507e6
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030288"
---
# <a name="mapping-data-flow-expression-builder"></a>映射数据流表达式生成器



Azure 数据工厂映射数据流中提供了表达式框，可在其中输入数据转换表达式。 请在这些框中使用数据流中的列、字段、变量、参数和函数。 若要生成表达式，请使用表达式生成器。单击转换中的表达式文本框即可启动表达式生成器。 选择转换的列时，有时还会看到“计算列”选项。 单击该选项也会启动表达式生成器。

![表达式生成器](media/data-flow/xpb1.png "表达式生成器")

表达式生成器工具默认使用文本编辑器选项。 自动填写功能从整个 Azure 数据工厂数据流对象模型读取数据，并支持语法检查和突出显示。

![表达式生成器自动填写](media/data-flow/expb1.png "表达式生成器自动填写")

## <a name="build-schemas-in-output-schema-pane"></a>在输出架构窗格中生成架构

![添加复杂列](media/data-flow/complexcolumn.png "添加列")

在左侧的输出架构窗格中，你将看到正在修改的列并将其添加到你的架构。 可在此处以交互方式生成简单复杂的数据结构。 使用 "添加列" 添加其他字段，并使用 "添加 subcolumn" 生成层次结构。

![添加 subcolumn](media/data-flow/addsubcolumn.png "添加 subcolumn")

## <a name="data-preview-in-debug-mode"></a>调试模式下的数据预览

![表达式生成器](media/data-flow/exp4b.png "表达式数据预览")

在处理数据流表达式时，请从 Azure 数据工厂数据流设计图面切换调试模式，从而启用正在生成的表达式中的数据结果的实时预览。 为表达式启用实时调试。

![调试模式](media/data-flow/debugbutton.png "调试按钮")

单击 "刷新" 按钮以实时更新您的源的实时示例。

![表达式生成器](media/data-flow/exp5.png "表达式数据预览")

## <a name="comments"></a>注释

使用单行和多行注释语法将注释添加到表达式：

![注释](media/data-flow/comments.png "注释")

## <a name="regular-expressions"></a>正则表达式

Azure 数据工厂数据流表达式语言（[此处提供了完整参考文档](https://aka.ms/dataflowexpressions)）支持包含正则表达式语法的函数。 使用正则表达式函数时，表达式生成器将尝试将反斜杠（\\）解释为转义字符序列。 在正则表达式中使用反斜杠时，请将整个正则表达式括在刻度（\`）中，或使用双反斜杠。

使用居中圆点符号的示例

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

或使用双斜杠

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="addressing-array-indexes"></a>寻址数组索引

使用返回数组的表达式函数时，请使用方括号 [] 来寻址返回数组对象中的特定索引。 数组从 1 开始。

![表达式生成器数组](media/data-flow/expb2.png "表达式数据预览")

## <a name="handling-names-with-special-characters"></a>处理带有特殊字符的名称

如果列名称包含特殊字符或空格，请使用大括号将该名称括起来。
* ```{[dbo].this_is my complex name$$$}```

## <a name="next-steps"></a>后续步骤

[开始生成数据转换表达式](data-flow-expression-functions.md)
