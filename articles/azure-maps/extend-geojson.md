---
title: 扩展的 GeoJSON 几何体 |微软 Azure 地图
description: 在本文中，您将了解 Microsoft Azure 映射如何扩展 GeoJSON 规范以表示某些几何体。
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 98db10f0fc7a417f39d4bb00e77af6bdea034a03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276395"
---
# <a name="extended-geojson-geometries"></a>扩展的 GeoJSON 几何体

Azure 地图提供了强大的 API 列表，用于在地理要素内部和沿线进行搜索。 这些 API 符合表示地理特征的标准[GeoJSON 规范][1]。  

[GeoJSON 规范][1]仅支持以下几何体：

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Point
* Polygon

某些 Azure 地图 API 接受不属于[GeoJSON 规范][1]的几何图形。例如，[搜索几何体](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)内部 API 接受圆形和多边形。

本文提供了有关 Azure Maps 如何扩展 [GeoJSON 规范][1]来表示某些几何图形的详细说明。

## <a name="circle"></a>圆形

`Circle` [GeoJSON 规范][1]不支持几何体。我们使用对象`GeoJSON Point Feature`来表示一个圆。

使用`Circle`对象表示的`GeoJSON Feature`几何必须包含__must__以下坐标和属性：

- 中心

    圆的中心使用`GeoJSON Point`对象表示。

- 半径

    圆形的 `radius` 使用 `GeoJSON Feature` 的属性表示。 半径值以米为单位，并且其类型必须为 `double`__。

- 子类型

    圆形几何图形还必须包含 `subType` 属性。 此属性必须是 属性的一部分，`GeoJSON Feature`其值应为_Circle_

#### <a name="example"></a>示例

以下是如何使用`GeoJSON Feature`对象表示圆的方式。 让我们在纬度处围绕圆：47.639754和经度：-122.126986，并指定半径等于100米：

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

`Rectangle` [GeoJSON 规范][1]不支持几何体。我们使用对象`GeoJSON Polygon Feature`来表示矩形。 矩形扩展主要用于 Web SDK 的绘图工具模块。

使用`Rectangle`对象表示的`GeoJSON Polygon Feature`几何必须包含__must__以下坐标和属性：

- 角落

    矩形的角使用`GeoJSON Polygon`对象的坐标表示。 应该有五个坐标，每个角一个。 并且，第五个坐标与第一个坐标相同，以关闭多边形环。 假定这些坐标对齐，并且开发人员可以根据需要旋转它们。

- 子类型

    矩形几何图形还必须包含该`subType`属性。 此属性必须是`GeoJSON Feature`属性的一部分，其值应为_矩形_

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

在 Azure 地图中了解有关 GeoJSON 数据的更多内容：

> [!div class="nextstepaction"]
> [地理围栏地理JSON格式](geofence-geojson.md)

查看与 Azure 地图和位置智能应用程序关联的常见技术术语的词汇表：

> [!div class="nextstepaction"]
> [Azure 地图词汇表](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946
