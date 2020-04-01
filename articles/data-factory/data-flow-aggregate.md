---
title: 映射数据流的聚合转换
description: 了解如何使用映射数据流聚合转换在 Azure 数据工厂中大规模聚合数据。
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: e4b076d96cad280c4da6c2424f056c2216c47602
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80408844"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>映射数据流的聚合转换 

聚合转换定义数据流中列的聚合。 使用表达式生成器，可以定义不同类型的聚合，如 SUM、MIN、MAX 和 COUNT，按现有列或计算列分组。

## <a name="group-by"></a>Group by

选择现有列或创建新的计算列，以用作聚合的按子句分组。 要使用现有列，请从下拉列表中选择它。 要创建新的计算列，请将鼠标悬停在子句上，然后单击"**计算"列**。 这将打开[数据流表达式生成器](concepts-data-flow-expression-builder.md)。 创建计算列后，请在 **"名称"字段中**输入输出列名称。 如果要按子句添加其他组，请将鼠标悬停在现有子句上，然后单击加号图标。

![按设置聚合转换组](media/data-flow/agg.png "按设置聚合转换组")

在聚合转换中，按子句分组是可选的。

## <a name="aggregate-column"></a>聚合列 

转到**聚合**选项卡以生成聚合表达式。 您可以使用聚合覆盖现有列，也可以使用新名称创建新字段。 聚合表达式在列名称选择器旁边的右侧框中输入。 要编辑表达式，请单击文本框以打开表达式生成器。 要添加其他聚合，请将鼠标悬停在现有表达式上，然后单击加号以创建新的聚合列或[列模式](concepts-data-flow-column-pattern.md)。

每个聚合表达式必须至少包含一个聚合函数。

![聚合转换聚合设置](media/data-flow/agg2.png "聚合转换聚合设置")


> [!NOTE]
> 在调试模式下，表达式生成器无法生成具有聚合函数的数据预览。 要查看聚合转换的数据预览，关闭表达式生成器并通过"数据预览"选项卡查看数据。

## <a name="reconnect-rows-and-columns"></a>重新连接行和列

聚合转换类似于 SQL 聚合选择查询。 按子句或聚合函数未包含在组中的列不会流到聚合转换的输出。 如果要在聚合输出中包含其他列，请执行以下方法之一：

* 使用聚合函数，如`last()`或`first()`包括该附加列。
* 使用[自联接模式](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/)将列重新加入输出流。

## <a name="removing-duplicate-rows"></a>删除重复行

聚合转换的常见用途是删除或标识源数据中的重复条目。 此过程称为重复数据消除。 基于按键分组的组，使用您选择的启发式方法确定要保留的重复行。 常见的启发`first()`式是 ，`last()`和`max()`。 `min()` 使用[列模式](concepts-data-flow-column-pattern.md)将规则应用于除按列分组之外的每个列。

![重复数据删除](media/data-flow/agg-dedupe.png "重复数据删除")

在上面的示例中，列`ProductID`和`Name`正在用于分组。 如果两行的两列具有相同的值，则它们被视为重复项。 在此聚合转换中，将保留匹配的第一行的值，并删除所有其他行的值。 使用列模式语法，所有名称不是`ProductID`并`Name`映射到其现有列名称并给出第一个匹配行的值的列。 输出架构与输入架构相同。

对于数据验证方案，`count()`该函数可用于计算有多少重复项。

## <a name="data-flow-script"></a>数据流脚本

### <a name="syntax"></a>语法

```
<incomingStream>
    aggregate(
           groupBy(
                <groupByColumnName> = <groupByExpression1>,
                <groupByExpression2>
               ),
           <aggregateColumn1> = <aggregateExpression1>,
           <aggregateColumn2> = <aggregateExpression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <aggregateTransformationName>
```

### <a name="example"></a>示例

下面的示例采用传入流`MoviesYear`，按列`year`对行进行分组。 转换创建一个聚合列`avgrating`，该列计算到列`Rating`的平均值。 此聚合转换名为`AvgComedyRatingsByYear`。

在数据工厂 UX 中，此转换类似于下图：

![按示例分组](media/data-flow/agg-script1.png "按示例分组")

![聚合示例](media/data-flow/agg-script2.png "聚合示例")

此转换的数据流脚本位于下面的代码段中。

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

![聚合数据流脚本](media/data-flow/aggdfs1.png "聚合数据流脚本")

```MoviesYear```：派生列定义年份和标题列```AvgComedyRatingByYear```：按年份```avgrating```分组的喜剧平均评级的聚合转换：为保存聚合值而创建的新列的名称

```
MoviesYear aggregate(groupBy(year),
    avgrating = avg(toInteger(Rating))) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>后续步骤

* 使用[窗口转换](data-flow-window.md)定义基于窗口的聚合
