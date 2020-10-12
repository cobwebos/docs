---
title: 映射数据流中的表达式生成器
description: 在 Azure 数据工厂中映射数据流时使用表达式生成器生成表达式
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/14/2020
ms.openlocfilehash: 4297cc83ab3fa280e15480aefcd5aef8734c65ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90531000"
---
# <a name="build-expressions-in-mapping-data-flow"></a>映射数据流中的生成表达式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

在映射数据流中，许多转换属性都作为表达式输入。 这些表达式由列值、参数、函数、运算符和在运行时计算为 Spark 数据类型的文本组成。 映射数据流具有专门的体验，旨在帮助您构建这些称为 " **表达式生成器**" 的表达式。 使用用于突出显示、语法检查和完成的  [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense) 代码完成功能，该表达式生成器旨在简化生成数据流。 本文介绍如何使用表达式生成器来有效地生成业务逻辑。

![表达式生成器](media/data-flow/expresion-builder.png "表达式生成器")

## <a name="open-expression-builder"></a>打开表达式生成器

打开 "表达式生成器" 有多个入口点。 它们都依赖于数据流转换的特定上下文。 最常见的用例是转换，如 [派生列](data-flow-derived-column.md) 和 [聚合](data-flow-aggregate.md) ，用户使用数据流表达式语言创建或更新列。 可以通过选择列列表上方的 " **打开表达式生成器** " 来打开表达式生成器。 您还可以单击列上下文并直接向该表达式打开 "表达式生成器"。

![打开表达式生成器派生](media/data-flow/open-expression-builder-derive.png "打开表达式生成器派生")

在某些转换（例如 [筛选器](data-flow-filter.md)）中，单击蓝色表达式文本框将打开 "表达式生成器"。 

![蓝色表达式框](media/data-flow/expressionbox.png "表达式生成器")

引用匹配或分组依据条件中的列时，表达式可以从列中提取值。 若要创建表达式，请选择 " **计算列**"。

![计算列选项](media/data-flow/computedcolumn.png "表达式生成器")

如果表达式或文本值为有效输入，请选择 " **添加动态内容** " 以生成一个计算结果为文本值的表达式。

![添加动态内容选项](media/data-flow/add-dynamic-content.png "表达式生成器")

## <a name="expression-elements"></a>Expression 元素

在映射数据流中，表达式可以由列值、参数、函数、局部变量、运算符和文本组成。 这些表达式的计算结果必须为 Spark 数据类型，如 string、boolean 或 integer。

![Expression 元素](media/data-flow/expression-elements.png "Expression 元素")

### <a name="functions"></a>函数

映射数据流具有可在表达式中使用的内置函数和运算符。 有关可用函数的列表，请参阅 [映射数据流语言参考](data-flow-expression-functions.md)。

#### <a name="address-array-indexes"></a>地址数组索引

当处理返回数组类型的列或函数时，使用方括号 ( [] ) 访问特定元素。 如果索引不存在，则表达式的计算结果为 NULL。

![表达式生成器数组](media/data-flow/expression-array.png "表达式数据预览")

> [!IMPORTANT]
> 在映射数据流中，数组是从1开始的，这意味着第一个元素由 index one 引用。 例如，myArray [1] 将访问名为 "myArray" 的数组的第一个元素。

### <a name="input-schema"></a>输入架构

如果数据流使用定义的架构，则可以在多个表达式中按名称引用列。 如果使用的是架构偏移，则可以使用或函数显式引用列， `byName()` `byNames()` 或使用列模式进行匹配。

#### <a name="column-names-with-special-characters"></a>带有特殊字符的列名称

如果列名称包含特殊字符或空格，请使用大括号将该名称括起来，以便在表达式中引用它们。

```{[dbo].this_is my complex name$$$}```

### <a name="parameters"></a>参数

