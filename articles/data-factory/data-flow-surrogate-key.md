---
title: 映射数据流代理密钥转换
description: 如何使用 Azure 数据工厂的映射数据流代理密钥转换生成顺序键值
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: bab48aa9079c1b8020bb828a6bb91bd244a78cf1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930208"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>映射数据流代理密钥转换



使用代理键转换将递增的非业务任意键值添加到数据流行集。 这对于在星型架构分析数据模型中设计维度表非常有用，在该模型中维度表中的每个成员都需要拥有一个非业务的唯一键（这是 Kimball DW 方法的一部分）。

![代理密钥转换](media/data-flow/surrogate.png "代理密钥转换")

“键列”是将赋予新代理键列的名称。

"起始值"是增量值的起始点。

## <a name="increment-keys-from-existing-sources"></a>来自现有源的增量键

如果要从源中存在的值启动序列，可以在代理密钥转换后立即使用派生列转换，并将这两个值一起添加：

![SK 添加最大值](media/data-flow/sk006.png "代理密钥转换添加最大值")

要用前面的最大值设定键值的种子，可以使用两种技术：

### <a name="database-sources"></a>数据库源

使用"查询"选项使用源转换从源中选择 MAX（）：

![代理密钥查询](media/data-flow/sk002.png "代理密钥转换查询")

### <a name="file-sources"></a>文件源

如果以前的最大值位于文件中，则可以使用源变换和聚合转换，并使用 MAX（） 表达式函数获取以前的最大值：

![代理密钥文件](media/data-flow/sk008.png "代理密钥文件")

在这两种情况下，都必须将传入的新数据与包含前一个最大值的源联接在一起：

![代理密钥联接](media/data-flow/sk004.png "代理密钥联接")

## <a name="next-steps"></a>后续步骤

这些示例使用[联接](data-flow-join.md)和[派生列](data-flow-derived-column.md)转换。
