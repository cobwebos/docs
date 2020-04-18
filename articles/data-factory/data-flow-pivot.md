---
title: 映射数据流枢轴转换
description: 使用 Azure 数据工厂映射数据流数据透视转换，从行到列透视数据
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: 980d7c3e1b1f69e76c091e2a4a74c8e5a4d0bb64
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606363"
---
# <a name="azure-data-factory-pivot-transformation"></a>Azure 数据工厂数据透视转换

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

在 ADF 数据流中使用“透视”作为聚合，其中一个或多个分组列将其不同的行值转换到各个列中。 实际上，你可以将行值透视到新列中（将数据转换为元数据）。

![透视选项](media/data-flow/pivot1.png "枢轴 1")

## <a name="group-by"></a>Group by

![透视选项](media/data-flow/pivot2.png "枢轴 2")

首先，为你的透视聚合设置要用作分组依据的列。 此处，你可以设置 1 个以上的列，列的列表旁边会显示一个 + 号。

## <a name="pivot-key"></a>枢轴键

![透视选项](media/data-flow/pivot3.png "枢轴 3")

透视键是 ADF 从行透视到列的列。 默认情况下，此字段的数据集中的每个唯一值都将透视到列。 但是，你也可以选择输入此数据集中要透视到列值的值。 这是确定将创建的新列的列。

## <a name="pivoted-columns"></a>旋转列

![透视选项](media/data-flow/pivot4.png "枢轴 4")

最后，你将选择要用于透视的值的聚合以及如何在转换的新输出投影中显示列。

（可选）你可以设置一个命名模式，在其中包含要在行值中的每个新列名称中添加的前缀、中间名和后缀。

例如，将"销售"旋转为"区域"将导致每个销售值产生新的列值，即"25"、"50"、"1000"等。但是，如果设置前缀值"Sales-"，则每个列值将添加"Sales-"到该值的开头。

![透视选项](media/data-flow/pivot5.png "枢轴 5")

将列排列设置为“正常”，将所有透视列与其聚合值组合在一起。 将列排列设置为“横向”将在列和值之间交替。

### <a name="aggregation"></a>聚合

若要设置要用于透视值的聚合，请单击“透视的列”窗格底部的字段。 你将进入 ADF 数据流表达式生成器，可以在其中构建聚合表达式并为新的聚合值提供描述性的别名。

在表达式生成器中使用 ADF 数据流表达式语言来描述透视列转换：https://aka.ms/dataflowexpressions。

## <a name="pivot-metadata"></a>数据透视元数据

数据透视转换将生成基于传入数据的动态的新列名称。 数据透视键生成每个新列名称的值。 如果不指定单个值，并希望为数据透视键中的每个唯一值创建动态列名称，则 UI 将不会在"检查"中显示元数据，并且不会向 Sink 转换传播列。 如果为数据透视键设置值，则 ADF 可以确定新的列名称，这些列名称将在"检查和接收器"映射中可供您使用。

### <a name="generate-a-new-model-from-dynamic-columns"></a>从动态列生成新模型

数据透视基于行值动态生成新的列名称。 您可以将这些新列转换为元数据，可在数据流中稍后引用。 为此，请单击"数据预览"选项卡。透视转换生成的所有新列都显示在表标题中，并带有"漂移"图标。 单击"地图漂移"按钮，将这些新列转换为元数据，使其成为数据流模型的一部分。

![列透视](media/data-flow/newpivot1.png "映射漂移枢轴列")

### <a name="landing-new-columns-in-sink"></a>在沉陷中登陆新列

即使使用数据透视中的动态列名称，您仍可以将新的列名称和值沉入目标存储中。 只需在"接收器"设置中设置"允许架构漂移"。 您将不会在列元数据中看到新的动态名称，但架构漂移选项将允许您登陆数据。

### <a name="view-metadata-in-design-mode"></a>在设计模式下查看元数据

如果要在"检查"中将新列名称视为元数据，并且希望看到列显式传播到"接收器"转换，然后在"透视键"选项卡中设置显式值。

### <a name="how-to-rejoin-original-fields"></a>如何重新联接原始字段
“透视”转换将仅对聚合、分组和透视操作中使用的列进行投影。 如果要在流中包括上一步中的其他列，请使用上一步中的新分支，并使用自联接模式将流与原始元数据连接。

## <a name="next-steps"></a>后续步骤

尝试[取消透视变换](data-flow-unpivot.md)将列值转换为行值。 
