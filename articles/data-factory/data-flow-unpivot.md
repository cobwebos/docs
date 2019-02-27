---
title: Azure 数据工厂映射数据流逆透视转换
description: Azure 数据工厂映射数据流逆透视转换
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 14326714fc8258e184024edb83666d3ed0c3eee7
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2019
ms.locfileid: "56270888"
---
# <a name="azure-data-factory-mapping-data-flow-unpivot-transformation"></a>Azure 数据工厂映射数据流逆透视转换

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

在 ADF 映射数据流中使用“逆透视”作为将非标准化数据集转换为更标准化版本的方法，通过将单个记录中多个列的值扩展为单个列中具有相同值的多个记录。

![逆透视转换](media/data-flow/unpivot1.png "逆透视选项 1")

## <a name="ungroup-by"></a>分组依据

![逆透视转换](media/data-flow/unpivot5.png "逆透视选项 2")

首先，为你的透视聚合设置要用作分组依据的列。 使用列列表旁边的 + 号设置一个或多个列用于取消组合。

## <a name="unpivot-key"></a>逆透视键

![逆透视转换](media/data-flow/unpivot6.png "逆透视选项 3")

透视键是 ADF 从行透视到列的列。 默认情况下，此字段的数据集中的每个唯一值都将透视到列。 但是，你也可以选择输入此数据集中要透视到列值的值。

## <a name="unpivoted-columns"></a>已逆透视列

![逆透视转换](media/data-flow//unpivot7.png "逆透视选项 4")

最后，选择要用于透视值的聚合以及如何在转换的新输出投影中显示列。

（可选）你可以设置一个命名模式，在其中包含要在行值中的每个新列名称中添加的前缀、中间名和后缀。

例如，通过“区域”透视“销售”只会为你提供每个销售值的新列值。 例如："25", "50", "1000", ...但是，如果设置“销售”的前缀值，那么“销售”将作为这些值的前缀。

<img src="media/data-flow/unpivot3.png" width="400">

将列排列设置为“正常”，将所有透视列与其聚合值组合在一起。 将列排列设置为“横向”将在列和值之间交替。

![逆透视转换](media/data-flow//unpivot7.png "逆透视选项 5")

最终的已逆透视数据结果集显示已逆透视到单独行值的列总值。
