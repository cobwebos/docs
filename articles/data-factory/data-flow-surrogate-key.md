---
title: Azure 数据工厂映射数据流代理键转换
description: 如何使用 Azure 数据工厂的映射数据流代理项键转换以生成密钥的连续值
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: eaa1c577f7e208400d3430222b006e0dbbd7956a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61350340"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>映射数据数据流代理项键转换

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

使用代理键转换将递增的非业务任意键值添加到数据流行集。 这对于在星型架构分析数据模型中设计维度表非常有用，在该模型中维度表中的每个成员都需要拥有一个非业务的唯一键（这是 Kimball DW 方法的一部分）。

![代理键转换](media/data-flow/surrogate.png "Surrogate Key Transformation")

“键列”是将赋予新代理键列的名称。

"起始值"是增量值的起始点。

## <a name="increment-keys-from-existing-sources"></a>从现有源增量密钥

如果你想要从一个值，在源中存在启动您的序列，可使用派生列转换紧跟代理键转换并将两个值相加：

![SK 添加最大](media/data-flow/sk006.png "代理项键转换添加最大")

以设定种子的键值与以前的最大值，有两种技术，可以使用：

### <a name="database-sources"></a>数据库源

使用"查询"选项以使用源转换从源选择 max （）：

![代理项键的查询](media/data-flow/sk002.png "代理项键转换查询")

### <a name="file-sources"></a>文件源

如果你以前的最大值是在文件中，可以使用聚合转换以及在源转换并使用 max （） 表达式函数获取上一个最大值：

![代理密钥文件](media/data-flow/sk008.png "代理密钥文件")

在这两种情况下，必须在源中包含的以前的最大值以及加入您传入的新数据：

![代理项键联接](media/data-flow/sk004.png "代理项键联接")

## <a name="next-steps"></a>后续步骤

这些示例使用[加入](data-flow-join.md)并[派生列](data-flow-derived-column.md)转换。
