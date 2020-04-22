---
title: 映射数据流的枢轴转换
description: 使用 Azure 数据工厂映射数据流数据透视转换，从行到列透视数据
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: a58444f81f60b48f9c2c76f13257a6a2431158a8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686425"
---
# <a name="pivot-transformation-in-mapping-data-flow"></a>映射数据流的枢轴转换


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用数据透视变换从单个列的唯一行值创建多个列。 透视是一个聚合变换，用于按列分组并使用[聚合函数](data-flow-expression-functions.md#aggregate-functions)生成数据透视列。

## <a name="configuration"></a>配置

透视转换需要三个不同的输入：按列分组、透视键以及如何生成透视列

### <a name="group-by"></a>Group by

![Group By 选项](media/data-flow/pivot2.png "*按选项分组")

选择要聚合透视列的列。 输出数据将按值按值将具有相同组的所有行分组到一行中。 在透视列中完成的聚合将发生在每个组中。

本部分为可选。 如果未按列选择组，则将聚合整个数据流，并且将只输出一行。

### <a name="pivot-key"></a>枢轴键

![枢轴键](media/data-flow/pivot3.png "枢轴键")

枢轴键是行值被透视到新列的列。 默认情况下，透视变换将为每个唯一行值创建新列。

在标记为 **"值"** 的部分中，可以输入要透视的特定行值。 只有在此节中输入的行值才会旋转。 启用**Null 值**将为列中的空值创建一个透视列。

### <a name="pivoted-columns"></a>旋转列

![旋转列](media/data-flow/pivot4.png "旋转列")

对于每个成为列的唯一透视键值，为每个组生成聚合行值。 您可以创建每个数据透视键的多个列。 每个数据透视列必须至少包含一个[聚合函数](data-flow-expression-functions.md#aggregate-functions)。

**列名称模式：** 选择如何设置每个数据透视列的列名称的格式。 输出的列名称将是透视键值、列前缀和可选前缀（足够的中间字符）的组合。 

**列排列：** 如果每个数据透视键生成多个透视列，请选择您希望对列进行排序的方式。 

**列前缀：** 如果每个数据透视键生成多个数据透视列，请为每个列输入列前缀。 如果只有一个透视列，则此设置是可选的。

## <a name="help-graphic"></a>帮助图

下图显示了不同数据透视组件如何相互交互

![数据透视帮助图形](media/data-flow/pivot5.png "透视帮助图形")

## <a name="pivot-metadata"></a>数据透视元数据

如果在枢轴键配置中未指定值，则在运行时将动态生成透视列。 透视列数将等于唯一透视键值数乘以透视列数。 由于这可能是一个不断变化的数字，UX 将不会在 **"检查"** 选项卡中显示列元数据，也不会显示列传播。 要转换这些列，请使用映射数据流的[列模式](concepts-data-flow-column-pattern.md)功能。 

如果设置了特定的透视键值，则透视列将显示在元数据中。 列名称将在"检查和接收器"映射中可供您使用。

### <a name="generate-metadata-from-drifted-columns"></a>从漂移列生成元数据

数据透视基于行值动态生成新的列名称。 您可以将这些新列添加到元数据中，这些元数据可在数据流中稍后引用。 为此，请使用数据预览中的[地图漂移](concepts-data-flow-schema-drift.md#map-drifted-columns-quick-action)快速操作。 

![列透视](media/data-flow/newpivot1.png "映射漂移枢轴列")

### <a name="sinking-pivoted-columns"></a>下沉旋转列

尽管透视列是动态的，但它们仍然可以写入目标数据存储。 启用**允许在接收器设置中漂移的架构**。 这将允许您编写元数据中未包括的列。 列元数据，但架构漂移选项将允许您登陆数据。

### <a name="rejoin-original-fields"></a>重新加入原始字段

透视转换将仅按和透视列投影组。 如果希望输出数据包含其他输入列，请使用[自联接](data-flow-join.md#self-join)模式。

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

尝试[取消透视变换](data-flow-unpivot.md)将列值转换为行值。 
