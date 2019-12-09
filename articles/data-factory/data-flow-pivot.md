---
title: 映射数据流透视转换
description: 使用 Azure 数据工厂映射数据流透视转换从行向列透视数据
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: 8f23b5e61e1aee83172a12466fac8d5b5003fea8
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930290"
---
# <a name="azure-data-factory-pivot-transformation"></a>Azure 数据工厂透视转换


在 ADF 数据流中使用“透视”作为聚合，其中一个或多个分组列将其不同的行值转换到各个列中。 实际上，你可以将行值透视到新列中（将数据转换为元数据）。

![透视选项](media/data-flow/pivot1.png "数据透视1")

## <a name="group-by"></a>分组依据

![透视选项](media/data-flow/pivot2.png "数据透视2")

首先，为你的透视聚合设置要用作分组依据的列。 此处，你可以设置 1 个以上的列，列的列表旁边会显示一个 + 号。

## <a name="pivot-key"></a>透视键

![透视选项](media/data-flow/pivot3.png "数据透视3")

透视键是 ADF 从行透视到列的列。 默认情况下，此字段的数据集中的每个唯一值都将透视到列。 但是，你也可以选择输入此数据集中要透视到列值的值。 此列将确定将创建的新列。

## <a name="pivoted-columns"></a>透视列

![透视选项](media/data-flow/pivot4.png "数据透视4")

最后，你将选择要用于透视的值的聚合以及如何在转换的新输出投影中显示列。

（可选）你可以设置一个命名模式，在其中包含要在行值中的每个新列名称中添加的前缀、中间名和后缀。

例如，按 "区域" 透视 "销售额" 将导致每个销售值的新列值，即 "25"、"50"、"1000" 等。但是，如果将前缀值设置为 "Sales-"，则每个列值会将 "Sales-" 添加到值的开头。

![透视选项](media/data-flow/pivot5.png "数据透视5")

将列排列设置为“正常”，将所有透视列与其聚合值组合在一起。 将列排列设置为“横向”将在列和值之间交替。

### <a name="aggregation"></a>聚合

若要设置要用于透视值的聚合，请单击“透视的列”窗格底部的字段。 你将进入 ADF 数据流表达式生成器，可以在其中构建聚合表达式并为新的聚合值提供描述性的别名。

在表达式生成器中使用 ADF 数据流表达式语言来描述透视列转换： https://aka.ms/dataflowexpressions 。

## <a name="pivot-metadata"></a>透视元数据

透视转换将生成基于传入数据动态的新列名称。 透视键为每个新列名生成值。 如果未指定单独的值，并且想要为透视键中的每个唯一值创建动态列名称，则 UI 将不会在检查中显示元数据，并且不会向接收器转换显示列传播。 如果为透视键设置值，则 ADF 可以确定新的列名称，并且这些列名称将在 "检查和接收器" 映射中可用。

### <a name="generate-a-new-model-from-dynamic-columns"></a>从动态列生成新模型

Pivot 基于行值动态生成新的列名称。 您可以将这些新列转换为可以在数据流中稍后引用的元数据。 为此，请单击 "数据预览" 选项卡。透视转换生成的所有新列都在表头中显示为 "偏移" 图标。 单击 "映射偏移" 按钮，将这些新列转换为元数据，使其成为数据流的模型的一部分。

![透视列](media/data-flow/newpivot1.png "映射偏移透视列")

### <a name="landing-new-columns-in-sink"></a>在接收器中登陆新列

即使在透视中有动态列名称，仍可将新的列名称和值接收到目标存储中。 只需在接收器设置中将 "允许架构偏移" 设置为 "开" 即可。 您在列元数据中看不到新的动态名称，但是架构偏移选项将允许您将数据置于支持的模式。

### <a name="view-metadata-in-design-mode"></a>在设计模式下查看元数据

如果要在 "检查" 中查看新的列名称，并且想要查看列显式传播到接收器转换，请在 "透视键" 选项卡中设置显式值。

### <a name="how-to-rejoin-original-fields"></a>如何重新联接原始字段
“透视”转换将仅对聚合、分组和透视操作中使用的列进行投影。 如果希望在流中包含上一步骤中的其他列，请使用上一步骤中的新分支，并使用自联接模式将流与原始元数据连接。

## <a name="next-steps"></a>后续步骤

尝试[逆透视转换](data-flow-unpivot.md)以将列值转换为行值。 
