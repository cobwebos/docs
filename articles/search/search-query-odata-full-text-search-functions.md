---
title: OData 全文搜索函数参考
titleSuffix: Azure Cognitive Search
description: Azure 认知搜索查询中的 OData 全文搜索函数 search.ismatch 和 search.ismatchscoring。
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
ms.openlocfilehash: 78f9e4d8fa80fdf74bdb5cd79f4489d12696fcc2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935783"
---
# <a name="odata-full-text-search-functions-in-azure-cognitive-search---searchismatch-and-searchismatchscoring"></a>Azure 认知搜索中的 OData 全文搜索函数 - `search.ismatch` 和 `search.ismatchscoring`

Azure 认知搜索支持使用 `search.ismatch` 和 `search.ismatchscoring` 函数在 [OData 筛选器表达式](query-odata-filter-orderby-syntax.md)上下文中进行全文搜索。 可以通过这些函数将全文搜索与严格的布尔筛选配合使用，所用方式在仅仅使用 [搜索 API](/rest/api/searchservice/search-documents) 的顶级 `search` 参数的情况下无法实现。

> [!NOTE]
> `search.ismatch` 和 `search.ismatchscoring` 函数只在[搜索 API](/rest/api/searchservice/search-documents) 的筛选器中受支持。 它们在[建议](/rest/api/searchservice/suggestions) API 或[自动完成](/rest/api/searchservice/autocomplete) API 中不受支持。

## <a name="syntax"></a>语法

以下 EBNF ([扩展 Backus-Naur 窗体](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定义和函数的语法 `search.ismatch` `search.ismatchscoring` ：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

下面还提供了交互式语法图：

> [!div class="nextstepaction"]
> [Azure 认知搜索的 OData 语法图](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> 请参阅[适用于 Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)以获取完整的 EBNF。

### <a name="searchismatch"></a>search.ismatch

`search.ismatch` 函数将全文搜索查询作为筛选表达式的一部分进行求值。 与搜索查询匹配的文档将在结果集中返回。 此函数的以下重载可用：

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

下表定义了这些参数：

| 参数名称 | 类型 | 说明 |
| --- | --- | --- |
| `search` | `Edm.String` | 搜索查询（采用[简单](query-simple-syntax.md)或[完整](query-lucene-syntax.md) Lucene 查询语法）。 |
| `searchFields` | `Edm.String` | 要在其中进行搜索的可搜索字段的逗号分隔列表；默认为索引中的所有可搜索字段。 在 `search` 参数中使用[字段搜索](query-lucene-syntax.md#bkmk_fields)时，在 Lucene 查询中指定的字段会覆盖在此参数中指定的任何字段。 |
| `queryType` | `Edm.String` | `'simple'` 或 `'full'`；默认为 `'simple'`。 指定 `search` 参数中使用的查询语言。 |
| `searchMode` | `Edm.String` | `'any'` 或 `'all'`，默认为 `'any'`。 指示必须匹配 `search` 参数中的任意搜索词还是全部搜索词才能将文档视为匹配。 在 `search` 参数中使用 [Lucene 布尔运算符](query-lucene-syntax.md#bkmk_boolean)时，这些运算符的优先级高于该参数。 |

所有上述参数均相当于[搜索 API 中的相应搜索请求参数](/rest/api/searchservice/search-documents)。

`search.ismatch` 函数返回类型为 `Edm.Boolean` 的值，这样你就可以使用布尔[逻辑运算符](search-query-odata-logical-operators.md)通过其他筛选器子表达式对其进行编辑。

> [!NOTE]
> Azure 认知搜索不支持在 lambda 表达式中使用 `search.ismatch` 或 `search.ismatchscoring`。 这意味着，如果对象集合可以将全文搜索匹配项与同一对象上严格的筛选器匹配项相关联，则不能基于该对象集合编写筛选器。 若要更详细地了解此限制和相关示例，请参阅[排查 Azure 认知搜索中的集合筛选器问题](search-query-troubleshoot-collection-filters.md)。 若要更深入地了解为何存在此限制，请参阅[了解 Azure 认知搜索中的集合筛选器](search-query-understand-collection-filters.md)。


### <a name="searchismatchscoring"></a>search.ismatchscoring

与 `search.ismatch` 函数一样，对于与作为参数传递的全文搜索查询匹配的文档，`search.ismatchscoring` 函数会返回 `true`。 它们之间的区别在于，与 `search.ismatchscoring` 查询匹配的文档的相关性分数与文档整体分数有关，而对于 `search.ismatch`，文档分数不会发生更改。 此函数的以下重载可用于与 `search.ismatch` 的参数相同的参数：

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

可以在同一筛选器表达式中使用 `search.ismatch` 和 `search.ismatchscoring` 函数。

## <a name="examples"></a>示例

查找包含“waterfront”一词的文档。 此筛选器查询与包含 `search=waterfront` 的[搜索请求](/rest/api/searchservice/search-documents)相同。

```odata-filter-expr
    search.ismatchscoring('waterfront')
```

查找带有“hostel”一词且评分大于或等于 4 分的文档，或带有“motel”一词且评分等于 5 分的文档。 请注意，如果没有 `search.ismatchscoring` 函数，则无法表达此请求。

```odata-filter-expr
    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5
```

查找没有“luxury”一词的文档。

```odata-filter-expr
    not search.ismatch('luxury')
```

查找包含短语“ocean view”或评分等于 5 分的文档。 `search.ismatchscoring` 查询仅针对 `HotelName` 和 `Rooms/Description` 字段执行。

仅与析取的第二个子句匹配的文档也将被返回，即 `Rating` 等于 5 分的酒店。 为了清楚地表明这些文档与表达式的任何得分部分都不匹配，它们返回的分数将为零。

```odata-filter-expr
    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5
```

在酒店描述中查找词条“hotel”和“airport”相距不超过 5 个字词且不允许吸烟（至少部分房间是这样）的文档。 此查询使用[完整 Lucene 查询语言](query-lucene-syntax.md)。

```odata-filter-expr
    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)
```

## <a name="next-steps"></a>后续步骤  

- [Azure 认知搜索中的筛选器](search-filters.md)
- [Azure 认知搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档（Azure 认知搜索 REST API）](/rest/api/searchservice/Search-Documents)