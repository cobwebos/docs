---
title: Azure 数据工厂映射数据流列模式
description: 使用 Azure 数据工厂列模式在映射数据流中创建通用数据转换模式
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 8f1fa6f7823c643278e52ffd0faa1c0ce4972ef8
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640251"
---
# <a name="mapping-data-flows-column-patterns"></a>映射数据流列模式

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

多个 Azure 数据工厂数据流转换支持“列模式”理念，因此你可以根据模式而不是硬编码的列名称来创建模板列。 您可以使用 "表达式生成器" 中的此功能定义模式以匹配转换列, 而不需要精确的特定字段名称。 如果传入源字段经常更改, 这种模式非常有用, 尤其是在更改文本文件或 NoSQL 数据库中的列的情况下。 这种情况有时称为 "架构偏移"。

当前在派生列和聚合转换以及作为 "基于规则的映射" 的选择和接收器转换中都找到了此 "灵活架构" 处理。

![列模式](media/data-flow/columnpattern2.png "列模式")

## <a name="column-patterns"></a>列模式
列模式用于处理架构偏差方案和传给方案。 它适用于你无法完全知道每个列名的情况。 可以在列名和列数据类型上进行模式匹配，然后生成一个用于转换的表达式，以便针对数据流中与 `name` & `type` 模式匹配的任何字段执行该操作。

将表达式添加到接受多种模式的转换时，请选择“添加列模式”。 列模式允许架构偏差列匹配模式。

生成模板列模式时，请在表达式中使用 `$$` 来表示对输入数据流中每个已匹配字段的引用。

如果选择使用某个表达式生成器正则表达式函数，则可随后使用 $1、$2、$3 ... 来引用正则表达式中匹配的子模式。

列模式方案的一个示例是对一系列传入字段使用 SUM。 聚合 SUM 计算位于“聚合”转换中。 然后, 你可以对匹配 "integer" 的字段类型的每个匹配项使用 SUM, 然后使用 $ $ 来引用表达式中的每个匹配项。

## <a name="match-columns"></a>匹配列
![列模式类型](media/data-flow/pattern2.png "模式类型")

若要基于列生成模式, 可以匹配列名、类型、流或位置, 并将它们的任意组合与表达式函数和正则表达式结合使用。

![列位置](media/data-flow/position.png "列位置")

## <a name="rule-based-mapping"></a>基于规则的映射
映射源中的列并选择转换时, 可以选择 "固定映射" 或 "基于规则的映射"。 如果你知道数据的架构, 并且期望源数据集中始终匹配特定静态名称的特定列, 则可以使用固定映射。 但在使用灵活的架构时, 请使用基于规则的映射。 你将能够使用上述规则构建模式匹配。

![基于规则的映射](media/data-flow/rule2.png "基于规则的映射")

## <a name="next-steps"></a>后续步骤
* 了解有关数据转换的 ADF 映射数据流[表达式语言](http://aka.ms/dataflowexpressions)的详细信息
* 在[接收器转换](data-flow-sink.md)中使用列模式, 并使用基于规则的映射[选择转换](data-flow-select.md)