---
title: OData 顺序-按引用
titleSuffix: Azure Cognitive Search
description: Azure 中的 order by 语法的 OData 语言参考认知搜索查询。
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
ms.openlocfilehash: e3ca19b5696b9a7ad9b68b180313753a5c9de912
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793297"
---
# <a name="odata-orderby-syntax-in-azure-cognitive-search"></a>Azure 中的 OData $orderby 语法认知搜索

 可以使用[OData **$orderby**参数](query-odata-filter-orderby-syntax.md)在 Azure 认知搜索中应用搜索结果的自定义排序顺序。 本文详细介绍 **$orderby**的语法。 有关如何在显示搜索结果时使用 **$orderby**的更多常规信息，请参阅[如何使用 Azure 认知搜索中的搜索结果](search-pagination-page-layout.md)。

## <a name="syntax"></a>语法

**$Orderby**参数接受一个逗号分隔的列表，该列表的最大**顺序**为32。 下面的 EBNF （[扩展的巴科斯-诺尔范式窗体](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)）描述了 order by 子句的语法：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

还提供交互式语法关系图：

> [!div class="nextstepaction"]
> [适用于 Azure 认知搜索的 OData 语法关系图](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> 请参阅[适用于 Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)，了解完整的 EBNF。

每个子句都具有排序条件，可选择后跟排序方向（`asc` 升序或 `desc` 为降序）。 如果未指定方向，则默认值为 "升序"。 排序条件可以是 `sortable` 字段的路径，也可以是对[`geo.distance`](search-query-odata-geo-spatial-functions.md)或[`search.score`](search-query-odata-search-score-function.md)函数的调用。

如果多个文档具有相同的排序条件，并且未使用 `search.score` 函数（例如，如果按数值 `Rating` 字段进行排序，并且三个文档的排名均为4），则按文档分数降序会损坏并列。 如果文档分数相同（例如，在请求中未指定全文搜索查询时），则相关文档的相对顺序是不确定的。

可以指定多个排序条件。 表达式的顺序决定最终排序顺序。 例如，若要按分数降序排序，然后按级别进行排序，则会 `$orderby=search.score() desc,Rating desc`语法。

**$orderby** 中 `geo.distance` 的语法与其在 **$filter** 中的语法相同。 如果在 **$orderby** 中使用 `geo.distance`，其应用到的字段必须为 `Edm.GeographyPoint` 类型，且还必须 `sortable`。

**$orderby** 中 `search.score` 的语法为 `search.score()`。 函数 `search.score` 不采用任何参数。

## <a name="examples"></a>示例

按基准费率对酒店进行升序排序：

    $orderby=BaseRate asc

按评分对酒店进行降序排序，然后按基准费率对酒店进行升序排序（请记住，升序是默认值）：

    $orderby=Rating desc,BaseRate

按评级对酒店降序排序，然后按与给定坐标的距离升序排序：

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

按搜索的降序对酒店排序，并按与给定坐标的距离升序排列。 在具有相同相关性分数和分级的两个旅馆之间，最靠近的酒店首先列出：

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>后续步骤  

- [如何在 Azure 认知搜索中使用搜索结果](search-pagination-page-layout.md)
- [Azure 认知搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [适用于 Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档&#40;Azure 认知搜索 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
