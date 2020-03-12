---
title: Azure Cosmos DB 索引编制策略
description: 了解如何在 Azure Cosmos DB 中配置和更改自动索引的默认索引策略和更高的性能。
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: thweiss
ms.openlocfilehash: 86dbcee7150adacd0e961dbe07cf66ad117d2041
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128946"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Azure Cosmos DB 中的索引策略

在 Azure Cosmos DB 中，每个容器都具有一个索引策略，该策略指示应如何为容器的项编制索引。 新创建的容器的默认索引策略将索引每个项的每个属性，为任何字符串或数字强制实施范围索引，并为 Point 类型的任何 GeoJSON 对象强制执行范围索引。 这使你无需事先考虑索引和索引管理，就可以获得较高的查询性能。

在某些情况下，可能需要替代此自动行为，以更好地满足需求。 您可以通过设置容器的索引*模式*来自定义容器的索引策略，并包括或排除*属性路径*。

> [!NOTE]
> 本文中所述的更新索引策略的方法仅适用于 Azure Cosmos DB 的 SQL （核心） API。

## <a name="indexing-mode"></a>索引模式

Azure Cosmos DB 支持两种索引模式：

- **一致**：创建、更新或删除项时，将同步更新索引。 这意味着，读取查询的一致性将是[为该帐户配置的一致性](consistency-levels.md)。
- **无**：在容器上禁用索引。 当容器用作纯键-值存储而无需辅助索引时，通常使用此项。 它还可用于提高大容量操作的性能。 大容量操作完成后，可将索引模式设置为一致，然后使用[IndexTransformationProgress](how-to-manage-indexing-policy.md#use-the-net-sdk-v2)进行监视，直到完成。

> [!NOTE]
> Cosmos DB 还支持延迟索引模式。 当引擎没有执行任何其他工作时，延迟索引会以较低的优先级对索引执行更新。 这可能导致查询结果**不一致或不完整**。 此外，对于大容量操作，使用延迟索引替代 "无" 也不会带来任何好处，因为对索引模式进行的任何更改都将导致删除并重新创建索引。 出于此原因，我们建议使用该客户。 若要提高大容量操作的性能，请将索引模式设置为 "无"，然后返回到一致模式并监视容器上的 `IndexTransformationProgress` 属性，直到完成。

默认情况下，索引策略设置为 `automatic`。 这是通过将索引策略中的 `automatic` 属性设置为 `true`来实现的。 如果将此属性设置为 "`true` 允许 Azure CosmosDB 在文档写入时自动为文档编制索引。

## <a id="include-exclude-paths"></a>包括和排除属性路径

自定义索引策略可以指定显式包含在索引中或从索引中排除的属性路径。 通过优化已编制索引的路径数，可以降低容器使用的存储量，并提高写入操作的延迟时间。 这些路径按照["索引概述" 部分中描述的方法](index-overview.md#from-trees-to-property-paths)定义，其中添加了以下内容：

- 以标量值（字符串或数字）开头的路径以结尾 `/?`
- 数组中的元素通过 `/[]` 表示法（而不是 `/0`、`/1` 等）进行寻址。
- `/*` 通配符可用于匹配该节点下的任何元素

再次取同一个示例：

```
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 }
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

- `headquarters`的 `employees` 路径 `/headquarters/employees/?`

- `locations`"`country` 路径 `/locations/[]/country/?`

- `headquarters` 下的任何内容的路径 `/headquarters/*`

例如，可以包含 `/headquarters/employees/?` 路径。 此路径将确保索引 employees 属性，但不会在此属性中为其他嵌套的 JSON 编制索引。

## <a name="includeexclude-strategy"></a>包含/排除策略

任何索引策略都必须将根路径 `/*` 包括为包含的或排除的路径。

- 包括根路径以选择性地排除不需要编制索引的路径。 这是建议的方法，因为它让 Azure Cosmos DB 主动为可添加到模型中的任何新属性编制索引。
- 排除根路径以有选择性地包含需要编制索引的路径。

- 对于包含以下字符的路径：字母数字字符和 _ （下划线），无需转义双引号的路径字符串（例如 "/path/？"）。 对于包含其他特殊字符的路径，需要为双引号（例如，"/\"path-abc\"/？"）转义路径字符串。 如果你希望路径中有特殊字符，则可以对每个路径进行转义以保证安全。 在功能上，如果你转义每个路径，而不是包含特殊字符的路径，则不会产生任何差别。

- 默认情况下，系统属性 "_etag" 将从索引中排除，除非将 etag 添加到用于索引的包含路径。

包括和排除路径时，可能会遇到以下属性：

- `kind` 可以是 `range` 或 `hash`。 范围索引功能提供哈希索引的所有功能，因此建议使用范围索引。

- `precision` 是在索引级别定义的包含路径的数字。 值 `-1` 表示最大精度。 建议始终将此值设置为 `-1`。

- `dataType` 可以是 `String` 或 `Number`。 这指示将对其进行索引的 JSON 属性的类型。

如果未指定此属性，则这些属性将具有以下默认值：

| **属性名称**     | **默认值** |
| ----------------------- | -------------------------------- |
| `kind`   | `range` |
| `precision`   | `-1`  |
| `dataType`    | `String` 和 `Number` |

请参阅[此部分](how-to-manage-indexing-policy.md#indexing-policy-examples)，了解包含和排除路径的索引策略示例。

## <a name="spatial-indexes"></a>空间索引

在索引策略中定义空间路径时，应定义 ```type``` 应应用于该路径的索引。 空间索引的可能类型包括：

* Point

* Polygon

* MultiPolygon

* LineString

默认情况下，Azure Cosmos DB 将不会创建任何空间索引。 如果要使用空间 SQL 内置函数，应对所需的属性创建空间索引。 有关添加空间索引的索引策略示例，请参阅[此部分](geospatial.md)。

## <a name="composite-indexes"></a>复合索引

具有包含两个或多个属性的 `ORDER BY` 子句的查询需要组合索引。 您还可以定义一个组合索引以提高许多相等性和范围查询的性能。 默认情况下，未定义复合索引，因此应根据需要[添加复合索引](how-to-manage-indexing-policy.md#composite-indexing-policy-examples)。

定义复合索引时，可以指定：

- 两个或多个属性路径。 定义属性路径的顺序。

- 顺序（升序或降序）。

> [!NOTE]
> 添加复合索引时，该查询将利用现有范围索引，直到新的复合索引添加完成。 因此，在添加复合索引时，您可能不会立即发现性能改进。 [使用其中一个 sdk](how-to-manage-indexing-policy.md)可以跟踪索引转换的进度。

### <a name="order-by-queries-on-multiple-properties"></a>对多个属性执行 ORDER BY 查询：

对于带有两个或更多属性的 `ORDER BY` 子句的查询使用组合索引时，将使用下列注意事项：

- 如果复合索引路径与 `ORDER BY` 子句中的属性顺序不匹配，则复合索引不支持该查询。

- 复合索引路径的顺序（升序或降序）还应与 `ORDER BY` 子句中的 `order` 匹配。

- 复合索引还支持在所有路径上采用相反顺序的 `ORDER BY` 子句。

请考虑以下示例，其中在属性名称、age 和 _ts 上定义了组合索引：

| **复合索引**     | **示例 `ORDER BY` 查询**      | **是否由复合索引支持？** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.name ASC, c.age asc``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.age ASC, c.name asc```   | ```No```             |
| ```(name ASC, age ASC)```    | ```SELECT * FROM c ORDER BY c.name DESC, c.age DESC``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c ORDER BY c.name ASC, c.age DESC``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC, timestamp ASC``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC``` | ```No```            |

你应自定义索引策略，以便可以为所有必要的 `ORDER BY` 查询提供服务。

### <a name="queries-with-filters-on-multiple-properties"></a>使用多个属性的筛选器的查询

如果查询中有两个或多个属性的筛选器，则为这些属性创建组合索引可能会很有用。

例如，请考虑以下查询，它对两个属性具有相等筛选器：

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age = 18
```

如果可以对（name ASC、age ASC）使用复合索引，则此查询将更高效，并占用更少的 RU。

使用范围筛选器的查询还可以使用复合索引进行优化。 但是，查询只能有一个范围筛选器。 范围筛选器包括 `>`、`<`、`<=`、`>=`和 `!=`。 范围筛选器应在复合索引中最后定义。

请考虑以下具有相等和范围筛选器的查询：

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age > 18
```

此查询在（name ASC，age ASC）上使用复合索引会更有效。 但是，该查询将不使用（age ASC，name ASC）上的复合索引，因为相等性筛选器必须在复合索引中首先定义。

为包含多个属性的筛选器的查询创建复合索引时，将使用以下注意事项

- 查询筛选器中的属性应与组合索引中的属性相匹配。 如果属性在复合索引中，但未作为筛选器包含在查询中，则查询将不会使用该组合索引。
- 如果查询在筛选器中包含未在复合索引中定义的其他属性，则将使用复合索引和范围索引的组合来计算查询。 这需要的 RU 比独占使用范围索引更少。
- 如果某个属性具有范围筛选器（`>`、`<`、`<=`、`>=`或 `!=`），则此属性应在复合索引中最后定义。 如果查询有多个范围筛选器，则不会使用该组合索引。
- 创建组合索引以优化具有多个筛选器的查询时，组合索引的 `ORDER` 不会影响结果。 此属性是可选的。
- 如果没有为具有多个属性筛选器的查询定义复合索引，查询仍将成功。 但是，可以使用复合索引减少查询的 RU 开销。

请考虑以下示例，其中在属性名称、年龄和时间戳上定义了组合索引：

| **复合索引**     | **示例查询**      | **是否由复合索引支持？** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name DESC, age ASC)```    | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c WHERE c.name != "John" AND c.age > 18``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 123049923``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp = 123049923``` | ```No```            |

### <a name="queries-with-a-filter-as-well-as-an-order-by-clause"></a>使用筛选器和 ORDER BY 子句的查询

如果查询筛选一个或多个属性，并在 ORDER BY 子句中具有不同的属性，则在筛选器中将属性添加到 `ORDER BY` 子句可能会很有帮助。

例如，通过将筛选器中的属性添加到 ORDER BY 子句，可以重写以下查询以利用复合索引：

使用范围索引进行查询：

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp
```

使用复合索引的查询：

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.name, c.timestamp
```

对于具有多个相等筛选器的查询，可以将相同的模式和查询优化通用化：

使用范围索引进行查询：

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.timestamp
```

使用复合索引的查询：

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.name, c.age, c.timestamp
```

创建复合索引以使用筛选器和 `ORDER BY` 子句优化查询时，需要注意以下事项：

* 如果查询筛选属性，这些属性应首先包含在 `ORDER BY` 子句中。
* 如果未使用筛选器对一个属性和一个单独的 `ORDER BY` 子句定义组合索引，则查询仍将成功。 但是，可以使用复合索引减少查询的 RU 开销，尤其是 `ORDER BY` 子句中的属性具有较高的基数时。
* 为具有多个属性的 `ORDER BY` 查询创建复合索引以及对多个属性使用筛选器的查询的所有注意事项仍适用。


| **复合索引**                      | **示例 `ORDER BY` 查询**                                  | **是否由复合索引支持？** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No`  |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC``` | ```No```   |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.age ASC, c.name ASC,c.timestamp ASC``` | `Yes` |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.timestamp ASC``` | `No` |

## <a name="modifying-the-indexing-policy"></a>修改索引策略

可以[通过使用 Azure 门户或某个受支持的 sdk](how-to-manage-indexing-policy.md)随时更新容器的索引策略。 索引策略更新会触发从旧索引到新索引的转换，这是在联机和就地执行的（因此在操作过程中不会消耗额外的存储空间）。 旧策略的索引会有效地转换为新策略，而不会影响在容器上预配的写入可用性或吞吐量。 索引转换是一个异步操作，而完成所需要的时间取决于预配的吞吐量、项数及其大小。

> [!NOTE]
> 在添加范围或空间索引时，查询可能不会返回所有匹配的结果，而不会返回任何错误。 这意味着，在完成索引转换之前，查询结果可能不一致。 [使用其中一个 sdk](how-to-manage-indexing-policy.md)可以跟踪索引转换的进度。

如果新的索引策略模式设置为 "一致"，则在索引转换正在进行时，不能应用其他索引策略更改。 可以通过将索引策略的模式设置为 "无" （这会立即删除索引）来取消正在运行的索引转换。

## <a name="indexing-policies-and-ttl"></a>索引策略和 TTL

[生存时间（TTL）功能](time-to-live.md)要求索引在其打开的容器上处于活动状态。 这意味着：

- 不能在索引模式设置为 "无" 的容器上激活 TTL，
- 不能在已激活 TTL 的容器上将索引模式设置为 "无"。

对于不需要为属性路径编制索引的方案，但需要 TTL，则可以将索引策略用于：

- 索引模式设置为 "一致"，
- 无包含路径和
- `/*` 为唯一排除的路径。

## <a name="next-steps"></a>后续步骤

阅读以下文章中有关索引的详细信息：

- [索引概述](index-overview.md)
- [如何管理索引策略](how-to-manage-indexing-policy.md)
