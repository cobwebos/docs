---
title: 在 Azure Maps 中扩展 GeoJSON 几何图形 | Microsoft Docs
description: 了解如何在 Azure Maps 中扩展 GeoJSON 几何图形
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: be3c31951c4721a861f9239c5220419dec11b6bf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60799148"
---
# <a name="extending-geojson-geometries"></a>扩展 GeoJSON 几何图形

Azure Maps 提供功能强大的 API 列表，用于在地理功能内搜索/基于地理功能搜索。
这些 API 针对 [GeoJSON 规范][1] 进行标准化，用于表示地理功能（例如：指明边界、路由）。  

[GeoJSON 规范][1] 仅支持以下几何图形：

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Point
* Polygon

某些 Azure Maps Api (例如：[搜索在几何图形](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)) 接受像"Circle"不是几何图形的一部分[GeoJSON 规范][1]。

本文提供了有关 Azure Maps 如何扩展 [GeoJSON 规范][1]来表示某些几何图形的详细说明。

### <a name="circle"></a>圆形

[GeoJSON 规范][1]不支持 `Circle` 几何图形。我们使用 `GeoJSON Feature` 对象表示圆形。

使用 `GeoJSON Feature` 对象表示的 `Circle` 几何图形必须包含以下各项：

1. 中心
   >圆形的中心使用 `GeoJSON Point` 类型表示。

2. 半径
   >圆形的 `radius` 使用 `GeoJSON Feature` 的属性表示。 半径值以米为单位，并且其类型必须为 `double`。

3. 子类型
   >圆形几何图形还必须包含 `subType` 属性。 此属性必须属于 `GeoJSON Feature` 属性的一部分，并且其值应为“Circle”


#### <a name="example"></a>示例

下面是如何将表示在为中心的圆圈 (纬度：47.639754，经度：-122.126986) 等于 100 米，使用一个半径`GeoJSON Feature`对象：

```json            
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}          
```

[1]: https://tools.ietf.org/html/rfc7946
