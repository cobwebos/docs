---
title: OData 比较运算符引用
titleSuffix: Azure Cognitive Search
description: Azure 认知搜索查询中使用 OData 比较运算符（eq、ne、gt、lt、ge 和 le）的语法和参考文档。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 62c8c93e07326e776cbe089042abc481544794bc
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113222"
---
# <a name="odata-comparison-operators-in-azure-cognitive-search---eq-ne-gt-lt-ge-and-le"></a>Azure 认知搜索中的 OData 比较运算符-`eq`、`ne`、`gt`、`lt`、`ge`和 `le`

Azure 认知搜索中[OData 筛选器表达式](query-odata-filter-orderby-syntax.md)中的最基本操作是将字段与给定值进行比较。 可进行两种类型的比较 -- 相等性比较和范围比较。 可以使用以下运算符将字段与常量值进行比较：

相等性运算符：

- `eq`：测试字段是否**等于**常数值
- `ne`：测试字段是否**不等于**常数值

范围运算符：

- `gt`：测试字段是否**大于**常数值
- `lt`：测试字段是否**小于**常数值
- `ge`：测试字段是否**大于或等于**常数值
- `le`：测试字段是否**小于或等于**常数值

可以结合使用范围运算符和[逻辑运算符](search-query-odata-logical-operators.md)来测试某个字段是否在特定的值范围内。 请参阅本文稍后提供的[示例](#examples)。

> [!NOTE]
> 如果需要，可将常量值放在运算符的左侧，将字段名称放在右侧。 对于范围运算符，比较的含义是相反的。 例如，如果常量值位于左侧，则 `gt` 会测试常量值是否大于字段。 还可以使用比较运算符将函数（例如 `geo.distance`）的结果与值进行比较。 对于类似于 `search.ismatch` 的布尔函数，将结果与 `true` 或 `false` 进行比较是可选的操作。

## <a name="syntax"></a>语法

以下 EBNF（[扩展巴科斯-瑙尔范式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)）定义一个使用比较运算符的 OData 表达式的语法。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

下面还提供了交互式语法图：

> [!div class="nextstepaction"]
> [适用于 Azure 认知搜索的 OData 语法关系图](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> 请参阅[适用于 Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)，了解完整的 EBNF。

比较表达式有两种形式。 它们之间的唯一差别在于，常量是显示在运算符的左侧还是右侧。 运算符另一侧的表达式必须是**变量**或函数调用。 变量可以是字段名称，或者 [Lambda 表达式](search-query-odata-collection-operators.md)中的范围变量。

## <a name="data-types-for-comparisons"></a>可比较的数据类型

比较运算符两侧的数据类型必须兼容。 例如，如果左侧是 `Edm.DateTimeOffset` 类型的字段，则右侧必须是日期时间常量。 数字数据类型更灵活。 可将任何数字类型的变量和函数与任何其他数字类型的常量进行比较，但存在下表中所述的几项限制。

| 变量或函数类型 | 常量值类型 | 限制 |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | 比较需要遵循 [`NaN` 特殊规则](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | 常量将转换为 `Edm.Double`，因此大数量级的值会损失精度 |
| `Edm.Double` | `Edm.Int32` | 不适用 |
| `Edm.Int64` | `Edm.Double` | 不允许对 `NaN`、`-INF` 或 `INF` 进行比较 |
| `Edm.Int64` | `Edm.Int64` | 不适用 |
| `Edm.Int64` | `Edm.Int32` | 在比较之前，常量将转换为 `Edm.Int64` |
| `Edm.Int32` | `Edm.Double` | 不允许对 `NaN`、`-INF` 或 `INF` 进行比较 |
| `Edm.Int32` | `Edm.Int64` | 不适用 |
| `Edm.Int32` | `Edm.Int32` | 不适用 |

对于不允许的比较（如将类型 `Edm.Int64` 的字段与 `NaN`进行比较），Azure 认知搜索 REST API 将返回 "HTTP 400：错误的请求" 错误。

> [!IMPORTANT]
> 尽管数字类型比较非常灵活，但我们强烈建议在筛选器中编写比较表达式，使常量值的数据类型与要比较的变量或函数的数据类型相同。 混合使用浮点值和整数值时（在这种情况下，隐式转换可能会损失精度），这一点尤其重要。

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>`null` 和 `NaN` 的特殊情况

使用比较运算符时，务必记住，Azure 认知搜索中的所有非集合字段都可能会 `null`。 下表显示了任何一侧可为 `null` 的比较表达式的所有可能结果：

| 运算符 | 只有字段或变量为 `null` 时的结果 | 只有常量为 `null` 时的结果 | 字段或变量和常量都为 `null` 时的结果 |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400：错误的请求错误 | HTTP 400：错误的请求错误 |
| `lt` | `false` | HTTP 400：错误的请求错误 | HTTP 400：错误的请求错误 |
| `ge` | `false` | HTTP 400：错误的请求错误 | HTTP 400：错误的请求错误 |
| `le` | `false` | HTTP 400：错误的请求错误 | HTTP 400：错误的请求错误 |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

总而言之，`null` 仅等于自身，而不小于或大于任何其他值。

如果索引包含 `Edm.Double` 类型的字段，而你将 `NaN` 值上传到这些字段，则在编写筛选器时需要考虑到这种情况。 Azure 认知搜索实现 IEEE 754 标准来处理 `NaN` 值，并且具有此类值的比较产生不明显的结果，如下表所示。

| 运算符 | 至少有一个操作数为 `NaN` 时的结果 |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

总而言之，`NaN` 不等于任何值（包括自身）。

### <a name="comparing-geo-spatial-data"></a>比较地理空间数据

不能直接将 `Edm.GeographyPoint` 类型的字段与常量值进行比较，但可以使用 `geo.distance` 函数。 此函数返回 `Edm.Double` 类型的值，因此，可将此值与数字常量进行比较，以根据与固定地理空间坐标之间的距离进行筛选。 请参阅下面的[示例](#examples)。

### <a name="comparing-string-data"></a>比较字符串数据

可以使用 `eq` 和 `ne` 运算符在筛选器中比较字符串，以找出完全匹配项。 这些比较区分大小写。

## <a name="examples"></a>示例

匹配 `Rating` 字段为 3 到 5（含）的文档：

    Rating ge 3 and Rating le 5

匹配 `Location` 字段与给定纬度和经度之间的距离小于 2 公里的文档：

    geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0

匹配 `LastRenovationDate` 字段大于或等于 2015 年 1 月 1 日午夜 (UTC) 的文档：

    LastRenovationDate ge 2015-01-01T00:00:00.000Z

匹配 `Details/Sku` 字段不为 `null` 的文档：

    Details/Sku ne null

匹配至少提供一间“豪华客房”，且 `Rooms/Type` 字段的字符串与筛选器完全匹配的酒店的文档：

    Rooms/any(room: room/Type eq 'Deluxe Room')

## <a name="next-steps"></a>后续步骤  

- [Azure 认知搜索中的筛选器](search-filters.md)
- [Azure 认知搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [适用于 Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档&#40;Azure 认知搜索 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
