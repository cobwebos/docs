---
title: Azure 数据工厂映射数据流表达式生成器
description: 用于 Azure 数据工厂映射数据流的表达式生成器
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/17/2019
ms.openlocfilehash: 3664a7c311e15ce3aa61fc71f98a46e3f2618143
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184679"
---
# <a name="mapping-data-flow-expression-builder"></a>映射数据流表达式生成器



在 Azure 数据工厂映射数据流中，你将找到表达式框，可在其中输入用于数据转换的表达式。 请在这些框中使用数据流中的列、字段、变量、参数和函数。 若要生成表达式，请使用表达式生成器。单击转换中的表达式文本框即可启动表达式生成器。 选择转换的列时，有时还会看到“计算列”选项。 单击该选项也会启动表达式生成器。

![表达式生成器](media/data-flow/xpb1.png "表达式生成器")

表达式生成器工具默认使用文本编辑器选项。 自动填写功能从整个 Azure 数据工厂数据流对象模型读取数据，并支持语法检查和突出显示。

![表达式生成器自动完成](media/data-flow/expb1.png "表达式生成器自动完成")

## <a name="build-schemas-in-output-schema-pane"></a>在输出架构窗格中生成架构

![添加复杂列](media/data-flow/complexcolumn.png "添加列")

在左侧的输出架构窗格中，你将看到正在修改的列并将其添加到你的架构。 可在此处以交互方式生成简单复杂的数据结构。 使用 "添加列" 添加其他字段，并使用 "添加 subcolumn" 生成层次结构。

![添加 subcolumn](media/data-flow/addsubcolumn.png "添加 Subcolumn")

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

Azure 数据工厂数据流表达式语言（[此处提供了完整参考文档](https://aka.ms/dataflowexpressions)）支持包含正则表达式语法的函数。 使用正则表达式函数时，表达式生成器将尝试将反斜杠（\\）解释为转义字符序列。 在正则表达式中使用反斜杠时，请将整个正则表达式括在计时周期（\`）或使用双反斜杠。

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

## <a name="keyboard-shortcuts"></a>键盘快捷方式

* ```Ctrl-K Ctrl-C```：注释整行
* ```Ctrl-K Ctrl-U```：取消注释
* ```F1```：提供编辑器帮助命令
* ```Alt-Down Arrow```：向下移动当前行
* ```Alt-Up Arrow```：向上移动当前行
* ```Cntrl-Space```：显示上下文帮助

## <a name="manual-comments"></a>手动注释

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

如果在表达式的顶部放置注释，它将显示在 "转换" 文本框中以记录转换表达式：

![注释](media/data-flow/comments2.png "注释")

## <a name="convert-to-dates-or-timestamps"></a>转换为日期或时间戳

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

请注意，若要在时间戳输出中包含字符串文本，则需要在 toString （）内包装转换

## <a name="handling-column-names-with-special-characters"></a>处理带有特殊字符的列名称

如果列名称包含特殊字符或空格，请使用大括号将该名称括起来。

```{[dbo].this_is my complex name$$$}```

## <a name="next-steps"></a>后续步骤

[开始生成数据转换表达式](data-flow-expression-functions.md)
