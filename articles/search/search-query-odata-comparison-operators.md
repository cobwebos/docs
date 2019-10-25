---
title: OData 比较运算符引用
titleSuffix: Azure Cognitive Search
description: Azure 中的 OData 比较运算符、eq、ne、gt、lt、ge 和 le 认知搜索查询。
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
ms.openlocfilehash: 068e2ec822f0a292ac83b3e48049830eb77b49f6
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793390"
---
# <a name="odata-comparison-operators-in-azure-cognitive-search---eq-ne-gt-lt-ge-and-le"></a>Azure 认知搜索中的 OData 比较运算符-`eq`、`ne`、`gt`、`lt`、`ge`和 `le`

Azure 认知搜索中[OData 筛选器表达式](query-odata-filter-orderby-syntax.md)中的最基本操作是将字段与给定值进行比较。 可以进行两种类型的比较：-相等比较和范围比较。 您可以使用下列运算符来比较字段和常量值：

相等运算符：

- `eq`：测试字段是否**等于**常数值
- `ne`：测试字段是否**不等于**常数值

范围运算符：

- `gt`：测试字段是否**大于**常数值
- `lt`：测试字段是否**小于**常数值
- `ge`：测试字段是否**大于或等于**常数值
- `le`：测试字段是否**小于或等于**常数值

可以结合使用范围运算符和[逻辑运算符](search-query-odata-logical-operators.md)来测试字段是否在某个值范围内。 请参阅本文后面的[示例](#examples)。

> [!NOTE]
> 如果需要，可以将常量值放在运算符的左侧，并将字段名称放在右侧。 对于范围运算符，比较的含义是相反的。 例如，如果常量值位于左侧，`gt` 会测试该常数值是否大于该字段。 您还可以使用比较运算符来比较函数的结果，如 `geo.distance`和值。 对于 `search.ismatch`等布尔函数，比较结果与 `true` 或 `false` 是可选的。

## <a name="syntax"></a>语法

以下 EBNF （[扩展巴科斯-诺尔范式窗体](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)）定义使用比较运算符的 OData 表达式的语法。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

还提供交互式语法关系图：

> [!div class="nextstepaction"]
> [适用于 Azure 认知搜索的 OData 语法关系图](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> 请参阅[适用于 Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)，了解完整的 EBNF。

比较表达式有两种形式。 它们之间唯一的区别在于常量是否显示在运算符的左侧或右侧。 运算符的另一侧的表达式必须是**变量**或函数调用。 变量可以是字段名称，也可以是[lambda 表达式](search-query-odata-collection-operators.md)时的范围变量。

## <a name="data-types-for-comparisons"></a>用于比较的数据类型

比较运算符两侧的数据类型必须兼容。 例如，如果左侧是 `Edm.DateTimeOffset`类型的字段，则右侧必须为日期时间常数。 数值数据类型更加灵活。 可以将任何数值类型的变量和函数与任何其他数值类型的常量进行比较，但有几个限制，如下表中所述。

| 变量或函数类型 | 常数值类型 | 限制 |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | 比较取决于[`NaN`的特殊规则](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | 常量转换为 `Edm.Double`，导致大数量级值的精度损失 |
| `Edm.Double` | `Edm.Int32` | 不适用 |
| `Edm.Int64` | `Edm.Double` | 不允许对 `NaN`、`-INF`或 `INF` 进行比较 |
| `Edm.Int64` | `Edm.Int64` | 不适用 |
| `Edm.Int64` | `Edm.Int32` | 在进行比较之前，常量转换为 `Edm.Int64` |
| `Edm.Int32` | `Edm.Double` | 不允许对 `NaN`、`-INF`或 `INF` 进行比较 |
| `Edm.Int32` | `Edm.Int64` | 不适用 |
| `Edm.Int32` | `Edm.Int32` | 不适用 |

对于不允许的比较（如将类型 `Edm.Int64` 的字段与 `NaN`进行比较），Azure 认知搜索 REST API 将返回 "HTTP 400：错误的请求" 错误。

> [!IMPORTANT]
> 即使数值类型比较灵活，我们强烈建议在筛选器中编写比较，使常量值的数据类型与要与之进行比较的变量或函数的数据类型相同。 当混合浮点值和整数值时，这一点尤其重要，在这种情况下，可能会丢失精度。

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>`null` 和 `NaN` 的特殊情况

使用比较运算符时，务必记住，Azure 认知搜索中的所有非集合字段都可能会 `null`。 下表显示比较表达式的所有可能结果，其中两侧可以 `null`：

| 运算符 | 仅 `null` 字段或变量时的结果 | 仅 `null` 常量时的结果 | 当字段或变量和常量都 `null` 时的结果 |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400：错误的请求错误 | HTTP 400：错误的请求错误 |
| `lt` | `false` | HTTP 400：错误的请求错误 | HTTP 400：错误的请求错误 |
| `ge` | `false` | HTTP 400：错误的请求错误 | HTTP 400：错误的请求错误 |
| `le` | `false` | HTTP 400：错误的请求错误 | HTTP 400：错误的请求错误 |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

总而言之，`null` 仅与自身相等，并且不小于或大于任何其他值。

如果索引中的字段类型为 `Edm.Double`，并将 `NaN` 值上传到这些字段，则在编写筛选器时需要考虑这一点。 Azure 认知搜索实现 IEEE 754 标准来处理 `NaN` 值，并且具有此类值的比较产生不明显的结果，如下表所示。

| 运算符 | 至少 `NaN` 一个操作数时的结果 |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

总而言之，`NaN` 不等于任何值，包括自身。

### <a name="comparing-geo-spatial-data"></a>比较异地空间数据

不能直接将 `Edm.GeographyPoint` 类型的字段与常量值进行比较，但可以使用 `geo.distance` 函数。 此函数返回 `Edm.Double`类型的值，因此，您可以将其与数值常量进行比较，以根据来自固定的地理空间坐标的距离进行筛选。 请参阅以下[示例](#examples)。

### <a name="comparing-string-data"></a>比较字符串数据

使用 `eq` 和 `ne` 运算符，可以在筛选器中比较完全匹配的字符串。 这些比较区分大小写。

## <a name="examples"></a>示例

匹配 `Rating` 字段介于3和5之间的文档（含）：

    Rating ge 3 and Rating le 5

匹配从给定纬度和经度 `Location` 字段小于2千米的文档：

    geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0

匹配 `LastRenovationDate` 字段大于或等于2015年1月1日午夜的文档：

    LastRenovationDate ge 2015-01-01T00:00:00.000Z

匹配 `Details/Sku` 字段未 `null`的文档：

    Details/Sku ne null

与至少一个房间具有 "高级房间" 类型的酒店的文档相匹配，其中 "`Rooms/Type`" 字段的字符串完全匹配筛选器：

    Rooms/any(room: room/Type eq 'Deluxe Room')

## <a name="next-steps"></a>后续步骤  

- [Azure 认知搜索中的筛选器](search-filters.md)
- [Azure 认知搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [适用于 Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档&#40;Azure 认知搜索 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
