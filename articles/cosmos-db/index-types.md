---
title: Azure Cosmos DB 中的索引类型
description: Azure Cosmos DB 中的索引类型概述
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.openlocfilehash: 5e7ee7c0bdfd0cff6be182e6d087cc264910e440
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59271554"
---
# <a name="index-types-in-azure-cosmos-db"></a>Azure Cosmos DB 中的索引类型

配置路径的索引策略时，有多个选项。 可以为每个路径指定一个或多个索引定义︰

- **数据类型：** String、Number、Point、Polygon 或 LineString（每个路径每种数据类型只能包含一个条目）。

- **索引种类：** 范围 （对于相等性、 范围或 ORDER BY 查询） 或者 （用于空间查询） 的空间。

- **精度：** 对于范围索引，最大精度值为-1，这也是默认值。

## <a name="index-kind"></a>索引种类

Azure Cosmos DB 支持范围索引和 / 或可配置对于字符串或数字数据类型，每个路径。

- **范围索引**支持高效的等式查询、 联接查询、 范围查询 (使用 >，<>、 =、 < =、 ！ =)，和 ORDER BY 查询。 ORDER BY 查询，默认情况下，还需要最大索引精度 (-1)。 数据类型可以是字符串或数字。

- **空间查询**支持高效的空间（范围内和距离）查询。 数据类型可以是 Point、Polygon 或 LineString。 对于可为 Point、Polygon 或 LineString 数据类型指定为每个路径，Azure Cosmos DB 还支持空间索引种类。 指定路径中的值必须是有效的 GeoJSON 片段，如 {"type":"Point", "coordinates": [0.0, 10.0]}。 Azure Cosmos DB 支持为 Point、Polygon 和 LineString 数据类型自动编制索引。

下面是示例的范围，查询和空间索引可用于提供：

| **索引种类** | **说明/用例** |
| ---------- | ---------------- |
| 范围      | 对 /prop/?（或 /）应用范围索引 可用于有效完成下列查询：<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop<br><br>范围通过 /props/ [] /？ (or / or /props/) 可用于有效完成下列查询：<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag = 5  |
| 空间    | /prop/? (or /) 可用于有效完成下列查询：<br><br>SELECT FROM collection c WHERE ST_DISTANCE(c.prop, {"type":"Point", "coordinates": [0.0, 10.0]}) < 40<br><br>SELECT FROM collection c WHERE ST_WITHIN(c.prop, {"type":"Point", ... }) --启用对点的索引编制<br><br>SELECT FROM collection c WHERE ST_WITHIN({"type":"Polygon", ... }, c.prop) --启用对多边形的索引编制。 |

## <a name="default-behavior-of-index-kinds"></a>索引种类的默认行为

- 如果没有范围索引（任何精度）告知可能需要执行扫描来提供查询，则默认情况下，使用范围运算符（如 >=）的查询会返回错误。

- 使用 REST API 中的 x-ms-documentdb-enable-scans 标头或使用 .NET SDK 的 EnableScanInQuery 请求选项，可以在没有范围索引的情况下执行范围查询。 如果在查询中有 Azure Cosmos DB 可以使用索引据其进行筛选的其他筛选器，则不返回错误。

- 默认情况下，如果没有空间索引，或者不能从索引提供其他筛选器，则空间查询会返回错误。 此类查询可以通过执行以扫描**x-ms-documentdb-启用-扫描**或**EnableScanInQuery**。

## <a name="index-precision"></a>索引精度

> [!NOTE]
> Azure Cosmos 容器支持不再需要自定义索引精度，最大精度值 (-1) 以外的值的新索引布局。 使用此方法，路径将始终以最大精度编制索引。 如果对索引策略中指定的精度值，在容器上的 CRUD 请求将以无提示方式忽略精度值，并从容器响应仅包含最大精度值 (-1)。  默认情况下，所有新的 Cosmos 容器使用的新的索引布局。

- 可以使用索引精度，以使索引存储开销和查询性能之间进行权衡。 对于数字，我们建议使用默认精度配置-1 （最大）。 因为数字是 JSON 格式的 8 个字节，这相当于 8 个字节的配置。 选择较低值的精度（如 1-7）意味着在某些范围内的值会映射到相同的索引条目。 因此，可以降低索引存储空间，但查询执行可能需要处理更多的项。 因此，它会消耗更多的吞吐量/RU。

- 索引精度对于字符串范围更加实用。 由于字符串可以是任意长度，索引精度的选择可能会影响字符串范围查询的性能。 它还可能会影响所需的索引存储空间量。 可以使用介于 1 和 100 之间的精度或 -1（最大精度）来配置字符串范围索引。 如果想要对字符串属性执行 ORDER BY 查询，则必须为相应路径指定精度 -1。

- 空间索引始终为所有类型（Point、LineString 和 Polygon）使用默认索引精度。 无法重写空间索引的默认索引精度。

当查询使用 ORDER BY，但针对最大精度的查询路径没有范围索引时，Azure Cosmos DB 会返回一个错误。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure Cosmos DB 中的索引，请参阅以下文章：

- [索引编制的概述](index-overview.md)
- [索引编制策略](indexing-policies.md)
- [索引路径](index-paths.md)

