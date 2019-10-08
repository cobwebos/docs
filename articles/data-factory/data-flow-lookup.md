---
title: Azure 数据工厂映射数据流查找转换
description: Azure 数据工厂映射数据流查找转换
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: ef72b7aed12afd1cee47b11bc7584d1e53bf2af5
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029342"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Azure 数据工厂映射数据流查找转换



使用“查找”将其他源的参考数据添加到数据流中。 查找转换需要一个定义的源，它指向你的引用表并匹配关键字段。

![查找转换](media/data-flow/lookup1.png "查找")

选择要在传入流字段和参考源字段之间匹配的关键字段。 必须先在数据流设计画布上创建一个新源，以在右侧用于查找。

找到匹配项后，参考源中生成的行和列将添加到数据流中。 可以在数据流的末尾选择要包含在接收器中的感兴趣的字段。

## <a name="match--no-match"></a>匹配/不匹配

进行查找转换后，可以使用 expression 函数 `isMatch()` 根据查找是否导致行匹配，来检查每个匹配行的结果，以检查每个匹配行的结果。

## <a name="optimizations"></a>优化

在数据工厂中，数据流在扩展的 Spark 环境中执行。 如果数据集适合工作节点内存空间，则可以优化查找性能。

![广播联接](media/data-flow/broadcast.png "广播联接")

### <a name="broadcast-join"></a>广播联接

选择 "左" 和/或 "右" "向右广播" "请求 ADF"，将整个数据集从查找关系的两侧推送到内存。

### <a name="data-partitioning"></a>数据分区

您还可以通过在查找转换的 "优化" 选项卡上选择 "设置分区" 来指定数据的分区，以创建可在每个辅助角色中更好地适应内存的数据集。

## <a name="next-steps"></a>后续步骤

[联接](data-flow-join.md)和[存在](data-flow-exists.md)转换在 ADF 映射数据流中执行类似任务。 接下来，请查看这些转换。
