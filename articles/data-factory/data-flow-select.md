---
title: 选择映射数据流中的转换
description: Azure 数据工厂映射数据流选择转换
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/02/2020
ms.openlocfilehash: 70e0a95a85920562af8bf9d3fffa6633709dccc5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84322084"
---
# <a name="select-transformation-in-mapping-data-flow"></a>选择映射数据流中的转换

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用 select 转换可对列进行重命名、删除或重新排序。 此转换不会更改行数据，而是选择要向下游传播的列。 

在 select 转换中，用户可以指定固定映射，使用模式执行基于规则的映射，或启用自动映射。 固定的和基于规则的映射可在相同的 select 转换中使用。 如果某列与其中一个已定义的映射不匹配，则将其删除。

## <a name="fixed-mapping"></a>固定映射

如果投影中定义的列少于50个，则默认情况下，所有已定义的列都将具有固定映射。 固定映射采用已定义的传入列，并将其映射为确切名称。

![固定映射](media/data-flow/fixedmapping.png "固定映射")

> [!NOTE]
> 不能使用固定映射来映射或重命名偏移列

### <a name="mapping-hierarchical-columns"></a>映射分层列

固定映射可用于将分层列的 subcolumn 映射到顶级列。 如果有已定义的层次结构，请使用 "列" 下拉列表选择 subcolumn。 选择转换将创建一个新列，其中包含 subcolumn 的值和数据类型。

![分层映射](media/data-flow/select-hierarchy.png "分层映射")

## <a name="rule-based-mapping"></a>基于规则的映射


> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xiXz]

如果希望一次映射多个列或传递偏移列下游，请使用基于规则的映射来定义使用列模式的映射。 根据 `name` 列的、 `type` 、 `stream` 和进行匹配 `position` 。 可以有固定和基于规则的映射的任意组合。 默认情况下，每列大于50的所有投影都将默认为基于规则的映射，该映射在每个列上都匹配并输出输入名称。 

若要添加基于规则的映射，请单击 "**添加映射**"，然后选择 "**基于规则的映射**"。

![基于规则的映射](media/data-flow/rule2.png "基于规则的映射")

每个基于规则的映射都需要两个输入：要匹配的条件以及每个映射列的名称。 这两个值都是通过[表达式生成器](concepts-data-flow-expression-builder.md)输入的。 在左侧的 "表达式" 框中，输入您的布尔匹配条件。 在右侧的 "表达式" 框中，指定匹配的列将映射到的列。

![基于规则的映射](media/data-flow/rule-based-mapping.png "基于规则的映射")

使用 `$$` 语法来引用匹配列的输入名称。 使用上述图像作为示例，假设用户想要匹配名称长度少于6个字符的所有字符串列。 如果一个传入列已命名 `test` ，则表达式 `$$ + '_short'` 将重命名该列 `test_short` 。 如果这是唯一存在的映射，则将从输出的数据中删除所有不符合条件的列。

模式匹配偏移和已定义的列。 若要查看规则所映射的定义列，请单击该规则旁边的眼镜图标。 使用数据预览来验证输出。

### <a name="regex-mapping"></a>Regex 映射

如果单击向下 v 形图标，则可以指定 regex 映射条件。 Regex 映射条件与与指定的正则表达式匹配的所有列名称匹配。 这可以与基于标准规则的映射结合使用。

![基于规则的映射](media/data-flow/regex-matching.png "基于规则的映射")

上面的示例匹配正则表达式模式 `(r)` 或任何包含小写字母的列名称。 与基于标准规则的映射类似，所有匹配的列都通过使用语法在右侧的条件下进行更改 `$$` 。

如果列名称中有多个正则表达式匹配项，则可以使用 `$n` "n" 引用哪个匹配项来引用特定的匹配项。 例如，"$2" 是指列名中的第二个匹配项。

### <a name="rule-based-hierarchies"></a>基于规则的层次结构

如果您定义的投影具有层次结构，则可以使用基于规则的映射来映射层次结构个子列。 指定匹配条件以及要映射其个子列的复杂列。 将使用右侧指定的 "名称为" 规则输出每个匹配的 subcolumn。

![基于规则的映射](media/data-flow/rule-based-hierarchy.png "基于规则的映射")

上面的示例匹配复杂列的所有个子列 `a` 。 `a`包含两个个子列 `b` 和 `c` 。 输出架构将包括两个列 `b` ，并且 `c` "名称为" 条件为 `$$` 。

### <a name="parameterization"></a>参数化

您可以使用基于规则的映射参数化列名称。 使用关键字 ```name``` 可根据参数匹配传入的列名称。 例如，如果你有一个数据流参数 ```mycolumn``` ，则可以创建一个与等于的任何列名匹配的规则 ```mycolumn``` 。 您可以将匹配列重命名为硬编码字符串（如 "业务密钥"）并显式引用它。 在此示例中，匹配条件为 ```name == $mycolumn``` ，名称条件为 "业务键"。 

## <a name="auto-mapping"></a>自动映射

添加 select 转换时，可以通过切换 "自动映射" 滑块来启用**自动映射**。 对于自动映射，select 转换将映射所有传入的列（不包括重复项），其名称与输入的名称相同。 这将包括偏移列，这意味着输出数据可能包含未在架构中定义的列。 有关偏移列的详细信息，请参阅[架构偏移](concepts-data-flow-schema-drift.md)。

![自动映射](media/data-flow/automap.png "自动映射")

启用自动映射后，选择转换将服从 "跳过重复" 设置，并为现有列提供新的别名。 当对同一流和自联接方案执行多个联接或查找时，别名非常有用。 

## <a name="duplicate-columns"></a>重复列

默认情况下，select 转换将删除输入和输出投影中的重复列。 重复的输入列通常来自联接和查找转换，其中的列名在联接的每一侧都是重复的。 如果将两个不同的输入列映射到相同的名称，可能会出现重复的输出列。 通过切换复选框，选择是删除还是传递重复列。

![跳过重复项](media/data-flow/select-skip-dup.png "跳过重复项")

## <a name="ordering-of-columns"></a>列排序

映射顺序决定了输出列的顺序。 如果多次映射某个输入列，则只会接受第一个映射。 对于任何重复的列删除，都将保留第一个匹配项。

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

下面是一个选择映射及其数据流脚本的示例：

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
* 使用 "选择重命名"、"重新排序" 和 "别名" 列后，使用[接收器转换](data-flow-sink.md)将数据插入到数据存储中。
