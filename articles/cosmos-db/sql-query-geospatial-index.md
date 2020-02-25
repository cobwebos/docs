---
title: 用 Azure Cosmos DB 索引地理空间数据
description: 为空间数据编制索引 Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 2cf682a404154b9c1bb94680b3adb673892c1c72
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566369"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>用 Azure Cosmos DB 索引地理空间数据

我们将 Azure Cosmos DB 的数据库引擎设计为真正的架构不可知，并为 JSON 提供一流的支持。 Azure Cosmos DB 的写入优化数据库引擎在本机上了解以 GeoJSON 标准表示的空间数据。

简单来说，测地坐标的几何图形会投影在 2D 平面上，并使用**四叉树**以渐进方式划分成单元格。 这些单元格会根据 **Hilbert 空间填充曲线**内的单元格位置映射到 1D，并保留点的位置。 此外，当位置数据进行索引编制后，会经历称为“分割”的过程，也就是说，在某个位置上相交的所有单元格都会被识别为键并存储在 Azure Cosmos DB 索引中。 在查询时，点和多边形等参数也会经过分割，以提取相关的格子 ID 范围，并用于从索引检索数据。

如果指定的索引策略包含/* （所有路径）的空间索引，则会为该容器中找到的所有数据编制索引，以便实现有效的空间查询。

> [!NOTE]
> Azure Cosmos DB 支持对点、Linestring、多边形和 MultiPolygons 进行索引
>
>

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
>
>
>

你还可以使用 Azure CLI、PowerShell 或任何 SDK 来[修改索引策略](how-to-manage-indexing-policy.md)。

## <a name="next-steps"></a>后续步骤

已经学会如何开始使用 Azure Cosmos DB 中的地理空间支持，下一步现在可以：

* 深入了解 [Azure Cosmos DB 查询](sql-query-getting-started.md)
* 详细了解如何[通过 Azure Cosmos DB 查询空间数据](sql-query-geospatial-query.md)
* 详细了解[Azure Cosmos DB 中的地理空间和 GeoJSON 位置数据](sql-query-geospatial-intro.md)