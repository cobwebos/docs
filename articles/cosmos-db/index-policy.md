---
title: Azure Cosmos DB 索引编制策略
description: 了解如何配置和更改默认索引策略，以便自动编制索引并提高 Azure Cosmos DB 的性能。
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: thweiss
ms.openlocfilehash: 944c05a28eb33c659bf4aaa600985530122f8d3e
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000327"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Azure Cosmos DB 中的索引策略

在 Azure Cosmos DB 中，每个容器都有一个确定了如何为容器项编制索引的索引策略。 新建容器的默认索引策略会对每个项的每个属性编制索引，对任何字符串或数字强制使用范围索引，对 Point 类型的任何 GeoJSON 对象强制使用空间索引。 这样，无需提前考虑索引和索引管理，就能获得较高的查询性能。

在某些情况下，你可能想要替代此自动行为，以便更好地满足自己的要求。 可以通过设置容器索引策略的索引模式来自定义该策略，并可以包含或排除属性路径。

> [!NOTE]
> 本文所述的更新索引策略的方法仅适用于 Azure Cosmos DB 的 SQL (Core) API。

## <a name="indexing-mode"></a>索引模式

Azure Cosmos DB 支持两种索引模式：

- **一致**：创建、更新或删除项时，将同步更新索引。 这意味着，读取查询的一致性是[为帐户配置的一致性](consistency-levels.md)。
- **无**：在容器上禁用索引。 将容器用作单纯的键-值存储时，通常会使用此设置，在此情况下无需使用辅助索引。 它还可用于提高大容量操作的性能。 大容量操作完成后，可将索引模式设置为一致，然后使用[IndexTransformationProgress](how-to-manage-indexing-policy.md#use-the-net-sdk-v2)进行监视，直到完成。

> [!NOTE]
> Cosmos DB 还支持延迟索引模式。 当引擎没有执行任何其他工作时，延迟索引会以较低的优先级对索引执行更新。 这可能导致查询结果**不一致或不完整**。 此外，对于大容量操作，使用延迟索引替代 "无" 也不会带来任何好处，因为对索引模式进行的任何更改都将导致删除并重新创建索引。 出于此原因，我们建议使用该客户。 若要提高大容量操作的性能，请将索引模式设置为 "无"，然后返回`IndexTransformationProgress`到一致模式并监视容器上的属性，直到完成。

默认情况下，索引策略设置为`automatic`。 这是通过将索引策略`automatic`中的属性设置为来`true`实现的。 如果将此属性`true`设置为，则允许 Azure CosmosDB 在文档写入时自动编制文档索引。

## <a name="including-and-excluding-property-paths"></a>包含和排除属性路径

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

例如，可以包含`/headquarters/employees/?`路径。 此路径将确保索引 employees 属性，但不会在此属性中为其他嵌套的 JSON 编制索引。

## <a name="includeexclude-strategy"></a>包含/排除策略

任何索引策略必须包含根路径 `/*` 作为包含或排除的路径。

- 包含根路径可以选择性地排除不需要编制索引的路径。 这是建议的方法，因为这样可以让 Azure Cosmos DB 主动为可以添加到模型的任何新属性编制索引。
- 排除根路径可以选择性地包含需要编制索引的路径。

- 对于包含以下字符的路径：字母数字字符和 _ （下划线），无需转义双引号的路径字符串（例如 "/path/？"）。 对于包含其他特殊字符的路径，需要在双引号中转义路径字符串（例如 "/\"path-abc\"/?"）。 如果预期路径中会出现特殊字符，出于安全考虑，可以转义每个路径。 在功能上，如果你转义每个路径，而不是包含特殊字符的路径，则不会产生任何差别。

- 默认情况下，系统属性“etag”被排除在索引之外，除非将 etag 添加到索引所包含的路径中。

包括和排除路径时，可能会遇到以下属性：

- `kind`可以是`range`或`hash`。 范围索引功能提供哈希索引的所有功能，因此建议使用范围索引。

- `precision` 在包含的路径的索引级别定义的一个数字。 值为`-1`指示最大精度。 建议始终将此值设置为`-1`。

- `dataType`可以是`String`或`Number`。 这指示将对其进行索引的 JSON 属性的类型。

如果未指定此属性，则这些属性将具有以下默认值：

| **属性名称**     | **默认值** |
| ----------------------- | -------------------------------- |
| `kind`   | `range` |
| `precision`   | `-1`  |
| `dataType`    | `String` 和 `Number` |

请参阅[此部分](how-to-manage-indexing-policy.md#indexing-policy-examples)，了解包含和排除路径的索引策略示例。

## <a name="spatial-indexes"></a>空间索引

在索引策略中定义空间路径时，应定义应该将哪个索引```type```应用于该路径。 空间索引的可能类型包括：

* Point

* Polygon

* MultiPolygon

* LineString

默认情况下，Azure Cosmos DB 将不会创建任何空间索引。 如果要使用空间 SQL 内置函数，应对所需的属性创建空间索引。 有关添加空间索引的索引策略示例，请参阅[此部分](geospatial.md)。

## <a name="composite-indexes"></a>组合索引

包含具有两个或多个属性的子句的查询需要组合索引。`ORDER BY` 您还可以定义一个组合索引以提高许多相等性和范围查询的性能。 默认情况下不会定义组合索引，因此，应根据需要[添加组合索引](how-to-manage-indexing-policy.md#composite-indexing-policy-examples)。

定义组合索引时，请指定：

- 两个或更多个属性路径。 属性路径的定义顺序非常重要。

- 顺序（升序或降序）。

> [!NOTE]
> 添加复合索引时，与其他索引类型一样，在更新索引时，查询可能会返回不一致的结果。

### <a name="order-by-queries-on-multiple-properties"></a>对多个属性执行 ORDER BY 查询：

对于带有两个或更多属性的`ORDER BY`子句的查询使用复合索引时，将使用下列注意事项：

- 如果复合索引路径与`ORDER BY`子句中的属性顺序不匹配，则复合索引不支持该查询。

- 复合索引路径的顺序（升序或降序）还应与`order` `ORDER BY`子句中的匹配。

- 复合索引还支持`ORDER BY`在所有路径上采用相反顺序的子句。

请考虑以下示例，其中在属性名称、age 和 _ts 上定义了组合索引：

| **组合索引**     | **示例 `ORDER BY` 查询**      | **是否由复合索引支持？** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.name ASC, c.age asc``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.age ASC, c.name asc```   | ```No```             |
| ```(name ASC, age ASC)```    | ```SELECT * FROM c ORDER BY c.name DESC, c.age DESC``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c ORDER BY c.name ASC, c.age DESC``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC, timestamp ASC``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC``` | ```No```            |

应该自定义索引策略，以便可为所有必要的 `ORDER BY` 查询提供服务。

### <a name="queries-with-filters-on-multiple-properties"></a>具有多个属性的筛选器的查询

如果查询中有两个或多个属性的筛选器，则为这些属性创建组合索引可能会很有用。

例如，请考虑以下查询，它对两个属性具有相等筛选器：

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age = 18
```

如果可以对（name ASC、age ASC）使用复合索引，则此查询将更高效，并占用更少的 RU。

使用范围筛选器的查询还可以使用复合索引进行优化。 但是，查询只能有一个范围筛选器。 范围筛选器`>`包括`<`、 `<=`、 `>=`、和`!=`。 范围筛选器应在复合索引中最后定义。

请考虑以下具有相等和范围筛选器的查询：

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age > 18
```

此查询在（name ASC，age ASC）上使用复合索引会更有效。 但是，该查询将不使用（age ASC，name ASC）上的复合索引，因为相等性筛选器必须在复合索引中首先定义。

为包含多个属性的筛选器的查询创建复合索引时，将使用以下注意事项

- 查询筛选器中的属性应与组合索引中的属性相匹配。 如果属性在复合索引中，但未作为筛选器包含在查询中，则查询将不会使用该组合索引。
- 如果查询在筛选器中包含未在复合索引中定义的其他属性，则将使用复合索引和范围索引的组合来计算查询。 这需要的 RU 比独占使用范围索引更少。
- 如果某个属性具有范围筛选器（`>`、 `<`、 `<=`、 `>=`或`!=`），则此属性应在复合索引中最后定义。 如果查询有多个范围筛选器，则不会使用该组合索引。
- 当创建组合索引以使用多个筛选器优化查询时`ORDER` ，组合索引的会对结果没有影响。 此属性是可选的。
- 如果没有为具有多个属性筛选器的查询定义复合索引，查询仍将成功。 但是，可以使用复合索引减少查询的 RU 开销。

请考虑以下示例，其中在属性名称、年龄和时间戳上定义了组合索引：

| **组合索引**     | **示例查询**      | **是否由复合索引支持？** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name DESC, age ASC)```    | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c WHERE c.name != "John" AND c.age > 18``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 123049923``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp = 123049923``` | ```No```            |

### <a name="queries-with-a-filter-as-well-as-an-order-by-clause"></a>使用筛选器和 ORDER BY 子句的查询

如果查询筛选一个或多个属性，并在 ORDER BY 子句中具有不同的属性，则将筛选器中的属性添加到`ORDER BY`子句可能会很有帮助。

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

创建复合索引以使用筛选器和`ORDER BY`子句优化查询时，请注意以下事项：

* 如果查询筛选属性，这些属性应首先包含在`ORDER BY`子句中。
* 如果未使用筛选器对一个属性和一个单独`ORDER BY`的子句定义组合索引，则查询仍将成功。 但是，可以使用复合索引减少查询的 RU 开销，尤其是在`ORDER BY`子句中的属性的基数较高时。
* 为具有多个属性的`ORDER BY`查询创建复合索引以及对多个属性使用筛选器的查询的所有注意事项仍适用。


| **组合索引**                      | **示例 `ORDER BY` 查询**                                  | **是否由复合索引支持？** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No`  |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC``` | ```No```   |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.age ASC, c.name ASC,c.timestamp ASC``` | `Yes` |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.timestamp ASC``` | `No` |

## <a name="modifying-the-indexing-policy"></a>修改索引策略

随时可以[使用 Azure 门户或某个支持的 SDK](how-to-manage-indexing-policy.md) 更新容器的索引策略。 更新索引策略会触发从旧索引到新索引的转换，该操作是在线和就地执行的（因此，在执行该操作期间不会消耗更多的存储空间）。 旧策略的索引将有效转换为新策略，而不会影响写入可用性或针对容器预配的吞吐量。 索引转换是一个异步操作，完成该操作所需的时间取决于预配的吞吐量、项的数目及其大小。

> [!NOTE]
> 当重新编制索引正在进行时，查询可能不会返回所有匹配的结果，且返回结果时不会返回任何错误。 这意味着，在索引转换完成之前，查询结果可能不一致。 可以[使用某个 SDK](how-to-manage-indexing-policy.md) 跟踪索引转换的进度。

如果新索引策略的模式设置为“一致”，当索引转换正在进行时，无法应用其他任何索引策略更改。 可以通过将索引策略的模式设置为“无”（立即删除索引），来取消正在运行的索引转换。

## <a name="indexing-policies-and-ttl"></a>索引策略和 TTL

[生存时间 (TTL) 功能](time-to-live.md)要求索引编制在启用它的容器中处于活动状态。 这表示：

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
