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
ms.openlocfilehash: 319f9cba23d088553f361b6a0d648bbde94e0743
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968555"
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

某些 Azure Maps API（例如：[在几何图形内搜索](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)）接受“圆形”（不属于 [GeoJSON 规范][1]）等几何图形。

本文提供了有关 Azure Maps 如何扩展 [GeoJSON 规范][1]来表示某些几何图形的详细说明。

### <a name="circle"></a>圆形

[GeoJSON 规范][1]不支持 `Circle` 几何图形。我们使用 `GeoJSON Feature` 对象表示圆形。

使用 `GeoJSON Feature` 对象表示的 `Circle` 几何图形__必须__包含以下各项：

1. 中心
   >圆形的中心使用 `GeoJSON Point` 类型表示。

2. 半径
   >圆形的 `radius` 使用 `GeoJSON Feature` 的属性表示。 半径值_以米为单位_且其类型必须为 `double`。

3. 子类型
   >圆形几何图形还必须包含 `subType` 属性。 此属性必须属于 `GeoJSON Feature` 的属性且其值应为_圆形_


#### <a name="example"></a>示例

下面介绍了如何使用 `GeoJSON Feature` 对象表示以（纬度：47.639754，经度：-122.126986）为中心、半径为 100 米的圆形：

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
