---
title: 映射数据流中的聚合转换-Azure 数据工厂 |Microsoft Docs
description: 了解如何在 Azure 数据工厂中大规模聚合数据以及映射数据流聚合转换。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 0201cbdd05cd8aae4afb92b459bf58fb5ff6a142
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026974"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>映射数据流中的聚合转换 



可以在聚合转换中定义数据流中列的聚合。 使用 "表达式生成器" 可以定义不同类型的聚合，如 SUM、MIN、MAX 和 COUNT，可以按现有或计算列进行分组。

## <a name="group-by"></a>分组依据
选择现有列或创建新的计算列，以用作聚合的 group by 子句。 若要使用现有列，请从下拉列表中选择所需的列。 若要创建新的计算列，请将鼠标悬停在子句上，并单击 "计算列"。 这将打开 "数据流[表达式生成器](concepts-data-flow-expression-builder.md)"。 创建计算列后，请在 "名称" 字段中输入输出列的名称。 如果要添加其他 group by 子句，请将鼠标悬停在现有子句上，并单击 "+"。

![聚合转换组（按设置]）(media/data-flow/agg.png "聚合转换组（按设置")）

> [!NOTE]
> 在聚合转换中，group by 子句是可选的。

## <a name="aggregate-column"></a>聚合列 
选择 "聚合" 选项卡以生成聚合表达式。 您可以选择现有列并使用聚合覆盖值，也可以使用新名称创建新的字段。 在列名称选择器旁的右侧框中输入聚合表达式。 若要编辑该表达式，请单击文本框打开 "表达式生成器"。 若要添加其他聚合，请将鼠标悬停在现有表达式上，并单击 "+" 创建新的聚合列或[列模式](concepts-data-flow-column-pattern.md)。

![聚合转换聚合设置](media/data-flow/agg2.png "聚合转换聚合设置")

> [!NOTE]
> 每个聚合表达式必须包含至少一个聚合函数。

> [!NOTE]
> 在调试模式下，表达式生成器不能用聚合函数生成数据预览。 若要查看聚合转换的数据预览，请关闭 "表达式生成器" 并通过 "数据预览" 选项卡来查看数据。

## <a name="reconnect-rows-and-columns"></a>重新连接行和列
聚合转换与 SQL 聚合选择查询密切等效。 未包含在 Group By 子句或聚合函数中的列不会流向聚合转换的输出。 如果要将其他列与聚合行输出一起包含，则必须执行以下操作之一：

* 使用聚合函数包含该附加列，如 Last （）或 First （）
* 使用[自联接模式](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/)在聚合之前重新联接列。

## <a name="next-steps"></a>后续步骤

* 使用[窗口转换](data-flow-window.md)定义基于窗口的聚合
