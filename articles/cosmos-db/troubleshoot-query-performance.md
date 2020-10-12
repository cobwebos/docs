---
title: 排查使用 Azure Cosmos DB 时遇到的查询问题
description: 了解如何识别、诊断和排查 Azure Cosmos DB SQL 查询问题。
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 09/12/2020
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: a6833f9d59eca4c2f0b49dd70684ade900226aba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90089983"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>排查使用 Azure Cosmos DB 时遇到的查询问题

本文逐步说明排查 Azure Cosmos DB 中的查询问题的一般建议方法。 虽然不应将本文中所述的步骤视为针对潜在查询问题的完全防御方法，但我们在其中包含了最常见的性能提示。 应将本文用作起点，以排查 Azure Cosmos DB 核心 (SQL) API 中查询速度缓慢或费用较高的问题。 还可以使用[诊断日志](cosmosdb-monitor-resource-logs.md)来识别速度缓慢或消耗大量吞吐量的查询。

可对 Azure Cosmos DB 中的查询优化进行广泛分类：

- 可降低查询请求单位 (RU) 费用的优化
- 仅降低延迟的优化

几乎可以肯定，降低查询的 RU 费用还将降低延迟。

本文提供了可通过使用 [营养数据集](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)重新创建的示例。

## <a name="common-sdk-issues"></a>常见 SDK 问题

阅读本指南之前，考虑与查询引擎无关的常见 SDK 问题将很有帮助。

- 遵循这些 [SDK 性能提示](performance-tips.md)。
    - [.NET SDK 故障排除指南](troubleshoot-dot-net-sdk.md)
    - [Java SDK 故障排除指南](troubleshoot-java-sdk-v4-sql.md)
- SDK 允许为查询设置 `MaxItemCount`，但不能指定最小项计数。
    - 代码应处理从零到 `MaxItemCount` 的任意页大小。
- 有时，即使未来页上包含结果，查询也可能包含空页， 其原因可能包括：
    - SDK 可能正在执行多个网络调用。
    - 查询检索文档所花费的时间可能很长。
