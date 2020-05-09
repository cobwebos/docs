---
title: 映射数据流中存在转换
description: 使用 Azure 数据工厂映射数据流中的存在转换检查现有行
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/07/2020
ms.openlocfilehash: 805b51bf4e6d8feab9539f660dfc72ca78b82d5c
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982626"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>映射数据流中存在转换

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Exists 转换是一个行筛选转换，用于检查数据是否存在于另一个源或流中。 输出流包括左侧流中存在或不存在于右侧流中的所有行。 Exists 转换类似于```SQL WHERE EXISTS```和。 ```SQL WHERE NOT EXISTS```

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vZKz]

## <a name="configuration"></a>Configuration

1. 在 "**正确的流**" 下拉列表中选择要检查是否存在的数据流。
1. 指定是否要在**存在类型**设置中查找数据。
1. 选择是否需要**自定义表达式**。
1. 选择要作为存在条件进行比较的键列。 默认情况下，数据流在每个流中的一列之间查找相等性。 若要通过计算值进行比较，请将鼠标悬停在列下拉列表中，然后选择 "**计算列**"。

![存在设置](media/data-flow/exists.png "存在1")

### <a name="multiple-exists-conditions"></a>多个存在条件

若要比较每个流中的多个列，请通过单击现有行旁边的加号图标来添加新的存在条件。 每个附加条件都通过 "and" 语句联接。 比较两个列与下面的表达式相同：

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>自定义表达式

若要创建包含非 "and" 和 "equals" 运算符的自由格式表达式，请选择 "**自定义表达式**" 字段。 通过单击蓝色框，通过数据流表达式生成器输入自定义表达式。

![存在自定义设置](media/data-flow/exists1.png "存在自定义")

## <a name="broadcast-optimization"></a>广播优化

![广播联接](media/data-flow/broadcast.png "广播联接")

在联接、查找和存在转换中，如果一个或两个数据流适合工作节点内存，则可以通过启用**广播**来优化性能。 默认情况下，spark 引擎将自动决定是否广播一方。 若要手动选择要广播的端，请选择 "**固定**"。

不建议禁用通过**Off**选项进行的广播，除非您的联接遇到超时错误。

## <a name="data-flow-script"></a>数据流脚本

### <a name="syntax"></a>语法

```
<leftStream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: { true | false },
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <existsTransformationName>
```

### <a name="example"></a>示例

下面的示例是一个名为`checkForChanges`的 exists 转换，它`NameNorm2`采用左侧流`TypeConversions`和正确流。  Exists 条件是一个表达式`NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region` ，如果每个流中的`EMPID`和`Region`列都匹配，则返回 true。 由于我们要检查是否存在， `negate`因此为 false。 我们未在 "优化" 选项卡中启用`broadcast`任何广播`'none'`，因此具有值。

在数据工厂 UX 中，此转换如下图所示：

![Exists 示例](media/data-flow/exists-script.png "Exists 示例")

此转换的数据流脚本位于下面的代码片段中：

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'auto'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>后续步骤

类似的转换是[查找](data-flow-lookup.md)和[联接](data-flow-join.md)。
