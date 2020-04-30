---
title: Azure 数据工厂映射数据流中的列模式
description: 使用 Azure 数据工厂中的列模式映射数据流创建通用数据转换模式
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/21/2019
ms.openlocfilehash: aacec8830948e08f66d71da88897670f7ef43788
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606120"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>使用映射数据流中的列模式

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

多个映射数据流转换允许您基于模式而不是硬编码的列名称引用模板列。 此匹配称为*列模式*。 您可以根据名称、数据类型、流或位置定义模式来匹配列，而不需要精确的字段名。 在以下两种情况下，列模式很有用：

* 如果传入源字段经常更改，例如更改文本文件或 NoSQL 数据库中的列的大小写。 此方案称为[架构偏差](concepts-data-flow-schema-drift.md)。
* 如果要对一大组列执行常见操作， 例如，想要将其列名中包含 "total" 的每个列转换为 double。

列模式当前在派生列、聚合、选择和接收器转换中可用。

## <a name="column-patterns-in-derived-column-and-aggregate"></a>派生列和聚合中的列模式

若要在聚合转换的派生列或聚合选项卡中添加列模式，请单击现有列右侧的加号图标。 选择 "**添加列模式**"。 

![列模式](media/data-flow/columnpattern.png "列模式")

使用 "[表达式生成器](concepts-data-flow-expression-builder.md)" 输入匹配条件。 `name`基于列的`type`、 `stream`、和`position`创建与列匹配的布尔表达式。 此模式将影响任何偏移或定义的列，其中条件返回 true。

Match 条件下的两个表达式框指定受影响的列的新名称和值。 用于`$$`引用匹配字段的现有值。 左侧表达式框定义名称，右侧表达式框定义值。

![列模式](media/data-flow/columnpattern2.png "列模式")

以上列模式匹配类型为 double 的每一列，并为每个匹配项创建一个聚合列。 新列的名称是匹配列的名称，与 "_total" 串联。 新列的值是已聚合的现有双精度值的总和。

若要验证匹配条件是否正确，可以在 "**检查**" 选项卡中验证已定义列的输出架构，或在 "**数据预览**" 选项卡中获取数据的快照。 

![列模式](media/data-flow/columnpattern3.png "列模式")

## <a name="rule-based-mapping-in-select-and-sink"></a>Select 和 sink 中基于规则的映射

映射源中的列并选择转换时，可以添加固定映射或基于规则的映射。 `name`根据列的`type`、、 `stream`和进行`position`匹配。 可以有固定和基于规则的映射的任意组合。 默认情况下，每列大于50的所有投影都将默认为基于规则的映射，该映射在每个列上都匹配并输出输入名称。 

若要添加基于规则的映射，请单击 "**添加映射**"，然后选择 "**基于规则的映射**"。

![基于规则的映射](media/data-flow/rule2.png "基于规则的映射")

每个基于规则的映射都需要两个输入：要匹配的条件以及每个映射列的名称。 这两个值都是通过[表达式生成器](concepts-data-flow-expression-builder.md)输入的。 在左侧的 "表达式" 框中，输入您的布尔匹配条件。 在右侧的 "表达式" 框中，指定匹配的列将映射到的列。

![基于规则的映射](media/data-flow/rule-based-mapping.png "基于规则的映射")

使用`$$`语法来引用匹配列的输入名称。 使用上述图像作为示例，假设用户想要匹配名称长度少于6个字符的所有字符串列。 如果一个传入列已命名`test`，则表达式`$$ + '_short'`将重命名该列`test_short`。 如果这是唯一存在的映射，则将从输出的数据中删除所有不符合条件的列。

模式匹配偏移和已定义的列。 若要查看规则所映射的定义列，请单击该规则旁边的眼镜图标。 使用数据预览来验证输出。

### <a name="regex-mapping"></a>Regex 映射

如果单击向下 v 形图标，则可以指定 regex 映射条件。 Regex 映射条件与与指定的正则表达式匹配的所有列名称匹配。 这可以与基于标准规则的映射结合使用。

![基于规则的映射](media/data-flow/regex-matching.png "基于规则的映射")

上面的示例匹配正则表达式`(r)`模式或任何包含小写字母的列名称。 与基于标准规则的映射类似，所有匹配的列都通过使用`$$`语法在右侧的条件下进行更改。

### <a name="rule-based-hierarchies"></a>基于规则的层次结构

如果您定义的投影具有层次结构，则可以使用基于规则的映射来映射层次结构个子列。 指定匹配条件以及要映射其个子列的复杂列。 将使用右侧指定的 "名称为" 规则输出每个匹配的 subcolumn。

![基于规则的映射](media/data-flow/rule-based-hierarchy.png "基于规则的映射")

上面的示例匹配复杂列`a`的所有个子列。 `a`包含两个`b`个子列`c`和。 输出架构将包括两个列`b` ， `c`并且 "名称为" 条件为`$$`。

## <a name="pattern-matching-expression-values"></a>模式匹配表达式的值。

* `$$`在运行时转换为每个匹配项的名称或值
* `name`表示每个传入列的名称
* `type`表示每个传入列的数据类型
* `stream`表示与流中的每个流或转换相关联的名称。
* `position`是数据流中列的序号位置

## <a name="next-steps"></a>后续步骤
* 了解有关数据转换的映射数据流[表达式语言](data-flow-expression-functions.md)的详细信息
* 在[接收器转换](data-flow-sink.md)中使用列模式，并使用基于规则的映射[选择转换](data-flow-select.md)
