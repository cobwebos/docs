---
title: 映射数据流的表达式生成器
description: 使用表达式生成器映射 Azure 数据工厂中的数据流生成表达式
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 196d917d31eb08af80587bba30d9f7e67bf8cbea
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991699"
---
# <a name="build-expressions-in-mapping-data-flow"></a>在映射数据流中生成表达式

在映射数据流时，许多转换属性作为表达式输入。 这些表达式由列值、参数、函数、运算符和文本组成，这些列值在运行时计算为 Spark 数据类型。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tkur]

## <a name="open-expression-builder"></a>打开表达式生成器

Azure 数据工厂用户体验中的表达式编辑界面称为表达式生成器。 输入表达式逻辑时，数据工厂使用[IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019)代码完成来突出显示、语法检查和自动完成。

![表达式生成器](media/data-flow/xpb1.png "表达式生成器")

在派生列和筛选器等转换（表达式是强制性的）中，通过选择蓝色表达式框打开表达式生成器。

![蓝色表达式框](media/data-flow/expressionbox.png "表达式生成器")

在匹配或按分组条件中引用列时，表达式可以从列中提取值。 要创建表达式，请选择 **"计算列**"。

![计算列选项](media/data-flow/computedcolumn.png "表达式生成器")

如果表达式或文本值是有效的输入，请选择 **"添加动态内容**"以生成计算到文本值的表达式。

![添加动态内容选项](media/data-flow/add-dynamic-content.png "表达式生成器")

## <a name="expression-language-reference"></a>表达式语言引用

映射数据流具有内置函数和运算符，可用于表达式。 有关可用函数的列表，请参阅[映射数据流中的表达式函数](data-flow-expression-functions.md)。

## <a name="column-names-with-special-characters"></a>具有特殊字符的列名称

当具有包含特殊字符或空格的列名称时，使用大括号括括该名称以在表达式中引用它们。

```{[dbo].this_is my complex name$$$}```

## <a name="preview-expression-results"></a>预览表达式结果

如果[调试模式](concepts-data-flow-debug-mode.md)已打开，则可以使用实时 Spark 群集查看表达式评估到的正在进行的预览。 构建逻辑时，可以实时调试表达式。 

![正在进行的预览](media/data-flow/exp4b.png "表达式数据预览")

选择 **"刷新"** 可根据源的实时示例更新表达式的结果。

![“刷新”按钮](media/data-flow/exp5.png "表达式数据预览")

## <a name="string-interpolation"></a>字符串内插

使用引号将文本字符串文本与表达式一起括起来。 可以包括表达式函数、列和参数。 当参数包含在查询字符串中时，字符串插值可用于避免大量使用字符串串联。 要使用表达式语法，请使用大括号将其封闭，

字符串插值的一些示例：

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="comment-expressions"></a>注释表达式

使用单行和多行注释语法向表达式添加注释。

![单行和多行注释语法](media/data-flow/comments.png "注释")

以下示例是有效的注释：

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

如果将注释放在表达式的顶部，则该注释将显示在转换文本框中，以记录转换表达式。

![转换文本框中的注释](media/data-flow/comments2.png "注释")

## <a name="regular-expressions"></a>正则表达式

许多表达式语言函数使用正则表达式语法。 使用正则表达式函数时，表达式生成器会尝试将反斜杠\\（ ） 解释为转义字符序列。 在正则表达式中使用反斜杠时，请将整个正则表达式封闭在反斜杠中，\`或使用双反斜杠。

使用回记的示例：

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

使用双斜杠的示例：

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="address-array-indexes"></a>地址数组索引

使用返回数组的表达式函数，请使用括号 （*） 来处理返回数组对象中的特定索引。 数组基于这些数组。

![表达式生成器数组](media/data-flow/expb2.png "表达式数据预览")

## <a name="keyboard-shortcuts"></a>键盘快捷方式

* Ctrl_K Ctrl_C：注释整行。
* Ctrl_K Ctrl_U：无注释。
* F1：提供编辑器帮助命令。
* Alt_向下箭头键：向下移动当前线。
* Alt_上箭头键：向上移动当前线。
* Ctrl_空格键：显示上下文帮助。

## <a name="convert-to-dates-or-timestamps"></a>转换为日期或时间戳

要在时间戳输出中包含字符串文本，请在 中```toString()```包装转换。

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

要将毫秒从纪元转换为日期或时间戳，请使用`toTimestamp(<number of milliseconds>)`。 如果时间以秒为单位，乘以 1，000。

```toTimestamp(1574127407*1000l)```

上一个表达式末尾的尾随"l"表示转换为长类型为内联语法。

## <a name="next-steps"></a>后续步骤

[开始构建数据转换表达式](data-flow-expression-functions.md)
