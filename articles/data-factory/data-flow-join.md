---
title: Azure 数据工厂映射数据流中的联接转换
description: 使用 Azure 数据工厂映射数据流中的联接转换合并两个数据源中的数据
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: aae502b350f2cf2e98849b2b6e25543516a0c547
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961841"
---
# <a name="join-transformation-in-mapping-data-flow"></a>映射数据流中的联接转换

使用联接转换可以在映射数据流中合并两个源或流中的数据。 输出流将包含这两个源中的所有列，这些列基于联接条件进行匹配。 

## <a name="join-types"></a>联接类型

映射数据流当前支持五种不同的联接类型。

### <a name="inner-join"></a>内部联接

内部联接仅输出在两个表中具有匹配值的行。

### <a name="left-outer"></a>左外部

左外部联接返回左侧流中的所有行，并匹配正确流中的记录。 如果左侧流中的某一行没有匹配项，则正确流中的输出列将设置为 NULL。 输出将是内部联接返回的行以及左侧流中不匹配的行。

### <a name="right-outer"></a>右外部

右外部联接返回右侧流中的所有行，并匹配左侧流中的记录。 如果右侧流中的某一行没有匹配项，则将左侧流中的输出列设置为 NULL。 输出将是内部联接返回的行以及右侧流中不匹配的行。

### <a name="full-outer"></a>完全外部

完全外部联接从两端输出所有列和行，并为不匹配的列提供 NULL 值。

### <a name="cross-join"></a>交叉联接

交叉联接根据条件输出两个流的叉积。 如果要使用不相等的条件，请将自定义表达式指定为交叉联接条件。 输出流将为满足联接条件的所有行。 若要创建输出每个行组合的笛卡尔积，请将 `true()` 指定为联接条件。

## <a name="configuration"></a>配置

1. 在右侧的 "**流**" 下拉列表中选择要连接的数据流。
1. 选择**联接类型**
1. 选择要在其上匹配的键列作为联接条件。 默认情况下，数据流在每个流中的一列之间查找相等性。 若要通过计算值进行比较，请将鼠标悬停在列下拉列表中，然后选择 "**计算列**"。

![联接转换](media/data-flow/join.png "联接")

## <a name="optimizing-join-performance"></a>优化联接性能

与 SSIS 之类的工具中的合并联接不同，联接转换不是强制的合并联接操作。 联接键无需排序。 联接操作是基于 Spark 中的最佳联接操作（广播或映射端联接）进行的。

![联接转换优化](media/data-flow/joinoptimize.png "联接优化")

如果其中一个或两个数据流适合工作节点内存，则通过在 "优化" 选项卡中启用**广播**，进一步优化性能。你还可以对联接操作上的数据进行重新分区，使其更适合于每个工作线程的内存。

## <a name="self-join"></a>自联接

若要将数据流自行联接，请使用 select 转换作为现有流的别名。 单击转换旁边的加号图标，然后选择 "**新建分支**"，创建一个新分支。 添加一个选择转换以对原始流使用别名。 添加联接转换，并选择原始流作为**左侧流**，并选择 "转换为**正确流**"。

![自联接](media/data-flow/selfjoin.png "自联接")

## <a name="testing-join-conditions"></a>测试联接条件

在调试模式下使用数据预览来测试联接转换时，请使用一组少量的已知数据。 在对大型数据集中的行进行采样时，无法预测将读取哪些行和键用于测试。 结果是非确定性的，这意味着联接条件可能不会返回任何匹配项。

## <a name="data-flow-script"></a>数据流脚本

### <a name="syntax"></a>语法

```
<leftStream>, <rightStream>
    join(
        <conditionalExpression>,
        joinType: { 'inner'> | 'outer' | 'left_outer' | 'right_outer' | 'cross' }
        broadcast: { 'none' | 'left' | 'right' | 'both' }
    ) ~> <joinTransformationName>
```

### <a name="inner-join-example"></a>内部联接示例

下面的示例是一个名为 `JoinMatchedData` 的联接转换，它采用 `TripData` 和右流 `TripFare`的左流。  联接条件是表达式 `hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}` 如果每个流中的 `hack_license`、`medallion`、`vendor_id`和 `pickup_datetime` 列匹配，则返回 true。 `joinType` 为 `'inner'`。 我们只启用左侧流中的广播，因此 `broadcast` 的值 `'left'`。

在数据工厂 UX 中，此转换如下图所示：

![联接示例](media/data-flow/join-script1.png "联接示例")

此转换的数据流脚本位于下面的代码片段中：

```
TripData, TripFare
    join(
        hack_license == { hack_license}
        && TripData@medallion == TripFare@medallion
        && vendor_id == { vendor_id}
        && pickup_datetime == { pickup_datetime},
        joinType:'inner',
        broadcast: 'left'
    )~> JoinMatchedData
```

### <a name="cross-join-example"></a>交叉联接示例

下面的示例是一个名为 `CartesianProduct` 的联接转换，它采用 `TripData` 和右流 `TripFare`的左流。 此转换采用两个流，并返回其行的笛卡尔积。 联接条件是 `true()` 的，因为它会输出完整的笛卡尔积。 `cross`中的 `joinType`。 我们只启用左侧流中的广播，因此 `broadcast` 的值 `'left'`。

在数据工厂 UX 中，此转换如下图所示：

![联接示例](media/data-flow/join-script2.png "联接示例")

此转换的数据流脚本位于下面的代码片段中：

```
TripData, TripFare
    join(
        true(),
        joinType:'cross',
        broadcast: 'left'
    )~> CartesianProduct
```

## <a name="next-steps"></a>后续步骤

联接数据后，创建一个[派生列](data-flow-derived-column.md)并将数据[接收](data-flow-sink.md)到目标数据存储区。
