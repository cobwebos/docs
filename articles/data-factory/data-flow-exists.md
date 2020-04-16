---
title: 映射数据流中存在转换
description: 使用 Azure 数据工厂映射数据流中存在转换检查现有行
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: 512d9a688d7f808056a91b5bc0484c378af33948
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413816"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>映射数据流中存在转换

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

存在转换是一个行筛选转换，用于检查数据是否存在于其他源或流中。 输出流包括左流中存在于右流中或不存在的所有行。 存在的转换类似于```SQL WHERE EXISTS```和```SQL WHERE NOT EXISTS```。

## <a name="configuration"></a>配置

1. 在**右流**下拉下下下下下选择要检查的是否存在的数据流。
1. 指定要查找是否存在"**存在"类型**设置中是否存在的数据。
1. 选择是否要自定义**表达式**。
1. 选择要比较为存在条件的关键列。 默认情况下，数据流在每个流中查找一列之间的相等性。 要通过计算值进行比较，请将鼠标悬停在列下拉列表上，然后选择 **"计算"列**。

![存在设置](media/data-flow/exists.png "存在 1")

### <a name="multiple-exists-conditions"></a>多个存在条件

要比较每个流中的多个列，请单击现有行旁边的加号图标来添加新存在条件。 每个附加条件都由"和"语句联接。 比较两列与以下表达式相同：

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>自定义表达式

要创建包含"和"和"等于"以外的运算符的自由格式表达式，请选择 **"自定义表达式"** 字段。 通过数据流表达式生成器通过单击蓝色框输入自定义表达式。

![存在自定义设置](media/data-flow/exists1.png "存在自定义")

## <a name="data-flow-script"></a>数据流脚本

### <a name="syntax"></a>语法

```
<leftStream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: { true | false },
        broadcast: {'none' | 'left' | 'right' | 'both'}
    ) ~> <existsTransformationName>
```

### <a name="example"></a>示例

下面的示例是一个称为"`checkForChanges`左流"和"`NameNorm2`右流`TypeConversions`"的存在的转换。  如果存在条件，则在每个流`NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region`中`EMPID`和`Region`列都匹配时返回 true 的表达式。 当我们检查存在时，`negate`是虚假的。 我们不启用任何广播在优化选项卡，所以`broadcast`有价值。 `'none'`

在数据工厂 UX 中，此转换类似于下图：

![存在示例](media/data-flow/exists-script.png "存在示例")

此转换的数据流脚本位于下面的代码段中：

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'none'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>后续步骤

类似的转换是[查找](data-flow-lookup.md)和[联接](data-flow-join.md)。
