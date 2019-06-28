---
title: 聚合转换映射数据在流中的 Azure 数据工厂 |Microsoft Docs
description: 了解如何在 Azure 数据工厂中使用映射数据流聚合转换进行缩放的数据聚合。
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 21135b26d4bc840b3fcb091e675e5e6bd24d8548
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312126"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>聚合转换中映射数据流 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

可以在聚合转换中定义数据流中列的聚合。 使用表达式生成器，可以定义不同类型的聚合，如 SUM、 MIN、 MAX 和现有或计算列的计数。

## <a name="group-by"></a>分组依据
选择一个现有列，或创建新的计算的列作为一个组使用 by 子句，为你聚合。 若要使用的现有列，请从下拉列表中选择所需的列。 若要创建新的计算的列，将鼠标悬停在 over 子句，并单击计算列。 这会打开[流数据的表达式生成器](concepts-data-flow-expression-builder.md)。 一旦创建计算的列，输入名称字段下面的输出列名称。 如果你想要将其他组添加子句，悬停在现有子句，然后单击 +。

![聚合转换组设置](media/data-flow/agg.png "聚合转换组设置")

> [!NOTE]
> Group by 子句中是可选聚合的转换。

## <a name="aggregate-column"></a>聚合列 
选择聚合选项卡上，若要生成聚合表达式。 您可以选择现有的列和值覆盖带有聚合，或使用新名称创建新的字段。 旁边的列名称选择器的右侧框中输入聚合表达式。 若要编辑表达式，请单击以打开表达式生成器的文本框中。 若要添加其他聚合，悬停在现有表达式，然后单击 + 以创建新的聚合列或[列模式](concepts-data-flow-column-pattern.md)。

![聚合转换聚合设置](media/data-flow/agg2.png "聚合转换聚合设置")

> [!NOTE]
> 每个聚合表达式必须包含至少一个聚合函数。

> [!NOTE]
> 在调试模式下，表达式生成器无法生成与聚合函数的数据预览。 若要查看数据 （预览版） for 聚合转换，请关闭表达式生成器，并查看通过的数据预览选项卡的数据。

## <a name="next-steps"></a>后续步骤

-定义基于窗口的聚合使用[窗口转换](data-flow-window.md)