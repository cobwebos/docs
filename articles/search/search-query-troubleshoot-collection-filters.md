---
title: OData 集合筛选器疑难解答-Azure 搜索
description: 排查 Azure 搜索查询中的 OData 集合筛选器错误。
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
ms.openlocfilehash: fbd43cc13d3b7377668aad2fadc874ae47422ee1
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647953"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-search"></a>Azure 搜索中的 OData 集合筛选器疑难解答

若要在 Azure 搜索中对集合字段进行[筛选](query-odata-filter-orderby-syntax.md), 可以[ `any`将`all`和运算符](search-query-odata-collection-operators.md)与**lambda 表达式**一起使用。 Lambda 表达式是应用于集合的每个元素的子筛选器。

并非每个筛选器表达式功能都可在 lambda 表达式中使用。 可用的功能因要筛选的集合字段的数据类型而异。 如果尝试在该上下文中不支持的 lambda 表达式中使用功能, 这可能会导致错误。 如果你在尝试对集合字段编写复杂筛选器时遇到此类错误, 本文将帮助你解决问题。

## <a name="common-collection-filter-errors"></a>常见集合筛选器错误

下表列出了在尝试执行集合筛选器时可能遇到的错误。 当您使用在 lambda 表达式中不支持的筛选表达式功能时, 将发生这些错误。 每个错误都提供了有关如何重写筛选器以避免错误的一些指导。 该表还包括指向本文相关部分的链接, 该链接提供了有关如何避免此错误的详细信息。

