---
title: OData 比较运算符参考-Azure 搜索
description: OData 比较运算符、 eq、 ne、 gt、 l t、 ge、 和 le、 在 Azure 搜索查询中。
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: b51bf3d77283ae828f47fdb0355d2deb43f071a1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079920"
---
# <a name="odata-comparison-operators-in-azure-search---eq-ne-gt-lt-ge-and-le"></a>在 Azure 搜索中的 OData 比较运算符`eq`， `ne`， `gt`， `lt`， `ge`，和 `le`

中的最基本操作[OData 筛选器表达式](query-odata-filter-orderby-syntax.md)Azure 搜索中为要比较为给定值的字段。 两种类型的比较可使用-相等比较和范围比较。 可以使用以下运算符来比较为常量值的字段：

相等运算符：

- `eq`：测试字段是否**等于**常量值
- `ne`：测试字段是否**不等于**常量值

范围运算符：

- `gt`：测试字段是否**大于**常量值
- `lt`：测试字段是否**小于**常量值
- `ge`：测试字段是否**大于或等于**常量值
- `le`：测试字段是否**小于或等于**常量值

可以结合使用范围运算符[逻辑运算符](search-query-odata-logical-operators.md)来测试是否为特定范围的值中的字段。 请参阅[示例](#examples)这篇文章中更高版本。

> [!NOTE]
> 如果您愿意，可以将常量值置于左侧和右侧的运算符和右侧的字段名称。 范围运算符反转比较的含义。 例如，如果常量的值是在左侧，`gt`像测试的常量值是否大于该字段。 此外可以使用比较运算符进行比较的函数，结果如`geo.distance`，值。 一个布尔值的函数等函数`search.ismatch`，比较将结果发送到`true`或`false`是可选的。

## <a name="syntax"></a>语法

以下 EBNF ([扩展巴科斯-诺尔范式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定义的使用比较运算符的 OData 表达式语法。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

交互式语法关系图也是可用的：

> [!div class="nextstepaction"]
> [Azure 搜索的 OData 语法关系图](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> 请参阅[适用于 Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)的完整 EBNF。

有两种形式的比较表达式。 唯一区别是运算符的常量是否显示在左侧-或-右侧。 必须是运算符的表达式的另一端**变量**或函数调用。 变量可以是某字段名称或某个范围变量的情况下[lambda 表达式](search-query-odata-collection-operators.md)。

## <a name="data-types-for-comparisons"></a>数据类型进行比较

比较运算符的两面上的数据类型必须兼容。 例如，如果左侧和右侧是一个类型的字段`Edm.DateTimeOffset`，然后在右侧必须是日期时间常量。 数值数据类型是更灵活。 下表中所述，您可以比较变量和常量的任何其他数值类型，有一些限制，与任何数值类型的函数。

| 变量或函数类型 | 常量值类型 | 限制 |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | 比较都将遵守[特殊规则 `NaN`](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | 常量转换为`Edm.Double`，从而导致大型大的值的精度损失 |
| `Edm.Double` | `Edm.Int32` | 不适用 |
| `Edm.Int64` | `Edm.Double` | 与比较`NaN`， `-INF`，或`INF`不允许 |
| `Edm.Int64` | `Edm.Int64` | 不适用 |
| `Edm.Int64` | `Edm.Int32` | 常量转换为`Edm.Int64`比较之前 |
| `Edm.Int32` | `Edm.Double` | 与比较`NaN`， `-INF`，或`INF`不允许 |
| `Edm.Int32` | `Edm.Int64` | 不适用 |
| `Edm.Int32` | `Edm.Int32` | 不适用 |

不允许，例如比较类型的字段的比较`Edm.Int64`到`NaN`，Azure 搜索 REST API 将返回"HTTP 400:错误的请求"时出错。

> [!IMPORTANT]
> 尽管数字类型比较灵活，我们强烈建议，以便与变量或要比较的函数相同的数据类型的常量值是在筛选器中编写的比较。 这一点尤其重要时混合浮点和整数值，可能会丢失精度的隐式转换的。

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>特殊情况下为`null`和 `NaN`

使用比较运算符，它时，请务必记住，可能会在 Azure 搜索中的所有非集合字段`null`。 下表显示了用于比较表达式的所有可能结果其中任何一侧，可以是`null`:

| 运算符 | 字段或变量时的结果 `null` | 仅该常量不在时的结果 `null` | 该字段或变量和常量时的结果 `null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400:无效的请求错误 | HTTP 400:无效的请求错误 |
| `lt` | `false` | HTTP 400:无效的请求错误 | HTTP 400:无效的请求错误 |
| `ge` | `false` | HTTP 400:无效的请求错误 | HTTP 400:无效的请求错误 |
| `le` | `false` | HTTP 400:无效的请求错误 | HTTP 400:无效的请求错误 |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

总之，`null`相同到其自身，且不小于或大于任何其他值。

如果索引包含类型的字段`Edm.Double`上, 传`NaN`给这些字段的值，你将需要编写筛选器时为该帐户。 Azure 搜索实现处理的 IEEE 754 标准`NaN`值和具有此类值的比较生成不明显的结果下, 表中所示。

| 运算符 | 至少一个操作数时的结果 `NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

总之，`NaN`不等于任何值，包括其本身。

### <a name="comparing-geo-spatial-data"></a>比较地理空间数据

您不能直接比较类型的字段`Edm.GeographyPoint`与常量值，但你可以使用`geo.distance`函数。 此函数返回类型的值`Edm.Double`，以便比较数值常量，以筛选根据常量的地理空间坐标的距离。 请参阅[示例](#examples)下面。

### <a name="comparing-string-data"></a>比较字符串数据

可以使用完全匹配项的筛选器中比较字符串`eq`和`ne`运算符。 这种比较并区分大小写。

## <a name="examples"></a>示例

匹配文档的位置`Rating`字段是介于 3 到 5，非独占之间：

    Rating ge 3 and Rating le 5

匹配文档的位置`Location`字段是小于 2 公里从给定的纬度和经度：

    geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0

匹配文档的位置`LastRenovationDate`字段是大于或等于 2015 年 1 月 1 日，UTC 午夜：

    LastRenovationDate ge 2015-01-01T00:00:00.000Z

匹配文档的位置`Details/Sku`字段不是`null`:

    Details/Sku ne null

匹配的酒店，其中至少一个空间都具有类型"豪华房间"，其中的文档的字符串`Rooms/Type`字段完全匹配筛选器：

    Rooms/any(room: room/Type eq 'Deluxe Room')

## <a name="next-steps"></a>后续步骤  

- [Azure 搜索中的筛选器](search-filters.md)
- [Azure 搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
