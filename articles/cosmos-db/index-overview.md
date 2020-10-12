---
title: Azure Cosmos DB 中的索引
description: 了解索引在 Azure Cosmos DB 中的工作原理，学习受支持的不同类型的索引，例如范围索引、空间索引和组合索引。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: tisande
ms.openlocfilehash: 3d07657fc3345ddd8dfadd163dc3c9f957d77af3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90068381"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Azure Cosmos DB 中的索引 - 概述

Azure Cosmos DB 是一种架构不可知的数据库，你可用它来迭代应用程序，而无需处理架构或索引管理。 默认情况下，Azure Cosmos DB 自动对[容器](databases-containers-items.md#azure-cosmos-containers)中所有项的每个属性编制索引，不用定义任何架构或配置辅助索引。

本文的目的是说明 Azure Cosmos DB 如何为数据编制索引以及如何使用索引来提高查询性能。 建议先阅读本部分，然后再探索如何自定义[索引策略](index-policy.md)。

## <a name="from-items-to-trees"></a>从项到树

每次在容器中存储项时，项的内容都投影为 JSON 文档，然后转换为树表示形式。 这意味着，该项的每个属性都在树中以节点的形式表示。 伪根节点被创建为项的所有第一级属性的父级。 叶节点包含项带有的实际标量值。

例如，请看以下项：

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

它由以下树表示：

:::image type="content" source="./media/index-overview/item-as-tree.png" alt-text="上一项以树的形式表示" border="false":::

请注意数组是如何在树中进行编码的：数组中的每个条目都获得一个中间节点，该节点标记了该数组中该条目的索引（0、1 等等）。

## <a name="from-trees-to-property-paths"></a>从树到属性路径

Azure Cosmos DB 将项转换为树的原因是，它允许通过这些树中属性的路径来引用属性。 若要获取属性的路径，可从根节点到该属性来遍历树，并将每个遍历的节点的标签连接起来。

下面是上述示例项中每个属性的路径：

- /locations/0/country:"Germany"
- /locations/0/city:"Berlin"
- /locations/1/country:"France"
- /locations/1/city:"Paris"
- /headquarters/country:"Belgium"
- /headquarters/employees:250
- /exports/0/city:"Moscow"
- /exports/1/city:"Athens"

写入项时，Azure Cosmos DB 会有效地对每个属性的路径及其相应的值编制索引。

## <a name="index-kinds"></a>索引类型

Azure Cosmos DB 目前支持三种类型的索引。

### <a name="range-index"></a>范围索引

范围索引基于已排序的树形结构。 范围索引类型用于：

- 相等查询：

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
   ```

   ```sql
   SELECT * FROM c WHERE c.property IN ("value1", "value2", "value3")
   ```

   数组元素上的相等匹配
   ```sql
    SELECT * FROM c WHERE ARRAY_CONTAINS(c.tags, "tag1")
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

- 字符串系统函数：

   ```sql
   SELECT * FROM c WHERE CONTAINS(c.property, "value")
   ```

   ```sql
   SELECT * FROM c WHERE STRINGEQUALS(c.property, "value")
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

空间索引可对地理空间对象（例如点、线、多边形和多面）进行有效查询。 这些查询使用ST_DISTANCE、ST_WITHIN 和 ST_INTERSECTS 关键字。 下面是使用空间索引类型的一些示例：

- 地理空间距离查询：

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- 在查询的地理空间：

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] })
   ```

- 地理空间相交查询：

   ```sql
   SELECT * FROM c WHERE ST_INTERSECTS(c.property, { 'type':'Polygon', 'coordinates': [[ [31.8, -5], [32, -5], [31.8, -5] ]]  })  
   ```

空间索引可在格式正确的 [GeoJSON](geospatial.md) 对象上使用。 目前支持点、线串、多边形和多面。

### <a name="composite-indexes"></a>组合索引

对多个字段执行操作时，组合索引可提高效率。 组合索引类型用于：

- 对多个属性的 `ORDER BY` 查询：

```sql
 SELECT * FROM container c ORDER BY c.property1, c.property2
```

- 使用筛选器和 `ORDER BY` 的查询。 如果在 `ORDER BY` 子句中添加筛选器属性，则这些查询可使用组合索引。

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' ORDER BY c.property1, c.property2
```

- 对两个或更多属性进行的带筛选器的查询，其中至少一个属性是等式筛选器

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' AND c.property2 > 'value'
```

只要有一个筛选器谓词使用某一索引类型，查询引擎就将在扫描其余部分之前先评估该谓词。 例如，如果你有一个 SQL 查询，如 `SELECT * FROM c WHERE c.firstName = "Andrew" and CONTAINS(c.lastName, "Liu")`

* 上面的查询将先使用索引筛选 firstName = "Andrew" 的条目， 然后通过后续管道传递所有 firstName = "Andrew" 的条目来评估 CONTAINS 筛选器谓词。

* 如果使用不采用索引（如 CONTAINS）的函数，可额外添加使用该索引的筛选器谓词来加快查询速度和避免完整容器扫描。 筛选子句的顺序并不重要。 查询引擎将确定哪些谓词更具选择性，并相应地运行查询。

## <a name="querying-with-indexes"></a>使用索引进行查询

通过编制数据索引时提取的路径，可在处理查询时轻松查找索引。 通过将查询的 `WHERE` 子句与已编制索引的路径的列表相匹配，可快速确定与查询谓词匹配的项。

例如，请看以下查询：`SELECT location FROM location IN company.locations WHERE location.country = 'France'`。 查询谓词（对项进行筛选，其中任何位置都采用“法国”作为其国家/地区）与下面用红色突出显示的路径相匹配：

:::image type="content" source="./media/index-overview/matching-path.png" alt-text="上一项以树的形式表示" border="false":::

> [!NOTE]
> 按单个属性排序的 `ORDER BY` 子句总是需要一个范围索引，如果它引用的路径没有范围索引，则会失败。 同样地，按多个属性排序的 `ORDER BY` 查询总是需要一个组合索引。

## <a name="next-steps"></a>后续步骤

阅读以下文章中有关索引的详细信息：

- [索引策略](index-policy.md)
- [如何管理索引策略](how-to-manage-indexing-policy.md)
