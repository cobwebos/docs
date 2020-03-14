---
title: 扩展的 GeoJSON 几何图形 |Microsoft Azure 映射
description: 在本文中，你将了解 Microsoft Azure Map 如何扩展 GeoJSON 规范以表示特定的几何。
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 98db10f0fc7a417f39d4bb00e77af6bdea034a03
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79276395"
---
# <a name="extended-geojson-geometries"></a>扩展的 GeoJSON 几何图形

Azure Maps 提供了一个功能强大的 Api 列表，用于在地理功能内进行搜索。 这些 Api 遵循表示地理功能的标准[GeoJSON 规范][1]。  

[GeoJSON 规范][1]仅支持以下几何图形：

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Point
* Polygon

某些 Azure Maps Api 接受不属于[GeoJSON 规范][1]的几何。例如，[几何图形内的搜索](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)接受圆圈和多边形。

本文详细说明了 Azure Maps 如何扩展[GeoJSON 规范][1]以表示某些几何。

## <a name="circle"></a>圆形

[GeoJSON 规范][1]不支持 `Circle` 几何。我们使用 `GeoJSON Point Feature` 对象来表示一个圆圈。

使用 `GeoJSON Feature` 对象表示的 `Circle` 几何__必须__包含以下坐标和属性：

- 中心

    圆的中心使用 `GeoJSON Point` 对象表示。

- 半径

    圆形的 `radius` 使用 `GeoJSON Feature` 的属性表示。 半径值以米为单位，并且其类型必须为`double`。

- 子类型

    圆形几何图形还必须包含 `subType` 属性。 此属性必须是 `GeoJSON Feature`的属性的一部分，并且其值应为_圆圈_

#### <a name="example"></a>示例

下面介绍如何使用 `GeoJSON Feature` 对象表示圆。 让我们将圆圈置于纬度：47.639754 和经度：-122.126986，并为其分配一个等于100计量的半径：

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

使用 `GeoJSON Polygon Feature` 对象表示的 `Rectangle` 几何__必须__包含以下坐标和属性：

- 内角

    使用 `GeoJSON Polygon` 对象的坐标表示矩形的角。 应该有五个坐标，每个角一个。 与第五个坐标相同，用于关闭多边形环。 假定这些坐标对齐，开发人员可以根据需要对其进行旋转。

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
