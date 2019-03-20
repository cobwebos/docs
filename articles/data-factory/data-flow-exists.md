---
title: Azure 数据工厂映射数据流 Exists 转换
description: Azure 数据工厂映射数据流 Exists 转换
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 18d8a0e231e8b4dbe33911dd6267966674366904
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734483"
---
# <a name="azure-data-factory-mapping-data-flow-exists-transformation"></a>Azure 数据工厂映射数据流 Exists 转换

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Exists 转换是阻止或允许数据行通过的行筛选转换。 Exists 转换类似于 ```SQL WHERE EXISTS``` 和 ```SQL WHERE NOT EXISTS```。 筛选器转换之后，数据流的结果行将包括源 1 列值存在于源 2 或不存在于源 2 中的所有行。

![Exists 设置](media/data-flow/exsits.png "exists 1")

为 Exists 选择第二个源，以便数据流可以将流 1 的值与流 2 进行比较。

从源 1 和源 2 中选择对其值进行 Exists 或 Not Exists 检查的列。
