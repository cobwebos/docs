---
title: 排查使用 Azure Cosmos DB 时遇到的查询问题
description: 了解如何识别、诊断和排查 Azure Cosmos DB SQL 查询问题。
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 04/22/2020
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: b3c6926f17e8378fd3b53bfd59a7c5ea8141adb4
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2020
ms.locfileid: "82097228"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>排查使用 Azure Cosmos DB 时遇到的查询问题

本文逐步说明排查 Azure Cosmos DB 中的查询问题的一般建议方法。 尽管不应考虑本文中所述的步骤来完全防范潜在的查询问题，但我们在此处提供了最常见的性能提示。 你应使用本文作为解决 Azure Cosmos DB 核心（SQL） API 中的慢速或昂贵查询的起点。 你还可以使用[诊断日志](cosmosdb-monitor-resource-logs.md)来确定速度缓慢或消耗大量吞吐量的查询。

可对 Azure Cosmos DB 中的查询优化进行广泛分类：

- 减少查询的请求单位（RU）费用的优化
- 仅减少延迟的优化

如果减少查询的 RU 费用，几乎肯定会降低延迟。

本文提供了可通过使用[营养](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)数据集重新创建的示例。

## <a name="common-sdk-issues"></a>常见 SDK 问题

阅读本指南之前，请考虑与查询引擎无关的常见 SDK 问题。

- 为了获得最佳性能，请遵循这些[性能提示](performance-tips.md)。
    > [!NOTE]
    > 为了提高性能，我们建议 Windows 64 位主机处理。 SQL SDK 包含一个本机 Microsoft.azure.documents.serviceinterop.dll，用于在本地分析和优化查询。 仅在 Windows x64 平台上支持 Microsoft.azure.documents.serviceinterop.dll。 对于 Linux 和不支持 Microsoft.azure.documents.serviceinterop.dll 的其他不受支持的平台，会对网关进行额外的网络调用以获取优化的查询。
- SDK 允许`MaxItemCount`为查询设置，但不能指定最小项计数。
    - 代码应处理任意页面大小，从0到`MaxItemCount`。
    - 页中的项数将始终小于或等于指定`MaxItemCount`的项数。 但是， `MaxItemCount`严格程度上是最大值，并且结果可能少于此量。
- 有时，即使在未来页面上有结果，查询也可能具有空页。 原因如下：
    - SDK 可能会执行多个网络调用。
    - 查询可能需要很长时间才能检索文档。
- 所有查询都有一个继续标记，该标记将允许查询继续。 请确保完全排出查询。 查看 SDK 示例，并使用上`while` `FeedIterator.HasMoreResults`的循环来排出整个查询。

## <a name="get-query-metrics"></a>获取查询度量值

在 Azure Cosmos DB 中优化查询时，第一步是始终获取查询[的查询度量值](profile-sql-api-query.md)。 还可以通过 Azure 门户获取这些指标。 在数据资源管理器中运行查询后，查询度量值将显示在 "**结果**" 选项卡的旁边：

