---
title: Azure 数据工厂数据流“联接”转换
description: Azure 数据工厂数据流“联接”转换
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: 517afe21fbf9241e2b2423525e9caee12a5603f6
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2019
ms.locfileid: "56270897"
---
# <a name="azure-data-factory-data-flow-join-transformation"></a>Azure 数据工厂数据流“联接”转换

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

在数据流中使用联接来组合两个表中的数据。 单击将成为左侧关系的转换，并从工具箱中添加一个“联接”转换。 在“联接”转换内，你将从数据流中选择要成为右侧关系的另一个数据流。

![“联接”转换](media/data-flow/join.png "联接")

## <a name="join-types"></a>联接类型

必须为“联接”转换选择联接类型

### <a name="inner-join"></a>内部联接

内部联接仅传递与两个表中的列条件都匹配的行

### <a name="left-outer"></a>左外部

除了内部联接返回的所有行之外，还会传递左侧流中不满足联接条件的所有行，并将来自另一个表的输出列设置为 NULL。

### <a name="right-outer"></a>右外部

除了内部联接返回的所有行之外，还会传递右侧流中不满足联接条件的所有行，并将与另一个表对应的输出列设置为 NULL。

### <a name="full-outer"></a>完全外部

对于另一个表中不存在的列，完全外部联接会生成两端中具有 NULL 值的所有列和行

### <a name="cross-join"></a>交叉联接

通过表达式指定两个流的叉积

## <a name="specify-join-conditions"></a>指定联接条件

左联接条件来自连接到联接左侧的数据流。 左联接条件是在底部连接到你的联接的第二个数据流，这将是到另一个流的直接连接器，或者是对另一个流的引用。

需要输入至少 1 (1..n) 个联接条件。 它们可以是从下拉菜单中选择的直接引用的字段，也可以是表达式。

## <a name="join-performance-optimizations"></a>联接性能优化

与 SSIS 等工具中的“合并联接”不同，ADF 数据流中的联接不是必需的合并联接操作。 因此，不需要首先对联接键进行排序。 联接操作将使用 Databricks 基于 Spark 中的最佳联接操作在 Spark 中进行：广播 / 端映射联接：

![“联接”转换优化](media/data-flow/joinoptimize.png "联接优化")

如果数据集可以容纳在 Databricks 辅助角色节点内存中，则我们可以优化你的联接性能。 你还可以在联接操作中指定数据分区，以创建能够更好地容纳到每个辅助角色的内存中的数据集。

## <a name="self-join"></a>自联接

可以通过使用“选择”转换为现有流指定别名，在 ADF 数据流中实现自联接条件。 首先，从流中创建一个“新分支”，然后添加一个“选择”转换来为整个原始流指定别名。

![自联接](media/data-flow/selfjoin.png "自联接")

在上面的关系图中，“选择”转换位于顶部。 它所做的所有工作就是将原始流的别名指定为“OrigSourceBatting”。 在它下方突出显示的“联接”转换中，你可以看到我们将此“选择别名”流用作右侧联接，以便可以在内部联接的左侧和右侧引用同一个键。
