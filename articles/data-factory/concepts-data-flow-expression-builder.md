---
title: 映射数据流中的表达式生成器
description: 在 Azure 数据工厂中映射数据流时使用表达式生成器生成表达式
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/9/2019
ms.openlocfilehash: 01aa2574ac6edd1ce5e1b209eac3e43bbed82fce
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969325"
---
# <a name="building-expressions-in-mapping-data-flow"></a>在映射数据流中构建表达式

在映射数据流中，许多转换属性都作为表达式输入。 这些表达式由列值、参数、函数、运算符和在运行时计算为 spark 数据类型的文本组成。

## <a name="opening-the-expression-builder"></a>打开表达式生成器

数据工厂 UX 中的表达式编辑接口称为 "**表达式生成器**"。 当你在表达式逻辑中输入时，数据工厂使用[IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019)代码完成来突出显示、语法检查和完成。

![表达式生成器](media/data-flow/xpb1.png "表达式生成器")

在转换（如派生列和筛选器）中，如果表达式是必需的，则通过单击蓝色表达式框打开表达式生成器。

![表达式生成器](media/data-flow/expressionbox.png "表达式生成器")

引用匹配或分组依据条件中的列时，表达式可以从列中提取值。 若要创建表达式，请选择 "计算列" 选项。

![表达式生成器](media/data-flow/computedcolumn.png "表达式生成器")

如果表达式或文本值为有效输入，"添加动态内容" 将允许您生成一个计算结果为文本的表达式。

![表达式生成器](media/data-flow/add-dynamic-content.png "表达式生成器")

## <a name="expression-language-reference"></a>表达式语言参考

映射数据流具有可在表达式中使用的内置函数和运算符。 可用函数的列表可在 "[映射数据流表达式语言](data-flow-expression-functions.md)参考" 页中找到。

## <a name="column-names-with-special-characters"></a>带有特殊字符的列名称

如果列名称包含特殊字符或空格，请使用大括号将该名称括起来，以便在表达式中引用它们。

```{[dbo].this_is my complex name$$$}```

## <a name="previewing-expression-results"></a>预览表达式结果

如果打开了[调试模式](concepts-data-flow-debug-mode.md)，则可以使用实时 spark 群集来查看表达式的计算结果。 构建逻辑时，可以实时调试表达式。 

![表达式生成器](media/data-flow/exp4b.png "表达式数据预览")

单击 "刷新" 按钮，根据源的实时示例更新表达式的结果。

![表达式生成器](media/data-flow/exp5.png "表达式数据预览")

## <a name="string-interpolation"></a>字符串内插

使用双引号将文字字符串文本与表达式一起使用。 可以包括表达式函数、列和参数。 在查询字符串中包含参数时，字符串内插有助于避免大量使用字符串串联。 若要使用表达式语法，请将其括在大括号中，

字符串内插的一些示例：

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="commenting-expressions"></a>注释表达式

使用单行和多行注释语法将注释添加到表达式：

![注释](media/data-flow/comments.png "注释")

下面是有效注释的示例：

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

如果在表达式的顶部放置注释，它将显示在 "转换" 文本框中以记录转换表达式：

![注释](media/data-flow/comments2.png "注释")

## <a name="regular-expressions"></a>正则表达式

许多表达式语言函数使用正则表达式语法。 使用正则表达式函数时，表达式生成器将尝试将反斜杠（\\）解释为转义字符序列。 在正则表达式中使用反斜杠时，请将整个正则表达式括在计时周期（\`）或使用双反斜杠。

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

## <a name="keyboard-shortcuts"></a>键盘快捷方式

* ```Ctrl-K Ctrl-C```：注释整行
* ```Ctrl-K Ctrl-U```：取消注释
* ```F1```：提供编辑器帮助命令
* ```Alt-Down Arrow```：向下移动当前行
* ```Alt-Up Arrow```：上移当前行
* ```Cntrl-Space```：显示上下文帮助

## <a name="convert-to-dates-or-timestamps"></a>转换为日期或时间戳

若要在时间戳输出中包含字符串文本，需要将转换打包到 ```toString()```中。

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

若要将毫秒数从 Epoch 转换为日期或时间戳，请使用 `toTimestamp(<number of milliseconds>)`。 如果时间以秒为单位，乘以1000。

```toTimestamp(1574127407*1000l)```

上面表达式末尾的尾部 "l" 表示转换为 long 类型的 "内联" 语法。

## <a name="next-steps"></a>后续步骤

[开始生成数据转换表达式](data-flow-expression-functions.md)
