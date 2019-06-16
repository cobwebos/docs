---
title: OData 排序依据参考-Azure 搜索
description: Azure 搜索查询中的排序依据语法的 OData 语言参考。
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
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
ms.openlocfilehash: 1ced35dc73e6d596fbeda32590ab0b69df396c5c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079751"
---
# <a name="odata-orderby-syntax-in-azure-search"></a>Azure 搜索中的 OData $orderby 语法

 可以使用[OData **$orderby**参数](query-odata-filter-orderby-syntax.md)应用在 Azure 搜索中的搜索结果的自定义排序顺序。 本文介绍的语法 **$orderby**在详细信息。 有关如何使用更多常规信息 **$orderby**搜索结果时，请参阅[如何使用搜索结果在 Azure 搜索中](search-pagination-page-layout.md)。

## <a name="syntax"></a>语法

**$Orderby**参数接受以逗号分隔列表的最多 32 个**order by 子句**。 通过以下 EBNF 描述的 order by 子句的语法 ([扩展巴科斯-诺尔范式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

交互式语法关系图也是可用的：

> [!div class="nextstepaction"]
> [Azure 搜索的 OData 语法关系图](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> 请参阅[适用于 Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)的完整 EBNF。

每个子句具有排序条件，可以选择后跟排序方向 (`asc`升序或`desc`按降序排序)。 如果未指定方向，默认值为升序。 排序条件可以是路径`sortable`字段或调用[ `geo.distance` ](search-query-odata-geo-spatial-functions.md)或[ `search.score` ](search-query-odata-search-score-function.md)函数。

如果多个文档都具有相同的排序条件并`search.score`函数未使用 (例如，如果您通过数字进行排序`Rating`字段和三个文档都有其分级为 4)，等同值将不起作用的文档评分降序排序。 文档分数相同时 （例如，没有在请求中指定的全文搜索查询时），则关联的文档的相对顺序是不确定。

可以指定多个排序条件。 表达式的顺序决定最终排序顺序。 例如，若要按分数后, 跟分级，按降序排序语法将为`$orderby=search.score() desc,Rating desc`。

**$orderby** 中 `geo.distance` 的语法与其在 **$filter** 中的语法相同。 如果在 **$orderby** 中使用 `geo.distance`，其应用到的字段必须为 `Edm.GeographyPoint` 类型，且还必须 `sortable`。

**$orderby** 中 `search.score` 的语法为 `search.score()`。 该函数`search.score`不采用任何参数。

## <a name="examples"></a>示例

按基准费率对酒店进行升序排序：

    $orderby=BaseRate asc

按评分对酒店进行降序排序，然后按基准费率对酒店进行升序排序（请记住，升序是默认值）：

    $orderby=Rating desc,BaseRate

对酒店降序的评分，然后从给定的坐标距离按升序进行排序：

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

排序酒店 search.score 和分级，降序，然后按升序从给定的坐标的距离。 具有相同的相关性评分的两个酒店，分级，首先列出最近的一个：

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>后续步骤  

- [Azure 搜索中如何使用搜索结果](search-pagination-page-layout.md)
- [Azure 搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
