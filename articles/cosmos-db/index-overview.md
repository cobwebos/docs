---
title: Azure Cosmos DB 中的索引
description: 了解索引的工作原理 Azure Cosmos DB、不同类型的索引（例如，支持的范围、空间、组合索引）。
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: thweiss
ms.openlocfilehash: 65186262095560d7ae54d32b218d1c01f1fb921d
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873618"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Azure Cosmos DB 中的索引-概述

Azure Cosmos DB 是架构无关的数据库，它使你能够在应用程序上循环访问，而无需处理架构或索引管理。 默认情况下，Azure Cosmos DB 自动为[容器](databases-containers-items.md#azure-cosmos-containers)中的所有项的每个属性编制索引，而无需定义架构或配置辅助索引。

本文的目的是说明 Azure Cosmos DB 如何为数据编制索引以及如何使用索引来提高查询性能。 建议在学习如何自定义[索引策略](index-policy.md)之前完成此部分。

## <a name="from-items-to-trees"></a>从项到树

每次将项存储在容器中时，都会将其内容投影为 JSON 文档，然后将其转换为树表示形式。 这意味着，该项的每个属性都表示为树中的节点。 伪根节点创建为该项的所有第一级属性的父节点。 叶节点包含项所携带的实际标量值。

例如，请考虑以下事项：

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

它将由以下树表示：

![表示为树的上一项](./media/index-overview/item-as-tree.png)

请注意如何在树中对数组进行编码：数组中的每个条目都将获取一个中间节点，该节点标记有该条目在数组（0，1等）中的索引。

## <a name="from-trees-to-property-paths"></a>从树到属性路径

Azure Cosmos DB 将项转换为树的原因在于，它允许通过这些树中的路径引用属性。 若要获取属性的路径，我们可以将树从根节点遍历到该属性，然后连接每个已遍历节点的标签。

下面是上述示例项中每个属性的路径：

    /locations/0/country: "Germany"
    /locations/0/city: "Berlin"
    /locations/1/country: "France"
    /locations/1/city: "Paris"
    /headquarters/country: "Belgium"
    /headquarters/employees: 250
    /exports/0/city: "Moscow"
    /exports/1/city: "Athens"

写入项时，Azure Cosmos DB 会有效地索引每个属性的路径及其相应的值。

## <a name="index-kinds"></a>索引类型

Azure Cosmos DB 目前支持三种索引。

### <a name="range-index"></a>范围索引

**范围**索引基于按序排序的树状结构。 范围索引类型用于：

- 相等查询：

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

范围索引可用于标量值（字符串或数字）。

### <a name="spatial-index"></a>空间索引

利用**空间**索引，可以在点、线条、多边形和 multipolygon 等地理空间对象上高效地进行查询。 这些查询使用 ST_DISTANCE、ST_WITHIN ST_INTERSECTS 关键字。 下面是使用空间索引种类的一些示例：

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

可以在格式正确的[GeoJSON](geospatial.md)对象上使用空间索引。 当前支持点、Linestring、多边形和 MultiPolygons。

### <a name="composite-indexes"></a>复合索引

在多个字段上执行操作时，**复合**索引可提高效率。 复合索引类型用于：

- `ORDER BY` 多个属性的查询：

```sql
 SELECT * FROM container c ORDER BY c.property1, c.property2
```

- 使用筛选器进行查询，并 `ORDER BY`。 如果将 filter 属性添加到 `ORDER BY` 子句中，则这些查询可以利用复合索引。

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' ORDER BY c.property1, c.property2
```

- 对两个或更多属性使用筛选器的查询，其中至少有一个属性是相等筛选器

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' AND c.property2 > 'value'
```

只要一个筛选器谓词使用索引种类的，查询引擎就会在扫描其余部分之前计算。 例如，如果您有一个 SQL 查询，如 `SELECT * FROM c WHERE c.firstName = "Andrew" and CONTAINS(c.lastName, "Liu")`

* 上面的查询将首先使用索引筛选 firstName 为 "Andrew" 的条目。 然后，它通过后续管道传递所有 firstName = "Andrew" 条目，以评估 CONTAINS 筛选器谓词。

* 通过添加使用索引的其他筛选器谓词，可以提高查询速度，并在使用不使用索引（例如包含）的函数时避免完全容器扫描。 筛选子句的顺序并不重要。 查询引擎将确定哪些谓词更具选择性，并相应地运行查询。


## <a name="querying-with-indexes"></a>使用索引进行查询

在对数据编制索引时提取的路径使在处理查询时可以轻松地查找索引。 通过将查询的 `WHERE` 子句与索引路径的列表进行匹配，可以非常快速地识别与查询谓词匹配的项。

例如，请考虑以下查询： `SELECT location FROM location IN company.locations WHERE location.country = 'France'`。 查询谓词（对项进行筛选，其中任何位置都将 "法国" 作为其国家/地区）将与下红色突出显示的路径匹配：

![匹配树中的特定路径](./media/index-overview/matching-path.png)

> [!NOTE]
> 按单个属性排序的 `ORDER BY` 子句*始终*需要范围索引，如果它引用的路径不具有范围索引，则会失败。 同样，`ORDER BY` 查询多个属性的排序*始终*需要组合索引。

## <a name="next-steps"></a>后续步骤

阅读以下文章中有关索引的详细信息：

- [索引策略](index-policy.md)
- [如何管理索引策略](how-to-manage-indexing-policy.md)