[![获取查询度量](./media/troubleshoot-query-performance/obtain-query-metrics.png)值](./media/troubleshoot-query-performance/obtain-query-metrics.png#lightbox)

获取查询度量值后，请将**检索到的文档计数**与查询的**输出文档计数**进行比较。 使用此比较来确定要在本文中查看的相关部分。

**检索的文档**数是查询引擎加载所需的文档数。 **输出文档计数**是查询结果所需的文档数。 如果**检索的文档计数**明显高于**输出文档计数**，则至少有一个查询部分无法使用索引，并且需要执行扫描。

请参阅以下各节，了解适用于你的方案的相关查询优化。

### <a name="querys-ru-charge-is-too-high"></a>查询的 RU 开销过高

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>检索的文档计数明显高于输出文档计数

- [在索引策略中包含必要的路径。](#include-necessary-paths-in-the-indexing-policy)

- [了解哪些系统函数使用索引。](#understand-which-system-functions-use-the-index)

- [了解哪些聚合查询使用索引。](#understand-which-aggregate-queries-use-the-index)

- [优化同时具有筛选器和 ORDER BY 子句的查询。](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

- [使用子查询优化联接表达式。](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>“已检索文档计数”约等于“输出文档计数”

- [最小化跨分区查询。](#minimize-cross-partition-queries)

- [优化具有多个属性筛选器的查询。](#optimize-queries-that-have-filters-on-multiple-properties)

- [优化同时具有筛选器和 ORDER BY 子句的查询。](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>查询的 RU 开销可接受，但延迟仍然过高

- [提高邻近性。](#improve-proximity)

- [增加预配的吞吐量。](#increase-provisioned-throughput)

- [增加 MaxConcurrency。](#increase-maxconcurrency)

- [增加 MaxBufferedItemCount。](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>“已检索文档计数”超过“输出文档计数”的查询

 **检索的文档**数是查询引擎加载所需的文档数。 **输出文档计数**是指查询返回的文档数。 如果**检索的文档计数**明显高于**输出文档计数**，则至少有一个查询部分无法使用索引，并且需要执行扫描。

下面是一个不是由索引完全提供的扫描查询示例：

查询：

 ```sql
SELECT VALUE c.description
FROM c
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
 ```

查询度量值：

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

**检索的文档计数**（60951）明显高于**输出文档计数**（7），表示此查询导致文档扫描。 在这种情况下，系统函数[UPPER （）](sql-query-upper.md)不使用索引。

### <a name="include-necessary-paths-in-the-indexing-policy"></a>在索引策略中包含所需的路径

索引策略应涵盖子句、 `WHERE` `ORDER BY`子句、 `JOIN`和大多数系统函数中包含的所有属性。 索引策略中指定的所需路径应与 JSON 文档中的属性相匹配。

> [!NOTE]
> Azure Cosmos DB 索引策略中的属性区分大小写

如果对[营养](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)数据集运行以下简单查询，则在对`WHERE`子句中的属性进行索引时，将会看到更低的 RU 费用：

#### <a name="original"></a>原始

查询：

```sql
SELECT *
FROM c
WHERE c.description = "Malabar spinach, cooked"
```

索引策略：

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/description/*"
        }
    ]
}
```

**RU 费用： 409.51 RU**

#### <a name="optimized"></a>已优化

更新的索引策略：

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

**RU 费用： 2.98 RU**

你可以随时将属性添加到索引策略，而不会影响写入可用性或性能。 如果将新属性添加到索引中，则使用属性的查询将立即使用新的可用索引。 查询在生成时将使用新索引。 因此，在进行索引重新生成时，查询结果可能会不一致。 如果为新属性编制了索引，则在重新生成索引期间，只会影响使用现有索引的查询。 可以[跟踪索引转换进度](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-net-sdk-v3)。

### <a name="understand-which-system-functions-use-the-index"></a>了解哪些系统函数使用索引

如果表达式可以转换为一系列字符串值，则可以使用索引。 否则，不能。

下面是一些可使用索引的常见字符串函数的列表：

- STARTSWITH(str_expr, str_expr)
- LEFT(str_expr, num_expr) = str_expr
- SUBSTRING （str_expr、num_expr、num_expr） = str_expr，但仅当第一个 num_expr 为0时

以下是一些不使用索引并必须加载每个文档的常见系统函数：

| **系统函数**                     | **优化建议**             |
| --------------------------------------- |------------------------------------------------------------ |
| CONTAINS                                | 使用 Azure 搜索进行全文搜索。                        |
| UPPER/LOWER                             | 不要使用系统函数来规范化数据以进行比较，而是在插入时规范化大小写。 类似于```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'```的查询```SELECT * FROM c WHERE c.name = 'BOB'```。 |
| 数学函数（非聚合） | 如果需要经常在查询中计算值，请考虑将该值存储为 JSON 文档中的属性。 |

------

即使系统函数不是，查询的其他部分仍可使用索引。

### <a name="understand-which-aggregate-queries-use-the-index"></a>了解哪些聚合查询使用索引

在大多数情况下，Azure Cosmos DB 中的聚合系统函数将使用索引。 但是，根据筛选器或聚合查询中的其他子句，查询引擎可能需要加载大量的文档。 通常，查询引擎将首先应用相等性和范围筛选器。 应用这些筛选器后，查询引擎可以评估其他筛选器，并根据需要将其余文档加载到计算聚合。

例如，假设有这两个示例查询，使用相等和`CONTAINS`系统函数筛选器的查询通常比只使用`CONTAINS`系统函数筛选器的查询更有效。 这是因为相等性筛选器首先应用，并使用索引，以便需要为更昂贵`CONTAINS`的筛选器加载文档。

仅限`CONTAINS`筛选器-更高的 RU 费用的查询：

```sql
SELECT COUNT(1)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

带有相等筛选器和`CONTAINS`筛选器-下 RU 费用的查询：

```sql
SELECT AVG(c._ts)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats" AND CONTAINS(c.description, "spinach")
```

下面是不会完全使用索引的聚合查询的其他示例：

#### <a name="queries-with-system-functions-that-dont-use-the-index"></a>使用不使用索引的系统函数进行查询

应参考相关的[系统函数页面](sql-query-system-functions.md)，查看其是否使用索引。

```sql
SELECT MAX(c._ts)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

#### <a name="aggregate-queries-with-user-defined-functionsudfs"></a>具有用户定义函数（UDF）的聚合查询

```sql
SELECT AVG(c._ts)
FROM c
WHERE udf.MyUDF("Sausages and Luncheon Meats")
```

#### <a name="queries-with-group-by"></a>使用 GROUP BY 的查询

查询的 RU 费用`GROUP BY`将随着`GROUP BY`子句中属性的基数的增加而增加。 例如，在下面的查询中，查询的 RU 费用将随着数字唯一说明的增加而增加。

聚合函数与`GROUP BY`子句的 ru 费用将高于聚合函数的 ru 费用。 在此示例中，查询引擎必须加载与`c.foodGroup = "Sausages and Luncheon Meats"`筛选器匹配的每个文档，以便 RU 费用应为高。

```sql
SELECT COUNT(1)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats"
GROUP BY c.description
```

如果你计划经常运行相同的聚合查询，则生成与运行单个查询的[Azure Cosmos DB 更改源](change-feed.md)相比，生成实时具体化视图可能更高效。

### <a name="optimize-queries-that-have-both-a-filter-and-an-order-by-clause"></a>优化同时具有筛选器和 ORDER BY 子句的查询

尽管具有筛选器和`ORDER BY`子句的查询通常使用范围索引，但如果它们可以从复合索引提供，则它们将更有效。 除了修改索引策略以外，还应将组合索引中的所有属性添加到 `ORDER BY` 子句。 此对查询所做的更改将确保它使用复合索引。  可以通过在[营养](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)数据集上运行查询来观察影响：

#### <a name="original"></a>原始

查询：

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c._ts ASC
```

索引策略：

```json
{

        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[]
}
```

**RU 费用： 44.28 RU**

#### <a name="optimized"></a>已优化

更新的查询（包含 `ORDER BY` 子句中的两个属性）：

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c.foodGroup, c._ts ASC
```

更新的索引策略：

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
        },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
    }

```

**RU 费用： 8.86 RU**

### <a name="optimize-join-expressions-by-using-a-subquery"></a>使用子查询优化 JOIN 表达式

多值子查询可以通过`JOIN`在每个 select-多个表达式之后（而不是在子句中的`WHERE`所有交叉联接之后）推送谓词来优化表达式。

请看下面的查询：

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0
AND n.nutritionValue < 10) AND s.amount > 1
```

**RU 费用： 167.62 RU**

对于此查询，索引将匹配其标记名称`infant formula` `nutritionValue`大于0且`amount`大于1的任何文档。 此处的 `JOIN` 表达式将针对应用任何筛选器之前的每个匹配文档，执行所有 tags、nutrients 和 servings 数组项的叉积计算。 然后，`WHERE` 子句将针对每个 `<c, t, n, s>` 元组应用筛选谓词。

例如，如果匹配文档每三个数组中的每个都有10个项，则它将扩展为 1 x 10 x 10 x 10 （即1000）元组。 此处使用子查询有助于筛选出联接的数组项，然后再加入下一个表达式。

此查询等效于前面的查询，但使用了子查询：

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**RU 费用： 22.17 RU**

假设标记数组中只有一个项与筛选器相匹配，并且 nutrients 和 servings 数组中只有五个项。 `JOIN`表达式将扩展到 1 x 1 x 5 x 5 = 25 项，而不是第一个查询中的1000项。

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>“已检索文档计数”等于“输出文档计数”的查询

如果**检索的文档数**约等于**输出文档计数**，则查询引擎不必扫描很多不必要的文档。 对于许多查询（如使用`TOP`关键字的查询），**检索到的文档计数**可能会超出**输出文档数**1。 您无需担心这一点。

### <a name="minimize-cross-partition-queries"></a>最小化跨分区查询

当请求单位和数据存储需求提高时，Azure Cosmos DB 将使用[分区](partitioning-overview.md)来扩展单个容器。 每个物理分区具有不同的独立索引。 如果查询具有与容器的分区键匹配的相等筛选器，则只需检查相关分区的索引。 此优化可减少查询所需的 ru 总数。

如果有大量预配的 ru （超过30000）或存储大量的数据（超过大约 100 GB），则可能会有足够大的容器来查看查询 RU 费用的显著降低。

例如，如果创建的容器具有分区键 foodGroup，则以下查询将只需要检查单个物理分区：

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

带有分区键的`IN`筛选器的查询将仅检查相关的物理分区，而不会 "扇出"：

```sql
SELECT *
FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

对分区键使用范围筛选器或对分区键没有任何筛选器的查询，将需要 "扇出"，并检查每个物理分区的结果索引：

```sql
SELECT *
FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT *
FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

### <a name="optimize-queries-that-have-filters-on-multiple-properties"></a>优化具有多个属性筛选器的查询

尽管具有多个属性的筛选器的查询通常使用范围索引，但如果它们可以从复合索引提供，则它们将更有效。 对于少量的数据，这种优化不会产生很大的影响。 但对于大量数据，这可能很有用。 对于每个组合索引，最多只能优化一个非相等性筛选器。 如果查询有多个非相等筛选器，请选择其中一个将使用该复合索引的筛选器。 其余部分将继续使用范围索引。 非相等性筛选器必须在组合索引中最后定义。 [了解有关复合索引的详细信息](index-policy.md#composite-indexes)。

下面是可以使用复合索引进行优化的查询的一些示例：

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts > 1575503264
```

下面是相关的组合索引：

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
                },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
}
```

## <a name="optimizations-that-reduce-query-latency"></a>降低查询延迟的优化

在许多情况下，如果查询延迟仍然太高，则 RU 费用可能是可接受的。 以下部分概述了降低查询延迟的技巧。 如果对同一个数据集多次运行同一个查询，该查询每次都会产生相同的 RU 开销。 但查询的延迟可能会因查询执行的不同而异。

### <a name="improve-proximity"></a>改善邻近性

与 Azure Cosmos DB 帐户不同的区域运行的查询将具有比在同一区域中运行时更高的延迟。 例如，如果您在台式计算机上运行代码，则应预计延迟大于或等于与 Azure Cosmos DB 在同一 Azure 区域内的虚拟机中的延迟（或更多）。 [在 Azure Cosmos DB 中全局分发数据](distribute-data-globally.md)是很简单的方法，以确保你的数据更接近于你的应用程序。

### <a name="increase-provisioned-throughput"></a>增大预配吞吐量

在 Azure Cosmos DB 中，预配的吞吐量以请求单位（ru）度量。 假设你有一个使用5个 ru 的吞吐量的查询。 例如，如果预配 1000 ru，则每秒可以运行查询200次。 如果尝试在没有足够的吞吐量的情况下运行查询，Azure Cosmos DB 将返回 HTTP 429 错误。 任何当前核心（SQL） API Sdk 在等待一小段时间后，都将自动重试该查询。 限制请求需要更长时间，因此增加预配的吞吐量可以提高查询延迟。 可以在 Azure 门户的 "**指标**" 边栏选项卡上观察[限制的请求总数](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors)。

### <a name="increase-maxconcurrency"></a>增大 MaxConcurrency

并行查询的工作原理是并行查询多个分区。 但是，与查询相关的每个分区集合中的数据会按顺序提取。 因此，如果您将 MaxConcurrency 设置为分区数，则您最有可能实现最高性能的查询，前提是所有其他系统条件保持不变。 如果不知道分区数，可以将 MaxConcurrency （或较早的 SDK 版本中的 MaxDegreesOfParallelism）设置为较高的数字。 系统会选择最小值（分区数、用户提供的输入）作为最大并行度。

### <a name="increase-maxbuffereditemcount"></a>增大 MaxBufferedItemCount

查询设计为当客户端正在处理当前结果批时预先提取结果。 预先获取有助于提高查询的整体延迟。 设置 MaxBufferedItemCount 将限制预提取结果的数量。 如果将此值设置为预期返回的结果数（或较高的数值），则查询可能会从预提取获得最大的收益。 如果将此值设置为-1，则系统将自动确定要缓冲的项目数。

## <a name="next-steps"></a>后续步骤
请参阅以下文章，了解有关如何针对每个查询度量 RUs、获取执行统计信息以优化查询的信息等：

* [使用 .NET SDK 获取 SQL 查询执行指标](profile-sql-api-query.md)
* [优化 Azure Cosmos DB 的查询性能](sql-api-sql-query-metrics.md)
* [.NET SDK 性能提示](performance-tips.md)
