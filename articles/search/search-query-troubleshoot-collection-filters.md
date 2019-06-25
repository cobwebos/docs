---
title: OData 集合筛选器-Azure 搜索故障排除
description: 排查在 Azure 搜索查询中的 OData 集合筛选器错误。
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
ms.openlocfilehash: c7fa00c82eea03a50bae22fcb1ad16e230aa5bcb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079621"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-search"></a>Azure 搜索中的 OData 集合筛选器进行故障排除

向[筛选器](query-odata-filter-orderby-syntax.md)在 Azure 搜索中的集合字段，可以使用[`any`并`all`运算符](search-query-odata-collection-operators.md)一起使用**lambda 表达式**。 Lambda 表达式是应用于集合的每个元素的子筛选器。

可在 lambda 表达式的筛选器表达式不是每个功能。 所提供的功能不同，具体取决于你想要筛选的集合字段的数据类型。 如果你尝试在该上下文中不受支持的 lambda 表达式中使用的功能，这可以导致错误。 如果尝试在集合字段上重写复杂的筛选器时遇到此类错误，本文将帮助您解决这个问题。

## <a name="common-collection-filter-errors"></a>集合筛选器的常见错误

下表列出了尝试执行收集筛选器时可能会遇到的错误。 使用筛选器表达式的 lambda 表达式中不受支持的功能时，会发生这些错误。 每个错误提供有关如何重写筛选器以避免此错误的一些指导。 该表还包括指向相关的部分，其中提供有关如何避免该错误的详细信息这篇文章的链接。

