---
title: Azure 数据工厂映射数据流存在转换
description: 如何使用数据工厂映射使用存在转换的数据流来检查现有行
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 8b488a079b2da1bcf0dd064025ed251a1dc25213
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029386"
---
# <a name="mapping-data-flow-exists-transformation"></a>映射数据流存在转换



Exists 转换是阻止或允许数据行通过的行筛选转换。 Exists 转换类似于 ```SQL WHERE EXISTS``` 和 ```SQL WHERE NOT EXISTS```。 存在转换后，数据流中的结果行将包含源1中的列值存在于源2中的所有行或源2中不存在的行。

![Exists 设置](media/data-flow/exists.png "exists 1")

为 Exists 选择第二个源，以便数据流可以将流 1 的值与流 2 进行比较。

从源 1 和源 2 中选择对其值进行 Exists 或 Not Exists 检查的列。

## <a name="multiple-exists-conditions"></a>多个存在条件

在出现的列条件中的每行旁边，你会发现，当你将鼠标指针悬停在行上时，将显示一个 + 符号。 这将允许您为 Exists 条件添加多行。 每个附加条件都是 "And"。

## <a name="custom-expression"></a>自定义表达式

![存在](media/data-flow/exists1.png "自定义")自定义设置

您可以单击 "自定义表达式" 来创建自由格式的表达式，因为存在或不存在条件。 选中此框将允许您将自己的表达式键入为条件。

## <a name="next-steps"></a>后续步骤

类似的转换是[查找](data-flow-lookup.md)和[联接](data-flow-join.md)。
