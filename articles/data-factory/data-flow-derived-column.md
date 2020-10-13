---
title: 映射数据流中的派生列转换
description: 了解如何使用映射数据流派生列转换在 Azure 数据工厂中大规模转换数据。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/14/2020
ms.openlocfilehash: 2e90a8779322cf8967ca9a194c6cc760f7c8b8f5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90531946"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>映射数据流中的派生列转换

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用派生列转换在数据流中生成新列或修改现有字段。

## <a name="create-and-update-columns"></a>创建和更新列

创建派生列时，可以生成新列或更新现有列。 在 " **列** " 文本框中，输入要创建的列。 若要覆盖架构中的现有列，可以使用 "列" 下拉列表。 若要生成派生列的表达式，请单击 " **输入表达式** " 文本框。 您可以开始键入表达式或打开表达式生成器来构造您的逻辑。

![派生列设置](media/data-flow/create-derive-column.png "派生列设置")

若要添加更多派生列，请单击列列表上方的 " **添加** " 或现有派生列旁边的加号图标。 选择“添加列”或“添加列模式”。

![新派生列选择](media/data-flow/add-derived-column.png "新派生列选择")

### <a name="column-patterns"></a>列模式

如果未显式定义架构，或者如果想要批量更新一组列，则需要创建列模式。 列模式允许使用基于列元数据的规则与列匹配，并为每个匹配列创建派生列。 有关详细信息，请了解如何在派生列转换中 [生成列模式](concepts-data-flow-column-pattern.md#column-patterns-in-derived-column-and-aggregate) 。

![列模式](media/data-flow/column-pattern-derive.png "列模式")

## <a name="building-schemas-using-the-expression-builder"></a>使用表达式生成器生成架构

使用映射数据流 [表达式生成器](concepts-data-flow-expression-builder.md)时，可以在 " **派生列** " 部分中创建、编辑和管理派生列。 在转换中创建或更改的所有列都将列出。 通过单击列名称以交互方式选择要编辑的列或模式。 若要添加其他列，请选择 " **新建** "，并选择是否要添加单个列或模式。

![新建列](media/data-flow/derive-add-column.png "新建列")

使用复杂列时，可以创建个子列。 为此，请单击任何列旁边的加号图标，然后选择 " **添加 subcolumn**"。 有关处理数据流中的复杂类型的详细信息，请参阅[映射数据流中的 JSON 处理](format-json.md#mapping-data-flow-properties)。

![添加子列](media/data-flow/derive-add-subcolumn.png "添加子列")

有关处理数据流中的复杂类型的详细信息，请参阅[映射数据流中的 JSON 处理](format-json.md#mapping-data-flow-properties)。

![添加复杂列](media/data-flow/derive-complex-column.png "添加列")

### <a name="locals"></a>局部变量

如果要在多个列之间共享逻辑或要划分逻辑，可以在派生列转换中创建本地。 本地是一组不会在下游传播到以下转换的逻辑。 可以通过转到 " **表达式元素** " 并选择 " **局部变量**"，在表达式生成器中创建局部变量。 通过选择 " **新建**" 创建一个新的。

![创建本地](media/data-flow/create-local.png "创建本地")

局部变量可以引用派生列中的任何 expression 元素，包括函数、输入架构、参数和其他局部变量。 引用其他局部变量时，order 会很重要，因为引用的本地需要 "高于" 当前的。

![创建本地2](media/data-flow/create-local-2.png "创建本地2")

若要引用派生列中的局部，请在 " **表达式元素** " 视图中单击本地，或在名称前面使用冒号引用它。 例如，名为 local1 的本地被引用 `:local1` 。 若要编辑本地定义，请将鼠标悬停在 "表达式元素" 视图中，然后单击铅笔图标。

![使用局部变量](media/data-flow/using-locals.png "使用局部变量")

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

下面的示例是一个名为 `CleanData` 的派生列，它接受传入的流 `MoviesYear` 并创建两个派生列。 第一个派生列将列 `Rating` 替换为整数类型的 Rating 值。 第二个派生列是与名称以“movies”开头的每个列匹配的模式。 对于每个匹配列，它会创建列 `movie`，该列等于前缀为“movie_”的匹配列的值。 

在数据工厂 UX 中，此转换如下图所示：

![派生示例](media/data-flow/derive-script.png "派生示例")

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

- 详细了解[映射数据流表达式语言](data-flow-expression-functions.md)。
