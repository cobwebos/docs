---
title: OData 逻辑运算符引用-Azure 搜索
description: Azure 搜索查询中的 OData 逻辑运算符、and、or 和 not。
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: bf4939a40a2fdf1c8fc6cf97beca0184b1604c98
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647999"
---
# <a name="odata-logical-operators-in-azure-search---and-or-not"></a>Azure 搜索中的 OData 逻辑运算符`and`- `or`、、`not`

Azure 搜索中的[OData 筛选器表达式](query-odata-filter-orderby-syntax.md)是计算结果为`true`或`false`的布尔表达式。 您可以编写一系列简单的筛选器, 并使用[布尔代数](https://en.wikipedia.org/wiki/Boolean_algebra)中的逻辑运算符编写一个[更简单](search-query-odata-comparison-operators.md)的筛选器, 从而编写复杂的筛选器:

- `and`：如果其左侧和右侧子`true`表达式的计算结果均为`true`, 则该二元运算符的计算结果为。
- `or`：一个二元运算符, 如果它`true`的左`true`值或右子表达式之一的计算结果为, 则计算结果为。
- `not`：一元运算符, 如果其子`true`表达式的`false`计算结果为, 则计算结果为, 反之亦然。

这些[运算符与`any`集合运算符和`all`](search-query-odata-collection-operators.md)一起, 使你可以构建可表达非常复杂的搜索条件的筛选器。

## <a name="syntax"></a>语法

以下 EBNF ([扩展巴科斯-诺尔范式窗体](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定义使用逻辑运算符的 OData 表达式的语法。

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

还提供交互式语法关系图:

> [!div class="nextstepaction"]
> [适用于 Azure 搜索的 OData 语法关系图](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> 有关完整的 EBNF, 请参阅[Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)。

逻辑表达式有两种`and`形式: binary (/`or`), 其中有两个子表达式和一元 (`not`), 其中只有一个。 子表达式可以是任何类型的布尔表达式:

- 类型的字段或范围变量`Edm.Boolean`
- 返回类型`Edm.Boolean`( `geo.intersects`如或) 的值的函数`search.ismatch`
- [比较表达式](search-query-odata-comparison-operators.md), 如`rating gt 4`
- [集合表达式](search-query-odata-collection-operators.md), 如`Rooms/any(room: room/Type eq 'Deluxe Room')`
- 布尔文本`true`或`false`。
- 使用`and`、 `or`和构造的其他逻辑表达式。`not`

> [!IMPORTANT]
> 在某些情况下, 并非所有类型的子表达式都可与`and` / `or`一起使用, 尤其是在 lambda 表达式中。 有关详细信息, 请参阅[Azure 搜索中的 OData 集合运算符](search-query-odata-collection-operators.md#limitations)。

### <a name="logical-operators-and-null"></a>逻辑运算符和`null`

大多数布尔表达式 (如函数和比较) 都`null`不能生成值, 并且逻辑运算符不能直接`null`应用于文本 (例如, `x and null`不允许)。 但是, 布尔字段可以是`null`, 因此, 需要注意`and`、 `or`和`not`运算符在出现 null 时的行为方式。 下表对此进行了总结, 其中`b`是一个类型`Edm.Boolean`为的字段:

| 表达式 | 当为`b`时的结果`null` |
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

`b`如果布尔字段本身显示在筛选器表达式中, 则其行为就像已写入`b` `b eq true`, 因此, 如果为`null`, 则表达式的计算结果`false`为。 同样, `not b`行为类似`not (b eq true)`, 因此它的计算`true`结果为。 通过这种方式`null` , 字段的行为与`false`相同。 这与使用和`and` `or`的其他表达式结合使用时的行为一致, 如下表所示。 尽管如此, 与`false` (`b eq false`) 的直接比较仍将计算为`false`。 换言之, `null`它不`false`等于, 即使它在布尔表达式中的行为类似。

## <a name="examples"></a>示例

与`rating`字段介于3和5之间的文档匹配 (含):

    rating ge 3 and rating le 5

匹配`ratings`字段的所有元素小于3或大于5的文档:

    ratings/all(r: r lt 3 or r gt 5)

匹配`location`字段在给定多边形内的文档, 并且该文档不包含术语 "public"。

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

在加拿大, 其中有一个高级房间, 其基础费率小于160的已范库中的酒店的文档匹配:

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>后续步骤  

- [Azure 搜索中的筛选器](search-filters.md)
- [Azure 搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [适用于 Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
