---
title: 映射数据流中的透视转换
description: 使用 Azure 数据工厂映射数据流透视转换从行向列透视数据
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: a58444f81f60b48f9c2c76f13257a6a2431158a8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81686425"
---
# <a name="pivot-transformation-in-mapping-data-flow"></a>映射数据流中的透视转换


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用透视转换从单个列的唯一行值创建多个列。 Pivot 是聚合转换，可选择 "按列分组" 并使用[聚合函数](data-flow-expression-functions.md#aggregate-functions)生成透视列。

## <a name="configuration"></a>配置

透视转换要求三个不同的输入： group by 列、透视键以及如何生成透视列

### <a name="group-by"></a>Group by

![Group By 选项](media/data-flow/pivot2.png "[Group by 选项")

选择要在其上聚合透视列的列。 输出数据会将具有相同组的所有行按值组合到一行中。 在透视列中完成的聚合将在每个组上进行。

本部分为可选。 如果未选择 "分组依据" 列，则将聚合整个数据流，并只输出一行。

### <a name="pivot-key"></a>透视键

![透视键](media/data-flow/pivot3.png "透视键")

透视键是其行值透视到新列的列。 默认情况下，透视转换将为每个唯一的行值创建一个新列。

在标有 "**值**" 的部分中，可以输入要透视的特定行值。 只有此部分中输入的行值才能透视。 启用**null 值**将为列中的 Null 值创建透视列。

### <a name="pivoted-columns"></a>透视列

![透视列](media/data-flow/pivot4.png "透视列")

对于每个成为列的唯一透视键值，为每个组生成一个聚合的行值。 可以为每个透视键创建多个列。 每个透视列必须包含至少一个[聚合函数](data-flow-expression-functions.md#aggregate-functions)。

**列名模式：** 选择设置每个透视列的列名称格式的方式。 输出列名称将是透视键值、列前缀和可选前缀（足够）的组合。 

**列排列：** 如果每个透视键生成了多个透视列，请选择要如何对列进行排序。 

**列前缀：** 如果每个透视键生成了多个透视列，请为每列输入一个列前缀。 如果只有一个透视列，则此设置是可选的。

## <a name="help-graphic"></a>帮助图

以下帮助图显示了不同的透视组件之间的交互方式

![透视帮助图形](media/data-flow/pivot5.png "透视帮助图")

## <a name="pivot-metadata"></a>透视元数据

如果在透视键配置中未指定任何值，则将在运行时动态生成透视列。 透视列数将等于唯一透视键值的数目乘以数据透视列的数目。 由于这可能是更改的数字，UX 不会在 "**检查**" 选项卡中显示列元数据，并且不会出现列传播。 若要转换这些列，请使用映射数据流的[列模式](concepts-data-flow-column-pattern.md)功能。 

如果设置了特定的透视键值，则透视列将出现在元数据中。 在 "检查和接收器" 映射中，将提供列名称。

### <a name="generate-metadata-from-drifted-columns"></a>从偏移列生成元数据

Pivot 基于行值动态生成新的列名称。 您可以将这些新列添加到可以在数据流中稍后引用的元数据中。 为此，请在数据预览中使用[map 偏移](concepts-data-flow-schema-drift.md#map-drifted-columns-quick-action)quick 操作。 

![列透视](media/data-flow/newpivot1.png "映射偏移透视列")

### <a name="sinking-pivoted-columns"></a>接收透视列

尽管透视的列是动态的，但仍可以将它们写入目标数据存储区。 启用接收器设置中的**允许架构偏差**。 这将允许你编写未包含在元数据中的列。 你的列元数据，但架构偏移选项将允许你进入数据。

### <a name="rejoin-original-fields"></a>重新加入原始字段

透视转换将只投影 by 和透视列。 如果希望输出数据包含其他输入列，请使用[自联接](data-flow-join.md#self-join)模式。

## <a name="data-flow-script"></a>数据流脚本

### <a name="syntax"></a>语法

```
<incomingStreamName>
    pivot(groupBy(Tm),
        pivotBy(<pivotKeyColumn, [<specifiedColumnName1>,...,<specifiedColumnNameN>]),
        <pivotColumnPrefix> = <pivotedColumnValue>,
        columnNaming: '< prefix >< $N | $V ><middle >< $N | $V >< suffix >',
        lateral: { 'true' | 'false'}
    ) ~> <pivotTransformationName
```
### <a name="example"></a>示例

"配置" 部分中显示的屏幕具有以下数据流脚本：

```
BasketballPlayerStats pivot(groupBy(Tm),
    pivotBy(Pos),
    {} = count(),
    columnNaming: '$V$N count',
    lateral: true) ~> PivotExample

```

## <a name="next-steps"></a>后续步骤

尝试[逆透视转换](data-flow-unpivot.md)以将列值转换为行值。 
