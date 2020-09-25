---
title: Azure Cosmos DB 中的地理空间和 GeoJSON 位置数据
description: 了解如何使用 Azure Cosmos DB 和 SQL API 创建空间对象。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: ee88b980c448bfbf581537aef4653fde5354623a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91302925"
---
# <a name="geospatial-and-geojson-location-data-in-azure-cosmos-db"></a>Azure Cosmos DB 中的地理空间和 GeoJSON 位置数据

本文介绍 Azure Cosmos DB 中的地理空间功能。 目前，只有 Azure Cosmos DB SQL API 帐户支持存储和访问地理空间数据。 阅读有关地理空间索引的文档后，你将能够回答以下问题：

* 如何在 Azure Cosmos DB 中存储空间数据？
* 如何使用 SQL 和 LINQ 查询 Azure Cosmos DB 中的地理空间数据？
* 如何在Azure Cosmos DB 中启用或禁用空间索引？

## <a name="spatial-data-use-cases"></a>空间数据用例

地理空间数据通常涉及邻近查询，例如“寻找我目前位置附近的所有咖啡厅”。 常见用例包括：

* 地理位置分析，用于推动特定位置市场营销计划。
* 基于位置的个性化，适用于零售和医疗保健等多个行业。
* 物流增强，用于优化运输。
* 风险分析，特别适用于保险和金融公司。
* 态势感知，用于发出警报和通知。

## <a name="introduction-to-spatial-data"></a>空间数据简介

空间数据用于描述空间中对象的位置和形状。 在大部分应用程序中，这些会对应于地球上的对象，也就是地理空间数据。 空间数据可以用来表示人、名胜古迹、城市边界或湖泊所处的位置。

Azure Cosmos DB SQL API 支持两种空间数据类型：geometry  数据类型和 geography 数据类型  。

- geometry  类型在欧几里得（平面）坐标系统中表示数据
- **Geography** 类型表示圆形地球坐标系中的数据。

## <a name="supported-data-types"></a>支持的数据类型

