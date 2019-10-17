---
title: 映射数据流中的聚合转换-Azure 数据工厂 |Microsoft Docs
description: 了解如何在 Azure 数据工厂中大规模聚合数据以及映射数据流聚合转换。
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 3f2a3219a731133ecaedf30fec1854f1a128caa3
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72431605"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>映射数据流中的聚合转换 

聚合转换定义数据流中列的聚合。 使用表达式生成器可以定义不同类型的聚合，如求和、MIN、MAX 和 COUNT，并按现有或计算列进行分组。

## <a name="group-by"></a>分组依据

选择现有列或创建新的计算列，以用作聚合的 group by 子句。 若要使用现有列，请从下拉列表中选择它。 若要创建新的计算列，请将鼠标悬停在子句上，并单击 "**计算列**"。 这将打开 "数据流[表达式生成器](concepts-data-flow-expression-builder.md)"。 创建计算列后，在 "**名称**" 字段中输入输出列的名称。 如果要添加其他 group by 子句，请将鼠标悬停在现有子句上，并单击加号图标。

![聚合转换组（按设置）](media/data-flow/agg.png "聚合转换组（按设置）")

在聚合转换中，group by 子句是可选的。

## <a name="aggregate-column"></a>聚合列 

请参阅 "**聚合**" 选项卡以生成聚合表达式。 您可以使用聚合覆盖现有列，或使用新名称创建新的字段。 在列名称选择器旁的右侧框中输入聚合表达式。 若要编辑该表达式，请单击文本框打开 "表达式生成器"。 若要添加其他聚合，请将鼠标悬停在现有表达式上，并单击加号图标创建新的聚合列或[列模式](concepts-data-flow-column-pattern.md)。

每个聚合表达式必须包含至少一个聚合函数。

![聚合转换聚合设置](media/data-flow/agg2.png "聚合转换聚合设置")


> [!NOTE]
> 在调试模式下，表达式生成器不能用聚合函数生成数据预览。 若要查看聚合转换的数据预览，请关闭 "表达式生成器" 并通过 "数据预览" 选项卡来查看数据。

## <a name="reconnect-rows-and-columns"></a>重新连接行和列

聚合转换类似于 SQL 聚合 select 查询。 未包含在 group by 子句或聚合函数中的列不会流向聚合转换的输出。 如果希望将其他列包含在聚合输出中，请执行以下方法之一：

* 使用聚合函数（如 `last()` 或 `first()`）包含该附加列。
* 使用[自联接模式](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/)将这些列重新加入到输出流中。

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

下面的示例使用传入的流 `MoviesYear` 并按列 `year` 对行进行分组。 转换将创建一个聚合列 `avgrating`，其计算结果为 `Rating` 列的平均值。 此聚合转换的名称为 `AvgComedyRatingsByYear`。 在数据工厂 UX 中，此转换如下图所示：

![按示例分组](media/data-flow/agg-script1.png "按示例分组")

![聚合示例](media/data-flow/agg-script2.png "聚合示例")

下面的代码片段中提供了此转换的数据流脚本。

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>后续步骤

* 使用[窗口转换](data-flow-window.md)定义基于窗口的聚合
