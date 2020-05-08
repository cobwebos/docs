---
title: 使用 Azure Cosmos DB 为地理空间数据编制索引
description: 使用 Azure Cosmos DB 为空间数据编制索引
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/03/2020
ms.author: tisande
ms.openlocfilehash: cd96f440c4e8c971d1f1473f667d31e60edef137
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82839193"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>使用 Azure Cosmos DB 为地理空间数据编制索引

我们将 Azure Cosmos DB 的数据库引擎设计为真正与架构无关并为 JSON 提供一流的支持。 Azure Cosmos DB 的写入优化数据库引擎原生了解以 GeoJSON 标准表示的空间数据。

简单来说，测地坐标的几何图形会投影在 2D 平面上，并使用**四叉树**以渐进方式划分成单元格。 这些单元格会根据 **Hilbert 空间填充曲线**内的单元格位置映射到 1D，并保留点的位置。 此外，当位置数据进行索引编制后，会经历称为 **分割**的过程，也就是说，在某个位置上相交的所有单元格都会被识别为键并存储在 Azure Cosmos DB 索引中。 在查询时，点和多边形等参数也会经过分割，以提取相关的格子 ID 范围，并用于从索引检索数据。

如果指定的索引策略包含“/*”（所有路径）的空间索引，则会为容器中找到的所有数据编制索引，以实现高效的空间查询。

> [!NOTE]
> Azure Cosmos DB 支持为 Point、LineString、Polygon 和 MultiPolygon 编制索引
>
>

## <a name="modifying-geospatial-data-type"></a>修改地理空间数据类型

在容器中，**地理空间配置**指定如何为空间数据编制索引。 为每个容器指定一个**地理空间配置**：地理或几何图形。

可以在 Azure 门户中的 "**地域**" 和 "**几何**" 空间类型之间切换。 必须先[使用边界框创建有效的空间几何索引编制策略](#geometry-data-indexing-examples)，然后才能切换到几何空间类型。

下面介绍如何在 Azure 门户中的**数据资源管理器**中设置**地理空间配置**：

![设置地理空间配置](./media/sql-query-geospatial-index/geospatial-configuration.png)

你还可以`geospatialConfig`在 .net SDK 中修改来调整**地理空间配置**：

如果未指定，则 `geospatialConfig` 将默认为 geography 数据类型。 修改 `geospatialConfig` 时，容器中所有现有地理空间数据都将重新编制索引。

下面是一个示例，它通过设置 `geospatialConfig` 属性并添加一个 boundingBox  ，将地理空间数据类型修改为 `geometry`：

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

以下 JSON 代码片段显示了为**地理**数据类型启用空间索引的索引策略。 它适用于地理数据类型的空间数据，并将为文档中找到的任何 GeoJSON 点、多边形、MultiPolygon 或 LineString 编制索引以便进行空间查询。 如果要使用 Azure 门户修改索引策略，可以为索引策略指定以下 JSON，以便对容器启用空间索引：

**具有地理空间索引功能的容器索引策略 JSON**

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

还可以使用 Azure CLI、PowerShell 或任何 SDK [修改索引策略](how-to-manage-indexing-policy.md)。

## <a name="geometry-data-indexing-examples"></a>地理数据索引编制示例

使用 geometry  数据类型时，与 geography 数据类型一样，必须指定用于编制索引的相关路径和类型。 此外，还必须在索引编制策略中指定 `boundingBox`，以指示需要针对该特定路径进行索引编制的区域。 每个地理空间路径都需要其自己的 `boundingBox`。

边界框包括以下属性：

- **xmin**：编制索引的最小 x 坐标
- **ymin**：编制索引的最小 y 坐标
- **xmax**：编制索引的最大 x 坐标
- **ymax**：编制索引的最大 y 坐标

边界框是必需的，因为几何图形数据占有的平面可以是无限的。 但是，空间索引需要有限空间。 对于 geography  数据类型，地球是边界，你无需设置边界框。

创建包含所有（或大部分）数据的边界框。 只有针对完全位于边界框内的对象进行计算的运算才能使用空间索引。 使边界框大于必要会对查询性能产生负面影响。

以下索引编制策略示例在 geospatialConfig  设置为 `geometry` 的情况下为 geometry  数据编制索引：

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

上面的索引编制策略有一个 boundingBox  ，其 x 坐标为 (-10, 10)，y 坐标为 (-20, 20)。 具有上述索引编制策略的容器将为完全在此区域内的所有 Point、Polygon、MultiPolygon 和 LineString 编制索引。

> [!NOTE]
> 如果尝试将具有 boundingBox  的索引编制策略添加到具有 `geography` 数据类型的容器中，则会失败。 在添加 boundingBox  之前，应将容器的 geospatialConfig  修改为 `geometry`。 你可以添加数据并修改索引编制策略的其余部分（例如路径和类型），这可以在为容器选择地理空间数据类型之前或之后进行。

## <a name="next-steps"></a>后续步骤

已经学会如何开始使用 Azure Cosmos DB 中的地理空间支持，下一步现在可以：

* 详细了解 [Azure Cosmos DB 查询](sql-query-getting-started.md)
* 详细了解如何[使用 Azure Cosmos DB 查询空间数据](sql-query-geospatial-query.md)
* 详细了解 [Azure Cosmos DB 中的地理空间数据和 GeoJSON 位置数据](sql-query-geospatial-intro.md)
