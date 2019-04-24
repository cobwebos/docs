---
title: Azure 数据工厂映射数据流派生列转换
description: 如何将大规模使用 Azure 数据工厂映射数据流派生列转换的数据
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: f53e122eb1b2a5b6dabb9a44aef42394d0c7edb6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60478682"
---
# <a name="mapping-data-flow-derived-column-transformation"></a>映射数据流派生列转换

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

使用派生列转换在数据流中生成新列或修改现有字段。

![派生列](media/data-flow/dc1.png "Derived Column")

可以在单个派生列转换中执行多个派生列操作。 单击“添加列”可在单个转换步骤中转换多个列。

在列字段中，可以选择新派生值要覆盖的现有列，或者单击“新建列”生成具有新派生值的新列。

表达式文本框将打开表达式生成器，可以在其中使用表达式函数为派生列生成表达式。

## <a name="column-patterns"></a>列模式

如果列名称是从你的源的变量，您可能希望构建内部派生列转换使用而不使用名为列的列模式。 请参阅[架构偏差](concepts-data-flow-schema-drift.md)一文，了解更多详细信息。

![列模式](media/data-flow/columnpattern.png "列模式")

## <a name="next-steps"></a>后续步骤

详细了解如何[数据工厂进行转换的表达式语言](http://aka.ms/dataflowexpressions)和[表达式生成器](concepts-data-flow-expression-builder.md)
