---
title: 在映射数据流中加入转换
description: 使用 Azure 数据工厂映射数据流的联接转换合并来自两个数据源的数据
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/02/2020
ms.openlocfilehash: 0c0e35f7f06afd0cafa4a1e353b7eda84ed226f2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413667"
---
# <a name="join-transformation-in-mapping-data-flow"></a>在映射数据流中加入转换

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

使用联接转换将来自映射数据流中的两个源或流的数据合并。 输出流将包括来自两个源的所有列，这些列基于联接条件进行匹配。 

## <a name="join-types"></a>联接类型

映射数据流当前支持五种不同的联接类型。

### <a name="inner-join"></a>内部联接

内部联接仅输出两个表中具有匹配值的行。

### <a name="left-outer"></a>左外部

左外部联接返回来自左流的所有行，并从右流返回匹配的记录。 如果来自左流的行不匹配，则来自右流的输出列将设置为 NULL。 输出将是内部联接返回的行以及来自左流的不匹配的行。

> [!NOTE]
> 数据流使用的 Spark 引擎偶尔会由于联接条件下的点菜产品而出现故障。 如果发生这种情况，您可以切换到自定义交叉联接并手动输入联接条件。 这可能会导致数据流的性能变慢，因为执行引擎可能需要计算关系两侧的所有行，然后筛选行。

### <a name="right-outer"></a>右外部

右外部联接从右流返回所有行，并从左流返回匹配的记录。 如果右流中的行不匹配，则左流的输出列将设置为 NULL。 输出将是内部联接返回的行加上右流中不匹配的行。

### <a name="full-outer"></a>完全外部

完全外部联接输出来自两侧的所有列和行，并且对不匹配的列具有 NULL 值。

### <a name="custom-cross-join"></a>自定义交叉联接

交叉联接根据条件输出两个流的交叉积。 如果使用的条件不是相等的，请指定自定义表达式作为交叉联接条件。 输出流将是满足联接条件的所有行。

可以使用此联接类型进行非联接和```OR```条件。

如果要显式生成完整的点菜产品，请使用联接前两个独立流中的每个派生列变换创建要匹配的合成键。 例如，在每个调用```SyntheticKey```的流中，在"派生列"中创建一个新列，```1```并将其设置为 。 然后用作```a.SyntheticKey == b.SyntheticKey```自定义联接表达式。

> [!NOTE]
> 请确保在自定义交叉联接中至少包含一个来自左右关系两侧的列。 使用静态值而不是每一侧的列执行交叉联接会导致对整个数据集进行完整扫描，从而导致数据流执行不佳。

## <a name="configuration"></a>配置

1. 在**右流**下拉下下拉列表中选择要加入的数据流。
1. 选择**您的加入类型**
1. 选择要匹配的键列，以便加入条件。 默认情况下，数据流在每个流中查找一列之间的相等性。 要通过计算值进行比较，请将鼠标悬停在列下拉列表上，然后选择 **"计算"列**。

![加入转换](media/data-flow/join.png "联接")

## <a name="optimizing-join-performance"></a>优化联接性能

与 SSIS 等工具中的合并联接不同，联接转换不是强制合并联接操作。 联接键不需要排序。 联接操作基于 Spark 中的最佳联接操作（广播或地图侧联接）进行。

![联接转型优化](media/data-flow/joinoptimize.png "联接优化")

如果其中一个或两个数据流适合辅助节点内存，则通过在优化选项卡中启用 **"广播**"来进一步优化性能。您还可以在联接操作上重新分区数据，使其更好地适合每个工作人员的内存。

## <a name="self-join"></a>自联接

要自行加入数据流，请用选择转换对现有流进行别名。 通过单击转换旁边的加号图标并选择 **"新建分支**"来创建新分支。 将选择转换添加到原始流的别名。 添加联接转换，并将原始流作为**左流**选择，选择转换作为**右流**。

![自联接](media/data-flow/selfjoin.png "自联接")

## <a name="testing-join-conditions"></a>测试联接条件

在调试模式下使用数据预览测试联接转换时，请使用一小组已知数据。 从大型数据集对行进行采样时，无法预测将读取哪些行和键进行测试。 结果是非确定性的，这意味着您的联接条件可能不会返回任何匹配项。

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

下面的示例是名为"`JoinMatchedData`左流"和"右流`TripData``TripFare`"的联接转换。  联接条件是，如果每个流`hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}`中的`hack_license`、`medallion`和`vendor_id``pickup_datetime`列匹配，则返回 true 的表达式。 `joinType` 为 `'inner'`。 我们只允许在左流广播，所以`broadcast`有价值。 `'left'`

在数据工厂 UX 中，此转换类似于下图：

![加入示例](media/data-flow/join-script1.png "加入示例")

此转换的数据流脚本位于下面的代码段中：

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

下面的示例是名为"`JoiningColumns`左流"和"右流`LeftStream``RightStream`"的联接转换。 此转换采用两个流，并将列大于列`leftstreamcolumn``rightstreamcolumn`的所有行合并在一起。 `joinType` 为 `cross`。 未启用`broadcast`广播具有价值`'none'`。

在数据工厂 UX 中，此转换类似于下图：

![加入示例](media/data-flow/join-script2.png "加入示例")

此转换的数据流脚本位于下面的代码段中：

```
LeftStream, RightStream
    join(
        leftstreamcolumn > rightstreamcolumn,
        joinType:'cross',
        broadcast: 'none'
    )~> JoiningColumns
```

## <a name="next-steps"></a>后续步骤

加入数据后，创建[派生列](data-flow-derived-column.md)并将数据[接收器](data-flow-sink.md)到目标数据存储。