| 错误消息 | 情况 | 有关详细信息，请参阅 |
| --- | --- | --- |
| 函数 ismatch 没有绑定到的范围变量的任何参数。 仅绑定的字段 （'any' 或 'all'） 的 lambda 表达式中支持的引用。 请更改筛选器，以便 ismatch 函数是外部 lambda 表达式，然后重试。 | 使用`search.ismatch`或`search.ismatchscoring`在 lambda 表达式 | [用于筛选复杂集合规则](#bkmk_complex) |
| 无效的 lambda 表达式。 找到相反应有循环访问的类型为 collection （edm.string） 字段的 lambda 表达式中测试相等。 对于 any，请使用窗体 x eq y 或 search.in(...) 的表达式。 为 all，请使用窗体 x ne y，不 (x eq y) 或不 search.in(...) 的表达式。 | 对类型的字段进行筛选 `Collection(Edm.String)` | [筛选字符串集合的规则](#bkmk_strings) |
| 无效的 lambda 表达式。 找到的不受支持的窗体的复杂的布尔表达式。 对于 any，请使用 Or 的 And，也称为析取范式标准形式的表达式。 例如： '(a and b) 或 （c 和 d） 在 a、 b、 c 和 d 时，比较或相等性的子表达式。 为 all，请使用 And 的 ORs，也称为合标准形式的表达式。 例如： '(a or b) 和 （c 或 d） 在 a、 b、 c 和 d 时，比较是否相等的子表达式。 比较表达式的示例: x gt 5，x le 2。 相等表达式的示例: x eq 5。 不等表达式的示例: x ne 5。 | 对类型的字段进行筛选`Collection(Edm.DateTimeOffset)`， `Collection(Edm.Double)`， `Collection(Edm.Int32)`，或 `Collection(Edm.Int64)` | [筛选可比较的集合的规则](#bkmk_comparables) |
| 无效的 lambda 表达式。 在循环访问的字段类型 Collection(Edm.GeographyPoint) 的 lambda 表达式中找到 geo.distance() 或 geo.intersects() 不支持的使用。 对于 any，请确保你比较 geo.distance() 使用 lt 或 le 运算符，并确保任何使用 geo.intersects() 不起作用。 为 all，请确保你比较 geo.distance() 使用 gt 或 ge 运算符，并确保任何使用 geo.intersects() 不起作用。 | 对类型的字段进行筛选 `Collection(Edm.GeographyPoint)` | [用于筛选 GeographyPoint 集合规则](#bkmk_geopoints) |
| 无效的 lambda 表达式。 在循环访问 Collection(Edm.GeographyPoint) 类型的字段的 lambda 表达式中不支持复杂的布尔表达式。 对于任何，请加入使用的子表达式或;和不受支持。 为 all，请加入使用的子表达式和;或不受支持。 | 对类型的字段进行筛选`Collection(Edm.String)`或 `Collection(Edm.GeographyPoint)` | [筛选字符串集合的规则](#bkmk_strings) <br/><br/> [用于筛选 GeographyPoint 集合规则](#bkmk_geopoints) |
| 无效的 lambda 表达式。 找到一个比较运算符 （lt、 le、 gt 或 ge 的一个）。 在循环访问的类型为 collection （edm.string） 字段的 lambda 表达式中不允许仅相等运算符。 对于 any，请使用 x eq y 形式的表达式。 为 all，请使用窗体 x ne y 或不 (x eq y) 的表达式。 | 对类型的字段进行筛选 `Collection(Edm.String)` | [筛选字符串集合的规则](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>如何编写有效的集合筛选器

编写有效的集合筛选器的规则是不同的每种数据类型。 以下部分介绍通过显示的示例的筛选器的支持功能，这不是规则：

- [筛选字符串集合的规则](#bkmk_strings)
- [用于筛选布尔集合规则](#bkmk_bools)
- [用于筛选 GeographyPoint 集合规则](#bkmk_geopoints)
- [筛选可比较的集合的规则](#bkmk_comparables)
- [用于筛选复杂集合规则](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>筛选字符串集合的规则

内部 lambda 表达式的字符串集合，可以使用的仅比较运算符包括`eq`和`ne`。

> [!NOTE]
> Azure 搜索不支持`lt` / `le` / `gt` / `ge`运算符对于字符串，是否内部或外部 lambda 表达式。

正文`any`可以仅测试是否相等的正文时`all`可以仅测试是否不相等。

还有可能要合并多个表达式通过`or`中的正文`any`，并通过`and`的正文中`all`。 由于`search.in`函数等同于结合使用的相等性检查`or`，还允许使用的正文中`any`。 也可反过来`not search.in`的正文中允许`all`。

例如，允许两个表达式：

- `tags/any(t: t eq 'books')`
- `tags/any(t: search.in(t, 'books, games, toys'))`
- `tags/all(t: t ne 'books')`
- `tags/all(t: not (t eq 'books'))`
- `tags/all(t: not search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' or t eq 'games')`
- `tags/all(t: t ne 'books' and not (t eq 'games'))`

而不允许两个表达式：

- `tags/any(t: t ne 'books')`
- `tags/any(t: not search.in(t, 'books, games, toys'))`
- `tags/all(t: t eq 'books')`
- `tags/all(t: search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' and t ne 'games')`
- `tags/all(t: t ne 'books' or not (t eq 'games'))`

<a name="bkmk_bools"></a>

## <a name="rules-for-filtering-boolean-collections"></a>用于筛选布尔集合规则

类型`Edm.Boolean`仅支持`eq`和`ne`运算符。 在这种情况下，它没有多少意义若要允许合并此类检查使用的相同范围变量的子句`and` / `or`因为这会始终导致 tautologies 或矛盾。

下面是允许的布尔值集合的筛选器的一些示例：

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

与不同的字符串集合，布尔集合在哪种类型的 lambda 表达式中具有可以在其使用运算符不受限制。 这两`eq`并`ne`可以在正文中使用`any`或`all`。

如下所示的表达式不允许为布尔值的集合：

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>用于筛选 GeographyPoint 集合规则

类型的值`Edm.GeographyPoint`集合中不能相互进行比较直接。 相反，它们必须使用作为参数`geo.distance`和`geo.intersects`函数。 `geo.distance`又必须为使用其中一个比较运算符的距离值比较函数`lt`， `le`， `gt`，或`ge`。 这些规则也适用于非集合 Edm.GeographyPoint 字段。

字符串集合一样`Edm.GeographyPoint`集合具有可以如何使用并在不同类型的 lambda 表达式中组合的地理空间函数的一些规则：

- 您可以使用的比较运算符`geo.distance`函数取决于 lambda 表达式的类型。 有关`any`，可以仅使用`lt`或`le`。 有关`all`，可以仅使用`gt`或`ge`。 对涉及的表达式的求反`geo.distance`，但您必须要更改的比较运算符 (`geo.distance(...) lt x`变得`not (geo.distance(...) ge x)`并`geo.distance(...) le x`变得`not (geo.distance(...) gt x)`)。
- 中的正文`all`，则`geo.intersects`函数必须进行求反。 相反，在正文`any`，则`geo.intersects`函数必须不起作用。
- 中的正文`any`，可以使用组合地理空间表达式`or`。 中的正文`all`，可以使用组合此类表达式`and`。

存在出于类似原因作为字符串集合上相等/不等限制更高版本的限制。 请参阅[Azure 搜索中的了解 OData 集合筛选器](search-query-understand-collection-filters.md)，更深入地了解这些原因。

以下是上的筛选器的一些示例`Edm.GeographyPoint`允许的集合：

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

不允许使用类似于下面的表达式`Edm.GeographyPoint`集合：

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>筛选可比较的集合的规则

本部分适用于以下数据类型：

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

类型，如`Edm.Int32`并`Edm.DateTimeOffset`支持所有六项的比较运算符： `eq`， `ne`， `lt`， `le`， `gt`，和`ge`。 通过这些类型的集合的 lambda 表达式可以包含使用任何这些运算符的简单表达式。 这同时适用于`any`和`all`。 例如，允许这些筛选器：

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

但是，有如何进行此类比较表达式组合到 lambda 表达式中更复杂的表达式的限制：

- 为规则`any`:
  - 简单的不等式表达式不能有效地结合任何其他表达式。 例如，允许使用此表达式：
    - `ratings/any(r: r ne 5)`

    但是，此表达式不是：
    - `ratings/any(r: r ne 5 and r gt 2)`

    和虽然允许使用此表达式，但它不有用因为条件重叠：
    - `ratings/any(r: r ne 5 or r gt 7)`
  - 简单的比较表达式涉及`eq`， `lt`， `le`， `gt`，或`ge`可以结合`and` / `or`。 例如：
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - 比较表达式结合`and`（连词） 可以进一步使用组合`or`。 作为布尔逻辑中已知此窗体"[析取范式标准形式](https://en.wikipedia.org/wiki/Disjunctive_normal_form)"(DNF)。 例如：
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- 为规则`all`:
  - 简单的等式表达式不能有效地结合任何其他表达式。 例如，允许使用此表达式：
    - `ratings/all(r: r eq 5)`

    但是，此表达式不是：
    - `ratings/all(r: r eq 5 or r le 2)`

    和虽然允许使用此表达式，但它不有用因为条件重叠：
    - `ratings/all(r: r eq 5 and r le 7)`
  - 简单的比较表达式涉及`ne`， `lt`， `le`， `gt`，或`ge`可以结合`and` / `or`。 例如：
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - 比较表达式结合`or`（表达式） 可以进一步使用组合`and`。 作为布尔逻辑中已知此窗体"[合标准形式](https://en.wikipedia.org/wiki/Conjunctive_normal_form)"(CNF)。 例如：
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>用于筛选复杂集合规则

Lambda 表达式通过复杂集合支持的基元类型的集合与 lambda 表达式灵活得多的语法。 可以使用任何筛选器构造此类 lambda 表达式，可以使用外部的一个，但只有两个例外中。

首先，函数`search.ismatch`和`search.ismatchscoring`在 lambda 表达式内不受支持。 有关详细信息，请参阅[Azure 搜索中的了解 OData 集合筛选器](search-query-understand-collection-filters.md)。

其次，引用不是字段*绑定*对范围变量 (所谓*自由变量*) 不允许使用。 例如，考虑以下两个等效 OData 筛选器表达式：

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

将允许第一个表达式，同时第二个窗体将被拒绝，因为`details/margin`不绑定到的范围变量`s`。

此规则也扩展到具有变量绑定在外部作用域中的表达式。 此类变量是免费方面出现的作用域。 例如，允许，而第二个等效的表达式无效，因为第一个表达式`s/name`是相对于范围变量的作用域可用`a`:

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

此限制不应为在实践中的问题，因为它是总能够构造筛选器，以便 lambda 表达式包含绑定的变量。

## <a name="cheat-sheet-for-collection-filter-rules"></a>速查表集合筛选器规则

下表总结了用于构造有效的筛选器的每个集合数据类型的规则。

[!INCLUDE [Limitations on OData lambda expressions in Azure Search](../../includes/search-query-odata-lambda-limitations.md)]

有关如何构造有效的筛选器，每个用例的示例，请参阅[如何编写有效的集合筛选器](#bkmk_examples)。

如果通常情况下，写入筛选器，了解从第一个原则规则帮助您多个只需记住它们，请参阅[Azure 搜索中的了解 OData 集合筛选器](search-query-understand-collection-filters.md)。

## <a name="next-steps"></a>后续步骤  

- [了解 Azure 搜索中的 OData 集合筛选器](search-query-understand-collection-filters.md)
- [Azure 搜索中的筛选器](search-filters.md)
- [Azure 搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
