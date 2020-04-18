---
title: Azure 数据工厂映射数据流的列模式
description: 使用 Azure 数据工厂映射数据流的列模式创建通用数据转换模式
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/21/2019
ms.openlocfilehash: aacec8830948e08f66d71da88897670f7ef43788
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606120"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>在映射数据流时使用列模式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

多个映射数据流转换允许您基于模式引用模板列，而不是硬编码的列名称。 此匹配称为*列模式*。 您可以根据名称、数据类型、流或位置定义模式以匹配列，而不是要求确切的字段名称。 有两种情况的列模式很有用：

* 如果传入源字段经常更改，例如更改文本文件或 NoSQL 数据库中的列。 此方案称为[架构漂移](concepts-data-flow-schema-drift.md)。
* 如果要对一组较大的列执行常见操作。 例如，想要将列名称中"总计"的每个列转换为双列。

列模式当前在派生列、聚合、选择和接收器转换中可用。

## <a name="column-patterns-in-derived-column-and-aggregate"></a>派生列和聚合中的列模式

要在派生列或聚合变换的聚合选项卡中添加列模式，请单击现有列右侧的加号图标。 选择 **"添加列"模式**。 

![列模式](media/data-flow/columnpattern.png "列模式")

使用[表达式生成器](concepts-data-flow-expression-builder.md)输入匹配条件。 创建一个布尔表达式，该表达式根据`name`列`type`的`stream`列`position`匹配列。 模式将影响条件返回 true 的任何列、漂移或定义。

匹配条件下方的两个表达式框指定受影响列的新名称和值。 用于`$$`引用匹配字段的现有值。 左侧表达式框定义名称，右侧表达式框定义值。

![列模式](media/data-flow/columnpattern2.png "列模式")

上述列模式匹配双精度类型的每一列，并为每个匹配创建一个聚合列。 新列的名称是匹配列的名称，与"_total"串联。 新列的值是现有双精度值的舍入聚合总和。

要验证匹配条件是否正确，可以在 **"检查"** 选项卡中验证已定义列的输出架构，或在 **"数据预览"** 选项卡中获取数据的快照。 

![列模式](media/data-flow/columnpattern3.png "列模式")

## <a name="rule-based-mapping-in-select-and-sink"></a>选择和接收器中的基于规则的映射

映射源中的列并选择转换时，可以添加固定映射或基于规则的映射。 根据`name`、、`type``stream`和`position`列匹配。 可以具有固定映射和基于规则的映射的任意组合。 默认情况下，所有大于 50 列的预测将默认为基于规则的映射，该映射与每列匹配并输出输入的名称。 

要添加基于规则的映射，请单击 **"添加映射"** 并选择**基于规则的映射**。

![基于规则的映射](media/data-flow/rule2.png "基于规则的映射")

每个基于规则的映射都需要两个输入：要匹配的条件以及每个映射列的名称。 这两个值都是通过[表达式生成器](concepts-data-flow-expression-builder.md)输入的。 在左侧表达式框中，输入布尔匹配条件。 在正确的表达式框中，指定匹配列将映射到的内容。

![基于规则的映射](media/data-flow/rule-based-mapping.png "基于规则的映射")

使用`$$`语法引用匹配列的输入名称。 以上面的图像为例，假设用户希望匹配名称短于六个字符的所有字符串列。 如果命名`test`了一个传入列，则`$$ + '_short'`表达式将重命名该列`test_short`。 如果这是唯一存在的映射，则不符合条件的所有列都将从输出的数据中删除。

模式匹配漂移列和定义的列。 要查看规则映射的已定义的列，请单击规则旁边的眼镜图标。 使用数据预览验证输出。

### <a name="regex-mapping"></a>雷格克斯映射

如果单击向下的 V 形图标，则可以指定正则表达式映射条件。 正则表达式映射条件与匹配指定正则表达式条件的所有列名称匹配。 这可以与基于规则的标准映射结合使用。

![基于规则的映射](media/data-flow/regex-matching.png "基于规则的映射")

上述示例与 regex 模式`(r)`或包含小写 r 的任何列名称匹配。 与基于标准规则的映射类似，使用`$$`语法使用右侧的条件会更改所有匹配列。

### <a name="rule-based-hierarchies"></a>基于规则的层次结构

如果定义的投影具有层次结构，则可以使用基于规则的映射来映射层次结构子列。 指定要映射的匹配条件和要映射的子列的复杂列。 每个匹配的子列都将使用右侧指定的"名称为"规则进行输出。

![基于规则的映射](media/data-flow/rule-based-hierarchy.png "基于规则的映射")

上述示例与复杂列的所有子列`a`匹配。 `a`包含两个子列`b`和`c`。 输出架构将包括两列`b`，并且`c`"名称为"条件为`$$`。

## <a name="pattern-matching-expression-values"></a>模式匹配表达式值。

* `$$`转换为运行时每个匹配项的名称或值
* `name`表示每个传入列的名称
* `type`表示每个传入列的数据类型
* `stream`表示与每个流关联的名称，或流中的转换
* `position`是数据流中列的位级位置

## <a name="next-steps"></a>后续步骤
* 了解有关数据转换的映射数据流[表达式语言](data-flow-expression-functions.md)的更多信息
* 在[接收器转换](data-flow-sink.md)中使用列模式，并使用基于规则的映射[选择变换](data-flow-select.md)
