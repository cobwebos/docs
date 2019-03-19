---
title: Azure 数据工厂映射数据流聚合转换
description: Azure 数据工厂数据流聚合转换
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 7b488b243c0520befb6b5470598f460b5a759fed
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2019
ms.locfileid: "56730030"
---
# <a name="azure-data-factory-mapping-data-flow-aggregate-transformation"></a>Azure 数据工厂映射数据流聚合转换

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

可以在聚合转换中定义数据流中列的聚合。 在表达式生成器中，可以定义不同类型的聚合（例如 SUM、MIN、MAX、COUNT 等）并在输出中创建新的字段，其中包含这些具有可选分组依据字段聚合。

![聚合转换选项](media/data-flow/agg.png "聚合 1")

## <a name="group-by"></a>分组依据
（可选）为聚合选择一个分组依据子句，并使用现有列的名称或新名称。 使用"添加列"添加多个分组依据子句，然后单击列名旁边的文本框以启动表达式生成器，以便仅为分组选择现有列、列组合或表达式。

## <a name="the-aggregate-column-tab"></a>聚合列选项卡 
（必需）选择“聚合列”选项卡以生成聚合表达式。 可以选择一个现有列以使用聚合覆盖值，或使用聚合的新名称创建新的字段。 在列名选择器旁的右侧框中输入要用于聚合的表达式。 单击该文本框将打开表达式生成器。

![聚合转换选项](media/data-flow/agg2.png "聚合器")

## <a name="data-preview-in-expression-builder"></a>表达式生成器中的数据预览

在“调试”模式下，表达式生成器无法使用聚合函数生成数据预览。 若要查看聚合转换的数据预览，请关闭表达式生成器并查看数据流设计器中的数据配置文件。
