---
title: Azure 数据工厂数据流派生列转换
description: Azure 数据工厂数据流派生列转换
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: aee005aed52563e214e24148af2563fd7869de76
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2019
ms.locfileid: "56270896"
---
# <a name="azure-data-factory-data-flow-derived-column-transformation"></a>Azure 数据工厂数据流派生列转换

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

使用派生列转换在数据流中生成新列或修改现有字段。

![派生列](media/data-flow/dc1.png "Derived Column")

可以在单个派生列转换中执行多个派生列操作。 单击“添加列”可在单个转换步骤中转换多个列。

在列字段中，可以选择新派生值要覆盖的现有列，或者单击“新建列”生成具有新派生值的新列。

表达式文本框将打开表达式生成器，可以在其中使用表达式函数为派生列生成表达式。
