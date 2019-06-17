---
title: OData 全文搜索的函数参考-Azure 搜索
description: OData 全文搜索功能，search.ismatch 和 search.ismatchscoring，在 Azure 搜索查询中。
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
ms.openlocfilehash: 158312a7afe88e7b9885376c5d28b01958acbbfb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079803"
---
# <a name="odata-full-text-search-functions-in-azure-search---searchismatch-and-searchismatchscoring"></a>在 Azure 搜索中的 OData 全文搜索函数`search.ismatch`和 `search.ismatchscoring`

Azure 搜索的上下文中支持全文搜索[OData 筛选器表达式](query-odata-filter-orderby-syntax.md)通过`search.ismatch`和`search.ismatchscoring`函数。 这些函数，您可以结合全文搜索的方式，只需通过使用顶级不可能实现的严格布尔筛选`search`的参数[搜索 API](https://docs.microsoft.com/rest/api/searchservice/search-documents)。

> [!NOTE]
> `search.ismatch`并`search.ismatchscoring`筛选器中仅支持函数[搜索 API](https://docs.microsoft.com/rest/api/searchservice/search-documents)。 中不支持[建议](https://docs.microsoft.com/rest/api/searchservice/suggestions)或[记忆式键入功能](https://docs.microsoft.com/rest/api/searchservice/autocomplete)Api。

## <a name="syntax"></a>语法

以下 EBNF ([扩展巴科斯-诺尔范式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定义的语法`search.ismatch`和`search.ismatchscoring`函数：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

交互式语法关系图也是可用的：

> [!div class="nextstepaction"]
> [Azure 搜索的 OData 语法关系图](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> 请参阅[适用于 Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)的完整 EBNF。

### <a name="searchismatch"></a>search.ismatch

`search.ismatch`函数计算的全文搜索查询结果作为筛选器表达式的一部分。 与搜索查询匹配的文档将在结果集中返回。 此函数的以下重载可用：

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

下表中定义参数：

| 参数名称 | Type | 描述 |
| --- | --- | --- |
| `search` | `Edm.String` | 搜索查询 (在[简单](query-simple-syntax.md)或[完整](query-lucene-syntax.md)Lucene 查询语法)。 |
| `searchFields` | `Edm.String` | 可搜索字段搜索; 中的逗号分隔列表默认值为索引中的所有可搜索字段。 使用时[审定搜索](query-lucene-syntax.md#bkmk_fields)中`search`参数，Lucene 查询中的字段说明符重写此参数中指定的任何字段。 |
| `queryType` | `Edm.String` | `'simple'` 或`'full'`; 默认为`'simple'`。 指定 `search` 参数中使用的查询语言。 |
| `searchMode` | `Edm.String` | `'any'` 或`'all'`，默认为`'any'`。 指示任何或所有搜索都术语中`search`参数必须匹配才能将文档视为匹配项。 使用时[Lucene 布尔运算符](query-lucene-syntax.md#bkmk_boolean)中`search`参数，它们将优先于此参数。 |

所有上述参数都是等效于相应[搜索 API 中搜索请求参数](https://docs.microsoft.com/rest/api/searchservice/search-documents)。

`search.ismatch`函数返回类型的值`Edm.Boolean`，它允许您在编写它时使用其他筛选器的子表达式使用布尔[逻辑运算符](search-query-odata-logical-operators.md)。

> [!NOTE]
> Azure 搜索不支持使用`search.ismatch`或`search.ismatchscoring`在 lambda 表达式内。 这意味着不能通过可将全文搜索匹配项的严格的筛选器匹配项与该对象相关联的对象的集合的写入筛选器。 此限制以及示例的详细信息，请参阅[故障排除 Azure 搜索中的集合筛选器](search-query-troubleshoot-collection-filters.md)。 存在此限制的原因的更多详细信息，请参阅[了解 Azure 搜索中的集合筛选器](search-query-understand-collection-filters.md)。


### <a name="searchismatchscoring"></a>search.ismatchscoring

`search.ismatchscoring`函数一样，`search.ismatch`函数，返回`true`作为参数传递的文档，全文搜索查询相匹配。 它们之间的区别在于，与 `search.ismatchscoring` 查询匹配的文档的相关性分数与文档整体分数有关，而对于 `search.ismatch`，文档分数不会发生更改。 此函数的以下重载可用于与 `search.ismatch` 的参数相同的参数：

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

同时`search.ismatch`和`search.ismatchscoring`可以在同一筛选器表达式中使用函数。

## <a name="examples"></a>示例

查找包含“waterfront”一词的文档。 此筛选器查询与包含 `search=waterfront` 的[搜索请求](https://docs.microsoft.com/rest/api/searchservice/search-documents)相同。

    search.ismatchscoring('waterfront')

查找带有“hostel”一词且评分大于或等于 4 分的文档，或带有“motel”一词且评分等于 5 分的文档。 请注意，如果没有 `search.ismatchscoring` 函数，则无法表达此请求。

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

查找没有“luxury”一词的文档。

    not search.ismatch('luxury')

查找包含短语“ocean view”或评分等于 5 分的文档。 `search.ismatchscoring` 查询仅针对 `HotelName` 和 `Rooms/Description` 字段执行。

将如此--返回酒店匹配仅析取的第二个子句的文档`Rating`等于 5。 若要使其清除，这些文档未与任何表达式的已评分的各部分进行匹配，分数等于零，则会返回。

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

查找文档的位置的术语"酒店"和"机场"彼此的酒店，说明中的 5 个字以内以及其中吸烟中不允许使用至少某些聊天室。 此查询使用[完整 Lucene 查询语言](query-lucene-syntax.md)。

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>后续步骤  

- [Azure 搜索中的筛选器](search-filters.md)
- [Azure 搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
