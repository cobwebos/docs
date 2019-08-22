---
title: OData 全文搜索函数引用-Azure 搜索
description: OData 全文搜索功能, 搜索 ismatch 和 ismatchscoring, 在 Azure 搜索查询中。
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
ms.openlocfilehash: c3b28c8799b09ddfe008df8539709c5a704ac6b4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648016"
---
# <a name="odata-full-text-search-functions-in-azure-search---searchismatch-and-searchismatchscoring"></a>Azure 搜索中的 OData 全文搜索功能- `search.ismatch`和`search.ismatchscoring`

Azure 搜索通过`search.ismatch`和`search.ismatchscoring`函数支持[OData 筛选表达式](query-odata-filter-orderby-syntax.md)的上下文中的全文搜索。 使用这些函数可以将全文搜索与严格的布尔筛选相结合, 只需使用`search` [搜索 API](https://docs.microsoft.com/rest/api/searchservice/search-documents)的顶级参数即可。

> [!NOTE]
> 仅搜索`search.ismatchscoring` [API](https://docs.microsoft.com/rest/api/searchservice/search-documents)中的筛选器支持和函数。`search.ismatch` [建议](https://docs.microsoft.com/rest/api/searchservice/suggestions)或[自动完成](https://docs.microsoft.com/rest/api/searchservice/autocomplete)api 中不支持它们。

## <a name="syntax"></a>语法

以下 EBNF ([扩展的巴科斯-诺尔范式窗体](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定义`search.ismatch`和`search.ismatchscoring`函数的语法:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

还提供交互式语法关系图:

> [!div class="nextstepaction"]
> [适用于 Azure 搜索的 OData 语法关系图](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> 有关完整的 EBNF, 请参阅[Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)。

### <a name="searchismatch"></a>search.ismatch

`search.ismatch`函数将全文搜索查询作为筛选表达式的一部分进行计算。 与搜索查询匹配的文档将在结果集中返回。 此函数的以下重载可用：

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

下表中定义了这些参数:

| 参数名称 | type | 描述 |
| --- | --- | --- |
| `search` | `Edm.String` | 搜索查询 ([简单](query-simple-syntax.md)或[完整](query-lucene-syntax.md)的 Lucene 查询语法)。 |
| `searchFields` | `Edm.String` | 要在其中搜索的可搜索字段的逗号分隔列表;默认为索引中的所有可搜索字段。 在`search`参数中使用[现场搜索](query-lucene-syntax.md#bkmk_fields)时, Lucene 查询中的字段说明符会重写此参数中指定的任何字段。 |
| `queryType` | `Edm.String` | `'simple'`或`'full'`; 默认值`'simple'`为。 指定 `search` 参数中使用的查询语言。 |
| `searchMode` | `Edm.String` | `'any'`或者`'all'`, 默认为`'any'`。 指示是否必须匹配`search`参数中的任何或所有搜索词, 以便将文档作为匹配项进行计数。 在`search`参数中使用[Lucene 布尔运算符](query-lucene-syntax.md#bkmk_boolean)时, 它们将优先于此参数。 |

以上所有参数都等效于[搜索 API 中的相应搜索请求参数](https://docs.microsoft.com/rest/api/searchservice/search-documents)。

函数将返回一个类型`Edm.Boolean`的值, 该值允许使用布尔[逻辑运算符](search-query-odata-logical-operators.md)将其与其他筛选器子表达式组合在一起。 `search.ismatch`

> [!NOTE]
> Azure 搜索不支持在 lambda `search.ismatch`表达式`search.ismatchscoring`中使用或。 这意味着不能对对象的集合编写筛选器, 这些筛选器可以将全文搜索匹配与相同对象上严格筛选器匹配项关联。 有关此限制以及示例的更多详细信息, 请参阅[Azure 搜索中的集合筛选器故障排除](search-query-troubleshoot-collection-filters.md)。 有关为何存在此限制的详细信息, 请参阅[了解 Azure 搜索中的集合筛选器](search-query-understand-collection-filters.md)。


### <a name="searchismatchscoring"></a>search.ismatchscoring

与函数一样, 函数为与作为参数传递的全文搜索查询匹配的文档返回`true`。 `search.ismatchscoring` `search.ismatch` 它们之间的区别在于，与 `search.ismatchscoring` 查询匹配的文档的相关性分数与文档整体分数有关，而对于 `search.ismatch`，文档分数不会发生更改。 此函数的以下重载可用于与 `search.ismatch` 的参数相同的参数：

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

`search.ismatch` 和`search.ismatchscoring`函数可以在同一个筛选表达式中使用。

## <a name="examples"></a>示例

查找包含“waterfront”一词的文档。 此筛选器查询与包含 `search=waterfront` 的[搜索请求](https://docs.microsoft.com/rest/api/searchservice/search-documents)相同。

    search.ismatchscoring('waterfront')

查找带有“hostel”一词且评分大于或等于 4 分的文档，或带有“motel”一词且评分等于 5 分的文档。 请注意，如果没有 `search.ismatchscoring` 函数，则无法表达此请求。

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

查找没有“luxury”一词的文档。

    not search.ismatch('luxury')

查找包含短语“ocean view”或评分等于 5 分的文档。 `search.ismatchscoring` 查询仅针对 `HotelName` 和 `Rooms/Description` 字段执行。

只会返回`Rating`与第二个析取子句匹配的文档。 若要清楚地说明这些文档与表达式的任何计分部分都不匹配, 则将返回分数等于零的值。

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

在旅馆说明中查找术语 "酒店" 和 "机场" 介于5个字以内的文档, 以及至少部分房间内不允许使用吸烟的文档。 此查询使用[完整 Lucene 查询语言](query-lucene-syntax.md)。

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>后续步骤  

- [Azure 搜索中的筛选器](search-filters.md)
- [Azure 搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [适用于 Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
