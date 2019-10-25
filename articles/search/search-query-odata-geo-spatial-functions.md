---
title: OData 地理空间函数引用
titleSuffix: Azure Cognitive Search
description: Azure 认知搜索查询中的 OData 地理空间函数、异地和地区交叉。
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
ms.openlocfilehash: 09034423e16c652cf6994b38f8d92574abc0ce55
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793337"
---
# <a name="odata-geo-spatial-functions-in-azure-cognitive-search---geodistance-and-geointersects"></a>Azure 认知搜索中的 OData 地理空间函数-`geo.distance` 和 `geo.intersects`

Azure 认知搜索通过 `geo.distance` 和 `geo.intersects` 函数支持[OData 筛选器表达式](query-odata-filter-orderby-syntax.md)中的地理空间查询。 `geo.distance` 函数返回两个点之间的距离（其中一个是字段或范围变量），另一个是作为筛选器一部分传递的常量。 如果给定点在给定多边形内（其中，点是字段或范围变量，而多边形指定为作为筛选器一部分传递的常量），则 `geo.intersects` 函数返回 `true`。

还可以在[ **$orderby**参数](search-query-odata-orderby.md)中使用 `geo.distance` 函数，以便按与给定点之间的距离对搜索结果进行排序。 **$orderby** 中 `geo.distance` 的语法与其在 **$filter** 中的语法相同。 在 **$orderby**中使用 `geo.distance` 时，它应用到的字段必须为类型 `Edm.GeographyPoint`，并且还必须可**排序**。

> [!NOTE]
> 在 **$orderby**参数中使用 `geo.distance` 时，传递给函数的字段必须只包含单个地理点。 换句话说，它的类型必须为 `Edm.GeographyPoint` 且不 `Collection(Edm.GeographyPoint)`。 不能对 Azure 认知搜索中的集合字段进行排序。

## <a name="syntax"></a>语法

以下 EBNF （[扩展的巴科斯-诺尔范式窗体](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)）定义 `geo.distance` 和 `geo.intersects` 函数的语法，以及它们运行时所在的地理空间值：

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*
```

还提供交互式语法关系图：

> [!div class="nextstepaction"]
> [适用于 Azure 认知搜索的 OData 语法关系图](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> 请参阅[适用于 Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)，了解完整的 EBNF。

### <a name="geodistance"></a>地域

`geo.distance` 函数使用 `Edm.GeographyPoint` 类型的两个参数，并返回一个 `Edm.Double` 值，该值表示二者之间的距离。 这不同于其他支持 OData 地理空间操作的服务，后者通常以米为单位返回距离。

`geo.distance` 的参数之一必须是地理点常量，另一个参数必须是字段路径（如果筛选器在类型为 `Collection(Edm.GeographyPoint)`的字段的情况下，则必须是范围变量）。 这些参数的顺序并不重要。

地理点常量的形式为 `geography'POINT(<longitude> <latitude>)'`，其中，经度和纬度为数值常量。

> [!NOTE]
> 在筛选器中使用 `geo.distance` 时，必须使用 `lt`、`le`、`gt`或 `ge`将函数返回的距离与常量进行比较。 比较距离时不支持使用 `eq` 和 `ne` 运算符。 例如，这是 `geo.distance`： `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`的正确用法。

### <a name="geointersects"></a>地域交集

`geo.intersects` 函数使用 `Edm.GeographyPoint` 类型的变量和常量 `Edm.GeographyPolygon` 并返回 `Edm.Boolean` -- 如果该点位于多边形边界内，则`true` 否则为。

多边形是作为一系列定义边界环的点（请参阅下面的[示例](#examples)）存储的二维表面。 多边形需要处于闭合状态，这意味着第一个点集和最后一个点集必须相同。 [多边形中的点必须采用逆时针顺序](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)。

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>跨180th 经线的地理空间查询和多边形

对于许多地理空间查询库，会构建一个包含180th 经线（接近 dateline）的查询，该查询可以是脱离限制的，也可以是需要解决的问题，例如在经线的任意一侧将多边形拆分为两个。

在 Azure 认知搜索中，如果查询形状是矩形，并且坐标沿纬度和纬度（例如 `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`）对齐，则包含180度经度的地理空间查询将按预期方式工作。 否则，对于非矩形的形状或未对齐的形状，请考虑使用拆分多边形方法。  

### <a name="geo-spatial-functions-and-null"></a>地理空间函数和 `null`

与 Azure 认知搜索中的所有其他非集合字段一样，类型 `Edm.GeographyPoint` 的字段可以包含 `null` 值。 当 Azure 认知搜索为 `null`的字段评估 `geo.intersects` 时，结果将始终为 `false`。 在这种情况下 `geo.distance` 的行为取决于上下文：

- 在筛选器中，`geo.distance` `null` 字段将导致 `null`。 这意味着文档将不匹配，因为 `null` 与任何非 null 值相比，其计算结果为 `false`。
- 使用 **$orderby**对结果进行排序时，`null` 字段的 `geo.distance` 会导致可能的最大距离。 当使用排序方向 `asc` （默认值）时，具有此类字段的文档的排序将低于所有其他字段，并且在 `desc`方向时，这些文档将高于其他值。

## <a name="examples"></a>示例

### <a name="filter-examples"></a>筛选器示例

查找给定引用点的10公里内的所有酒店（其中 location 是类型 `Edm.GeographyPoint`的字段）：

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

查找给定视区中被描述为多边形的所有酒店（其中 location 是 `Edm.GeographyPoint`类型的字段）。 请注意，多边形是闭合的（第一个点集和最后一个点集必须相同），且[这些点必须按逆时针顺序列出](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)。

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Order-by 示例

按 `rating`按降序对酒店进行排序，然后按与给定坐标的距离升序排序：

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

按降序对酒店排序，`search.score` 和 `rating`，然后按从给定坐标开始的升序进行排序，以便在两个具有相同分级的酒店之间进行排序，最接近的一家酒店首先列出：

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>后续步骤  

- [Azure 认知搜索中的筛选器](search-filters.md)
- [Azure 认知搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [适用于 Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档&#40;Azure 认知搜索 REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
