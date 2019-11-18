---
title: OData 逻辑运算符引用
titleSuffix: Azure Cognitive Search
description: 在 Azure 认知搜索查询中使用 OData 逻辑运算符（and、or 和 not）的语法和参考文档。
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
ms.openlocfilehash: 2d3952f7d2adc26892cbebcd962f2ea25b86de7d
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113184"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>Azure 认知搜索中的 OData 逻辑运算符-`and`、`or``not`

Azure 认知搜索中的[OData 筛选表达式](query-odata-filter-orderby-syntax.md)是计算结果为 `true` 或 `false`的布尔表达式。 你可以编写复杂的筛选器，只需先编写一系列[较简单的筛选器](search-query-odata-comparison-operators.md)，然后使用[布尔代数](https://en.wikipedia.org/wiki/Boolean_algebra)中的逻辑运算符将其组合在一起即可：

- `and`：如果其左右子表达式的计算结果都为 `true`，则计算结果为 `true` 的二元运算符。
- `or`：如果一个二元运算符的左侧或右侧子表达式的计算结果为 `true`，则计算结果为 `true`。
- `not`：一个一元运算符，该运算符的计算结果为 `true` 如果其子表达式的计算结果为 `false`，反之亦然。

可以使用这些运算符以及[集合运算符 `any` 和 `all`](search-query-odata-collection-operators.md) 来构建筛选器，以便表述很复杂的搜索条件。

## <a name="syntax"></a>语法

以下 EBNF（[扩展巴科斯-瑙尔范式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)）定义一个使用逻辑运算符的 OData 表达式的语法。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

下面还提供了交互式语法图：

> [!div class="nextstepaction"]
> [适用于 Azure 认知搜索的 OData 语法关系图](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> 请参阅[适用于 Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)，了解完整的 EBNF。

有两种形式的逻辑表达式：二元表达式 (`and`/`or`) 和一元表达式 (`not`)，前者有两个子表达式，后者只有一个子表达式。 子表达式可以是任何类型的布尔表达式：

- 类型为 `Edm.Boolean` 的字段或范围变量
- 返回值的类型为 `Edm.Boolean` 的函数，例如 `geo.intersects` 或 `search.ismatch`
- [比较表达式](search-query-odata-comparison-operators.md)，例如 `rating gt 4`
- [集合表达式](search-query-odata-collection-operators.md)，例如 `Rooms/any(room: room/Type eq 'Deluxe Room')`
- 布尔文本值 `true` 或 `false`。
- 使用 `and`、`or` 和 `not` 构造的其他逻辑表达式。

> [!IMPORTANT]
> 某些情况下，并非所有类型的子表达式都可以与 `and`/`or` 配合使用，尤其是在 lambda 表达式中。 有关详细信息，请参阅[Azure 认知搜索中的 OData 集合运算符](search-query-odata-collection-operators.md#limitations)。

### <a name="logical-operators-and-null"></a>逻辑运算符和 `null`

大多数布尔表达式（例如函数表达式和比较表达式）不会生成 `null` 值，逻辑运算符不能直接应用于 `null` 文本（例如，不允许 `x and null`）。 但是，布尔字段可以是 `null`，因此你需要了解 `and`、`or` 和 `not` 运算符在 null 存在时的行为。 这在下表中进行了汇总，其中，`b` 是类型为 `Edm.Boolean` 的字段：

| 表达式 | 当 `b` 为 `null` 时的结果 |
| --- | --- |
| `b` | `false` |
| `not b` | `true` |
| `b eq true` | `false` |
| `b eq false` | `false` |
| `b eq null` | `true` |
| `b ne true` | `true` |
| `b ne false` | `true` |
| `b ne null` | `false` |
| `b and true` | `false` |
| `b and false` | `false` |
| `b or true` | `true` |
| `b or false` | `false` |

当布尔字段 `b` 本身出现在筛选器表达式中时，它会表现得像是编写为 `b eq true` 一样。因此，如果 `b` 为 `null`，则表达式的求值结果为 `false`。 类似地，`not b` 表现得像 `not (b eq true)`，因此其求值结果为 `true`。 同样，`null` 字段表现得像 `false`。 这与通过 `and` 和 `or` 将它们与其他表达式组合使用时的表现一致，如上表所示。 尽管如此，直接与 `false` (`b eq false`) 比较的求值结果仍为 `false`。 换言之，`null` 不等于 `false`，尽管其在布尔表达式中的行为与之类似。

## <a name="examples"></a>示例

匹配 `rating` 字段为 3 到 5（含）的文档：

    rating ge 3 and rating le 5

匹配 `ratings` 字段的所有元素都小于 3 或大于 5 的文档：

    ratings/all(r: r lt 3 or r gt 5)

匹配 `location` 字段在给定的多边形之内的文档，文档不含“public”一词。

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

匹配加拿大温哥华酒店的文档，此类酒店存在基础费率不到 160 加元的豪华客房：

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>后续步骤  

- [Azure 认知搜索中的筛选器](search-filters.md)
- [Azure 认知搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [适用于 Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档&#40;Azure 认知搜索 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
