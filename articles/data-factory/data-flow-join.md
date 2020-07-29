---
title: 映射数据流中的联接转换
description: 使用 Azure 数据工厂映射数据流中的联接转换合并两个数据源中的数据
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/15/2020
ms.openlocfilehash: f95f35fe0d17afdeec864674d3360fc3b172cad1
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683362"
---
# <a name="join-transformation-in-mapping-data-flow"></a>映射数据流中的联接转换

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用联接转换可以在映射数据流中合并来自两个源或流的数据。 输出流将包含这两个源中基于联接条件匹配的所有列。 

## <a name="join-types"></a>联接类型

映射数据流目前支持五种不同的联接类型。

### <a name="inner-join"></a>内部联接

内联仅输出在两个表中具有匹配值的行。

### <a name="left-outer"></a>左外部

左外部联接返回左侧流中的所有行以及右侧流中的匹配记录。 如果左侧流中的某一行没有匹配项，则右侧流中的输出列将设置为 NULL。 输出为内联返回的行以及左侧流中不匹配的行。

> [!NOTE]
> 由于联接条件中可能出现笛卡尔积，因此，数据流使用的 Spark 引擎有时会失败。 如果发生这种情况，可以切换到自定义交叉联接并手动输入联接条件。 这样做可能会导致数据流性能下降，因为执行引擎可能需要计算关系两侧的所有行，然后对行进行筛选。

### <a name="right-outer"></a>右外部

右外部联接返回右侧流中的所有行以及左侧流中的匹配记录。 如果右侧流中的某一行没有匹配项，则左侧流中的输出列将设置为 NULL。 输出为内联返回的行以及右侧流中不匹配的行。

### <a name="full-outer"></a>完全外部

完全外部联接输出两侧中的所有列和行，其中不匹配的列为 NULL 值。

### <a name="custom-cross-join"></a>自定义交叉联接

交叉联接根据条件输出两个流的叉积。 如果使用的条件不相等，请指定一个自定义表达式作为交叉联接条件。 输出流为所有符合联接条件的行。

可以将此联接类型用于非等值联接和 ```OR``` 条件。

如果要显式生成完整的笛卡尔积，请在联接之前，在两个独立流的每个流中使用派生列转换来创建要匹配的合成键。 例如，在每个名为 ```SyntheticKey``` 的流的派生列中创建一个新列，并将其设置为等于 ```1```。 然后使用 ```a.SyntheticKey == b.SyntheticKey``` 作为自定义联接表达式。

> [!NOTE]
> 在自定义交叉联接中，确保包含左右关系每一侧中的至少一列。 如果对静态值而不是每一侧的列执行交叉联接，则会对整个数据集进行完全扫描，从而导致数据流性能不佳。

## <a name="configuration"></a>配置

1. 在“右侧流”下拉菜单中选择要联接的数据流。
1. 选择“联接类型”
1. 选择要根据联接条件进行匹配的键列。 默认情况下，数据流在每个流中的一列之间查找相等性。 若要通过计算值进行比较，请将鼠标悬停在列下拉菜单上，然后选择“计算列”。

![联接转换](media/data-flow/join.png "联接")

### <a name="non-equi-joins"></a>非等值联接

若要在联接条件中使用条件运算符，例如不等于 (!=) 或大于 (>)，请在两列之间更改运算符下拉菜单。 非等值联接要求使用“优化”选项卡中的“固定”广播来广播两个流中的至少一个流。

![非等值联接](media/data-flow/non-equi-join.png "非等值联接")

## <a name="optimizing-join-performance"></a>优化联接性能

与 SSIS 等工具中的合并联接不同，联接转换不是强制性的合并联接操作。 联接键无需排序。 联接操作基于 Spark 中的最佳联接操作（广播或映射侧联接）进行。

![联接转换优化](media/data-flow/joinoptimize.png "联接优化")

在联接、查找和存在转换中，如果工作器节点内存可容纳一个数据流或同时容纳两个数据流，则可以通过启用“广播”来优化性能。 默认情况下，Spark 引擎将自动决定是否广播一侧。 若要手动选择要广播的一侧，请选择“固定”。

建议不要通过“关闭”选项来禁用广播，除非联接遇到超时错误。

## <a name="self-join"></a>自联接

若要对数据流进行自联接，请使用选择转换为现有流添加别名。 通过单击转换旁边的加号图标并选择“新建分支”，来创建新的分支。 添加选择转换，以便为原始流添加别名。 添加联接转换，并选择原始流作为“左侧流”，选择转换作为“右侧流”。

![自联接](media/data-flow/selfjoin.png "自联接")

## <a name="testing-join-conditions"></a>测试联接条件

在调试模式下使用数据预览测试联接转换时，请使用一小组已知数据。 对大型数据集中的行进行采样时，无法预测将读取哪些行和键进行测试。 结果是非确定性的，这意味着你的联接条件可能不会返回任何匹配项。

## <a name="data-flow-script"></a>数据流脚本

### <a name="syntax"></a>语法

```
<leftStream>, <rightStream>
    join(
        <conditionalExpression>,
        joinType: { 'inner'> | 'outer' | 'left_outer' | 'right_outer' | 'cross' }
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <joinTransformationName>
```

### <a name="inner-join-example"></a>内联示例

下面的示例是一个名为 `JoinMatchedData` 的联接转换，它采用左侧流 `TripData` 和右侧流 `TripFare`。  联接条件为表达式 `hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}`，如果每个流中的 `hack_license`、`medallion`、`vendor_id` 和 `pickup_datetime` 列均匹配，则返回 true。 `joinType` 为 `'inner'`。 我们仅在左侧流中启用广播，因此 `broadcast` 的值为 `'left'`。

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

### <a name="custom-cross-join-example"></a>自定义交叉联接示例

下面的示例是一个名为 `JoiningColumns` 的联接转换，它采用左侧流 `LeftStream` 和右侧流 `RightStream`。 此转换接收两个流，并将 `leftstreamcolumn` 列大于 `rightstreamcolumn` 列的所有行联接在一起。 `joinType` 为 `cross`。 未启用广播，因此 `broadcast` 的值为 `'none'`。

在数据工厂 UX 中，此转换如下图所示：

![联接示例](media/data-flow/join-script2.png "联接示例")

此转换的数据流脚本位于下面的代码片段中：

```
LeftStream, RightStream
    join(
        leftstreamcolumn > rightstreamcolumn,
        joinType:'cross',
        broadcast: 'none'
    )~> JoiningColumns
```

## <a name="next-steps"></a>后续步骤

联接数据后，创建一个[派生列](data-flow-derived-column.md)并将数据[接收](data-flow-sink.md)到目标数据存储中。
