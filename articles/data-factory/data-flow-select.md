---
title: 映射数据流 选择转换
description: Azure 数据工厂映射数据流 选择转换
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/18/2020
ms.openlocfilehash: cfa15f5424dcd5d52b03fb65afe051444127f5ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065238"
---
# <a name="select-transformation-in-mapping-data-flow"></a>在映射数据流中选择转换

使用选择转换重命名、删除或重新排序列。 此转换不会更改行数据，而是选择向下游传播的列。 

在选择转换中，用户可以指定固定映射、使用模式执行基于规则的映射或启用自动映射。 固定映射和基于规则的映射都可以在同一选择转换中使用。 如果列与定义的映射之一不匹配，则将删除该列。

## <a name="fixed-mapping"></a>固定映射

如果投影中定义的列少于 50 列，则默认情况下所有定义的列都将具有固定映射。 固定映射采用定义的传入列，并将其映射为确切名称。

![固定映射](media/data-flow/fixedmapping.png "固定映射")

> [!NOTE]
> 不能使用固定映射映射或重命名漂移列

### <a name="mapping-hierarchical-columns"></a>映射分层列

固定映射可用于将分层列的子列映射到顶级列。 如果您有定义的层次结构，请使用列下拉列表来选择子列。 选择转换将创建一个新列，该列的值和数据类型为子列。

![分层映射](media/data-flow/select-hierarchy.png "分层映射")

## <a name="rule-based-mapping"></a>基于规则的映射

如果要同时映射许多列或向下游传递漂移列，请使用基于规则的映射使用列模式定义映射。 根据`name`、、`type``stream`和`position`列匹配。 可以具有固定映射和基于规则的映射的任意组合。 默认情况下，所有大于 50 列的预测将默认为基于规则的映射，该映射与每列匹配并输出输入的名称。 

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

如果列名称中有多个正则表达式匹配项，则可以使用"n"引用哪个匹配项`$n`引用特定匹配项。 例如，"$2"是指列名中的第二个匹配项。

### <a name="rule-based-hierarchies"></a>基于规则的层次结构

如果定义的投影具有层次结构，则可以使用基于规则的映射来映射层次结构子列。 指定要映射的匹配条件和要映射的子列的复杂列。 每个匹配的子列都将使用右侧指定的"名称为"规则进行输出。

![基于规则的映射](media/data-flow/rule-based-hierarchy.png "基于规则的映射")

上述示例与复杂列的所有子列`a`匹配。 `a`包含两个子列`b`和`c`。 输出架构将包括两列`b`，并且`c`"名称为"条件为`$$`。

### <a name="parameterization"></a>参数化

您可以使用基于规则的映射对列名称进行参数化。 使用 关键字```name```将传入列名称与参数匹配。 例如，如果您有一个数据流参数```mycolumn```，则可以创建与等于 的任何列名称匹配的规则。 ```mycolumn``` 您可以将匹配列重命名为硬编码字符串（如"业务密钥"）并显式引用它。 在此示例中，匹配条件是```name == $mycolumn```，名称条件是"业务密钥"。 

## <a name="auto-mapping"></a>自动映射

添加选择转换时，可以通过切换 **"自动映射**"滑块启用自动映射。 使用自动映射，选择转换映射所有传入列（不包括重复列），其名称与其输入的名称相同。 这将包括漂移列，这意味着输出数据可能包含架构中未定义的列。 有关漂移列的详细信息，请参阅[架构漂移](concepts-data-flow-schema-drift.md)。

![自动映射](media/data-flow/automap.png "自动映射")

打开自动映射后，选择转换将遵循跳过重复设置，并为现有列提供新的别名。 在同一流和自联接方案中执行多个联接或查找时，别名非常有用。 

## <a name="duplicate-columns"></a>重复列

默认情况下，选择转换将删除输入和输出投影中的重复列。 重复的输入列通常来自联接和查找转换，其中列名称在联接的每一侧重复。 如果将两个不同的输入列映射到同一名称，则可能会出现重复的输出列。 通过切换复选框，选择是删除还是传递重复列。

![跳过重复项](media/data-flow/select-skip-dup.png "跳过重复项")

## <a name="ordering-of-columns"></a>列的顺序

映射的顺序确定输出列的顺序。 如果多次映射输入列，则仅遵守第一个映射。 对于任何重复的列删除，将保留第一个匹配项。

## <a name="data-flow-script"></a>数据流脚本

### <a name="syntax"></a>语法

```
<incomingStream>
    select(mapColumn(
        each(<hierarchicalColumn>, match(<matchCondition>), <nameCondition> = $$), ## hierarchical rule-based matching
        <fixedColumn>, ## fixed mapping, no rename
        <renamedFixedColumn> = <fixedColumn>, ## fixed mapping, rename
        each(match(<matchCondition>), <nameCondition> = $$), ## rule-based mapping
        each(patternMatch(<regexMatching>), <nameCondition> = $$) ## regex mapping
    ),
    skipDuplicateMapInputs: { true | false },
    skipDuplicateMapOutputs: { true | false }) ~> <selectTransformationName>
```

### <a name="example"></a>示例

下面是选择映射及其数据流脚本的示例：

![选择脚本示例](media/data-flow/select-script-example.png "选择脚本示例")

```
DerivedColumn1 select(mapColumn(
        each(a, match(true())),
        movie,
        title1 = title,
        each(match(name == 'Rating')),
        each(patternMatch(`(y)`),
            $1 + 'regex' = $$)
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> Select1
```

## <a name="next-steps"></a>后续步骤
* 使用 Select 重命名、重新排序和别名列后，使用["接收器"转换](data-flow-sink.md)将数据放入数据存储中。
