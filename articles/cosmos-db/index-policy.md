---
title: Azure Cosmos DB 索引编制策略
description: 了解如何配置和更改默认索引策略，以便自动编制索引并提高 Azure Cosmos DB 的性能。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: tisande
ms.openlocfilehash: 68adfb8b4cfb7c665a8e8b162b4698a095bb671e
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82869939"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Azure Cosmos DB 中的索引策略

在 Azure Cosmos DB 中，每个容器都有一个确定了如何为容器项编制索引的索引策略。 新建容器的默认索引策略会对每个项的每个属性编制索引，对任何字符串或数字强制使用范围索引，对 Point 类型的任何 GeoJSON 对象强制使用空间索引。 这样，无需提前考虑索引和索引管理，就能获得较高的查询性能。

在某些情况下，你可能想要替代此自动行为，以便更好地满足自己的要求。 可以通过设置容器索引策略的索引模式来自定义该策略，并可以包含或排除属性路径。  

> [!NOTE]
> 本文所述的更新索引策略的方法仅适用于 Azure Cosmos DB 的 SQL (Core) API。

## <a name="indexing-mode"></a>索引模式

Azure Cosmos DB 支持两种索引模式：

- **一致**：创建、更新或删除项时，索引将以同步方式更新。 这意味着，读取查询的一致性是[为帐户配置的一致性](consistency-levels.md)。
- **无**：针对该容器禁用索引。 将容器用作单纯的键-值存储时，通常会使用此设置，在此情况下无需使用辅助索引。 它还可用于改善批量操作的性能。 批量操作完成后，可将索引模式设置为“一致”，然后使用 [IndexTransformationProgress](how-to-manage-indexing-policy.md#dotnet-sdk) 进行监视，直到完成。

> [!NOTE]
> Azure Cosmos DB 还支持延迟索引模式。 当引擎未执行任何其他工作时，延迟索引将以低得多的优先级对索引执行更新。 这可能导致查询结果**不一致或不完整**。 如果计划查询 Cosmos 容器，则不应选择“延迟索引”。

默认情况下，索引策略设置为 `automatic`。 为此，可将索引策略中的 `automatic` 属性设置为 `true`。 将此属性设置为 `true` 可让 Azure CosmosDB 在写入文档时自动为文档编制索引。

## <a name="including-and-excluding-property-paths"></a><a id="include-exclude-paths"></a>包括和排除属性路径

自定义索引策略可以指定要在索引编制中显式包含或排除的属性路径。 通过优化编制索引的路径数量，可以减少容器使用的存储量并改善写入操作的延迟。 这些路径是遵循[索引概述部分所述的方法](index-overview.md#from-trees-to-property-paths)定义的，补充要求如下：

- 指向标量值（字符串或数字）的路径以 `/?` 结尾
- 数组中的元素通过 `/[]` 表示法（而不是 `/0`、`/1` 等）统一寻址
- 可以使用 `/*` 通配符来匹配节点下的任意元素

沿用前面的示例：

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

- `headquarters` 的 `employees` 路径是 `/headquarters/employees/?`

- `locations` 的 `country` 路径是 `/locations/[]/country/?`

- `headquarters` 下的任何内容的路径是 `/headquarters/*`

例如，可以包含 `/headquarters/employees/?` 路径。 此路径确保为 employees 属性编制索引，但不会为此属性中的其他嵌套 JSON 编制索引。

## <a name="includeexclude-strategy"></a>包含/排除策略

任何索引策略必须包含根路径 `/*` 作为包含或排除的路径。

- 包含根路径可以选择性地排除不需要编制索引的路径。 这是建议的方法，因为这样可以让 Azure Cosmos DB 主动为可以添加到模型的任何新属性编制索引。
- 排除根路径可以选择性地包含需要编制索引的路径。

- 对于包含常规字符（包括字母数字字符和下划线 _）的路径，无需在双引号中转义路径字符串（例如 "/path/?"）。 对于包含其他特殊字符的路径，需要在双引号中转义路径字符串（例如 "/\"path-abc\"/?"）。 如果预期路径中会出现特殊字符，出于安全考虑，可以转义每个路径。 在功能上，转义每个路径与仅转义包含特殊字符的路径没有任何差别。

- 默认情况下，系统属性 `_etag` 被排除在索引之外，除非将 etag 添加到索引所包含的路径中。

- 如果将索引模式设为“一致”  ，则会自动为系统属性 `id` 和 `_ts` 编制索引。

包含和排除路径时，可能会遇到以下属性：

- `kind` 可以是 `range` 或 `hash`。 范围索引功能提供哈希索引的所有功能，因此我们建议使用范围索引。

- `precision` 在包含的路径的索引级别定义的一个数字。 `-1` 值表示最大精度。 我们建议始终将此值设置为 `-1`。

- `dataType` 可以是 `String` 或 `Number`。 这表示要编制索引的 JSON 属性的类型。

如果未指定，这些属性将使用以下默认值：

| **属性名称**     | **默认值** |
| ----------------------- | -------------------------------- |
| `kind`   | `range` |
| `precision`   | `-1`  |
| `dataType`    | `String` 和 `Number` |

有关包含和排除路径的索引策略示例，请参阅[此部分](how-to-manage-indexing-policy.md#indexing-policy-examples)。

## <a name="includeexclude-precedence"></a>包含/排除优先级

如果包含的路径和排除的路径有冲突，则更精确的路径优先。

下面是一个示例：

**包含的路径**：`/food/ingredients/nutrition/*`

**排除的路径**：`/food/ingredients/*`

在这种情况下，包含的路径优先于排除的路径，因为它更精确。 根据这些路径，将从索引中排除`food/ingredients`路径或嵌套内的任何数据。 例外情况是包含的路径中的数据： `/food/ingredients/nutrition/*`，将对其进行索引。

下面是有关 Azure Cosmos DB 中包含和排除的路径优先级的一些规则：

- 深度路径比较窄的路径更精确。 例如： `/a/b/?`比`/a/?`更精确。

- `/?`比`/*`更精确。 例如`/a/?` ，其优先级高于`/a/*`此`/a/?`优先级。

- 路径`/*`必须是包含路径或排除路径。

## <a name="spatial-indexes"></a>空间索引

在索引策略中定义空间路径时，应定义要将哪个索引 ```type``` 应用到该路径。 空间索引的可能类型包括：

* Point

* Polygon

* MultiPolygon

* LineString

Azure Cosmos DB 默认不会创建任何空间索引。 若要使用空间 SQL 内置函数，应该对所需的属性创建空间索引。 有关添加空间索引的索引策略示例，请参阅[此部分](geospatial.md)。

## <a name="composite-indexes"></a>组合索引

包含 `ORDER BY` 子句（该子句包含两个或更多个属性）的查询需要一个组合索引。 还可以定义一个组合索引来改善许多相等性和范围查询的性能。 默认情况下不会定义组合索引，因此，应根据需要[添加组合索引](how-to-manage-indexing-policy.md#composite-indexing-policy-examples)。

与包含或排除的路径不同，不能使用`/*`通配符创建路径。 每个复合路径在路径`/?`的末尾都具有隐式，无需指定。 复合路径导致标量值，这是组合索引中包含的唯一值。

定义组合索引时，请指定：

- 两个或更多个属性路径。 属性路径的定义顺序非常重要。

- 顺序（升序或降序）。

> [!NOTE]
> 添加组合索引时，该查询将利用现有范围索引，直到新的组合索引添加已完成。 因此，在添加组合索引时，可能不会立即观察到性能改进。 可以[使用某个 SDK](how-to-manage-indexing-policy.md) 跟踪索引转换的进度。

### <a name="order-by-queries-on-multiple-properties"></a>针对多个属性的 ORDER BY 查询：

对包含 `ORDER BY` 子句（该子句包含两个或更多个属性）的查询使用组合索引时，请注意以下事项：

- 如果组合索引路径与 `ORDER BY` 子句中的属性顺序不匹配，则组合索引无法支持查询。

- 组合索引路径的顺序（升序或降序）还应与 `ORDER BY` 子句中的 `order` 相匹配。

- 组合索引还支持在所有路径中使用反向顺序的 `ORDER BY` 子句。

考虑以下示例，其中针对属性 name、age 和 _ts 定义了组合索引：

| **组合索引**     | **示例 `ORDER BY` 查询**      | **是否受组合索引的支持？** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.name ASC, c.age asc``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.age ASC, c.name asc```   | ```No```             |
| ```(name ASC, age ASC)```    | ```SELECT * FROM c ORDER BY c.name DESC, c.age DESC``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c ORDER BY c.name ASC, c.age DESC``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC, timestamp ASC``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC``` | ```No```            |

应该自定义索引策略，以便可为所有必要的 `ORDER BY` 查询提供服务。

### <a name="queries-with-filters-on-multiple-properties"></a>包含针对多个属性的筛选器的查询

如果查询包含针对两个或更多个属性的筛选器，为这些属性创建组合索引可能会有帮助。

例如，考虑以下查询，其中包含针对两个属性的相等性筛选器：

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age = 18
```

如果能够针对 (name ASC, age ASC) 利用组合索引，则此查询将更加高效：花费的时间更少，且消耗的 RU 更少。

还可以使用组合索引来优化包含范围筛选器的查询。 但是，查询只能包含一个范围筛选器。 范围筛选器包括 `>`、`<`、`<=`、`>=` 和 `!=`。 范围筛选器应在组合索引中最后定义。

考虑以下查询，其中同时包含相等性筛选器和范围筛选器：

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age > 18
```

针对 (name ASC, age ASC) 使用组合索引可以更有效地运行此查询。 但是，该查询不会针对 (age ASC, name ASC) 利用组合索引，因为相等性筛选器必须在组合索引中首先定义。

为包含针对多个属性的筛选器的查询创建组合索引时，请注意以下事项

- 查询筛选器中的属性应与组合索引中的属性相匹配。 如果某个属性在组合索引中，但未作为筛选器包含在查询中，则查询不会利用该组合索引。
- 如果查询包含筛选器中的其他属性，但这些属性未在组合索引中定义，则会结合使用组合索引和范围索引来评估查询。 这样，所需的 RU 数就比专门使用范围索引更少。
- 如果某个属性包含范围筛选器（`>`、`<`、`<=`、`>=` 或 `!=`），则此属性应在组合索引中最后定义。 如果某个查询包含多个范围筛选器，则该查询不会利用组合索引。
- 创建组合索引来优化包含多个筛选器的查询时，组合索引的 `ORDER` 不会对结果造成任何影响。 此属性是可选的。
- 如果没有为包含针对多个属性的筛选器的查询定义组合索引，该查询仍会成功。 但是，使用组合索引可以减少查询的 RU 开销。

考虑以下示例，其中针对属性 name、age 和 timestamp 定义了组合索引：

| **组合索引**     | **示例查询**      | **是否受组合索引的支持？** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name DESC, age ASC)```    | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c WHERE c.name != "John" AND c.age > 18``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 123049923``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp = 123049923``` | ```No```            |

### <a name="queries-with-a-filter-as-well-as-an-order-by-clause"></a>包含筛选器和 ORDER BY 子句的查询

如果查询针对一个或多个属性进行筛选，并在 ORDER BY 子句中包含不同的属性，则将筛选器中的属性添加到 `ORDER BY` 子句可能会有帮助。

例如，通过将筛选器中的属性添加到 ORDER BY 子句，可以重写以下查询来利用组合索引：

使用范围索引的查询：

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp
```

使用组合索引的查询：

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.name, c.timestamp
```

对于包含多个相等性筛选器的查询，可以通用化相同的模式和查询优化：

使用范围索引的查询：

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.timestamp
```

使用组合索引的查询：

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.name, c.age, c.timestamp
```

创建组合索引来优化包含筛选器和 `ORDER BY` 子句的查询时，请注意以下事项：

* 如果查询针对属性进行筛选，应该首先将这些属性包含在 `ORDER BY` 子句中。
* 对于包含针对一个属性的筛选器并包含一个使用不同属性的独立 `ORDER BY` 子句的查询，如果未为它定义组合索引，该查询仍会成功。 但是，使用组合索引可以减少查询的 RU 开销，尤其是 `ORDER BY` 子句中的属性具有较高的基数时。
* 有关为包含多个属性的 `ORDER BY` 查询，以及为包含针对多个属性的筛选器的查询创建组合查询的所有注意事项仍然适用。


| **组合索引**                      | **示例 `ORDER BY` 查询**                                  | **是否受组合索引的支持？** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No`  |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC``` | ```No```   |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.age ASC, c.name ASC,c.timestamp ASC``` | `Yes` |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.timestamp ASC``` | `No` |

## <a name="modifying-the-indexing-policy"></a>修改索引策略

随时可以[使用 Azure 门户或某个支持的 SDK](how-to-manage-indexing-policy.md) 更新容器的索引策略。 更新索引策略会触发从旧索引到新索引的转换，该操作是在线和就地执行的（因此，在执行该操作期间不会消耗更多的存储空间）。 旧策略的索引将有效转换为新策略，而不会影响写入可用性或针对容器预配的吞吐量。 索引转换是一个异步操作，完成该操作所需的时间取决于预配的吞吐量、项的数目及其大小。

> [!NOTE]
> 当添加范围索引或空间索引时，查询可能不会返回所有匹配的结果，并且在返回结果时不会返回任何错误。 这意味着，在索引转换完成之前，查询结果可能不一致。 可以[使用某个 SDK](how-to-manage-indexing-policy.md) 跟踪索引转换的进度。

如果新索引策略的模式设置为“一致”，当索引转换正在进行时，无法应用其他任何索引策略更改。 可以通过将索引策略的模式设置为“无”（立即删除索引），来取消正在运行的索引转换。

## <a name="indexing-policies-and-ttl"></a>索引策略和 TTL

[生存时间 (TTL) 功能](time-to-live.md)要求索引编制在启用它的容器中处于活动状态。 这意味着：

- 无法在索引模式设置为“无”的容器中激活 TTL。
- 无法在已激活 TTL 的容器中将索引模式设置为“无”。

对于不需要为任何属性路径编制索引，但需要激活 TTL 的情况，可以结合以下设置使用索引策略：

- 将索引模式设置为“一致”，并且
- 不使用包含的路径，并且
- 将 `/*` 用作唯一排除的路径。

## <a name="next-steps"></a>后续步骤

阅读以下文章中有关索引的详细信息：

- [索引概述](index-overview.md)
- [如何管理索引策略](how-to-manage-indexing-policy.md)
