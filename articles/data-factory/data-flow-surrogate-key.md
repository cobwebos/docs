---
title: Azure 数据工厂映射数据流代理键转换
description: 如何使用 Azure 数据工厂的映射数据流代理键转换生成顺序键值
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 144d6298a13d35d94a68b35c443a3a47cefcfc2a
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387164"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>映射数据流代理键转换



使用代理键转换将递增的非业务任意键值添加到数据流行集。 这对于在星型架构分析数据模型中设计维度表非常有用，在该模型中维度表中的每个成员都需要拥有一个非业务的唯一键（这是 Kimball DW 方法的一部分）。

![代理键转换](media/data-flow/surrogate.png "代理键转换")

“键列”是将赋予新代理键列的名称。

"起始值"是增量值的起始点。

## <a name="increment-keys-from-existing-sources"></a>从现有源增加密钥

如果要从源中存在的值开始序列，可以在代理键转换后立即使用派生列转换，并将两个值相加：

![SK 添加 Max](media/data-flow/sk006.png "代理键转换添加 Max")

若要使用以前的最大值播种密钥值，可使用以下两种方法：

### <a name="database-sources"></a>数据库源

使用 "查询" 选项，通过源转换选择源中的 MAX （）：

![代理键查询](media/data-flow/sk002.png "代理键转换查询")

### <a name="file-sources"></a>文件源

如果以前的最大值位于文件中，则可以将源转换与聚合转换一起使用，并使用 MAX （） expression 函数获取上一个最大值：

![代理键文件](media/data-flow/sk008.png "代理键文件")

在这两种情况下，你都必须将传入的新数据与包含之前的最大值的源一起联接：

![代理键联接](media/data-flow/sk004.png "代理键联接")

## <a name="next-steps"></a>后续步骤

这些示例使用[联接](data-flow-join.md)和[派生列](data-flow-derived-column.md)转换。
