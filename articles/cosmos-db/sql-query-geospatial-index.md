---
title: 使用 Azure 宇宙数据库索引地理空间数据
description: 使用 Azure 宇宙数据库索引空间数据
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: eb0a2b2778b3217e185b9883def6eaa54674cc5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137897"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>使用 Azure 宇宙数据库索引地理空间数据

我们设计了 Azure Cosmos DB 的数据库引擎，使之真正与架构无关，并为 JSON 提供一流支持。 Azure Cosmos DB 的写入优化数据库引擎本机可理解 GeoJSON 标准中表示的空间数据。

简单来说，测地坐标的几何图形会投影在 2D 平面上，并使用**四叉树**以渐进方式划分成单元格。 这些单元格会根据 **Hilbert 空间填充曲线**内的单元格位置映射到 1D，并保留点的位置。 此外，当位置数据进行索引编制后，会经历称为“分割”**** 的过程，也就是说，在某个位置上相交的所有单元格都会被识别为键并存储在 Azure Cosmos DB 索引中。 在查询时，点和多边形等参数也会经过分割，以提取相关的格子 ID 范围，并用于从索引检索数据。

如果指定包含 /*（所有路径）的空间索引策略，则容器内找到的所有数据都为有效空间查询编制索引。

> [!NOTE]
> Azure Cosmos DB 支持点、线弦、多边形和多多边形的索引
>
>

## <a name="modifying-geospatial-data-type"></a>修改地理空间数据类型

在容器中，`geospatialConfig`指定地理空间数据的索引方式。 应指定每个容器`geospatialConfig`的一个：地理或几何。 如果未指定，将`geospatialConfig`默认为地理数据类型。 修改 时，`geospatialConfig`将重新编制容器中的所有现有地理空间数据。

> [!NOTE]
> Azure Cosmos DB 目前仅支持对 .NET SDK 中的地理空间配置进行修改，该版本为 3.6 及以上版本。
>

下面是一个示例，用于通过设置`geometry``geospatialConfig`属性并添加**边界Box**来修改地理空间数据类型：

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

## <a name="geography-data-indexing-examples"></a>地理数据索引示例

以下 JSON 代码段显示一个索引策略，该策略为**地理**数据类型启用了空间索引。 它对于具有地理数据类型的空间数据有效，并将索引在文档中找到的任何 GeoJSON 点、多边形、多面或 LineString 以进行空间查询。 如果使用 Azure 门户修改索引策略，则可以为索引策略指定以下 JSON 以在容器上启用空间索引：

**具有地理空间索引的容器索引策略 JSON**

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

您还可以使用 Azure CLI、PowerShell 或任何 SDK[修改索引策略](how-to-manage-indexing-policy.md)。

## <a name="geometry-data-indexing-examples"></a>几何数据索引示例

对于与地理数据类型类似的**几何**数据类型，必须指定要索引的相关路径和类型。 此外，还必须在索引策略中指定`boundingBox`， 以指示要为该特定路径编制索引的所需区域。 每个地理空间路径都需要它自己的`boundingBox`。

边界框由以下属性组成：

- **xmin**： 最小索引 x 坐标
- **ymin**： 最小索引 y 坐标
- **xmax**： 最大索引 x 坐标
- **ymax**： 最大索引 y 坐标

需要边界框，因为几何数据占用的平面可以是无限的。 但是，空间索引需要有限的空间。 对于**地理**数据类型，地球是边界，您不需要设置边界框。

应创建一个包含所有（或大部分）数据的边界框。 只有完全在边界框内的对象上计算的操作才能利用空间索引。 不应使边界框明显大于必要值，因为这会对查询性能产生负面影响。

下面是一个索引策略的示例，该策略将**几何数据**索引，地理**空间配置**设置为`geometry`：

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

上述索引策略的**x**坐标为 （-10， 10） 和 y 坐标（-20， 20）。 具有上述索引策略的容器将索引完全位于此区域内的所有点、多边形、多多边形和线字符串。

> [!NOTE]
> 如果尝试将带有**边界框**的索引策略添加到具有`geography`数据类型的容器中，它将失败。 在添加**边界框**`geometry`之前，应修改容器的**地理空间配置**。 在选择容器的地理空间数据类型之前或之后，可以添加数据并修改索引策略的其余部分（如路径和类型）。

## <a name="next-steps"></a>后续步骤

已经学会如何开始使用 Azure Cosmos DB 中的地理空间支持，下一步现在可以：

* 深入了解 [Azure Cosmos DB 查询](sql-query-getting-started.md)
* 了解有关使用[Azure Cosmos DB 查询空间数据](sql-query-geospatial-query.md)
* 了解有关[Azure 宇宙 DB 中的地理空间和地理JSON 位置数据](sql-query-geospatial-intro.md)