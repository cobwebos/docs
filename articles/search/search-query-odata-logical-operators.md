---
title: OData 逻辑运算符参考-Azure 搜索
description: OData 的逻辑运算符，并且，或者，并不是，在 Azure 搜索查询中。
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
ms.openlocfilehash: de93765117b4cafe70e5ad277e32ca0a1fa8d90a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079764"
---
# <a name="odata-logical-operators-in-azure-search---and-or-not"></a>在 Azure 搜索中的 OData 逻辑运算符`and`， `or`， `not`

[OData 筛选器表达式](query-odata-filter-orderby-syntax.md)在 Azure 搜索是计算结果为的布尔表达式`true`或`false`。 您可以通过编写一系列的编写复杂的筛选器[更简单的筛选器](search-query-odata-comparison-operators.md)并将其使用逻辑运算符从编写[的布尔代数](https://en.wikipedia.org/wiki/Boolean_algebra):

- `and`：二元运算符的计算结果为`true`如果这两个其左侧和右侧的子表达式的计算结果为`true`。
- `or`：二元运算符的计算结果为`true`如果的一个左或向右子表达式的计算结果为`true`。
- `not`：一元运算符的计算结果为`true`如果及其子表达式的计算结果为`false`，反之亦然。

这些一起[集合运算符`any`并`all` ](search-query-odata-collection-operators.md)，允许您构造可以表示非常复杂的搜索条件的筛选器。

## <a name="syntax"></a>语法

以下 EBNF ([扩展巴科斯-诺尔范式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定义的使用逻辑运算符的 OData 表达式语法。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

交互式语法关系图也是可用的：

> [!div class="nextstepaction"]
> [Azure 搜索的 OData 语法关系图](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> 请参阅[适用于 Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)的完整 EBNF。

有两种形式的逻辑表达式： 二进制 (`and`/`or`)，其中有两个的子表达式和一元 (`not`)，其中只有一个。 子表达式可以是任何类型的布尔表达式：

- 类型的字段或范围变量 `Edm.Boolean`
- 返回类型的值的函数`Edm.Boolean`，如`geo.intersects`或 `search.ismatch`
- [比较表达式](search-query-odata-comparison-operators.md)，如 `rating gt 4`
- [集合表达式](search-query-odata-collection-operators.md)，如 `Rooms/any(room: room/Type eq 'Deluxe Room')`
- 布尔值文字`true`或`false`。
- 使用构造其他逻辑表达式`and`， `or`，和`not`。

> [!IMPORTANT]
> 其中并不是所有类型的子表达式可都用于某些情况下`and` / `or`，尤其是深入了解 lambda 表达式。 请参阅[Azure 搜索中的 OData 集合运算符](search-query-odata-collection-operators.md#limitations)有关详细信息。

### <a name="logical-operators-and-null"></a>逻辑运算符和 `null`

无法生成大多数的布尔表达式，如函数和比较`null`值和逻辑运算符不能应用于`null`直接文本 (例如，`x and null`不允许)。 但是，布尔字段可以是`null`，因此您需要了解的如何`and`， `or`，和`not`运算符的行为出现的情况下为 null。 下表总结了这其中`b`是类型的字段`Edm.Boolean`:

| 表达式 | 结果时`b`是 `null` |
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

布尔字段时`b`本身中显示的筛选器表达式，其行为就像它已编写`b eq true`，因此，如果`b`是`null`，该表达式的计算结果为`false`。 同样，`not b`的行为类似于`not (b eq true)`，因此它的计算结果为`true`。 这样一来，`null`字段的行为与相同`false`。 这是与使用其他表达式结合使用时的行为方式与一致`and`和`or`上, 表中所示。 尽管如此，与直接比较`false`(`b eq false`) 仍将计算结果为`false`。 换而言之，`null`不等于`false`，即使它在布尔表达式中的行为类似于它。

## <a name="examples"></a>示例

匹配文档的位置`rating`字段是介于 3 到 5，非独占之间：

    rating ge 3 and rating le 5

匹配文档的位置的所有元素`ratings`字段都是不超过 3 个或大于 5:

    ratings/all(r: r lt 3 or r gt 5)

匹配文档的位置`location`字段是在给定的多边形，文档不包含术语"public"。

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

匹配在加拿大温哥华的酒店的文档没有基础的高级机房速率小于 160 时：

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>后续步骤  

- [Azure 搜索中的筛选器](search-filters.md)
- [Azure 搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
