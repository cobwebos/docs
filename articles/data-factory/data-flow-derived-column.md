---
title: 映射数据流中的派生列转换
description: 了解如何使用映射数据流派生列转换在 Azure 数据工厂中大规模转换数据。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/15/2019
ms.openlocfilehash: 66396de52b3709c1d9357f32a375a29a8dcdbd1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77048754"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>映射数据流中的派生列转换

使用派生的列转换在数据流中生成新列或修改现有字段。

## <a name="derived-column-settings"></a>派生列设置

要覆盖现有列，请通过列下拉列表选择它。 否则，将列选择字段用作文本框，并在新列的名称中键入。 要生成派生列的表达式，请单击"输入表达式"框以打开["数据流表达式生成器](concepts-data-flow-expression-builder.md)"。

![派生列设置](media/data-flow/dc1.png "派生列设置")

要添加其他派生列，请将鼠标悬停在现有派生列上，然后单击加号图标。 选择 **"添加列**"或 **"添加列"模式**。 如果列名称从源中可变，则列模式可能会派上用场。 有关详细信息，请参阅[列模式](concepts-data-flow-column-pattern.md)。

![新派生列选择](media/data-flow/columnpattern.png "新派生列选择")

## <a name="build-schemas-in-output-schema-pane"></a>在输出架构窗格中生成架构

要修改并添加到架构中的列列将列在"输出架构"窗格中。 您可以在此处以交互方式构建简单而复杂的数据结构。 要添加其他字段，请选择"**添加列**"。 要生成层次结构，请选择"**添加子列**"。

![添加子列](media/data-flow/addsubcolumn.png "添加子列")

有关处理数据流中复杂类型的详细信息，请参阅[映射数据流的 JSON 处理](format-json.md#mapping-data-flow-properties)。

![添加复杂列](media/data-flow/complexcolumn.png "添加列")

## <a name="data-flow-script"></a>数据流脚本

### <a name="syntax"></a>语法

```
<incomingStream>
    derive(
           <columnName1> = <expression1>,
           <columnName2> = <expression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <deriveTransformationName>
```

### <a name="example"></a>示例

下面的示例是名为的`CleanData`派生列，该列获取传入流`MoviesYear`并创建两个派生列。 第一个派生列将列`Rating`替换为评级的值作为整数类型。 第二个派生列是匹配名称以"电影"开头的每个列的模式。 对于每个匹配列，它创建一个`movie`列，该列等于以"movie_"为预缀的匹配列的值。 

在数据工厂 UX 中，此转换类似于下图：

![派生示例](media/data-flow/derive-script1.png "派生示例")

此转换的数据流脚本位于下面的代码段中：

```
MoviesYear derive(
                Rating = toInteger(Rating),
                each(
                    match(startsWith(name,'movies')),
                    'movie' = 'movie_' + toString($$)
                )
            ) ~> CleanData
```

## <a name="next-steps"></a>后续步骤

- 了解有关[映射数据流表达式语言](data-flow-expression-functions.md)的更多信息。
