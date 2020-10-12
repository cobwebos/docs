---
title: 映射数据流中的透视转换
description: 使用 Azure 数据工厂映射数据流透视转换将数据从行透视到列
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/17/2020
ms.openlocfilehash: e098182c000cbe05df533434a41c55b797ef876f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87086653"
---
# <a name="pivot-transformation-in-mapping-data-flow"></a>映射数据流中的透视转换


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

借助透视转换，可根据单个列的唯一行值创建多个列。 透视是一种聚合转换，需选择分组依据列并使用[聚合函数](data-flow-expression-functions.md#aggregate-functions)生成透视键。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4C4YN]

## <a name="configuration"></a>配置

透视转换需要三种不同的输入：分组依据列、透视键以及如何生成已透视列

### <a name="group-by"></a>Group by

![Group by 选项](media/data-flow/pivot2.png "Group By 选项")

选择要在其基础上聚合已透视列的列。 输出数据会将所有具有相同分组依据值的行分组为一行。 在已透视列中完成的聚合将在每个组上进行。

本部分为可选。 如果未选择分组依据列，则将聚合整个数据流，并且仅输出一行。

### <a name="pivot-key"></a>透视键

![透视键](media/data-flow/pivot3.png "透视键")

透视键是将其行值透视到新列的列。 默认情况下，透视转换将为每个唯一的行值创建一个新列。

在标有“值”的部分中，可以输入要透视的特定行值。 将仅透视在此部分中输入的行值。 启用“Null 值”将为列中的 Null 值创建一个已透视列。

### <a name="pivoted-columns"></a>已透视列

![已透视列](media/data-flow/pivot4.png "已透视列")

对于变成列的每个唯一透视键值，将为每个组生成一个聚合的行值。 你可以为每个透视键创建多个列。 每个透视列必须至少包含一个[聚合函数](data-flow-expression-functions.md#aggregate-functions)。

**列名称模式：** 选择如何格式化每个透视列的列名称。 输出的列名称将为透视键值、列前缀和可选前缀、后缀、中间字符的组合。 

**列排列方式：** 如果为每个透视键生成多个透视列，请选择所需的列排序方式。 

**列前缀：** 如果为每个透视键生成多个透视列，请为每一列输入一个列前缀。 如果只有一个已透视列，则此设置是可选的。

## <a name="help-graphic"></a>帮助图形

以下帮助图形显示了不同透视组件之间的交互方式

![透视帮助图形](media/data-flow/pivot5.png "透视帮助图形")

## <a name="pivot-metadata"></a>透视元数据

如果未在透视键配置中指定任何值，则将在运行时动态生成已透视列。 已透视列的数量将等于唯一透视列值的数量乘以透视列的数量。 由于该数字可能会不断变化，因此 UX 不会在“检查”选项卡中显示列元数据，并且不会进行列传播。 若要转换这些列，请使用映射数据流的[列模式](concepts-data-flow-column-pattern.md)功能。 

如果设置了特定的透视列值，则已透视列将出现在元数据中。 “检查”和“接收器映射”中将提供列名称。

### <a name="generate-metadata-from-drifted-columns"></a>根据偏移列生成元数据

透视基于行值动态生成新的列名称。 可以将这些新列添加到元数据中，以便稍后在数据流中引用。 为此，请使用数据预览中的[映射偏移列](concepts-data-flow-schema-drift.md#map-drifted-columns-quick-action)快速操作。 

![透视列](media/data-flow/newpivot1.png "映射偏移透视列")

### <a name="sinking-pivoted-columns"></a>接收已透视列

尽管已透视列是动态的，但仍可以将它们写入目标数据存储中。 在接收器设置中启用“允许架构偏差”。 这将允许你写入未包含在元数据中的列。 列元数据中不会显示新的动态名称，但是架构偏差选项允许你移入数据。

### <a name="rejoin-original-fields"></a>重新联接原始字段

透视转换仅投影分组依据列和已透视列。 如果希望输出数据包含其他输入列，请使用[自联接](data-flow-join.md#self-join)模式。

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

配置部分中显示的屏幕具有以下数据流脚本：

```
BasketballPlayerStats pivot(groupBy(Tm),
    pivotBy(Pos),
    {} = count(),
    columnNaming: '$V$N count',
    lateral: true) ~> PivotExample

```

## <a name="next-steps"></a>后续步骤

尝试使用[逆透视转换](data-flow-unpivot.md)将列值转换为行值。 
