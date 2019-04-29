---
title: Azure 数据工厂映射数据流“透视”转换
description: 从行到使用 Azure 数据工厂映射数据流透视转换的列的数据进行透视
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: e16cac281b77f3ca93d9ef358ae806203bc8b663
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61348415"
---
# <a name="azure-data-factory-pivot-transformation"></a>Azure 数据工厂透视转换
[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

在 ADF 数据流中使用“透视”作为聚合，其中一个或多个分组列将其不同的行值转换到各个列中。 实际上，你可以将行值透视到新列中（将数据转换为元数据）。

![透视选项](media/data-flow/pivot1.png "透视 1")

## <a name="group-by"></a>分组依据

![透视选项](media/data-flow/pivot2.png "透视 2")

首先，为你的透视聚合设置要用作分组依据的列。 此处，你可以设置 1 个以上的列，列的列表旁边会显示一个 + 号。

## <a name="pivot-key"></a>透视键

![透视选项](media/data-flow/pivot3.png "透视 3")

透视键是 ADF 从行透视到列的列。 默认情况下，此字段的数据集中的每个唯一值都将透视到列。 但是，你也可以选择输入此数据集中要透视到列值的值。 这是将确定将创建的新列的列。

## <a name="pivoted-columns"></a>透视的列

![透视选项](media/data-flow/pivot4.png "透视 4")

最后，你将选择要用于透视的值的聚合以及如何在转换的新输出投影中显示列。

（可选）你可以设置一个命名模式，在其中包含要在行值中的每个新列名称中添加的前缀、中间名和后缀。

例如，按“区域”透视“销售”将会得到每个销售值的新列值，例如“25”、“50”、“1000”，等等。但是，如果将前缀值"Sales-"，每个列的值将添加"销售-"值的开头。

![透视选项](media/data-flow/pivot5.png "透视 5")

将列排列设置为“正常”，将所有透视列与其聚合值组合在一起。 将列排列设置为“横向”将在列和值之间交替。

### <a name="aggregation"></a>聚合

若要设置要用于透视值的聚合，请单击“透视的列”窗格底部的字段。 你将进入 ADF 数据流表达式生成器，可以在其中构建聚合表达式并为新的聚合值提供描述性的别名。

在表达式生成器中使用 ADF 数据流表达式语言来描述透视列转换： https://aka.ms/dataflowexpressions。

## <a name="pivot-metadata"></a>数据透视表的元数据

透视转换将生成是动态的基于传入数据的新列名称。 透视键生成每个新的列名称的值。 如果您未指定单独的值并想要在透视键中创建每个唯一值的动态列名称，然后 UI 将不显示在检查的元数据，并会有任何列传播到接收器转换。 如果将值设置为透视键，然后 ADF 可以确定新的列名和这些列名将检查可用和接收器映射。

### <a name="landing-new-columns-in-sink"></a>登陆接收器中的新列

即使使用 Pivot 中的动态列名称，可以仍到目标存储区中接收器新列名称和值。 只需设置"允许架构偏差"接收器设置中。 则不会看到新的动态名称在列元数据，但架构偏差选项将允许您将数据移。

### <a name="view-metadata-in-design-mode"></a>在设计模式下查看元数据

如果你想要查看新的列名称作为元数据检查，并且你想要看到显式传播到接收器转换的列，然后在透视键选项卡中设置显式值。

### <a name="how-to-rejoin-original-fields"></a>如何重新联接原始字段
“透视”转换将仅对聚合、分组和透视操作中使用的列进行投影。 如果你想要在流中包括的其他列从上一步，使用上一步的一个新分支，并使用自联接模式连接的原始元数据的流。

## <a name="next-steps"></a>后续步骤

请尝试[逆透视转换](data-flow-unpivot.md)以将列的值转换为行值。 
