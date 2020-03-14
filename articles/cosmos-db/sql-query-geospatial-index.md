---
title: 用 Azure Cosmos DB 索引地理空间数据
description: 为空间数据编制索引 Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: eb0a2b2778b3217e185b9883def6eaa54674cc5b
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137897"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>用 Azure Cosmos DB 索引地理空间数据

我们将 Azure Cosmos DB 的数据库引擎设计为真正的架构不可知，并为 JSON 提供一流的支持。 Azure Cosmos DB 的写入优化数据库引擎在本机上了解以 GeoJSON 标准表示的空间数据。

简单来说，测地坐标的几何图形会投影在 2D 平面上，并使用**四叉树**以渐进方式划分成单元格。 这些单元格会根据 **Hilbert 空间填充曲线**内的单元格位置映射到 1D，并保留点的位置。 此外，当位置数据进行索引编制后，会经历称为“分割”的过程，也就是说，在某个位置上相交的所有单元格都会被识别为键并存储在 Azure Cosmos DB 索引中。 在查询时，点和多边形等参数也会经过分割，以提取相关的格子 ID 范围，并用于从索引检索数据。

如果指定的索引策略包含/* （所有路径）的空间索引，则会为该容器中找到的所有数据编制索引，以便实现有效的空间查询。

> [!NOTE]
> Azure Cosmos DB 支持对点、Linestring、多边形和 MultiPolygons 进行索引
>
>

## <a name="modifying-geospatial-data-type"></a>修改地理空间数据类型

在容器中，`geospatialConfig` 指定如何索引地理空间数据。 应为每个容器指定一个 `geospatialConfig`：地理或几何图形。 如果未指定，则 `geospatialConfig` 将默认为 geography 数据类型。 修改 `geospatialConfig`后，容器中的所有现有地理空间数据将重新编制索引。

> [!NOTE]
> Azure Cosmos DB 当前仅支持在3.6 和更高版本中修改 .NET SDK 中的 geospatialConfig。
>

下面是通过设置 `geospatialConfig` 属性并添加**boundingBox**将地理空间数据类型修改为 `geometry` 的示例：

```csharp
    //Retrieve the container's details
    ContainerResponse containerResponse = await client.GetContainer("db", "spatial").ReadContainerAsync();
    //Set GeospatialConfig to Geometry
    GeospatialConfig geospatialConfig = new GeospatialConfig(GeospatialType.Geometry);
    containerResponse.Resource.GeospatialConfig = geospatialConfig;
    // Add a spatial index including the required boundingBox
    SpatialPath spatialPath = new SpatialPath
            {  
                Path = "/locations/*",
                BoundingBox = new BoundingBoxProperties(){
                    Xmin = 0,
                    Ymin = 0,
                    Xmax = 10,
                    Ymax = 10
                }
            };
    spatialPath.SpatialTypes.Add(SpatialType.Point);
    spatialPath.SpatialTypes.Add(SpatialType.LineString);
    spatialPath.SpatialTypes.Add(SpatialType.Polygon);
    spatialPath.SpatialTypes.Add(SpatialType.MultiPolygon);

    containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);

    // Update container with changes
    await client.GetContainer("db", "spatial").ReplaceContainerAsync(containerResponse.Resource);
```

## <a name="geography-data-indexing-examples"></a>地域数据索引示例

下面的 JSON 代码段显示了为**geography**数据类型启用空间索引的索引策略。 它适用于地理数据类型的空间数据，并将为空间查询在文档中找到的所有 GeoJSON 点、多边形、MultiPolygon 或 LineString 编制索引。 如果要使用 Azure 门户修改索引策略，可以为索引策略指定以下 JSON，以便在容器上启用空间索引：

**容器索引策略 JSON 与地理空间索引**

```json
    {
       "automatic":true,
       "indexingMode":"Consistent",
        "includedPaths": [
        {
            "path": "/*"
        }
        ],
        "spatialIndexes": [
        {
            "path": "/*",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ],
       "excludedPaths":[]
    }
```

> [!NOTE]
> 如果文档中的 GeoJSON 位置值格式不正确或无效，则不会为其编制索引以用于空间查询。 可以使用 ST_ISVALID 和 ST_ISVALIDDETAILED 验证位置值。

你还可以使用 Azure CLI、PowerShell 或任何 SDK 来[修改索引策略](how-to-manage-indexing-policy.md)。

## <a name="geometry-data-indexing-examples"></a>Geometry 数据索引示例

对于**geometry**数据类型，与 geography 数据类型类似，您必须指定要建立索引的相关路径和类型。 此外，还必须指定索引策略中的 `boundingBox`，以指示要为该特定路径编制索引的所需区域。 每个地理空间路径都需要自己的`boundingBox`。

边界框由以下属性组成：

- **xmin**：最小索引 x 坐标
- **ymin**：最低索引的 y 坐标
- **xmax**：最大索引 x 坐标
- **ymax**：最大索引的 y 坐标

边界框是必需的，因为几何数据占据一个可能是无限的平面。 但是，空间索引需要有限空间。 对于**geography**数据类型，地球是边界，无需设置边界框。

应创建包含所有（或大部分）数据的边界框。 只有在完全位于边界框内部的对象上计算的操作才能利用空间索引。 不应使边界框明显大于必要，因为这会对查询性能产生负面影响。

下面是索引策略的示例，它使用**geospatialConfig**设置为 `geometry`来索引**几何**数据：

```json
 {
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/\"_etag\"/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/locations/*",
            "types": [
                "Point",
                "LineString",
                "Polygon",
                "MultiPolygon"
            ],
            "boundingBox": {
                "xmin": -10,
                "ymin": -20,
                "xmax": 10,
                "ymax": 20
            }
        }
    ]
}
```

上述索引策略的 x 坐标为（-10，10） **boundingBox** ，y 坐标为（-20，20）。 带有以上索引策略的容器将为完全在该区域内的所有点、多边形、MultiPolygons 和 Linestring 编制索引。

> [!NOTE]
> 如果尝试将具有**boundingBox**的索引策略添加到 `geography` 数据类型的容器，则会失败。 添加**boundingBox**前，应修改要 `geometry` 的容器**geospatialConfig** 。 在选择容器的地理空间数据类型之前或之后，你可以添加数据并修改索引策略的其余部分（如路径和类型）。

## <a name="next-steps"></a>后续步骤

已经学会如何开始使用 Azure Cosmos DB 中的地理空间支持，下一步现在可以：

* 深入了解 [Azure Cosmos DB 查询](sql-query-getting-started.md)
* 详细了解如何[通过 Azure Cosmos DB 查询空间数据](sql-query-geospatial-query.md)
* 详细了解[Azure Cosmos DB 中的地理空间和 GeoJSON 位置数据](sql-query-geospatial-intro.md)