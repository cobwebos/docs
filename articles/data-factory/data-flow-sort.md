---
title: Azure 数据工厂映射数据流排序转换
description: Azure 数据工厂映射数据排序转换
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: 029ce3c509d3f4d241012d3786e60f0c6e95fdc2
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387175"
---
# <a name="azure-data-factory-data-flow-sort-transformations"></a>Azure 数据工厂数据流排序转换



![排序设置](media/data-flow/sort.png "排序")

借助排序转换，可对当前数据流上的传入行进行排序。 来自排序转换的传出行随后将遵循设置的排序规则。 可以选择单个列，然后使用每个字段旁边的箭头指示器对其进行升序或降序排序。 如果在应用排序前需要修改列，单击“计算列”以启动表达式编辑器。 这将提供为排序操作生成表达式的机会，而不是只为该排序应用某一列。

## <a name="case-insensitive"></a>不区分大小写
如果在对字符串或文本字段排序时想要忽略大小写，可打开“不区分大小写”。

“仅在分区内排序”利用 Spark 数据分区。 通过仅对每个分区内的传入数据排序，数据流可以对分区数据进行排序，而不是对整个数据流进行排序。

排序转换中的每个排序条件都可以重新排列。 因此，如果需要在排序优先级中将列上移，可使用鼠标抓取该行并在排序列表中将其上移或下移。

分区对排序的影响

ADF 数据流在大数据 Spark 群集上执行，数据跨多个节点和分区分布。 构建数据流时，如果要依靠排序转换让数据保持同一顺序，请务必记住这一点。 如果选择在后续转换中对数据进行重新分区，则可能会因为数据重排而丢失排序。

## <a name="next-steps"></a>后续步骤

排序后，您可能希望使用[聚合转换](data-flow-aggregate.md)
