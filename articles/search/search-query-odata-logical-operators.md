---
title: OData 逻辑运算符引用
titleSuffix: Azure Cognitive Search
description: Azure 认知搜索查询中的 OData 逻辑运算符 and、or 和 not。
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
ms.openlocfilehash: 4e016047d66e49f17c08d4b92a1c865f4b63e39b
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793323"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>Azure 认知搜索中的 OData 逻辑运算符-`and`、`or``not`

Azure 认知搜索中的[OData 筛选表达式](query-odata-filter-orderby-syntax.md)是计算结果为 `true` 或 `false`的布尔表达式。 您可以编写一系列简单的筛选器，并使用[布尔代数](https://en.wikipedia.org/wiki/Boolean_algebra)中的逻辑运算符编写一个[更简单](search-query-odata-comparison-operators.md)的筛选器，从而编写复杂的筛选器：

- `and`：如果其左右子表达式的计算结果都为 `true`，则计算结果为 `true` 的二元运算符。
- `or`：如果一个二元运算符的左侧或右侧子表达式的计算结果为 `true`，则计算结果为 `true`。
- `not`：一个一元运算符，该运算符的计算结果为 `true` 如果其子表达式的计算结果为 `false`，反之亦然。

这些[运算符与集合运算符一起 `any` 和 `all`](search-query-odata-collection-operators.md)使你可以构建可表达非常复杂的搜索条件的筛选器。

## <a name="syntax"></a>语法

以下 EBNF （[扩展巴科斯-诺尔范式窗体](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)）定义使用逻辑运算符的 OData 表达式的语法。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

还提供交互式语法关系图：

> [!div class="nextstepaction"]
> [适用于 Azure 认知搜索的 OData 语法关系图](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> 请参阅[适用于 Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)，了解完整的 EBNF。

逻辑表达式有两种形式： binary （`and`/`or`），其中有两个子表达式，一元（`not`），其中只有一个。 子表达式可以是任何类型的布尔表达式：

- `Edm.Boolean` 类型的字段或范围变量
- 返回 `Edm.Boolean`类型的值的函数，如 `geo.intersects` 或 `search.ismatch`
- [比较表达式](search-query-odata-comparison-operators.md)，如 `rating gt 4`
- [集合表达式](search-query-odata-collection-operators.md)，如 `Rooms/any(room: room/Type eq 'Deluxe Room')`
- 布尔值 `true` 或 `false`。
- 使用 `and`、`or`和 `not`构造的其他逻辑表达式。

> [!IMPORTANT]
> 在某些情况下，并非所有类型的子表达式都可用于 `and`/`or`，特别是在 lambda 表达式中。 有关详细信息，请参阅[Azure 认知搜索中的 OData 集合运算符](search-query-odata-collection-operators.md#limitations)。

### <a name="logical-operators-and-null"></a>逻辑运算符和 `null`

大多数布尔表达式（如函数和比较）不能产生 `null` 值，并且逻辑运算符不能直接应用于 `null` 文本（例如，不允许 `x and null`）。 但是，布尔字段可以 `null`，因此，您需要了解 `and`、`or`和 `not` 运算符在存在 null 的情况下的行为方式。 下表对此进行了总结，其中 `b` 是 `Edm.Boolean`类型的字段：

| 表达式 | `b` 为 `null` 时的结果 |
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

如果布尔字段 `b` 在筛选器表达式中单独显示，则其行为如同已 `b eq true`编写的一样，因此，如果 `null``b`，则表达式的计算结果为 `false`。 同样，`not b` 的行为类似于 `not (b eq true)`，因此它的计算结果为 `true`。 这样，`null` 字段的行为与 `false`相同。 这与使用 `and` 和 `or`的其他表达式结合使用时的行为一致，如上表中所示。 尽管如此，对 `false` （`b eq false`）的直接比较仍会计算为 `false`。 换句话说，`null` 不等于 `false`，即使它在布尔表达式中的行为类似。

## <a name="examples"></a>示例

匹配 `rating` 字段介于3和5之间的文档（含）：

    rating ge 3 and rating le 5

匹配 `ratings` 字段的所有元素小于3或大于5的文档：

    ratings/all(r: r lt 3 or r gt 5)

匹配 `location` 字段位于给定多边形内的文档，并且该文档不包含术语 "public"。

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

在加拿大，其中有一个高级房间，其基础费率小于160的已范库中的酒店的文档匹配：

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>后续步骤  

- [Azure 认知搜索中的筛选器](search-filters.md)
- [Azure 认知搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [适用于 Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档&#40;Azure 认知搜索 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
