---
title: Azure Cosmos DB 中的索引
description: 了解 Azure Cosmos DB 中索引的工作原理。
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: thweiss
ms.openlocfilehash: d679208914eb7d1f74bfaec77fbcff196909a2f4
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299782"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Azure Cosmos DB 中的索引 - 概述

Azure Cosmos DB 是一种架构不可知的数据库，使你能够迭代应用程序，而无需处理架构或索引管理。 默认情况下，Azure Cosmos DB 将自动为[容器](databases-containers-items.md#azure-cosmos-containers)中所有项的每个属性编制索引，而无需定义任何架构或配置辅助索引。

本文旨在说明 Azure Cosmos DB 如何为数据编制索引，以及它如何使用索引来提高查询性能。 在探索如何自定义[索引策略](index-policy.md)之前，建议先阅读本部分。

## <a name="from-items-to-trees"></a>从项到树

每当在容器中存储某个项时，该项的内容将投影为 JSON 文档，然后转换为树表示形式。 这意味着，该项的每个属性表示为树中的一个节点。 系统会创建一个伪根节点，作为该项的所有第一级属性的父级。 叶节点包含项携带的实际标量值。

例如，假设存在以下项：

```json
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
```

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

Azure Cosmos DB 目前支持三种索引。

### <a name="range-index"></a>范围索引

**范围**索引基于按序排序的树状结构。 范围索引类型用于：

- 等式查询：

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
   ```

   ```sql
   SELECT * FROM c WHERE c.property IN ("value1", "value2", "value3")
   ```

   数组元素上的相等匹配
   ```sql
    SELECT * FROM c WHERE ARRAY_CONTAINS(c.tags, "tag1”)
    ```

- 范围查询：

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ```
  （适用于 `>`、`<`、`>=`、`<=`、`!=`）

- 检查属性是否存在：

   ```sql
   SELECT * FROM c WHERE IS_DEFINED(c.property)
   ```

- 字符串前缀匹配（CONTAINS 关键字将不利用范围索引）：

   ```sql
   SELECT * FROM c WHERE STARTSWITH(c.property, "value")
   ```

- `ORDER BY` 查询：

   ```sql
   SELECT * FROM container c ORDER BY c.property
   ```

- `JOIN` 查询：

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

可以针对标量值（字符串或数字）使用范围索引。

### <a name="spatial-index"></a>空间索引

利用**空间**索引，可以在点、线条、多边形和 multipolygon 等地理空间对象上高效地进行查询。 这些查询使用 ST_DISTANCE、ST_WITHIN、ST_INTERSECTS 关键字。 下面是使用空间索引种类的一些示例：

- 地理空间距离查询：

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- 查询中的地理空间：

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] } })
   ```

- 地理空间交叉查询：

   ```sql
   SELECT * FROM c WHERE ST_INTERSECTS(c.property, { 'type':'Polygon', 'coordinates': [[ [31.8, -5], [32, -5], [31.8, -5] ]]  })  
   ```

可以针对格式正确的 [GeoJSON](geospatial.md) 对象使用空间索引。 当前支持 Points、LineStrings、Polygons 和 MultiPolygons。

### <a name="composite-indexes"></a>组合索引

在多个字段上执行操作时，**复合**索引可提高效率。 复合索引类型用于：

- 针对多个属性的 `ORDER BY` 查询：

```sql
 SELECT * FROM container c ORDER BY c.property1, c.property2
```

- 使用筛选器和 `ORDER BY` 的查询。 如果将 Filter 属性添加到 `ORDER BY` 子句，则这些查询可以利用组合索引。

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' ORDER BY c.property1, c.property2
```

- 对两个或更多属性使用筛选器的查询，其中至少有一个属性是相等筛选器

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' AND c.property2 > 'value'
```

只要一个筛选器谓词使用索引种类的，查询引擎就会在扫描其余部分之前计算。 例如，如果你有一个 SQL 查询，如 `SELECT * FROM c WHERE c.firstName = "Andrew" and CONTAINS(c.lastName, "Liu")`

* 上面的查询将首先使用索引筛选 firstName 为 "Andrew" 的条目。 然后，它通过后续管道传递所有 firstName = "Andrew" 条目，以评估 CONTAINS 筛选器谓词。

* 通过添加使用索引的其他筛选器谓词，可以提高查询速度，并在使用不使用索引（例如包含）的函数时避免完全容器扫描。 筛选子句的顺序并不重要。 查询引擎将确定哪些谓词更具选择性，并相应地运行查询。


## <a name="querying-with-indexes"></a>使用索引进行查询

处理查询时，可以使用为数据编制索引时提取的路径轻松查找索引。 通过将查询的 `WHERE` 子句与已编制索引的路径列表进行匹配，可以快速识别与查询谓词匹配的项。

例如，考虑以下查询：`SELECT location FROM location IN company.locations WHERE location.country = 'France'`。 查询谓词（按项筛选，其中的任意位置使用“France”作为其国家/地区）将与下面红色突出显示的路径相匹配：

![匹配树中的特定路径](./media/index-overview/matching-path.png)

> [!NOTE]
> 按单个属性排序的 `ORDER BY` 子句始终需要一个范围索引，如果它引用的路径不包含范围索引，则会失败。 同样，按多个属性排序的 `ORDER BY` 查询始终需要组合索引。

## <a name="next-steps"></a>后续步骤

阅读以下文章中有关索引的详细信息：

- [索引策略](index-policy.md)
- [如何管理索引策略](how-to-manage-indexing-policy.md)
