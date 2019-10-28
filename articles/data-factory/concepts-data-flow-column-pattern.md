---
title: Azure 数据工厂映射数据流中的列模式
description: 使用 Azure 数据工厂中的列模式映射数据流创建通用数据转换模式
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/21/2019
ms.openlocfilehash: 0c9a3c2ef05f4a11933ca7fc81c7c0f87a612293
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72789944"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>使用映射数据流中的列模式

多个映射数据流转换允许您基于模式而不是硬编码的列名称引用模板列。 此匹配称为*列模式*。 您可以根据名称、数据类型、流或位置定义模式来匹配列，而不需要精确的字段名。 在以下两种情况下，列模式很有用：

* 如果传入源字段经常更改，例如更改文本文件或 NoSQL 数据库中的列的大小写。 此方案称为[架构偏差](concepts-data-flow-schema-drift.md)。
* 如果要对一大组列执行常见操作， 例如，想要将其列名中包含 "total" 的每个列转换为 double。

列模式当前在派生列、聚合、选择和接收器转换中可用。

## <a name="column-patterns-in-derived-column-and-aggregate"></a>派生列和聚合中的列模式

若要在聚合转换的派生列或聚合选项卡中添加列模式，请单击现有列右侧的加号图标。 选择 "**添加列模式**"。 

![列模式](media/data-flow/columnpattern.png "列模式")

使用 "[表达式生成器](concepts-data-flow-expression-builder.md)" 输入匹配条件。 基于列的 `name`、`type`、`stream`和 `position` 创建与列匹配的布尔表达式。 此模式将影响任何偏移或定义的列，其中条件返回 true。

Match 条件下的两个表达式框指定受影响的列的新名称和值。 使用 `$$` 引用匹配字段的现有值。 左侧表达式框定义名称，右侧表达式框定义值。

![列模式](media/data-flow/columnpattern2.png "列模式")

以上列模式匹配类型为 double 的每一列，并为每个匹配项创建一个聚合列。 新列的名称是匹配列的名称，与 "_total" 串联。 新列的值是已聚合的现有双精度值的总和。

若要验证匹配条件是否正确，可以在 "**检查**" 选项卡中验证已定义列的输出架构，或在 "**数据预览**" 选项卡中获取数据的快照。 

![列模式](media/data-flow/columnpattern3.png "列模式")

## <a name="rule-based-mapping-in-select-and-sink"></a>Select 和 sink 中基于规则的映射

映射源中的列并选择转换时，可以添加固定映射或基于规则的映射。 如果你知道数据的架构并希望源数据集中的特定列始终与特定静态名称匹配，请使用固定映射。 如果使用的是灵活的架构，请使用基于规则的映射基于列的 `name`、`type`、`stream`和 `position` 来构建模式匹配。 可以有固定和基于规则的映射的任意组合。 

若要添加基于规则的映射，请单击 "**添加映射**"，然后选择 "**基于规则的映射**"。

![基于规则的映射](media/data-flow/rule2.png "基于规则的映射")

在左侧的 "表达式" 框中，输入您的布尔匹配条件。 在右侧的 "表达式" 框中，指定匹配的列将映射到的列。 使用 `$$` 引用匹配字段的现有名称。

如果单击向下 v 形图标，则可以指定 regex 映射条件。

单击基于规则的映射旁边的 "眼镜" 图标，以查看哪些定义的列已匹配以及它们映射到的内容。

![基于规则的映射](media/data-flow/rule1.png "基于规则的映射")

在上面的示例中，创建了两个基于规则的映射。 第一种方法是将所有列都命名为 "movie"，并将其映射到其现有值。 第二个规则使用 regex 来匹配所有以 "movie" 开头的列，并将其映射到列 "movieId"。

如果规则产生多个相同的映射，请启用 "**跳过重复输入**" 或 "**跳过重复的输出**"，以防止重复项。

## <a name="pattern-matching-expression-values"></a>模式匹配表达式的值。

* `$$` 在运行时转换为每个匹配项的名称或值
* `name` 表示每个传入列的名称
* `type` 表示每个传入列的数据类型
* `stream` 表示与流中的每个流或转换相关联的名称。
* `position` 是数据流中列的序号位置

## <a name="next-steps"></a>后续步骤
* 了解有关数据转换的映射数据流[表达式语言](data-flow-expression-functions.md)的详细信息
* 在[接收器转换](data-flow-sink.md)中使用列模式，并使用基于规则的映射[选择转换](data-flow-select.md)
