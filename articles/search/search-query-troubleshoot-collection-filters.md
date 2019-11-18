---
title: OData 集合筛选器疑难解答
titleSuffix: Azure Cognitive Search
description: 了解解决 Azure 认知搜索查询中的 OData 集合筛选器错误的方法。
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
ms.openlocfilehash: e82fa00226c964d5ba774cdf06f5b0f3898bdc55
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113079"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-cognitive-search"></a>Azure 认知搜索中的 OData 集合筛选器疑难解答

若要对 Azure 认知搜索中的集合字段进行[筛选](query-odata-filter-orderby-syntax.md)，可以将[`any` 和 `all` 运算符](search-query-odata-collection-operators.md)与**lambda 表达式**一起使用。 Lambda 表达式是应用到每个集合元素的子筛选器。

并非每个筛选表达式功能都可在 Lambda 表达式中使用。 可用的功能因要筛选的集合字段的数据类型而异。 如果你尝试在 Lambda 表达式中使用该上下文不支持的某个功能，可能会导致错误。 如果你在尝试基于集合字段编写复杂的筛选器时遇到此类错误，本文可帮助你排查问题。

## <a name="common-collection-filter-errors"></a>常见的集合筛选器错误

下表列出了在尝试执行集合筛选器时可能会遇到的错误。 使用 Lambda 表达式中不支持的筛选表达式功能时，将发生这些错误。 每个错误提供了有关如何重写筛选器以避免该错误的一些指导。 表格中还包含了本文相关部分的链接，其中提供了有关如何避免该错误的详细信息。

