---
title: Azure Cosmos DB 中的索引
description: 了解 Azure Cosmos DB 中索引的工作原理。
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: thweiss
ms.openlocfilehash: 633d0f619132ee93951cfe0dc329a7514a38ef57
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66240742"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Azure Cosmos DB 中的索引 - 概述

Azure Cosmos DB 是一种架构不可知的数据库，使你能够迭代应用程序，而无需处理架构或索引管理。 默认情况下，Azure Cosmos DB 将自动为[容器](databases-containers-items.md#azure-cosmos-containers)中所有项的每个属性编制索引，而无需定义任何架构或配置辅助索引。

本文旨在说明 Azure Cosmos DB 如何为数据编制索引，以及它如何使用索引来提高查询性能。 在探索如何自定义[索引策略](index-policy.md)之前，建议先阅读本部分。

## <a name="from-items-to-trees"></a>从项到树

每当在容器中存储某个项时，该项的内容将投影为 JSON 文档，然后转换为树表示形式。 这意味着，该项的每个属性表示为树中的一个节点。 系统会创建一个伪根节点，作为该项的所有第一级属性的父级。 叶节点包含项携带的实际标量值。

例如，假设存在以下项：

    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 },
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }

该项可由以下树表示:

![上一个项表示为一个树](./media/index-overview/item-as-tree.png)

请注意树中的数组编码方式：数组中的每个条目将获取一个中间节点，该节点标有该条目在数组中的索引（0、1 等等）。

## <a name="from-trees-to-property-paths"></a>从树到属性路径

Azure Cosmos DB 将项转换为树的原因是便于按照属性在这些树中的路径引用这些属性。 若要获取某个属性的路径，可以在树中从根节点遍历到该属性，并将遍历的每个节点的标签连接起来。

下面是上述示例项中每个属性的路径：

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

写入某个项时，Azure Cosmos DB 会有效地为每个属性的路径及其相应值编制索引。

## <a name="index-kinds"></a>索引类型

Azure Cosmos DB 目前支持两种类型的索引：

**range** 索引类型用于：

- 等式查询： 

   ```sql SELECT * FROM container c WHERE c.property = 'value'```

- 范围查询： 

   ```sql SELECT * FROM container c WHERE c.property > 'value'``` (适用于`>`， `<`， `>=`， `<=`， `!=`)

- `ORDER BY` 查询：

   ```sql SELECT * FROM container c ORDER BY c.property```

- `JOIN` 查询： 

   ```sql SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'```

可以针对标量值（字符串或数字）使用范围索引。

**spatial** 索引类型用于：

- 地理空间距离查询： 

   ```sql SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40```

- 查询中的地理空间： 

   ```sql SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })```

可以针对格式正确的 [GeoJSON](geospatial.md) 对象使用空间索引。 目前支持点、线串和多边形。

**composite** 索引类型用于：

- 针对多个属性的 `ORDER BY` 查询： 

   ```sql SELECT * FROM container c ORDER BY c.firstName, c.lastName```

## <a name="querying-with-indexes"></a>使用索引进行查询

处理查询时，可以使用为数据编制索引时提取的路径轻松查找索引。 通过将查询的 `WHERE` 子句与已编制索引的路径列表进行匹配，可以快速识别与查询谓词匹配的项。

例如，考虑以下查询：`SELECT location FROM location IN company.locations WHERE location.country = 'France'`。 查询谓词（按项筛选，其中的任意位置使用“France”作为其国家/地区）将与下面红色突出显示的路径相匹配：

![匹配树中的特定路径](./media/index-overview/matching-path.png)

> [!NOTE]
> 按单个属性排序的 `ORDER BY` 子句始终需要一个范围索引，如果它引用的路径不包含范围索引，则会失败。  同样，多 `ORDER BY` 查询始终需要组合索引。 

## <a name="next-steps"></a>后续步骤

阅读以下文章中有关索引的详细信息：

- [索引策略](index-policy.md)
- [如何管理索引策略](how-to-manage-indexing-policy.md)
