---
title: Azure 数据工厂数据流“联接”转换
description: Azure 数据工厂数据流“联接”转换
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: da6c3c90ebbeffcf468aad3809da097976d8ef0d
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387237"
---
# <a name="mapping-data-flow-join-transformation"></a>映射数据流联接转换



在数据流中使用联接来组合两个表中的数据。 单击将成为左侧关系的转换，并从工具箱中添加一个“联接”转换。 在“联接”转换内，你将从数据流中选择要成为右侧关系的另一个数据流。

![联接转换](media/data-flow/join.png "Join")

## <a name="join-types"></a>联接类型

对于联接转换，选择 "联接类型" 是必需的。

### <a name="inner-join"></a>内部联接

内部联接将仅传递与两个表中的列条件相匹配的行。

### <a name="left-outer"></a>左外部

除了内部联接返回的所有行之外，还会传递左侧流中不满足联接条件的所有行，并将来自另一个表的输出列设置为 NULL。

### <a name="right-outer"></a>右外部

除了内部联接返回的所有行之外，还会传递右侧流中不满足联接条件的所有行，并将与另一个表对应的输出列设置为 NULL。

### <a name="full-outer"></a>完全外部

完全外部生成的所有列和行均为空值，对于其他表中不存在的列。

### <a name="cross-join"></a>交叉联接

使用表达式指定两个流的叉积。 您可以使用此来创建自定义联接条件。

## <a name="specify-join-conditions"></a>指定联接条件

左联接条件来自连接到联接左侧的数据流。 左联接条件是在底部连接到你的联接的第二个数据流，这将是到另一个流的直接连接器，或者是对另一个流的引用。

需要输入至少 1 (1..n) 个联接条件。 它们可以是从下拉菜单中选择的直接引用的字段，也可以是表达式。

## <a name="join-performance-optimizations"></a>联接性能优化

与 SSIS 等工具中的“合并联接”不同，ADF 数据流中的联接不是必需的合并联接操作。 因此，不需要首先对联接键进行排序。 将基于 Spark 中的最佳联接操作执行联接操作：广播/映射端联接：

![联接转换优化](media/data-flow/joinoptimize.png "联接优化")

如果可以将数据集装入工作节点内存，则可以优化联接性能。 你还可以在联接操作中指定数据分区，以创建能够更好地容纳到每个辅助角色的内存中的数据集。

## <a name="self-join"></a>自联接

可以通过使用“选择”转换为现有流指定别名，在 ADF 数据流中实现自联接条件。 首先，从流中创建一个“新分支”，然后添加一个“选择”转换来为整个原始流指定别名。

![自联接](media/data-flow/selfjoin.png "自联接")

在上面的关系图中，选择转换位于顶部。 它所做的所有工作就是将原始流的别名指定为“OrigSourceBatting”。 在它下方突出显示的“联接”转换中，你可以看到我们将此“选择别名”流用作右侧联接，以便可以在内部联接的左侧和右侧引用同一个键。

## <a name="composite-and-custom-keys"></a>复合和自定义键

您可以在联接转换内进行动态构建自定义键和组合键。 为其他联接列添加行，每个关系行旁边有一个加号（+）。 或在 "表达式生成器" 中为动态联接值计算一个新键值。

## <a name="next-steps"></a>后续步骤

联接数据后，可以[创建新列](data-flow-derived-column.md)并将[数据接收到目标数据存储](data-flow-sink.md)。
