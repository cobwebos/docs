---
title: 映射数据流中的派生列转换
description: 了解如何在 Azure 数据工厂中大规模转换数据以及映射数据流派生列转换。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/15/2019
ms.openlocfilehash: c2d4c9b089a6673a5cff716d2901cc2b9483ede1
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74972245"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>映射数据流中的派生列转换

使用派生列转换可以在数据流中生成新列，也可以修改现有字段。

## <a name="derived-column-settings"></a>派生列设置

若要覆盖现有列，请通过列下拉列表选择它。 否则，使用 "列选择" 字段作为文本框，然后键入新列的名称。 若要生成派生列的表达式，请单击 "输入表达式" 框以打开 "数据流[表达式生成器](concepts-data-flow-expression-builder.md)"。

![派生列设置](media/data-flow/dc1.png "派生列设置")

若要添加其他派生列，请将鼠标悬停在现有的派生列上，并单击加号图标。 选择 "**添加列**" 或 "**添加列模式**"。 如果列名称是来自源的变量，则列模式可能会很方便。 有关详细信息，请参阅[列模式](concepts-data-flow-column-pattern.md)。

![新的派生列选择](media/data-flow/columnpattern.png "新的派生列选择")

## <a name="build-schemas-in-output-schema-pane"></a>在输出架构窗格中生成架构

要修改并添加到架构中的列列在 "输出架构" 窗格中。 可在此处以交互方式生成简单复杂的数据结构。 若要添加其他字段，请选择 "**添加列**"。 若要生成层次结构，请选择 "**添加 subcolumn**"。

![添加 subcolumn](media/data-flow/addsubcolumn.png "添加 Subcolumn")

有关处理数据流中的复杂类型的详细信息，请参阅[映射数据流中的 JSON 处理](concepts-data-flow-json.md)

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

下面的示例是一个名为 `CleanData` 的派生列，它采用传入流 `MoviesYear` 并创建两个派生列。 第一个派生列将分级值为的列 `Rating` 替换为整数类型。 第二个派生列是与名称以 "电影" 开头的每个列匹配的模式。 对于每个匹配的列，它将创建一个列 `movie`，该列等于前缀为 "movie_" 的匹配列的值。 

在数据工厂 UX 中，此转换如下图所示：

![派生示例](media/data-flow/derive-script1.png "派生示例")

此转换的数据流脚本位于下面的代码片段中：

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

- 了解有关[映射数据流表达式语言](data-flow-expression-functions.md)的详细信息。