参数是在运行时从管道传递到数据流的值。 若要引用参数，请单击 " **表达式元素** " 视图中的参数，或在名称前面使用美元符号引用它。 例如，将引用一个名为 parameter1 的参数 `$parameter1` 。 若要了解详细信息，请参阅 [参数映射数据流](parameters-data-flow.md)。

### <a name="locals"></a>局部变量

如果要在多个列之间共享逻辑，或想要划分逻辑，可以在派生的 column\. 中创建本地 若要引用本地，请在 " **表达式元素** " 视图中单击本地，或在名称前面使用冒号引用它。 例如，名为 local1 的本地被引用 `:local1` 。 详细了解 [派生列文档](data-flow-derived-column.md#locals)中的局部变量。

## <a name="preview-expression-results"></a>预览表达式结果

如果打开了 [调试模式](concepts-data-flow-debug-mode.md) ，则可以通过交互方式使用调试群集来预览表达式的计算结果。 选择 "数据预览" 旁边的 " **刷新** " 以更新数据预览的结果。 您可以查看给定输入列的每一行的输出。

![正在进行的预览](media/data-flow/preview-expression.png "表达式数据预览")

## <a name="string-interpolation"></a>字符串内插

创建使用 expression 元素的长字符串时，可以使用字符串内插轻松构建复杂的字符串逻辑。 如果查询字符串中包含参数，则字符串内插可避免广泛使用字符串串联。 使用双引号将文字字符串文本和表达式括起来。 可以包括表达式函数、列和参数。 若要使用表达式语法，请将其括在大括号中，

字符串内插的一些示例：

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

* ```"{:playerName} is a {:playerRating} player"```

## <a name="commenting-expressions"></a>注释表达式

使用单行和多行注释语法向表达式添加注释。

下面的示例是有效的注释：

* ```/* This is my comment */```

* ```/* This is a```
* ```multi-line comment */```

如果在表达式的顶部放置注释，则该注释将显示在 "转换" 文本框中以记录转换表达式。

!["转换" 文本框中的注释](media/data-flow/comment-expression.png "注释")

## <a name="regular-expressions"></a>正则表达式

许多表达式语言函数使用正则表达式语法。 使用正则表达式函数时，表达式生成器会尝试将反斜杠 (\\) 解释为转义字符序列。 如果在正则表达式中使用反斜杠，请将整个正则表达式括在反撇号 (\`) 或使用双反斜杠。

使用反撇号的示例：

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

使用双斜杠的示例：

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="keyboard-shortcuts"></a>键盘快捷键

下面列出了表达式生成器中可用的快捷方式。 创建表达式时，可以使用大多数 intellisense 快捷方式。

* Ctrl + K Ctrl + C：注释整行。
* Ctrl + K Ctrl + U：取消注释。
* F1：提供编辑器帮助命令。
* Alt + 向下键：向下移动当前行。
* Alt + 向上键：上移当前行。
* Ctrl + 空格键：显示上下文帮助。

## <a name="commonly-used-expressions"></a>常用表达式

### <a name="convert-to-dates-or-timestamps"></a>转换为日期或时间戳

若要在时间戳输出中包含字符串文本，请在中包装转换 ```toString()``` 。

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

若要将毫秒数从 epoch 转换为日期或时间戳，请使用 `toTimestamp(<number of milliseconds>)` 。 如果时间以秒为单位，乘以1000。

```toTimestamp(1574127407*1000l)```

前面表达式末尾的尾部 "l" 表示转换为 long 类型作为内联语法。

### <a name="find-time-from-epoch-or-unix-time"></a>查找来自 epoch 或 Unix 时间的时间

toLong ( currentTimestamp ( # A2-toTimestamp ( "1970-01-01 00：00： 00.000"，' yyyy-MM-dd HH： MM： ss。SSS ") ) * 1000l

## <a name="next-steps"></a>后续步骤

[开始生成数据转换表达式](data-flow-expression-functions.md)
