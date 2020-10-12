---
title: OData 地理空间函数参考
titleSuffix: Azure Cognitive Search
description: 有关如何在 Azure 认知搜索查询中使用 OData 地理空间函数、geo.distance 和 geo.intersects 的语法和参考文档。
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
ms.openlocfilehash: 376cece922ca424ec78011224852b1fa5499da16
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88934831"
---
# <a name="odata-geo-spatial-functions-in-azure-cognitive-search---geodistance-and-geointersects"></a>Azure 认知搜索中的 OData 地理空间函数 - `geo.distance` 和 `geo.intersects`

Azure 认知搜索支持使用 `geo.distance` 和 `geo.intersects` 函数通过 [OData 筛选器表达式](query-odata-filter-orderby-syntax.md)进行地理空间查询。 `geo.distance` 函数以公里为单位返回两点之间的距离，一个点是字段或范围变量，另一个点是作为筛选器一部分传递的常量。 如果给定的点位于给定的多边形范围内（其中该点是字段或范围变量，多边形指定为作为筛选器一部分传递的常量），`geo.intersects` 函数会返回 `true`。

`geo.distance` 函数也可用在 [ **$orderby** 参数](search-query-odata-orderby.md)中，以便按照与给定点的距离对搜索结果排序。 **$orderby** 中 `geo.distance` 的语法与其在 **$filter** 中的语法相同。 如果在 **$orderby** 中使用 `geo.distance`，则它应用到的字段必须为 `Edm.GeographyPoint` 类型，而且它还必须是**可排序的**。

> [!NOTE]
> 在 **$orderby** 参数中使用 `geo.distance` 时，传递给函数的字段只能包含单个地理点。 换言之，它必须是类型 `Edm.GeographyPoint` 而非 `Collection(Edm.GeographyPoint)`。 不可能在 Azure 认知搜索中对集合字段排序。

## <a name="syntax"></a>语法

以下 EBNF ([扩展 Backus-Naur 窗体](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定义和函数的语法 `geo.distance` ，以及 `geo.intersects` 它们运行时所在的地理空间值：

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

下面还提供了交互式语法图：

> [!div class="nextstepaction"]
> [Azure 认知搜索的 OData 语法图](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> 请参阅[适用于 Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)以获取完整的 EBNF。

### <a name="geodistance"></a>geo.distance

`geo.distance` 函数使用两个类型为 `Edm.GeographyPoint` 的参数并返回一个 `Edm.Double` 值，该值是二者之间的距离，以公里为单位。 这与支持 OData 地理空间运算的其他服务不同，后者通常以米为单位返回距离。

`geo.distance` 的一个参数必须是地理点常量，另一个参数必须是字段路径（如果针对类型为 `Collection(Edm.GeographyPoint)` 的字段进行筛选器迭代，则另一个参数必须是范围变量）。 这些参数的顺序不重要。

地理点常量采用 `geography'POINT(<longitude> <latitude>)'` 形式，其中的经度和纬度为数字常量。

> [!NOTE]
> 如果在筛选器中使用 `geo.distance`，则必须使用 `lt`、`le`、`gt` 或 `ge` 将函数返回的距离与常量进行比较。 比较距离时不支持使用 `eq` 和 `ne` 运算符。 例如，这是 `geo.distance` 的正确用法：`$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`。

### <a name="geointersects"></a>geo.intersects

`geo.intersects` 函数使用类型为 `Edm.GeographyPoint` 的变量并使用常量 `Edm.GeographyPolygon`，在点位于多边形边界内的情况下返回 `Edm.Boolean` -- `true`，否则返回 `false`。

多边形是二维表面，作为定义边界环的一系列点存储（参见以下[示例](#examples)）。 多边形需要处于闭合状态，这意味着第一个点集和最后一个点集必须相同。 [多边形中的点必须采用逆时针顺序](/rest/api/searchservice/supported-data-types#Anchor_1)。

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>跨越 180 度经线的地理空间查询和多边形

对于许多地理空间查询库，构建包含 180 度经线（国际日期变更线附近）的查询要么被禁止，要么需要变通方法，例如将多边形拆分为两个，子午线两侧各一个。

在 Azure 认知搜索中，如果查询形状为矩形并且坐标沿经度和纬度方向与网格布局对齐（例如，`geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`），则包含 180 度经度的地理空间查询将按预期工作。 否则，对于非矩形的形状或未对齐的形状，请考虑使用拆分多边形方法。  

### <a name="geo-spatial-functions-and-null"></a>地理空间函数和 `null`

与 Azure 认知搜索中的所有其他非集合字段一样，类型为 `Edm.GeographyPoint` 的字段可以包含 `null` 值。 当 Azure 认知搜索对 `null` 字段的 `geo.intersects` 求值时，结果始终为 `false`。 在这种情况下，`geo.distance` 的行为取决于上下文：

- 在筛选器中，`null` 字段的 `geo.distance` 会生成 `null`。 这意味着文档不会匹配，因为与任何非 null 值一比较，`null` 的求值结果均为 `false`。
- 使用 **$orderby** 对结果排序时，`null` 字段的 `geo.distance` 会生成最大的可能距离。 包含此类字段的文档在使用排序方向 `asc`（默认）排序时其位置会低于所有其他文档，在方向为 `desc` 时会高于所有其他文档。

## <a name="examples"></a>示例

### <a name="filter-examples"></a>筛选器示例

查找与给定参考点的距离在 10 公里范围内的所有酒店（其中 location 是 `Edm.GeographyPoint` 类型的字段）：

```odata-filter-expr
    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10
```

查找描述为多边形的给定视区内的所有酒店（其中 location 是 `Edm.GeographyPoint` 类型的字段）。 请注意，多边形是闭合的（第一个点集和最后一个点集必须相同），且[这些点必须按逆时针顺序列出](/rest/api/searchservice/supported-data-types#Anchor_1)。

```odata-filter-expr
    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')
```

### <a name="order-by-examples"></a>Order-by 示例

按 `rating` 对酒店进行降序排序，然后按距给定坐标的距离进行升序排序：

```odata-filter-expr
    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

按 `search.score` 和 `rating` 对酒店进行降序排序，然后按距给定坐标的距离进行升序排序，以便在两个具有相同评分的酒店中，将距离最近的酒店列在前面：

```odata-filter-expr
    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

## <a name="next-steps"></a>后续步骤  

- [Azure 认知搜索中的筛选器](search-filters.md)
- [Azure 认知搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [Azure 认知搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档（Azure 认知搜索 REST API）](/rest/api/searchservice/Search-Documents)