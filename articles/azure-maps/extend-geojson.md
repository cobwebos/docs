---
title: Azure Maps 中的扩展的 GeoJSON 几何 |Microsoft Docs
description: 了解如何在 Azure Maps 中扩展 GeoJSON 几何图形
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 52325248d21a5d5112c9a7f9497c3e03fdf102a4
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881986"
---
# <a name="extended-geojson-geometries"></a>扩展的 GeoJSON 几何图形

Azure Maps 提供功能强大的 API 列表，用于在地理功能内搜索/基于地理功能搜索。
这些 Api 标准化了表示地理功能的[GeoJSON 规范][1](例如: 状态边界、路由)。  

[GeoJSON 规范][1]仅支持以下几何图形:

* GeometryCollection
* LineString
* MultiLineString
* MultiPoint
* MultiPolygon
* Point
* Polygon

某些 Azure Maps Api (例如:[在几何图形内搜索](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry))接受不属于[GeoJSON 规范][1]的几何, 如 "圆圈"。

本文详细说明了 Azure Maps 如何扩展[GeoJSON 规范][1]以表示某些几何。

## <a name="circle"></a>圆圈

`Circle` [GeoJSON 规范][1]不支持该几何图形。我们使用`GeoJSON Point Feature`对象来表示圆。

使用 `GeoJSON Feature` 对象表示的 `Circle` 几何图形必须包含以下各项：

- 居中

    圆的中心使用`GeoJSON Point`对象表示。

- 半径

    圆形的 `radius` 使用 `GeoJSON Feature` 的属性表示。 半径值以米为单位，并且其类型必须为 `double`。

- 子类型

    圆形几何图形还必须包含 `subType` 属性。 此属性必须是的属性的一部分`GeoJSON Feature`, 并且其值应为_圆圈_

#### <a name="example"></a>示例

下面介绍如何表示中心 (纬度:47.639754, 经度:-122.126986), 其 radius 等于100米, 使用`GeoJSON Feature`对象:

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

## <a name="rectangle"></a>矩形

`Rectangle` [GeoJSON 规范][1]不支持该几何图形。我们使用`GeoJSON Polygon Feature`对象来表示矩形。 矩形扩展主要由 Web SDK 的 "绘图工具" 模块使用。

使用 `GeoJSON Polygon Feature` 对象表示的 `Rectangle` 几何图形必须包含以下各项：

- 内角

    使用`GeoJSON Polygon`对象的坐标表示矩形的角。 应该有五个坐标, 每个角有一个坐标, 第五个坐标与第1个坐标相同, 用于关闭多边形环。 这些坐标将假定为开发人员所需的对齐和旋转。

- SubType

    矩形几何图形还必须包含`subType`属性。 此属性必须是的属性的一部分`GeoJSON Feature`, 并且其值应为_长方形_

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

详细了解 Azure Maps 中的 GeoJSON 数据:

> [!div class="nextstepaction"]
> [地域隔离区内 GeoJSON 格式](geofence-geojson.md)

查看与 Azure Maps 和位置智能应用程序相关联的常见技术术语的术语表:

> [!div class="nextstepaction"]
> [Azure Maps 术语表](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946