Azure Cosmos DB 支持对使用 [GeoJSON 规范](https://tools.ietf.org/html/rfc7946)表示的地理空间点数据进行索引编制和查询。 GeoJSON 数据结构永远是有效的 JSON 对象，因此可以通过 Azure Cosmos DB 来存储和查询，无需任何专用的工具或库。

Azure Cosmos DB 支持以下空间数据类型：

- Point
- LineString
- Polygon
- MultiPolygon

### <a name="points"></a>Point（点）

**点** 代表空间中的单一位置。 在地理空间数据中，某个点所代表的确切位置可能是杂货店、电话亭、汽车或城市的街道地址。  点使用其坐标对或经纬度，以 GeoJSON 格式（和 Azure Cosmos DB）表示。

以下是点的 JSON 示例：

Azure Cosmos DB 中的点 

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

如下面包含位置数据的用户配置文件示例所示，空间数据类型可以嵌入在 Azure Cosmos DB 文档中：

存储在 Azure Cosmos DB 中包含位置的用户配置文件 

```json
{
    "id":"cosmosdb-profile",
    "screen_name":"@CosmosDB",
    "city":"Redmond",
    "topics":[ "global", "distributed" ],
    "location":{
        "type":"Point",
        "coordinates":[ 31.9, -4.8 ]
    }
}
```

### <a name="points-in-a-geometry-coordinate-system"></a>几何坐标系统中的点

对于几何  数据类型，GeoJSON 规范首先指定水平轴，然后指定垂直轴。

### <a name="points-in-a-geography-coordinate-system"></a>地理坐标系统中的点

对于 **geography** 数据类型，GeoJSON 规范先指定经度，再指定纬度。 与其他地图应用程序一样，经度和纬度为角度，并以度为单位表示。 经度值从本初子午线测量，并介于 -180 度和 180.0 度之间；纬度值从赤道测量，并介于 -90.0 度和 90.0 度之间。

Azure Cosmos DB 会将坐标解释为按照 WGS-84 参考系统表示。 有关坐标参考系统的更多详细信息，请参阅下文。

### <a name="linestrings"></a>LineString

**LineString** 表示空间中一连串的点（两个或更多个）以及连接这些点的线段。 在地理空间数据中，LineString 通常用来表示高速公路或河流。

**GeoJSON 中的 LineString**

```json
    "type":"LineString",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ]
    ] ]
```

### <a name="polygons"></a>Polygon（多边形）

**多边形** 是形成闭合的 LineString 的相连接的点的边界。 多边形通常用来表示自然构成物（例如湖泊），或表示政治管辖权（例如省/市/自治区）。 下面是 Azure Cosmos DB 中的一个多边形示例：

GeoJSON 中的多边形 

```json
{
    "type":"Polygon",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ],
        [ 32, -4.7 ],
        [ 32, -5 ],
        [ 31.8, -5 ]
    ] ]
}
```

> [!NOTE]
> GeoJSON 规范需要此数据才能形成有效的多边形；若要创建一个闭合的形状，最后一个坐标对应该与第一个坐标对相同。
>
> 多边形内的点必须以逆时针顺序指定。 以顺时针顺序指定的多边形表示其中的区域倒转。
>
>

### <a name="multipolygons"></a>MultiPolygon

**MultiPolygon** 是包含零个或多个多边形的数组。 各个 **MultiPolygon** 的边不能重叠，也不能具有任何公共区域。 它们的一个或多个点可以接触。

**GeoJSON 中的 MultiPolygon**

```json
{
    "type":"MultiPolygon",
    "coordinates":[[[
        [52.0, 12.0],
        [53.0, 12.0],
        [53.0, 13.0],
        [52.0, 13.0],
        [52.0, 12.0]
        ]],
        [[
        [50.0, 0.0],
        [51.0, 0.0],
        [51.0, 5.0],
        [50.0, 5.0],
        [50.0, 0.0]
        ]]]
}
```

## <a name="coordinate-reference-systems"></a>坐标参考系统

由于地球的形状不规则，地理空间数据的坐标可以用许多坐标参考系统 (CRS) 来表示，而这些系统各有自己的参考框架和测量单位。 例如，“英国国家网格”对英国而言是非常精确的参考系统，但对其他地区则不是。

现今最常使用的 CRS 是世界测地系统 [WGS-84](https://earth-info.nga.mil/GandG/update/index.php)。 GPS 设备和许多地图服务（包括谷歌地图和必应地图 API）均使用 WGS-84。 Azure Cosmos DB 仅支持对使用 WGS-84 CRS 的地理空间数据进行索引编制和查询。

## <a name="creating-documents-with-spatial-data"></a>创建包含空间数据的文档
创建包含 GeoJSON 值的文档时，值会根据容器的索引策略，自动以空间索引进行索引编制。 如果以动态类型化语言（如 Python 或 Node.js）使用 Azure Cosmos DB SDK，则必须创建有效的 GeoJSON。

**在 Node.js 中创建包含地理空间数据的文档**

```javascript
var userProfileDocument = {
    "id":"cosmosdb",
    "location":{
        "type":"Point",
        "coordinates":[ -122.12, 47.66 ]
    }
};

client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
    // additional code within the callback
});
```

如果使用 SQL API，则可以在 `Microsoft.Azure.Cosmos.Spatial` 命名空间中使用 `Point`、`LineString`、`Polygon` 和 `MultiPolygon` 类，将位置信息嵌入应用程序对象中。 这些类有助于简化将空间数据序列化和反序列化为 GeoJSON 的过程。

**在 .NET 中创建包含地理空间数据的文档**

```csharp
using Microsoft.Azure.Cosmos.Spatial;

public class UserProfile
{
    [JsonProperty("id")]
    public string id { get; set; }

    [JsonProperty("location")]
    public Point Location { get; set; }

    // More properties
}

await container.CreateItemAsync( new UserProfile
    {
        id = "cosmosdb",
        Location = new Point (-122.12, 47.66)
    });
```

如果没有经纬度信息，但有物理地址或位置名称，如城市或国家/地区，则可以使用必应地图 REST 服务等地理编码服务来查找实际的坐标。 在[此处](https://msdn.microsoft.com/library/ff701713.aspx)详细了解必应地图地理编码。

## <a name="next-steps"></a>后续步骤

已经学会如何开始使用 Azure Cosmos DB 中的地理空间支持，下一步现在可以：

* 详细了解 [Azure Cosmos DB 查询](sql-query-getting-started.md)
* 详细了解如何[使用 Azure Cosmos DB 查询空间数据](sql-query-geospatial-query.md)
* 详细了解如何[使用 Azure Cosmos DB 为空间数据编制索引](sql-query-geospatial-index.md)
