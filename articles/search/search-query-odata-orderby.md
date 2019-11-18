---
title: OData 顺序-按引用
titleSuffix: Azure Cognitive Search
description: 在 Azure 认知搜索查询中使用 order by 的语法和语言参考文档。
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
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113148"
---
# <a name="odata-orderby-syntax-in-azure-cognitive-search"></a>Azure 中的 OData $orderby 语法认知搜索

 可以使用[OData **$orderby**参数](query-odata-filter-orderby-syntax.md)在 Azure 认知搜索中应用搜索结果的自定义排序顺序。 本文详细介绍 **$orderby** 的语法。 有关如何在显示搜索结果时使用 **$orderby**的更多常规信息，请参阅[如何使用 Azure 认知搜索中的搜索结果](search-pagination-page-layout.md)。

## <a name="syntax"></a>语法

**$orderby** 参数接受最多 32 个 **order-by 子句**的逗号分隔列表。 order-by 子句的语法由以下 EBNF（[扩展巴科斯-瑙尔范式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)）描述：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

下面还提供了交互式语法图：

> [!div class="nextstepaction"]
> [适用于 Azure 认知搜索的 OData 语法关系图](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> 请参阅[适用于 Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)，了解完整的 EBNF。

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
- [适用于 Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档&#40;Azure 认知搜索 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