- 所有查询都包含一个继续标记，该标记将允许查询继续进行。 请确保完全耗尽查询。 详细了解如何 [处理多个结果页](sql-query-pagination.md#handling-multiple-pages-of-results)

## <a name="get-query-metrics"></a>获取查询指标

在 Azure Cosmos DB 中优化查询时，第一步始终是[获取查询的查询指标](profile-sql-api-query.md)。 也可以通过 Azure 门户获取这些指标。 在数据资源管理器中运行查询后，可在“结果”选项卡旁边看到查询指标：

:::image type="content" source="./media/troubleshoot-query-performance/obtain-query-metrics.png" alt-text="获取查询指标" lightbox="./media/troubleshoot-query-performance/obtain-query-metrics.png":::

获取查询指标后，将查询的“已检索文档计数”与“输出文档计数”进行比较 。 使用这种比较可以确定要在本文中查看的相关部分。

“已检索文档计数”是查询引擎需要加载的文档数。 “输出文档计数”是查询结果所需的文档数。 如果“已检索文档计数”明显大于“输出文档计数”，则表明查询中至少有一个部分无法使用索引并需要执行扫描 。

请参阅以下部分，了解适用于你的方案的相关查询优化。

### <a name="querys-ru-charge-is-too-high"></a>查询的 RU 费用过高

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>“已检索文档计数”明显大于“输出文档计数”

- [在索引策略中包含所需的路径。](#include-necessary-paths-in-the-indexing-policy)

- [了解哪些系统函数使用索引。](#understand-which-system-functions-use-the-index)

- [了解哪些聚合查询使用索引。](#understand-which-aggregate-queries-use-the-index)

- [优化同时具有筛选器和 ORDER BY 子句的查询。](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

- [使用子查询优化 JOIN 表达式。](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>“已检索文档计数”约等于“输出文档计数”

- [最大限度地减少跨分区查询。](#minimize-cross-partition-queries)

- [优化对多个属性具有筛选器的查询。](#optimize-queries-that-have-filters-on-multiple-properties)

- [优化同时具有筛选器和 ORDER BY 子句的查询。](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>查询的 RU 费用可接受，但延迟仍然过高

- [提高邻近度。](#improve-proximity)

- [增加预配的吞吐量。](#increase-provisioned-throughput)

- [增加 MaxConcurrency。](#increase-maxconcurrency)

- [增加 MaxBufferedItemCount。](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>“已检索文档计数”超过“输出文档计数”的查询

 “已检索文档计数”是查询引擎需要加载的文档数。 “输出文档计数”是查询返回的文档数。 如果“已检索文档计数”明显大于“输出文档计数”，则表明查询中至少有一个部分无法使用索引并需要执行扫描 。

下面是一个不完全由索引提供服务的扫描查询示例：

查询：

 ```sql
SELECT VALUE c.description
FROM c
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
 ```

查询指标：

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

“已检索文档计数”(60,951) 明显大于“输出文档计数”(7)，指示此查询导致了文档扫描 。 在本例中，系统函数 [UPPER()](sql-query-upper.md) 不使用索引。

### <a name="include-necessary-paths-in-the-indexing-policy"></a>在索引策略中包含所需的路径

索引策略应涵盖 `WHERE` 子句、`ORDER BY` 子句、`JOIN` 和大多数系统函数中包含的所有属性。 索引策略中指定的所需路径应与 JSON 文档中的属性相匹配。

> [!NOTE]
> Azure Cosmos DB 索引策略中的属性区分大小写

如果对 [nutrition](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) 数据集运行以下简单查询，你将注意到，在为 `WHERE` 子句中的属性编制索引时，RU 费用要低得多：

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

RU 费用：409.51 RU

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

RU 费用：2.98 RU

可以随时将属性添加到索引策略，而不会影响写入可用性或性能。 如果将新属性添加到索引，则使用此属性的查询将立即使用新的可用索引。 查询将在生成新索引时使用该索引。 因此，在重新生成索引时，查询结果可能会不一致。 如果为新属性编制索引，则在重新生成索引期间，仅使用现有索引的查询将不受影响。 你可以[跟踪索引转换进度](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-net-sdk-v3)。

### <a name="understand-which-system-functions-use-the-index"></a>了解哪些系统函数使用索引

如果表达式可以被转换为一系列字符串值，则该表达式可以使用索引， 否则不可使用索引。

下面是一些可使用索引的常用字符串函数的列表：

- STARTSWITH(str_expr1, str_expr2, bool_expr)  
- CONTAINS(str_expr, str_expr, bool_expr)
- LEFT(str_expr, num_expr) = str_expr
- SUBSTRING(str_expr, num_expr, num_expr) = str_expr，但前提是第一个 num_expr 为 0

下面是一些不使用索引且必须加载每个文档的常用系统函数：

| 系统函数                     | 优化意见             |
| --------------------------------------- |------------------------------------------------------------ |
| UPPER/LOWER                             | 不要使用系统函数来规范化数据以进行比较，而是在插入时规范化大小写。 诸如 ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` 的查询将变成 ```SELECT * FROM c WHERE c.name = 'BOB'```。 |
| 数学函数（非聚合） | 如果需要频繁计算查询中的某个值，请考虑在 JSON 文档中将此值存储为属性。 |

------

即使系统函数不使用索引，查询的其他部分也仍可以使用索引。

### <a name="understand-which-aggregate-queries-use-the-index"></a>了解哪些聚合查询使用索引

在大多数情况下，Azure Cosmos DB 中的聚合系统函数将使用索引。 但是，根据聚合查询中的筛选器或其他子句，可能需要查询引擎来加载大量文档。 通常情况下，查询引擎将首先应用相等性和范围筛选器。 应用这些筛选器后，查询引擎可以评估其他筛选器，并根据需要加载其余文档以计算聚合。

例如，给定以下两个示例查询，同时具有相等性和 `CONTAINS` 系统函数筛选器的查询总体上要比只具有 `CONTAINS` 系统函数筛选器的查询更加高效。 这是因为在需要为费用更高的 `CONTAINS` 筛选器加载文档之前，首先应用了相等性筛选器且相等性筛选器使用了索引。

仅具有 `CONTAINS` 筛选器的查询 - 较高的 RU 费用：

```sql
SELECT COUNT(1)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

同时具有相等性筛选器和 `CONTAINS` 筛选器的查询 - 较低的 RU 费用：

```sql
SELECT AVG(c._ts)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats" AND CONTAINS(c.description, "spinach")
```

下面是不会完全使用索引的聚合查询的其他示例：

#### <a name="queries-with-system-functions-that-dont-use-the-index"></a>具有系统函数且不使用索引的查询

应参阅相关[系统函数页](sql-query-system-functions.md)以查看其是否使用索引。

```sql
SELECT MAX(c._ts)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

#### <a name="aggregate-queries-with-user-defined-functionsudfs"></a>具有用户定义函数 (UDF) 的聚合查询

```sql
SELECT AVG(c._ts)
FROM c
WHERE udf.MyUDF("Sausages and Luncheon Meats")
```

#### <a name="queries-with-group-by"></a>具有 GROUP BY 的查询

随着 `GROUP BY` 子句中属性基数的增加，具有 `GROUP BY` 的查询的 RU 费用也将增加。 例如，在下面的查询中，查询的 RU 费用将随着数值唯一描述的增加而增加。

具有 `GROUP BY` 子句的聚合函数的 RU 费用将高于单独的聚合函数的 RU 费用。 在此示例中，查询引擎必须加载与 `c.foodGroup = "Sausages and Luncheon Meats"` 筛选器匹配的每个文档，因此 RU 费用预计会很高。

```sql
SELECT COUNT(1)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats"
GROUP BY c.description
```

如果计划频繁运行相同的聚合查询，则与运行单个查询相比，使用 [Azure Cosmos DB 更改源](change-feed.md)生成实时具体化视图可能更加高效。

### <a name="optimize-queries-that-have-both-a-filter-and-an-order-by-clause"></a>优化同时具有筛选器和 ORDER BY 子句的查询

虽然具有筛选器和 `ORDER BY` 子句的查询通常使用范围索引，但如果能够通过组合索引提供这些查询，这些查询将会更加高效。 除了修改索引策略以外，还应将组合索引中的所有属性添加到 `ORDER BY` 子句。 对查询进行的此更改可确保该查询使用组合索引。  可以通过对 [nutrition](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json) 数据集运行查询来观察影响：

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

RU 费用：44.28 RU

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

RU 费用：8.86 RU

### <a name="optimize-join-expressions-by-using-a-subquery"></a>使用子查询优化 JOIN 表达式

多值子查询可以通过在 `WHERE` 子句中的每个 select-many 表达式后面（而不是所有交叉联接后面）推送谓词，来优化 `JOIN` 表达式。

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

RU 费用：167.62 RU

对于此查询，索引将匹配包含名为 `infant formula`、`nutritionValue` 大于 0 且 `amount` 大于 1 的标记的任何文档。 此处的 `JOIN` 表达式将在应用任何筛选器之前，对每个匹配文档执行 tags、nutrients 和 servings 数组的所有项的叉积计算。 然后，`WHERE` 子句将对每个 `<c, t, n, s>` 元组应用筛选谓词。

例如，如果匹配文档在这三个数组中的每个数组中都具有 10 个项，则该文档将扩展为 1 x 10 x 10 x 10（即 1000）个元组。 在此处使用子查询可帮助在与下一个表达式联接之前，筛选出联接的数组项。

此查询等效于前面的查询，但使用了子查询：

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

RU 费用：22.17 RU

假设 tags 数组中只有一个项与筛选器相匹配，而 nutrients 和 servings 数组各有 5 个项。 那么，`JOIN` 表达式将扩展为 1 x 1 x 5 x 5 = 25 个项，而不是第一个查询中的 1000 个项。

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>“已检索文档计数”等于“输出文档计数”的查询

如果“已检索文档计数”约等于“输出文档计数”，则查询引擎无需扫描许多不必要的文档 。 对于许多查询（例如，使用 `TOP` 关键字的查询）而言，“已检索文档计数”可能要比“输出文档计数”多 1 。 你无需为此担心。

### <a name="minimize-cross-partition-queries"></a>最大限度地减少跨分区查询

当请求单位和数据存储需求增加时，Azure Cosmos DB 将使用[分区](partitioning-overview.md)来扩展单个容器。 每个物理分区具有不同的独立索引。 如果查询具有匹配容器分区键的相等性筛选器，则你只需检查相关分区的索引。 这种优化可以减少查询所需的 RU 总数。

如果有大量预配的 RU（超过 30,000）或存储了大量数据（约超过 100 GB），那么你可能需要有一个足够大的容器，以查看查询 RU 费用的显著降低。

例如，如果使用分区键 foodGroup 创建容器，则以下查询只需检查单个物理分区：

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

具有带分区键的 `IN` 筛选器的查询将仅检查相关的物理分区，而不会“扇出”：

```sql
SELECT *
FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

对分区键使用范围筛选器或者对分区键不使用任何筛选器的查询将需要“扇出”，并检查每个物理分区的索引，以查看结果：

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

### <a name="optimize-queries-that-have-filters-on-multiple-properties"></a>优化对多个属性具有筛选器的查询

虽然对多个属性具有筛选器的查询通常使用范围索引，但如果能够通过组合索引提供这些查询，这些查询将会更加高效。 对于少量数据而言，这种优化不会产生明显影响。 但对于大量数据，它可能非常有用。 对于每个组合索引，最多只能优化一个非相等性筛选器。 如果查询具有多个非相等性筛选器，请选取其中一个将使用组合索引的筛选器。 其余筛选器将继续使用范围索引。 非相等性筛选器必须在组合索引中最后定义。 [详细了解组合索引](index-policy.md#composite-indexes)。

下面是一些可以通过组合索引进行优化的查询示例：

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

## <a name="optimizations-that-reduce-query-latency"></a>可降低查询延迟的优化

在许多情况下，当查询延迟仍然过高时，RU 费用是可接受的。 以下部分概述了降低查询延迟的提示。 如果对同一数据集多次运行同一查询，则该查询每次都会产生相同的 RU 费用。 但是，每次执行查询时，查询延迟可能各不相同。

### <a name="improve-proximity"></a>提高邻近度

在非 Azure Cosmos DB 帐户的区域中运行的查询，比在同一区域中运行的查询的延迟更高。 例如，如果在台式机上运行代码，则延迟比从 Azure Cosmos DB 所在的同一 Azure 区域中的某个虚拟机上运行查询要高出几十或几百毫秒（或更高）。 可以轻松[在 Azure Cosmos DB 中全局分发数据](distribute-data-globally.md)，以确保将数据置于更靠近应用的位置。

### <a name="increase-provisioned-throughput"></a>增加预配的吞吐量

在 Azure Cosmos DB 中，预配的吞吐量以请求单位 (RU) 计量。 假设某个查询消耗 5 RU 吞吐量。 如果预配 1000 RU，则每秒可以运行该查询 200 次。 如果在没有足够可用吞吐量的情况下尝试运行查询，Azure Cosmos DB 将返回 HTTP 429 错误。 任何当前核心 (SQL) API SDK 在等待一小段时间后，都将自动重试该查询。 受限制的请求需要花费更长的时间，因此增加预配的吞吐量可以改进查询延迟。 可以在 Azure 门户的“指标”边栏选项卡上观察[受限制的请求总数](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors)。

### <a name="increase-maxconcurrency"></a>增加 MaxConcurrency

并行查询的方式是并行查询多个分区。 但就查询本身而言，会按顺序提取单个已分区集合中的数据。 因此，如果将 MaxConcurrency 设置为分区数，则最有可能实现查询的最高性能，但前提是所有其他系统条件仍保持不变。 如果不知道分区数，可将 MaxConcurrency（或旧 SDK 版本中的 MaxDegreesOfParallelism）设置为较大的数字。 系统会选择最小值（分区数、用户提供的输入）作为最大并行度。

### <a name="increase-maxbuffereditemcount"></a>增加 MaxBufferedItemCount

查询专用于在客户端处理当前一批结果时预提取结果。 预提取可帮助改进查询的总体延迟。 设置 MaxBufferedItemCount 会限制预提取结果的数目。 如果将此值设置为预期返回的结果数（或更大的数目），则查询从预提取中获益的程度将最大。 如果将此值设置为 -1，则系统将自动确定要缓冲的项数。

## <a name="next-steps"></a>后续步骤
参阅以下文章，了解有关如何按查询度量 RU、获取执行统计信息以优化查询等信息：

* [使用 .NET SDK 获取 SQL 查询执行指标](profile-sql-api-query.md)
* [优化 Azure Cosmos DB 的查询性能](sql-api-sql-query-metrics.md)
* [.NET SDK 性能提示](performance-tips.md)
