---
title: OData 顺序-按引用-Azure 搜索
description: Azure 搜索查询中 order by 语法的 OData 语言参考。
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
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
ms.openlocfilehash: 8ee44549931100a1affa5e2bb9e5cda904c05ed1
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647541"
---
# <a name="odata-orderby-syntax-in-azure-search"></a>Azure 搜索中的 OData $orderby 语法

 可以使用[OData **$orderby**参数](query-odata-filter-orderby-syntax.md)在 Azure 搜索中对搜索结果应用自定义排序顺序。 本文详细介绍 **$orderby**的语法。 有关如何在显示搜索结果时使用 **$orderby**的更多常规信息, 请参阅[如何在 Azure 搜索中使用搜索结果](search-pagination-page-layout.md)。

## <a name="syntax"></a>语法

**$Orderby**参数接受一个逗号分隔的列表, 该列表的最大**顺序**为32。 下面的 EBNF ([扩展的巴科斯-诺尔范式窗体](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 描述了 order by 子句的语法:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

还提供交互式语法关系图:

> [!div class="nextstepaction"]
> [适用于 Azure 搜索的 OData 语法关系图](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> 有关完整的 EBNF, 请参阅[Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)。

每个子句都具有排序条件, 可选择后跟排序`asc`方向 (升序`desc`或降序)。 如果未指定方向, 则默认值为 "升序"。 排序条件可以是`sortable`字段的路径, 也可以是对[`geo.distance`](search-query-odata-geo-spatial-functions.md)或[`search.score`](search-query-odata-search-score-function.md)函数的调用。

如果多个文档具有相同的排序条件, `search.score`但未使用该函数 (例如, 如果您按数值`Rating`字段进行排序, 并且三个文档的排名均为 4), 则按文档分数将按降序对并列进行分解。 如果文档分数相同 (例如, 在请求中未指定全文搜索查询时), 则相关文档的相对顺序是不确定的。

可以指定多个排序条件。 表达式的顺序决定最终排序顺序。 例如, 若要按分数降序排序, 然后按级别进行排序, 则语法`$orderby=search.score() desc,Rating desc`为。

**$orderby** 中 `geo.distance` 的语法与其在 **$filter** 中的语法相同。 如果在 **$orderby** 中使用 `geo.distance`，其应用到的字段必须为 `Edm.GeographyPoint` 类型，且还必须 `sortable`。

**$orderby** 中 `search.score` 的语法为 `search.score()`。 函数`search.score`不采用任何参数。

## <a name="examples"></a>示例

按基准费率对酒店进行升序排序：

    $orderby=BaseRate asc

按评分对酒店进行降序排序，然后按基准费率对酒店进行升序排序（请记住，升序是默认值）：

    $orderby=Rating desc,BaseRate

按评级对酒店降序排序, 然后按与给定坐标的距离升序排序:

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

按搜索的降序对酒店排序, 并按与给定坐标的距离升序排列。 在具有相同相关性分数和分级的两个旅馆之间, 最靠近的酒店首先列出:

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>后续步骤  

- [如何在 Azure 搜索中使用搜索结果](search-pagination-page-layout.md)
- [Azure 搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [适用于 Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
