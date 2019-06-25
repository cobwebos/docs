---
title: Azure Cosmos DB 索引编制策略
description: 了解如何配置和更改默认索引策略，以便自动编制索引并提高 Azure Cosmos DB 的性能。
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: thweiss
ms.openlocfilehash: 3f19668cc4fb4f4f4a900c157aa79de83ad1b79b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2019
ms.locfileid: "67163728"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Azure Cosmos DB 中的索引策略

在 Azure Cosmos DB 中，每个容器都有一个确定了如何为容器项编制索引的索引策略。 新建容器的默认索引策略会对每个项的每个属性编制索引，对任何字符串或数字强制使用范围索引，对 Point 类型的任何 GeoJSON 对象强制使用空间索引。 这样，无需提前考虑索引和索引管理，就能获得较高的查询性能。

在某些情况下，你可能想要替代此自动行为，以便更好地满足自己的要求。 可以通过设置容器索引策略的索引模式来自定义该策略，并可以包含或排除属性路径。  

> [!NOTE]
> 更新这篇文章中所述的索引策略的方法仅适用于 Azure Cosmos DB 的 SQL （核心） API。

## <a name="indexing-mode"></a>索引模式

Azure Cosmos DB 支持两种索引模式：

- **一致**：如果容器的索引策略设置为“一致”，则创建、更新或删除项时，索引将以同步方式更新。 这意味着，读取查询的一致性是[为帐户配置的一致性](consistency-levels.md)。

- **无**：如果容器的索引策略设置为“无”，则会有效地针对该容器禁用索引。 将容器用作单纯的键-值存储时，通常会使用此设置，在此情况下无需使用辅助索引。 它还有助于加速批量插入操作。

## <a name="including-and-excluding-property-paths"></a>包含和排除属性路径

自定义索引策略可以指定要在索引编制中显式包含或排除的属性路径。 通过优化编制索引的路径数量，可以减少容器使用的存储量并改善写入操作的延迟。 这些路径是遵循[索引概述部分所述的方法](index-overview.md#from-trees-to-property-paths)定义的，补充要求如下：

- 指向标量值（字符串或数字）的路径以 `/?` 结尾
- 数组中的元素通过 `/[]` 表示法（而不是 `/0`、`/1` 等）统一寻址
- 可以使用 `/*` 通配符来匹配节点下的任意元素

沿用前面的示例：

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

- `headquarters` 的 `employees` 路径是 `/headquarters/employees/?`
- `locations` 的 `country` 路径是 `/locations/[]/country/?`
- `headquarters` 下的任何内容的路径是 `/headquarters/*`

将某个路径显式包含在索引策略中时，它还必须定义要将哪些索引类型应用到该路径；对于每个索引类型，必须定义此索引要应用到的数据类型：

| 索引类型 | 允许的目标数据类型 |
| --- | --- |
| 范围 | 字符串或数字 |
| 空间 | 点、线串或多边形 |

例如，我们可以包含 `/headquarters/employees/?`路径，并指定要针对 `String` 和 `Number` 值的该路径应用 `Range` 索引。

### <a name="includeexclude-strategy"></a>包含/排除策略

任何索引策略必须包含根路径 `/*` 作为包含或排除的路径。

- 包含根路径可以选择性地排除不需要编制索引的路径。 这是建议的方法，因为这样可以让 Azure Cosmos DB 主动为可以添加到模型的任何新属性编制索引。
- 排除根路径可以选择性地包含需要编制索引的路径。

- 有关路径替换为包含的常规字符： 字母数字字符和 _ （下划线），无需转义围绕双引号括起来 （例如，"/ 路径 /？"） 的路径字符串。 对于包含其他特殊字符的路径，需要在双引号中转义路径字符串（例如 "/\"path-abc\"/?"）。 如果预期路径中会出现特殊字符，出于安全考虑，可以转义每个路径。 功能上，如果转义 Vs 不仅仅具有特殊字符的每个路径，它不起作用。

请参阅[此部分](how-to-manage-indexing-policy.md#indexing-policy-examples)中的索引策略示例。

## <a name="composite-indexes"></a>组合索引

按两个或更多个属性运行 `ORDER BY` 的查询需要一个组合索引。 目前，只有多 `ORDER BY` 查询才使用组合索引。 默认情况下不会定义组合索引，因此，应根据需要[添加组合索引](how-to-manage-indexing-policy.md#composite-indexing-policy-examples)。

定义组合索引时，请指定：

- 两个或更多个属性路径。 属性路径的定义顺序非常重要。
- 顺序（升序或降序）。

使用组合索引时请注意以下事项：

- 如果组合索引路径与 ORDER BY 子句中的属性顺序不匹配，则组合索引无法支持查询

- 组合索引路径的顺序（升序或降序）还应与 ORDER BY 子句中的顺序相匹配。

- 组合索引还支持在所有路径中使用反向顺序的 ORDER BY 子句。

考虑以下示例，其中针对属性 a、b 和 c 定义了组合索引：

| **组合索引**     | **示例 `ORDER BY` 查询**      | **受索引支持？** |
| ----------------------- | -------------------------------- | -------------- |
| ```(a asc, b asc)```         | ```ORDER BY  a asc, b asc```        | ```Yes```            |
| ```(a asc, b asc)```          | ```ORDER BY  b asc, a asc```        | ```No```             |
| ```(a asc, b asc)```          | ```ORDER BY  a desc, b desc```      | ```Yes```            |
| ```(a asc, b asc)```          | ```ORDER BY  a asc, b desc```       | ```No```             |
| ```(a asc, b asc, c asc)``` | ```ORDER BY  a asc, b asc, c asc``` | ```Yes```            |
| ```(a asc, b asc, c asc)``` | ```ORDER BY  a asc, b asc```        | ```No```            |

应该自定义索引策略，以便可为所有必要的 `ORDER BY` 查询提供服务。

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

## <a name="obsolete-attributes"></a>已弃用的属性

使用索引策略时，可能会遇到现已弃用的以下属性：

- `automatic` 是在索引策略的根部分定义的布尔值。 现在会忽略该属性；如果所用的工具需要该属性，可将其设置为 `true`。
- `precision` 在包含的路径的索引级别定义的一个数字。 现在会忽略该属性；如果所用的工具需要该属性，可将其设置为 `-1`。
- `hash` 是一个索引类型，现已由 range 类型取代。

## <a name="next-steps"></a>后续步骤

阅读以下文章中有关索引的详细信息：

- [索引概述](index-overview.md)
- [如何管理索引策略](how-to-manage-indexing-policy.md)