| 错误消息 | 场景 | 有关详细信息，请参阅 |
| --- | --- | --- |
| 函数 'ismatch' 没有任何参数绑定到范围变量的 's'。 Lambda 表达式中仅支持绑定的字段引用（'any' 或 'all'）。 请更改筛选器，使 'ismatch' 函数位于 Lambda 表达式的外部，然后重试。 | 在 Lambda 表达式中使用 `search.ismatch` 或 `search.ismatchscoring` | [有关筛选复杂集合的规则](#bkmk_complex) |
| Lambda 表达式无效。 找到了一个相等性或不相等性测试，但循环访问 Collection(Edm.String) 类型的的字段的 Lambda 表达式预期需要相反的测试。 对于 'any'，请使用 'x eq y' 或 'search.in(...)' 格式的表达式。 对于 'all'，请使用 'x ne y'、'not (x eq y)' 或 'not search.in(...)' 格式的表达式。 | 根据 `Collection(Edm.String)` 类型的字段进行筛选 | [有关筛选字符串集合的规则](#bkmk_strings) |
| Lambda 表达式无效。 找到了不受支持格式的复杂布尔表达式。 对于 'any'，请使用“AND 的 OR”格式（也称为析取范式）的表达式。 例如：'(a and b) or (c and d)'，其中 a、b、c 和 d 是比较或相等性子表达式。 对于 'all'，请使用“OR 的 AND”格式（也称为合取范式）的表达式。 例如：'(a or b) and (c or d)'，其中 a、b、c 和 d 是比较或不相等性子表达式。 比较表达式的示例：'x gt 5'、'x le 2'。 相等性表达式的示例：'x eq 5'。 不相等性表达式的示例：'x ne 5'。 | 根据 `Collection(Edm.DateTimeOffset)`、`Collection(Edm.Double)`、`Collection(Edm.Int32)` 或 `Collection(Edm.Int64)` 类型的字段进行筛选 | [有关筛选可比较集合的规则](#bkmk_comparables) |
| Lambda 表达式无效。 在循环访问 Collection(Edm.GeographyPoint) 类型的字段的 Lambda 表达式中找到 geo.distance() 或 geo.intersects() 的不受支持用法。 对于 'any'，请确保使用 'lt' 或 'le' 运算符比较 geo.distance()，并确保不要对 geo.intersects() 的任何用法进行求反。 对于 'all'，请确保使用 'gt' 或 'ge' 运算符比较 geo.distance()，并确保对 geo.intersects() 的任何用法进行求反。 | 根据 `Collection(Edm.GeographyPoint)` 类型的字段进行筛选 | [有关筛选 GeographyPoint 集合的规则](#bkmk_geopoints) |
| Lambda 表达式无效。 循环访问 Collection(Edm.GeographyPoint) 类型的字段的 Lambda 表达式中不支持复杂布尔表达式。 对于 'any'，请使用 'or' 联接子表达式；不支持 'and'。 对于 'all'，请使用 'and' 联接子表达式；不支持 'or'。 | 根据 `Collection(Edm.String)` 或 `Collection(Edm.GeographyPoint)` 类型的字段进行筛选 | [有关筛选字符串集合的规则](#bkmk_strings) <br/><br/> [有关筛选 GeographyPoint 集合的规则](#bkmk_geopoints) |
| Lambda 表达式无效。 找到比较运算符（一个 'lt'、'le'、'gt' 或 'ge'）。 循环访问 Collection(Edm.String) 类型的字段的 Lambda 表达式中只允许相等性运算符。 对于 'any'，请使用 'x eq y' 格式的表达式。 对于 'all'，请使用 'x ne y' 或 'not (x eq y)' 格式的表达式。 | 根据 `Collection(Edm.String)` 类型的字段进行筛选 | [有关筛选字符串集合的规则](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>如何编写有效的集合筛选器

有关编写有效集合筛选器的规则对于每种数据类型各不相同。 以下部分通过示例介绍了这些规则，其中指出了支持和不支持的筛选器功能：

- [有关筛选字符串集合的规则](#bkmk_strings)
- [有关筛选布尔集合的规则](#bkmk_bools)
- [有关筛选 GeographyPoint 集合的规则](#bkmk_geopoints)
- [有关筛选可比较集合的规则](#bkmk_comparables)
- [有关筛选复杂集合的规则](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>有关筛选字符串集合的规则

在字符串集合的 Lambda 表达式中，只能使用 `eq` 和 `ne` 比较运算符。

> [!NOTE]
> Azure 认知搜索不支持 `lt`的 /`le`/`gt`/`ge` 个字符串，无论是在 lambda 表达式内部还是外部。

只能在 `any` 的正文中测试相等性，只能在 `all` 的正文中测试不相等性。

还可以通过 `or` 正文中的 `any`，以及通过 `and` 正文中的 `all`，来合并多个表达式。 由于 `search.in` 函数等效于将相等性检查与 `or` 相结合，因此，`any` 的正文中也允许该函数。 相比之下，`not search.in` 的正文中允许 `all`。

例如，允许以下表达式：

- `tags/any(t: t eq 'books')`
- `tags/any(t: search.in(t, 'books, games, toys'))`
- `tags/all(t: t ne 'books')`
- `tags/all(t: not (t eq 'books'))`
- `tags/all(t: not search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' or t eq 'games')`
- `tags/all(t: t ne 'books' and not (t eq 'games'))`

不允许以下表达式：

- `tags/any(t: t ne 'books')`
- `tags/any(t: not search.in(t, 'books, games, toys'))`
- `tags/all(t: t eq 'books')`
- `tags/all(t: search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' and t ne 'games')`
- `tags/all(t: t ne 'books' or not (t eq 'games'))`

<a name="bkmk_bools"></a>

## <a name="rules-for-filtering-boolean-collections"></a>有关筛选布尔集合的规则

类型 `Edm.Boolean` 仅支持 `eq` 和 `ne` 运算符。 因此，允许合并此类子句来检查包含 `and`/`or` 的相同范围变量的做法没有太大意义，因为这始终会导致同义反复或矛盾。

下面是允许的根据布尔集合进行筛选的一些示例：

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

与字符串集合不同，布尔集合对于可在哪种类型的 Lambda 表达式中使用哪个运算符不设限制。 在 `eq` 或 `ne` 的正文中可以使用 `any` 和 `all`。

布尔集合不允许如下所示的表达式：

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>有关筛选 GeographyPoint 集合的规则

集合中 `Edm.GeographyPoint` 类型的值不能直接相互比较。 必须将它们用作 `geo.distance` 和 `geo.intersects` 函数的参数。 然后，必须使用 `geo.distance`、`lt`、`le` 或 `gt` 比较运算符将 `ge` 函数与距离值进行比较。 这些规则也适用于非集合 Edm.GeographyPoint 字段。

与字符串集合一样，`Edm.GeographyPoint` 集合在如何在不同类型的 Lambda 表达式中使用与合并地理空间函数方面也实施了一些规则：

- 可在 `geo.distance` 函数中使用哪些比较运算符取决于 Lambda 表达式的类型。 对于 `any`，只能使用 `lt` 或 `le`。 对于 `all`，只能使用 `gt` 或 `ge`。 可将涉及 `geo.distance` 的表达式求反，但必须更改比较运算符（`geo.distance(...) lt x` 更改为 `not (geo.distance(...) ge x)`，`geo.distance(...) le x` 更改为 `not (geo.distance(...) gt x)`）。
- 在 `all` 的正文中，必须将 `geo.intersects` 函数求反。 相反，在 `any` 的正文中，不得将 `geo.intersects` 函数求反。
- 在 `any` 的正文中，可以使用 `or` 来合并地理空间表达式。 在 `all` 的正文中，可以使用 `and` 来合并此类表达式。

之所以存在上述限制，是因为字符串集合存在类似的相等性/不相等性限制。 若要深入了解这些原因，请参阅[了解 Azure 中的 OData 集合筛选器认知搜索](search-query-understand-collection-filters.md)。

下面是允许的根据 `Edm.GeographyPoint` 集合进行筛选的一些示例：

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

`Edm.GeographyPoint` 集合不允许如下所示的表达式：

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>有关筛选可比较集合的规则

本部分适用于以下所有数据类型：

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

`Edm.Int32` 和 `Edm.DateTimeOffset` 等类型支持所有六个比较运算符：`eq`、`ne`、`lt`、`le`、`gt` 和 `ge`。 这些类型的集合上的 Lambda 表达式可以包含使用上述任一运算符的简单表达式。 这适用于 `any` 和 `all`。 例如，允许以下筛选器：

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

但是，在如何在 Lambda 表达式中将此类比较表达式合并成更复杂的表达式方面存在一些限制：

- 适用于 `any` 的规则：
  - 简单的不相等性表达式不能有效地与任何其他表达式合并。 例如，允许以下表达式：
    - `ratings/any(r: r ne 5)`

    但不允许以下表达式：
    - `ratings/any(r: r ne 5 and r gt 2)`

    尽管允许此表达式，但由于条件重叠，此表达式没有作用：
    - `ratings/any(r: r ne 5 or r gt 7)`
  - 涉及 `eq`、`lt`、`le`、`gt` 或 `ge` 的简单比较表达式可与 `and`/`or` 合并。 例如：
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - 使用 `and`（合取）合并的比较表达式可以通过 `or` 进一步合并。 此格式在布尔逻辑中称为“[析取范式](https://en.wikipedia.org/wiki/Disjunctive_normal_form)”(DNF)。 例如：
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- 适用于 `all` 的规则：
  - 简单的相等性表达式不能有效地与任何其他表达式合并。 例如，允许以下表达式：
    - `ratings/all(r: r eq 5)`

    但不允许以下表达式：
    - `ratings/all(r: r eq 5 or r le 2)`

    尽管允许此表达式，但由于条件重叠，此表达式没有作用：
    - `ratings/all(r: r eq 5 and r le 7)`
  - 涉及 `ne`、`lt`、`le`、`gt` 或 `ge` 的简单比较表达式可与 `and`/`or` 合并。 例如：
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - 使用 `or`（析取）合并的比较表达式可以通过 `and` 进一步合并。 此格式在布尔逻辑中称为“[合取范式](https://en.wikipedia.org/wiki/Conjunctive_normal_form)”(CNF)。 例如：
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>有关筛选复杂集合的规则

基于复杂集合的 Lambda 表达式支持的语法比基于基元类型的集合的 Lambda 表达式更灵活。 可在外部使用的任何筛选器构造也可以在此类 Lambda 表达式内部使用，只存在两种例外情况。

第一，Lambda 表达式中不支持 `search.ismatch` 和 `search.ismatchscoring` 函数。 有关详细信息，请参阅[了解 Azure 认知搜索中的 OData 集合筛选器](search-query-understand-collection-filters.md)。

第二，不允许引用未绑定到范围变量（所谓的“自由变量”）的字段。 例如，考虑以下两个等效的 OData 筛选表达式：

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

将允许第一个表达式，而第二种格式将被拒绝，因为 `details/margin` 未绑定到范围变量 `s`。

此规则还会延伸到在外部范围中绑定了变量的表达式。 此类变量的显示范围是自由的。 例如，允许第一个表达式，但不允许第二个等效的表达式，因为 `s/name` 相对于范围变量 `a` 的范围是自由的：

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

在实践中，此限制应该不是一个问题，因为始终可以构造筛选器，使 Lambda 表达式只包含绑定的变量。

## <a name="cheat-sheet-for-collection-filter-rules"></a>集合筛选器规则速查表

下表汇总了有关为每个集合数据类型构建有效筛选器的规则。

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

有关如何为每个用例的构建有效筛选器的示例，请参阅[如何编写有效的集合筛选器](#bkmk_examples)。

如果经常编写筛选器，并且理解来自第一个原则的规则不仅可帮助你记住，但请参阅[了解 Azure 认知搜索中的 OData 集合筛选器](search-query-understand-collection-filters.md)。

## <a name="next-steps"></a>后续步骤  

- [了解 Azure 中的 OData 集合筛选器认知搜索](search-query-understand-collection-filters.md)
- [Azure 认知搜索中的筛选器](search-filters.md)
- [Azure 认知搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [适用于 Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档&#40;Azure 认知搜索 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
