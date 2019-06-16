---
title: OData 地理空间函数参考-Azure 搜索
description: OData 地理空间函数，geo.distance 和 geo.intersects，在 Azure 搜索查询中。
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
ms.openlocfilehash: 0ce63ab1143c784eb3e10f47c20ef2b5034d63a7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079790"
---
# <a name="odata-geo-spatial-functions-in-azure-search---geodistance-and-geointersects"></a>在 Azure 搜索中的 OData 地理空间函数`geo.distance`和 `geo.intersects`

Azure 搜索支持中的地理空间查询[OData 筛选器表达式](query-odata-filter-orderby-syntax.md)通过`geo.distance`和`geo.intersects`函数。 `geo.distance`函数两个点之间以公里为单位返回距离，其中一个作为一个字段或范围变量和常量的那个作为筛选器一部分传递。 `geo.intersects`函数返回`true`如果给定的点位于给定的多边形，其中的点是字段或范围变量和多边形指定为常量作为筛选器一部分传递。

`geo.distance`函数也可在[ **$orderby**参数](search-query-odata-orderby.md)通过给定的点的距离的搜索结果进行排序。 **$orderby** 中 `geo.distance` 的语法与其在 **$filter** 中的语法相同。 使用时`geo.distance`中 **$orderby**，它所应用于此字段必须是类型`Edm.GeographyPoint`并且它必须是**可排序**。

## <a name="syntax"></a>语法

以下 EBNF ([扩展巴科斯-诺尔范式](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) 定义的语法`geo.distance`和`geo.intersects`函数，以及在其上运行的地理空间值：

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

交互式语法关系图也是可用的：

> [!div class="nextstepaction"]
> [Azure 搜索的 OData 语法关系图](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> 请参阅[适用于 Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)的完整 EBNF。

### <a name="geodistance"></a>geo.distance

`geo.distance`函数采用两个参数的类型`Edm.GeographyPoint`，并返回`Edm.Double`是它们以公里为单位之间的距离的值。 这不同于其他支持 OData 地理空间操作，通常以米为单位返回距离的服务。

参数之一`geo.distance`都必须是地理位置点常量和其他都必须是字段路径 (或在筛选器的情况下的某个范围变量循环访问类型的字段`Collection(Edm.GeographyPoint)`)。 这些参数的顺序并不重要。

地理位置点常量是窗体的`geography'POINT(<longitude> <latitude>)'`，其中的经度和纬度是数值常量。

> [!NOTE]
> 使用时`geo.distance`必须在筛选器中，比较常量使用函数返回的距离`lt`， `le`， `gt`，或`ge`。 比较距离时不支持使用 `eq` 和 `ne` 运算符。 例如，这是正确用法`geo.distance`: `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`。

### <a name="geointersects"></a>geo.intersects

`geo.intersects`函数采用一个类型的变量`Edm.GeographyPoint`和常量`Edm.GeographyPolygon`，并返回`Edm.Boolean`  --  `true`如果点是该多边形的边界内`false`否则为。

多边形是存储为一系列点定义边界环的二维表面 (请参阅[示例](#examples)下面)。 多边形需要处于闭合状态，这意味着第一个点集和最后一个点集必须相同。 [多边形中的点必须采用逆时针顺序](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)。

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>地理空间查询和多边形跨越 180th 经线

对于许多地理空间查询表述查询包括 180th 经线 （附近国际日期变更线） 的库是仍受限或需要一种解决方法，如将多边形拆分为两个，其中一个两侧的经线。

Azure 搜索中包括 180 度经度的地理空间查询将正常工作，如果查询形状为矩形，并且你的坐标对齐到网格布局沿经度和纬度 (例如， `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`)。 否则，对于非矩形的形状或未对齐的形状，请考虑使用拆分多边形方法。  

### <a name="geo-spatial-functions-and-null"></a>地理空间函数和 `null`

像在 Azure 搜索中，类型的字段的所有其他非集合字段`Edm.GeographyPoint`可以包含`null`值。 当 Azure 搜索的计算结果`geo.intersects`的字段，则为`null`，结果将始终是`false`。 行为`geo.distance`这种情况下取决于上下文：

- 在筛选器，`geo.distance`的`null`字段中的结果`null`。 这意味着文档将不匹配，因为`null`比较为任何非 null 的值的计算结果为`false`。
- 当对使用结果进行排序 **$orderby**，`geo.distance`的`null`字段中的最大可能的距离的结果。 具有此类字段的文档将低于其他所有人进行排序时的排序方向`asc`是使用 （默认值），和更高版本比时的方向是所有其他`desc`。

## <a name="examples"></a>示例

### <a name="filter-examples"></a>筛选器示例

找到的与给定的参照点相距 10 公里以内的所有酒店 (其中 location 是类型的`Edm.GeographyPoint`):

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

查找描述为多边形的给定视区中的所有酒店 (其中 location 是类型的`Edm.GeographyPoint`)。 请注意，多边形是闭合的（第一个点集和最后一个点集必须相同），且[这些点必须按逆时针顺序列出](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)。

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Order-by 示例

对酒店按降序进行排序`rating`，然后按升序距离从给定的坐标：

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

排序降序排序依据的酒店`search.score`和`rating`，然后在升序按距离从给定的坐标以便之间具有相同级别的两个酒店，最近的一个首先列出的是：

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>后续步骤  

- [Azure 搜索中的筛选器](search-filters.md)
- [Azure 搜索的 OData 表达式语言概述](query-odata-filter-orderby-syntax.md)
- [Azure 搜索的 OData 表达式语法参考](search-query-odata-syntax-reference.md)
- [搜索文档（Azure 搜索服务 REST API）](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
