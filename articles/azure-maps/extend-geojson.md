---
title: 扩展的 GeoJSON 几何图形 |Microsoft Azure 映射
description: 在本文中，你将了解 Microsoft Azure Map 如何扩展 GeoJSON 规范以表示特定几何。
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 75ad83411edfdfe7545e8f80df17fea56e317ee0
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911640"
---
# <a name="extended-geojson-geometries"></a>扩展的 GeoJSON 几何图形

Azure Maps 提供功能强大的 API 列表，用于在地理功能内搜索/基于地理功能搜索。
这些 Api 标准化了表示地理功能的[GeoJSON 规范][1]（例如：状态边界、路由）。  

[GeoJSON 规范][1]仅支持以下几何图形：

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Point
* Polygon

某些 Azure Maps Api （例如：[在几何图形内搜索](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)）接受不属于[GeoJSON 规范][1]的几何图形，如 "圆圈"。

本文详细说明了 Azure Maps 如何扩展[GeoJSON 规范][1]以表示某些几何。

## <a name="circle"></a>圆形

[GeoJSON 规范][1]不支持 `Circle` 几何。我们使用 `GeoJSON Point Feature` 对象来表示一个圆圈。

使用 `GeoJSON Feature` 对象表示的 `Circle` 几何图形必须包含以下各项：

- 中心

    圆的中心使用 `GeoJSON Point` 对象表示。

- 半径

    圆形的 `radius` 使用 `GeoJSON Feature` 的属性表示。 半径值以米为单位，并且其类型必须为 `double`。

- 子类型

    圆形几何图形还必须包含 `subType` 属性。 此属性必须是 `GeoJSON Feature`的属性的一部分，并且其值应为_圆圈_

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

## <a name="rectangle"></a>Rectangle

[GeoJSON 规范][1]不支持 `Rectangle` 几何。我们使用 `GeoJSON Polygon Feature` 对象来表示一个矩形。 矩形扩展主要由 Web SDK 的 "绘图工具" 模块使用。

使用 `GeoJSON Polygon Feature` 对象表示的 `Rectangle` 几何图形必须包含以下各项：

- 内角

    使用 `GeoJSON Polygon` 对象的坐标表示矩形的角。 应该有五个坐标，每个角有一个坐标，第五个坐标与第1个坐标相同，用于关闭多边形环。 这些坐标将假定为开发人员所需的对齐和旋转。

- 子类型

    矩形几何还必须包含 `subType` 属性。 此属性必须是 `GeoJSON Feature`的属性的一部分，并且其值应为_长方形_

### <a name="example"></a>示例

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Polygon",
        "coordinates": [[[5,25],[14,25],[14,29],[5,29],[5,25]]]
    },
    "properties": {
        "subType": "Rectangle"
    }
}

```
## <a name="next-steps"></a>后续步骤

详细了解 Azure Maps 中的 GeoJSON 数据：

> [!div class="nextstepaction"]
> [地域隔离区内 GeoJSON 格式](geofence-geojson.md)

查看与 Azure Maps 和位置智能应用程序相关联的常见技术术语的术语表：

> [!div class="nextstepaction"]
> [Azure Maps 术语表](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946
