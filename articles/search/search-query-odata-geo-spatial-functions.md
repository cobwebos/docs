---
title: OData 地理空间函数引用-Azure 搜索
description: Azure 搜索查询中的 OData 地理空间函数、异地和地理。
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
ms.openlocfilehash: 9585a9a7ea976ed32ccb8eed1e69877339196f87
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647567"
---
# <a name="odata-geo-spatial-functions-in-azure-search---geodistance-and-geointersects"></a>Azure 搜索中的 OData 地理空间函数- `geo.distance`和`geo.intersects`

Azure 搜索通过`geo.distance`和`geo.intersects`函数支持[OData 筛选表达式](query-odata-filter-orderby-syntax.md)中的地理空间查询。 `geo.distance`函数返回两个点之间的距离 (一是字段或范围变量), 另一个是作为筛选器一部分传递的常量。 如果给定点`true`在给定多边形内, 则该函数返回,其中点为字段或范围变量,多边形指定为作为筛选器一部分传递的常量。`geo.intersects`

还可以在[ **$orderby**参数](search-query-odata-orderby.md)中使用函数,以便按与给定点之间的距离对搜索结果进行排序。`geo.distance` **$orderby** 中 `geo.distance` 的语法与其在 **$filter** 中的语法相同。 在`geo.distance` **$orderby**中使用时, 它应用到的字段必须为类型`Edm.GeographyPoint` , 并且还必须可**排序**。

## <a name="syntax"></a>语法

以下 EBNF ([扩展的巴科斯-诺尔范式窗体](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定义`geo.distance`和`geo.intersects`函数的语法, 以及它们运行时所在的地理空间值:

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

还提供交互式语法关系图:

> [!div class="nextstepaction"]
> [适用于 Azure 搜索的 OData 语法关系图](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> 有关完整的 EBNF, 请参阅[Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)。

### <a name="geodistance"></a>地域

函数采用类型`Edm.GeographyPoint`为的两个参数, 并`Edm.Double`返回一个值, 该值表示二者之间的距离。 `geo.distance` 这不同于其他支持 OData 地理空间操作的服务, 后者通常以米为单位返回距离。

要`geo.distance`使用的参数之一必须是地理点常量, 另一个参数必须是字段路径 (如果筛选器在类型`Collection(Edm.GeographyPoint)`为的字段, 则为范围变量)。 这些参数的顺序并不重要。

地理点常量的形式`geography'POINT(<longitude> <latitude>)'`为, 其中, 经度和纬度为数值常量。

> [!NOTE]
> 在筛选`geo.distance`器中使用时, 必须使用`lt`、 `le`、 `gt`或`ge`将函数返回的距离与常量进行比较。 比较距离时不支持使用 `eq` 和 `ne` 运算符。 例如, 以下是的正确用法`geo.distance`:。 `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`

### <a name="geointersects"></a>地域交集

`Edm.GeographyPolygon` `false` `Edm.Boolean`  -- 函数采用类型`Edm.GeographyPoint`和常数的变量, `true`如果该点在多边形的边界内, 则返回; 否则返回。 `geo.intersects`

多边形是作为一系列定义边界环的点 (请参阅下面的[示例](#examples)) 存储的二维表面。 多边形需要处于闭合状态，这意味着第一个点集和最后一个点集必须相同。 [多边形中的点必须采用逆时针顺序](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)。

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>跨180th 经线的地理空间查询和多边形

对于许多地理空间查询库, 会构建一个包含180th 经线 (接近 dateline) 的查询, 该查询可以是脱离限制的, 也可以是需要解决的问题, 例如在经线的任意一侧将多边形拆分为两个。

在 Azure 搜索中, 包含180度的地理空间查询将按预期方式工作 (如果查询形状是矩形, 并且坐标沿纬度和纬度对齐网格布局`geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`) (例如)。 否则，对于非矩形的形状或未对齐的形状，请考虑使用拆分多边形方法。  

### <a name="geo-spatial-functions-and-null"></a>地理空间函数和`null`

与 Azure 搜索中的所有其他非集合字段一样, 类型`Edm.GeographyPoint`的字段可以包含`null`值。 当 Azure 搜索对`geo.intersects`的字段`null`求值时, 结果将始终为`false`。 `geo.distance`在这种情况下, 的行为取决于上下文:

- 在筛选器`geo.distance`中, `null`字段的结果`null`为。 这意味着文档将不匹配, 因为`null`与任何非 null 值相比, 计算结果`false`为。
- 使用 **$orderby**对`geo.distance` `null`结果进行排序时, 字段会产生最大的可用距离。 当使用排序方向`asc` (默认值) 时, 具有此类字段的文档的排序将低于所有其他字段, 而当方向是`desc`时, 将比其他字段更高。

## <a name="examples"></a>示例

### <a name="filter-examples"></a>筛选器示例

查找给定引用点的10公里内的所有酒店 (其中位置是类型`Edm.GeographyPoint`的字段):

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

查找给定视区中被描述为多边形的所有酒店 (其中位置是类型`Edm.GeographyPoint`的字段)。 请注意，多边形是闭合的（第一个点集和最后一个点集必须相同），且[这些点必须按逆时针顺序列出](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)。

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Order-by 示例

按降序`rating`对酒店进行排序, 然后按与给定坐标的距离升序排序:

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

按`search.score` 和`rating`, 然后按降序对酒店进行排序, 然后按与给定坐标的距离进行升序排序, 以便在两个具有相同分级的酒店之间进行排序, 最接近的一家酒店首先列出:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>后续步骤  

- [Azure 搜索中的筛选器](search-filters.md)
- [Azure 搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [适用于 Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
