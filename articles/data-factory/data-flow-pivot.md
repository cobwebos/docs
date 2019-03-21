---
title: Azure 数据工厂映射数据流“透视”转换
description: Azure 数据工厂映射数据流“透视”转换
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 5548a62218aaac2e4da3853e8e5d43a584922bc0
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57569886"
---
# <a name="azure-data-factory-mapping-data-flow-pivot-transformation"></a>Azure 数据工厂映射数据流“透视”转换

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

在 ADF 数据流中使用“透视”作为聚合，其中一个或多个分组列将其不同的行值转换到各个列中。 实际上，你可以将行值透视到新列中（将数据转换为元数据）。

![透视选项](media/data-flow/pivot1.png "透视 1")

## <a name="group-by"></a>分组依据

![透视选项](media/data-flow/pivot2.png "透视 2")

首先，为你的透视聚合设置要用作分组依据的列。 此处，你可以设置 1 个以上的列，列的列表旁边会显示一个 + 号。

## <a name="pivot-key"></a>透视键

![透视选项](media/data-flow/pivot3.png "透视 3")

透视键是 ADF 从行透视到列的列。 默认情况下，此字段的数据集中的每个唯一值都将透视到列。 但是，你也可以选择输入此数据集中要透视到列值的值。

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

### <a name="how-to-rejoin-original-fields"></a>如何重新联接原始字段
> [!NOTE]
> “透视”转换将仅对聚合、分组和透视操作中使用的列进行投影。 如果你想要在流中包括的其他列从上一步，使用上一步的一个新分支，并使用自联接模式连接的原始元数据的流。

## <a name="next-steps"></a>后续步骤

请尝试[逆透视转换](data-flow-unpivot.md)以将列的值转换为行值。 
