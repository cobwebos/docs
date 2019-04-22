---
title: Azure 数据工厂映射数据流 Exists 转换
description: Azure 数据工厂映射数据流 Exists 转换
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 6ce27ba699ae766ed4d2428f67d91379464bb9f1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59783456"
---
# <a name="azure-data-factory-mapping-data-flow-exists-transformation"></a>Azure 数据工厂映射数据流 Exists 转换

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Exists 转换是阻止或允许数据行通过的行筛选转换。 Exists 转换类似于 ```SQL WHERE EXISTS``` 和 ```SQL WHERE NOT EXISTS```。 筛选器转换之后，数据流的结果行将包括源 1 列值存在于源 2 或不存在于源 2 中的所有行。

![Exists 设置](media/data-flow/exsits.png "exists 1")

为 Exists 选择第二个源，以便数据流可以将流 1 的值与流 2 进行比较。

从源 1 和源 2 中选择对其值进行 Exists 或 Not Exists 检查的列。

## <a name="multiple-exists-conditions"></a>多个存在条件

中存在您列条件的每一行旁, 找到 + 符号可用时悬停在到达行。 这样，您可以添加多个行 Exists 条件。

## <a name="next-steps"></a>后续步骤

