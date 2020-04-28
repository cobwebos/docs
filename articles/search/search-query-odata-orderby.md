---
title: OData order-by 参考
titleSuffix: Azure Cognitive Search
description: 语法和语言参考文档，用于在 Azure 认知搜索查询中使用 order-by。
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
ms.openlocfilehash: 99ec639b88f3334530243242aadfa0ab52a40df0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "74113148"
---
# <a name="odata-orderby-syntax-in-azure-cognitive-search"></a>Azure 认知搜索中的 OData $orderby 语法

 可以使用 [OData **$orderby** 参数](query-odata-filter-orderby-syntax.md)对 Azure 认知搜索中的搜索结果应用自定义排序顺序。 本文详细介绍 **$orderby** 的语法。 有关如何在呈现搜索结果时使用 **$orderby** 的更多常规信息，请参阅[如何在 Azure 认知搜索中使用搜索结果](search-pagination-page-layout.md)。

## <a name="syntax"></a>语法

**$orderby** 参数接受最多 32 个 **order-by 子句**的逗号分隔列表。 order-by 子句的语法由以下 EBNF（[扩展巴科斯-瑙尔范式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)）描述：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

交互式语法图也可用：

> [!div class="nextstepaction"]
> [Azure 认知搜索的 OData 语法图](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> 请参阅[适用于 Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)以获取完整的 EBNF。

每个子句都有排序条件，可以选择后跟排序方向（`asc` 表示升序，或 `desc` 表示降序）。 如果不指定方向，则默认为升序。 排序条件可以是 `sortable` 字段的路径，也可以是对 [`geo.distance`](search-query-odata-geo-spatial-functions.md) 或 [`search.score`](search-query-odata-search-score-function.md) 函数的调用。

如果多个文档具有相同的排序条件且未使用 `search.score` 函数（例如，如果按数字 `Rating` 字段排序，并且三个文档的评分均为 4 分），则会通过以降序按文档分数排序来打破平分的局面。 当文档分数相同时（例如，当请求中没有指定全文搜索查询时），平分文档的相对排序是不确定的。

可以指定多个排序条件。 表达式的顺序决定最终排序顺序。 例如，若要按分数降序排序，然后按评分排序，语法将为 `$orderby=search.score() desc,Rating desc`。

`geo.distance`$orderby**中** 的语法与其在 **$filter** 中的语法相同。 如果在 `geo.distance`$orderby**中使用**，其应用到的字段必须为 `Edm.GeographyPoint` 类型，且还必须 `sortable`。

`search.score`$orderby**中** 的语法为 `search.score()`。 函数 `search.score` 不接受任何参数。

## <a name="examples"></a>示例

按基准费率对酒店进行升序排序：

    $orderby=BaseRate asc

按评分对酒店进行降序排序，然后按基准费率对酒店进行升序排序（请记住，升序是默认值）：

    $orderby=Rating desc,BaseRate

按评分对酒店进行降序排序，然后按距给定坐标的距离进行升序排序：

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

按 search.score 和评分对酒店进行降序排序，然后按距给定坐标的距离进行升序排序。 在相关性评分和评分相同的两家酒店之间，将距离最近的酒店列在前面：

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>后续步骤  

- [如何在 Azure 认知搜索中使用搜索结果](search-pagination-page-layout.md)
- [Azure 认知搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档（Azure 认知搜索 REST API）](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
