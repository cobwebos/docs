---
title: Azure 数据工厂映射数据流查找转换
description: Azure 数据工厂映射数据流查找转换
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/03/2019
ms.openlocfilehash: 01a1dba18e21a38695146560bbf85cf1a042ba02
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596579"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Azure 数据工厂映射数据流查找转换

使用“查找”将其他源的参考数据添加到数据流中。 查找转换需要一个定义的源，它指向你的引用表并匹配关键字段。

![查找转换](media/data-flow/lookup1.png "查找")

选择要在传入流字段和参考源字段之间匹配的关键字段。 必须先在数据流设计画布上创建一个新源，以在右侧用于查找。

找到匹配项后，参考源中生成的行和列将添加到数据流中。 可以在数据流的末尾选择要包含在接收器中的感兴趣的字段。 或者，在查找时使用 "选择" 转换来修剪字段列表，以便仅保留两个流中要保留的字段。

查找转换执行左外部联接的等效操作。 因此，你会看到左侧源中的所有行都与你的右侧的匹配项相结合。 如果在查找中有多个匹配值，或者如果想要自定义查找表达式，更可取的方法是切换到联接转换并使用交叉联接。 这将避免执行时出现笛卡尔积错误。

## <a name="match--no-match"></a>匹配/不匹配

进行查找转换后，可以使用 expression 函数 `isMatch()` 根据查找是否导致行匹配，来检查每个匹配行的结果，以检查每个匹配行的结果。

![查找模式](media/data-flow/lookup111.png "查找模式")

使用查找转换后，可以在 ```isMatch()``` 函数上添加有条件拆分转换拆分。 在上面的示例中，匹配行经过最顶部的流，而不匹配的行则流过 ```NoMatch``` 流。

## <a name="first-or-last-value"></a>第一个或最后一个值

如果有多个匹配项与查找相匹配，则可能需要选取第一个或最后一个匹配项来减少多个匹配行。 您可以在查找后使用聚合转换来执行此操作。

在这种情况下，会使用一个名为 ```PickFirst``` 的聚合转换选取查找匹配项中的第一个值。

![查找聚合](media/data-flow/lookup333.png "查找聚合")

![首先查找](media/data-flow/lookup444.png "首先查找")

## <a name="optimizations"></a>优化

在数据工厂中，数据流在扩展的 Spark 环境中执行。 如果数据集适合工作节点内存空间，则可以优化查找性能。

![广播联接](media/data-flow/broadcast.png "广播联接")

### <a name="broadcast-join"></a>广播联接

选择 "左" 和/或 "右" "向右广播" "请求 ADF"，将整个数据集从查找关系的两侧推送到内存。 对于较小的数据集，这可以极大地提高查找性能。

### <a name="data-partitioning"></a>数据分区

您还可以通过在查找转换的 "优化" 选项卡上选择 "设置分区" 来指定数据的分区，以创建可在每个辅助角色中更好地适应内存的数据集。

## <a name="next-steps"></a>后续步骤

* [联接](data-flow-join.md)和[存在](data-flow-exists.md)转换在 ADF 映射数据流中执行类似任务。 接下来，请查看这些转换。
* 使用带有 ```isMatch()``` 的有[条件拆分](data-flow-conditional-split.md)拆分匹配和不匹配值的行