| 错误消息 | 发生 | 有关详细信息，请参阅 |
| --- | --- | --- |
| 函数 "ismatch" 没有绑定到范围变量的 "" 的参数。 Lambda 表达式 ("任何" 或 "全部") 中仅支持绑定字段引用。 请更改筛选器, 使 "ismatch" 函数位于 lambda 表达式之外, 然后重试。 | 在`search.ismatch` lambda `search.ismatchscoring`表达式中使用或 | [用于筛选复杂集合的规则](#bkmk_complex) |
| Lambda 表达式无效。 在循环访问类型集合 (Edm) 的字段的 lambda 表达式中找到了相等或不相等测试。 对于 "any", 请使用 "x eq y" 或 "search.in (...)" 形式的表达式。 对于 "all", 请使用 "x ne y"、"not (x eq y)" 或 "not search.in (...)" 形式的表达式。 | 筛选类型为`Collection(Edm.String)` | [用于筛选字符串集合的规则](#bkmk_strings) |
| Lambda 表达式无效。 找到不受支持的复杂布尔表达式格式。 对于 "any", 请使用 "Or of ANDs" (也称为析取范式 Normal 窗体) 的表达式。 例如: "(a 和 b) 或 (c 和 d)", 其中 a、b、c 和 d 是比较或相等子表达式。 对于 "all", 请使用 "ANDs of Or" (也称为联合 Normal 窗体) 的表达式。 例如: "(a 或 b) 和 (c 或 d)", 其中 a、b、c 和 d 是比较或不相等子表达式。 比较表达式的示例: "x gt 5"、"x le 2"。 相等表达式的示例: "x eq 5"。 不等表达式的示例: "x ne 5"。 | 筛选类型`Collection(Edm.DateTimeOffset)`为`Collection(Edm.Double)`、 、或的字段`Collection(Edm.Int32)``Collection(Edm.Int64)` | [用于筛选可比较集合的规则](#bkmk_comparables) |
| Lambda 表达式无效。 在循环访问类型集合 (GeographyPoint) 的字段的 lambda 表达式中发现不支持的地域 () 或地域交集 ()。 对于 "any", 请确保使用 "lt" 或 "le" 运算符比较 geo (), 并确保不会对 geo 交集 () 进行任何使用。 对于 "全部", 请确保使用 "g t" 或 "ge" 运算符比较了 geo (), 并确保对地域交集 () 的任何使用都进行了取反运算。 | 筛选类型为`Collection(Edm.GeographyPoint)` | [用于筛选 GeographyPoint 集合的规则](#bkmk_geopoints) |
| Lambda 表达式无效。 在循环访问类型集合 (GeographyPoint) 的字段的 lambda 表达式中不支持复杂的布尔表达式。 对于 "any", 请将子表达式与 "or" 联接;不支持 "and"。 对于 "all", 请将子表达式与 "and" 联接;不支持 "or"。 | 筛选类型`Collection(Edm.String)`为或的字段`Collection(Edm.GeographyPoint)` | [用于筛选字符串集合的规则](#bkmk_strings) <br/><br/> [用于筛选 GeographyPoint 集合的规则](#bkmk_geopoints) |
| Lambda 表达式无效。 找到比较运算符 ("lt"、"le"、"g t" 或 "ge" 之一)。 在循环访问类型为 Collection (Edm) 的字段的 lambda 表达式中只允许使用相等运算符。 对于 "any", 请使用 "x eq y" 形式的表达式。 对于 "all", 请使用 "x ne y" 或 "not (x eq y)" 形式的表达式。 | 筛选类型为`Collection(Edm.String)` | [用于筛选字符串集合的规则](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>如何编写有效的集合筛选器

对于每种数据类型, 用于编写有效集合筛选器的规则各不相同。 以下部分通过显示受支持的筛选器功能和不支持的筛选器功能的示例来描述规则:

- [用于筛选字符串集合的规则](#bkmk_strings)
- [用于筛选布尔集合的规则](#bkmk_bools)
- [用于筛选 GeographyPoint 集合的规则](#bkmk_geopoints)
- [用于筛选可比较集合的规则](#bkmk_comparables)
- [用于筛选复杂集合的规则](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>用于筛选字符串集合的规则

在字符串集合的 lambda 表达式中, 唯一可以使用的比较运算符是`eq`和。 `ne`

> [!NOTE]
> Azure 搜索不支持字符串的`lt` / `le` / 运算符,`gt`无论是在 lambda 表达式内部还是外部。 / `ge`

的主体`any`只能测试相等性, 而的主体只能测试是否不`all`相等。

还可以通过`or`将多个表达式组合到的正文`any`中, 并通过`and`将其合并到的正文`all`中。 由于函数等效于将相等性检查与结合`or`使用, 因此它`any`在的主体中也是允许的。 `search.in` 相反, `not search.in`在的主体`all`中允许。

例如, 允许使用以下表达式:

- `tags/any(t: t eq 'books')`
- `tags/any(t: search.in(t, 'books, games, toys'))`
- `tags/all(t: t ne 'books')`
- `tags/all(t: not (t eq 'books'))`
- `tags/all(t: not search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' or t eq 'games')`
- `tags/all(t: t ne 'books' and not (t eq 'games'))`

但不允许这些表达式:

- `tags/any(t: t ne 'books')`
- `tags/any(t: not search.in(t, 'books, games, toys'))`
- `tags/all(t: t eq 'books')`
- `tags/all(t: search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' and t ne 'games')`
- `tags/all(t: t ne 'books' or not (t eq 'games'))`

<a name="bkmk_bools"></a>

## <a name="rules-for-filtering-boolean-collections"></a>用于筛选布尔集合的规则

类型`Edm.Boolean` 仅支持`ne`和运算符。 `eq` 同样, 允许将检查同一范围变量`and` / `or`的这类子句组合起来并不太合理, 因为这会导致 tautologies 或矛盾。

下面是允许使用的布尔集合上的一些筛选器示例:

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

与字符串集合不同, 布尔集合没有限制可用于哪种类型的 lambda 表达式。 和`eq` 都`ne`可以在`any`或的主体中使用。`all`

布尔集合不允许使用如下所示的表达式:

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>用于筛选 GeographyPoint 集合的规则

集合中类型`Edm.GeographyPoint`的值不能直接进行比较。 相反, 它们必须用作和`geo.distance` `geo.intersects`函数的参数。 相反`geo.distance` , 该函数必须与某个比较运算符`lt`( `le` `gt`、或`ge`) 进行比较。 这些规则也适用于非集合 GeographyPoint 字段。

与字符串集合一样`Edm.GeographyPoint` , 集合具有一些规则, 用于说明如何在不同类型的 lambda 表达式中使用地理空间函数和组合:

- 可对`geo.distance`函数使用哪些比较运算符取决于 lambda 表达式的类型。 对于`any`, 只能`lt`使用或`le`。 对于`all`, 只能`gt`使用或`ge`。 您可以`geo.distance`否定涉及的表达式, 但必须更改比较运算符 (`geo.distance(...) lt x`变为`not (geo.distance(...) ge x)`和`geo.distance(...) le x`变为`not (geo.distance(...) gt x)`)。
- 在的主体`all`中, 该函数`geo.intersects`必须是否定的。 相反, 在的主体`any`中`geo.intersects` , 函数不得取反。
- `or`在中`any`, 可以使用将地理空间表达式组合在一起。 在的正文`all`中, 此类表达式可以使用`and`进行组合。

上述限制存在的原因类似于字符串集合的相等/不相等限制。 若要深入了解这些原因, 请参阅[了解 Azure 搜索中的 OData 集合筛选器](search-query-understand-collection-filters.md)。

下面是有关`Edm.GeographyPoint`允许的集合的一些筛选器示例:

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

`Edm.GeographyPoint`集合不允许使用如下所示的表达式:

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>用于筛选可比较集合的规则

本部分适用于以下所有数据类型:

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

类型`Edm.Int32` (如和`Edm.DateTimeOffset` ) 支持所有六个比较运算符: `eq`、 `ne`、 `lt`、 `le`、 `gt`和。 `ge` 这些类型的集合上的 Lambda 表达式可以使用其中任何一个运算符包含简单的表达式。 这同时`any`适用于和`all`。 例如, 允许使用以下筛选器:

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

但是, 在 lambda 表达式中, 这种比较表达式如何合并到更复杂的表达式中也存在一些限制:

- `any`规则:
  - 不能将简单不相等表达式与任何其他表达式有用。 例如, 允许使用以下表达式:
    - `ratings/any(r: r ne 5)`

    但此表达式并不是:
    - `ratings/any(r: r ne 5 and r gt 2)`

    尽管此表达式是允许的, 但它并不有用, 因为条件重叠:
    - `ratings/any(r: r ne 5 or r gt 7)`
  - 可以将涉及`eq` `lt` `and` /、 、`gt`、或的简单`or`比较表达式与结合起来。 `ge` `le` 例如：
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - 与 (连词) `and`组合的比较表达式可以使用`or`进一步组合。 此窗体在布尔逻辑中称为 "[析取范式 Normal form](https://en.wikipedia.org/wiki/Disjunctive_normal_form)" (DNF)。 例如：
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- `all`规则:
  - 简单的相等表达式不能与任何其他表达式有用组合。 例如, 允许使用以下表达式:
    - `ratings/all(r: r eq 5)`

    但此表达式并不是:
    - `ratings/all(r: r eq 5 or r le 2)`

    尽管此表达式是允许的, 但它并不有用, 因为条件重叠:
    - `ratings/all(r: r eq 5 and r le 7)`
  - 可以将涉及`ne` `lt` `and` /、 、`gt`、或的简单`or`比较表达式与结合起来。 `ge` `le` 例如：
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - 与 (disjunctions) `or`组合的比较表达式可以使用`and`进一步组合。 此窗体在布尔逻辑中称为 "[联合 Normal form](https://en.wikipedia.org/wiki/Conjunctive_normal_form)" (.cnf)。 例如：
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>用于筛选复杂集合的规则

复杂集合上的 lambda 表达式支持比基元类型集合上的 lambda 表达式更灵活的语法。 你可以使用此类 lambda 表达式中的任何筛选器构造, 你可以在其中使用它, 只有两个例外。

首先, lambda 表达式`search.ismatch`中`search.ismatchscoring`的函数和不受支持。 有关详细信息, 请参阅[了解 Azure 搜索中的 OData 集合筛选器](search-query-understand-collection-filters.md)。

其次, 不允许引用未*绑定*到范围变量 (所谓的*自由变量*) 的字段。 例如, 请考虑下面两个等效的 OData 筛选器表达式:

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

将允许第一个表达式, 而第二个窗体将被拒绝`details/margin` , 因为未绑定到范围`s`变量。

此规则还扩展到具有外部作用域中的变量的表达式。 此类变量在其出现的范围内是免费的。 例如, 允许第一个表达式, 而不允许使用第二个等效表达式, `s/name`因为对范围变量`a`的范围而言是可自由的:

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

此限制不应是实际问题, 因为它始终可以构造筛选器, 使 lambda 表达式只包含绑定变量。

## <a name="cheat-sheet-for-collection-filter-rules"></a>集合筛选规则的备忘单

下表总结了为每个集合数据类型构建有效筛选器的规则。

[!INCLUDE [Limitations on OData lambda expressions in Azure Search](../../includes/search-query-odata-lambda-limitations.md)]

有关如何为每种情况构造有效筛选器的示例, 请参阅[如何编写有效的集合筛选器](#bkmk_examples)。

如果经常编写筛选器, 并且理解来自第一个原则的规则不仅可帮助您记住它们, 但请参阅[了解 Azure 搜索中的 OData 集合筛选器](search-query-understand-collection-filters.md)。

## <a name="next-steps"></a>后续步骤  

- [了解 Azure 搜索中的 OData 集合筛选器](search-query-understand-collection-filters.md)
- [Azure 搜索中的筛选器](search-filters.md)
- [Azure 搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [适用于 Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
