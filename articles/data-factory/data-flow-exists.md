---
title: Azure 数据工厂映射数据流存在转换
description: 如何使用数据工厂映射使用存在转换的数据流来检查现有行
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 6048a6d30d37b9d2b46c3105c5f8eac0a9ca41c0
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387839"
---
# <a name="mapping-data-flow-exists-transformation"></a>映射数据流存在转换



Exists 转换是阻止或允许数据行通过的行筛选转换。 Exists 转换类似于 ```SQL WHERE EXISTS``` 和 ```SQL WHERE NOT EXISTS```。 存在转换后，数据流中的结果行将包含源1中的列值存在于源2中的所有行或源2中不存在的行。

![存在设置](media/data-flow/exists.png "存在1")

为 Exists 选择第二个源，以便数据流可以将流 1 的值与流 2 进行比较。

从源 1 和源 2 中选择对其值进行 Exists 或 Not Exists 检查的列。

## <a name="multiple-exists-conditions"></a>多个存在条件

在出现的列条件中的每行旁边，你会发现，当你将鼠标指针悬停在行上时，将显示一个 + 符号。 这将允许您为 Exists 条件添加多行。 每个附加条件都是 "And"。

## <a name="custom-expression"></a>自定义表达式

![存在自定义设置](media/data-flow/exists1.png "存在自定义")

您可以单击 "自定义表达式" 来创建自由格式的表达式，因为存在或不存在条件。 选中此框将允许您将自己的表达式键入为条件。

## <a name="next-steps"></a>后续步骤

类似的转换是[查找](data-flow-lookup.md)和[联接](data-flow-join.md)。
