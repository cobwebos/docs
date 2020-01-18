---
title: 使用 Azure Cosmos DB 排查查询问题
description: 了解如何识别、诊断 Azure Cosmos DB SQL 查询问题并对其进行故障排除。
author: ginamr
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 01/14/2020
ms.author: girobins
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: 5f4728c4b604c606d12edcc7a00879b31e54bc85
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264265"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>使用 Azure Cosmos DB 排查查询问题

本文逐步讲解 Azure Cosmos DB 中用于排查查询问题的常规建议方法。 虽然本文档中所述的步骤不应被视为可能的查询问题的 "全部捕获"，但我们在此处提供了最常见的性能提示。 在 Azure Cosmos DB 的核心（SQL） API 中，你应使用本文档来解决速度缓慢或开销较高的查询。 你还可以使用[诊断日志](cosmosdb-monitor-resource-logs.md)来确定缓慢或消耗大量吞吐量的查询。

您可以在 Azure Cosmos DB 中广泛地分类查询优化：用于减少查询的请求单位（RU）费用的优化，以及只是减少延迟的优化。 通过减少查询的 RU 费用，几乎当然还会降低延迟。

本文档将使用可以使用[营养](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)数据集重新创建的示例。

### <a name="obtaining-query-metrics"></a>获取查询度量值：

在 Azure Cosmos DB 中优化查询时，第一步是始终[获取查询的查询度量值](profile-sql-api-query.md)。 还可通过 Azure 门户获取这些内容，如下所示：

[![获取查询指标](./media/troubleshoot-query-performance/obtain-query-metrics.png)](./media/troubleshoot-query-performance/obtain-query-metrics.png#lightbox)

获取查询度量值后，请将检索到的文档计数与查询的输出文档计数进行比较。 使用此比较来确定下面参考的相关部分。

检索的文档数是查询加载所需的文档数。 输出文档计数是查询结果所需的文档数。 如果检索的文档计数明显高于输出文档计数，则表明查询中至少有一个部分无法利用索引，需要执行扫描。

你可以参考以下部分来了解适用于你的方案的相关查询优化：

### <a name="querys-ru-charge-is-too-high"></a>查询的 RU 费用太高

#### <a name="retrieved-document-count-is-significantly-greater-than-output-document-count"></a>检索的文档计数明显大于输出文档计数

- [在索引策略中包含必要的路径](#include-necessary-paths-in-the-indexing-policy)

- [了解哪些系统函数使用索引](#understand-which-system-functions-utilize-the-index)

- [使用筛选器和 ORDER BY 子句的查询](#queries-with-both-a-filter-and-an-order-by-clause)

- [使用子查询优化联接表达式](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>检索的文档计数约等于输出文档计数

- [避免跨分区查询](#avoid-cross-partition-queries)

- [多个属性的筛选器](#filters-on-multiple-properties)

- [使用筛选器和 ORDER BY 子句的查询](#queries-with-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>查询的 RU 费用是可接受的，但延迟仍然太高

- [提高邻近性](#improve-proximity)

- [增加预配的吞吐量](#increase-provisioned-throughput)

- [增加 MaxConcurrency](#increase-maxconcurrency)

- [增加 MaxBufferedItemCount](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>检索到的文档计数超出输出文档计数的查询

 检索的文档数是查询加载所需的文档数。 输出文档计数是查询结果所需的文档数。 如果检索的文档计数明显高于输出文档计数，则表明查询中至少有一个部分无法利用索引，需要执行扫描。

 下面是一个扫描查询的示例，该查询并不完全提供索引。

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

检索的文档计数（60951）明显大于输出文档计数（7），因此此查询需要执行扫描。 在这种情况下，系统函数[UPPER （）](sql-query-upper.md)不使用索引。

## <a name="include-necessary-paths-in-the-indexing-policy"></a>在索引策略中包含必要的路径

索引策略应涵盖 `WHERE` 子句、`ORDER BY` 子句、`JOIN`和大多数系统函数中包含的所有属性。 索引策略中指定的路径应与 JSON 文档中的属性匹配（区分大小写）。

如果在[营养](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)数据集上运行一个简单的查询，则在为 `WHERE` 子句中的属性编制索引时，将观察到更低的 RU 费用。

### <a name="original"></a>原始

查询：

```sql
SELECT * FROM c WHERE c.description = "Malabar spinach, cooked"
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

**Ru 费用：** 409.51 RU

### <a name="optimized"></a>优化

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

**Ru 费用：** 2.98 RU

你可以随时向索引策略添加其他属性，而不会影响写入可用性或性能。 如果将新属性添加到索引中，则使用此属性的查询会立即利用新的可用索引。 生成查询时，查询将使用新索引。 因此，查询结果可能会不一致，因为正在重新生成索引。 如果为新属性编制了索引，则在重新生成索引时，仅使用现有索引的查询将不会受到影响。 您可以[跟踪索引转换进度](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy#use-the-net-sdk-v3)。

## <a name="understand-which-system-functions-utilize-the-index"></a>了解哪些系统函数使用索引

如果表达式可以转换为一系列字符串值，则它可以使用索引，否则不可使用索引。

下面是可以使用索引的字符串函数的列表：

- STARTSWITH(str_expr, str_expr)
- LEFT(str_expr, num_expr) = str_expr
- SUBSTRING(str_expr, num_expr, num_expr) = str_expr，但前提是第一个 num_expr 为 0

下面列出了一些不使用索引并必须加载每个文档的常见系统函数：

| **系统函数**                     | **优化建议**             |
| --------------------------------------- |------------------------------------------------------------ |
| CONTAINS                                | 使用 Azure 搜索进行全文搜索                        |
| 上限/下限                             | 不要使用 system 函数在每次进行比较时规范化数据，而是在插入时标准化大小写。 然后，诸如 ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` 这样的查询就会成为 ```SELECT * FROM c WHERE c.name = 'BOB'``` |
| 数学函数（非聚合） | 如果需要频繁计算查询中的值，请考虑将此值存储为 JSON 文档中的属性。 |

------

尽管系统函数不使用索引，查询的其他部分仍可能利用索引。

## <a name="queries-with-both-a-filter-and-an-order-by-clause"></a>使用筛选器和 ORDER BY 子句的查询

虽然带有筛选器的查询和 `ORDER BY` 子句通常使用范围索引，但如果它们可以从复合索引提供，则它们将更有效。 除了修改索引策略之外，还应将组合索引中的所有属性添加到 `ORDER BY` 子句。 此查询修改将确保它利用复合索引。  可以通过在[营养](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)数据集上运行查询来观察影响。

### <a name="original"></a>原始

查询：

```sql
SELECT * FROM c WHERE c.foodGroup = “Soups, Sauces, and Gravies” ORDER BY c._ts ASC
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

**Ru 费用：** 44.28 RU

### <a name="optimized"></a>优化

更新的查询（包括 `ORDER BY` 子句中的两个属性）：

```sql
SELECT * FROM c 
WHERE c.foodGroup = “Soups, Sauces, and Gravies” 
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

**Ru 费用：** 8.86 RU

## <a name="optimize-join-expressions-by-using-a-subquery"></a>使用子查询优化联接表达式
多值子查询可以优化 `JOIN` 表达式，方法是：在每个 select-多个表达式后推送谓词，而不是在 `WHERE` 子句中的所有交叉联接后推送。

请考虑下列查询：

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0
AND n.nutritionValue < 10) AND s.amount > 1
```

**Ru 费用：** 167.62 RU

对于此查询，该索引将匹配其标记名称为 "幼儿 formula"、nutritionValue 大于0且为大于1的值的任何文档。 此处的 `JOIN` 表达式将在应用任何筛选器之前，对每个匹配的文档执行标记、nutrients 和 servings 数组的所有项的叉积。 然后，`WHERE` 子句将对每个 `<c, t, n, s>` 元组应用筛选器谓词。

例如，如果匹配文档每三个数组中的每个都有10个项，则它将扩展为 1 x 10 x 10 x 10 （即1000）元组。 使用此处的子查询，可以在联接到下一个表达式之前，帮助筛选出联接的数组项。

此查询等效于前面的查询，但使用子查询：

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**Ru 费用：** 22.17 RU

假设标记数组中只有一个项与筛选器匹配，并且 nutrients 和 servings 数组都有五项。 然后，`JOIN` 表达式将扩展到 1 x 1 x 5 x 5 = 25 项，而不是第一个查询中的1000项。

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>检索到的文档计数等于输出文档计数的查询

如果检索的文档数约等于输出文档计数，则意味着查询不必扫描很多不必要的文档。 对于许多查询，如使用 TOP 关键字的查询，检索到的文档计数可能会超出输出文档数1。 这不会导致问题。

## <a name="avoid-cross-partition-queries"></a>避免跨分区查询

Azure Cosmos DB 使用[分区](partitioning-overview.md)来缩放单个容器，因为请求单元和数据存储需求增加。 每个物理分区都有单独的独立索引。 如果查询具有与容器的分区键匹配的相等筛选器，则只需检查相关分区的索引。 此优化可减少查询所需的 RU 总数。

如果有大量预配 RU （超过30000）或存储了大量数据（超过 100 GB），则很可能有足够大的容器来查看查询 RU 费用的显著降低。

例如，如果创建的容器具有分区键 foodGroup，则以下查询只需要检查单个物理分区：

```sql
SELECT * FROM c
WHERE c.foodGroup = “Soups, Sauces, and Gravies” and c.description = "Mushroom, oyster, raw"
```

这些查询还会通过在查询中包含分区键进行优化：

```sql
SELECT * FROM c
WHERE c.foodGroup IN(“Soups, Sauces, and Gravies”, “"Vegetables and Vegetable Products”) and  c.description = "Mushroom, oyster, raw"
```

对分区键使用范围筛选器或对分区键没有任何筛选器的查询，将需要 "扇出"，并检查每个物理分区的结果索引。

```sql
SELECT * FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT * FROM c
WHERE c.foodGroup > “Soups, Sauces, and Gravies” and c.description = "Mushroom, oyster, raw"
```

## <a name="filters-on-multiple-properties"></a>多个属性的筛选器

虽然在多个属性上使用筛选器的查询通常使用范围索引，但如果它们可以从复合索引提供，则它们将更有效。 对于少量的数据，这种优化不会产生很大的影响。 但对于大量数据，它可能很有用。 对于每个复合索引，最多只能优化一个非相等筛选器。 如果你的查询具有多个非相等筛选器，你应选取其中一个将使用该组合索引的筛选器。 余数将继续使用范围索引。 非相等筛选器必须在复合索引中最后定义。 [了解有关复合索引的详细信息](index-policy.md#composite-indexes)

下面是一些可以使用复合索引进行优化的查询示例：

```sql
SELECT * FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT * FROM c
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

## <a name="optimizations-that-reduce-query-latency"></a>减少查询延迟的优化：

在许多情况下，RU 费用可能是可接受的，但查询延迟仍然太高。 以下部分概述了降低查询延迟的技巧。 如果在同一数据集上多次运行相同的查询，每次都将具有相同的 RU 费用。 但是，查询的延迟可能会因查询执行而异。

## <a name="improve-proximity"></a>提高邻近性

从 Azure Cosmos DB 帐户之外的其他区域中运行的查询将具有比在同一区域中运行的延迟更高的延迟。 例如，如果您在台式计算机上运行代码，则应预计延迟为数十或几百（或更多）毫秒，大于在 Azure Cosmos DB 的同一 Azure 区域中的虚拟机。 [在 Azure Cosmos DB 中全局分发数据](distribute-data-globally.md)是很简单的方法，以确保你的数据更接近于你的应用程序。

## <a name="increase-provisioned-throughput"></a>增加预配的吞吐量

在 Azure Cosmos DB 中，预配的吞吐量以请求单位（RU）度量。 假设你有一个使用 5 RU 吞吐量的查询。 例如，如果你预配 1000 RU，则每秒可以运行查询200次。 如果尝试在没有足够的吞吐量的情况下运行查询，Azure Cosmos DB 将返回 HTTP 429 错误。 任何当前核心（SQL） API sdk 将在等待一小段时间后自动重试该查询。 限制请求需要更长时间，因此增加预配的吞吐量可以提高查询延迟。 可以在 Azure 门户的 "指标" 边栏选项卡中查看[请求限制请求总数](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors)。

## <a name="increase-maxconcurrency"></a>增加 MaxConcurrency

并行查询的工作方式是并行查询多个分区。 但就查询本身而言，会按顺序提取单个已分区集合中的数据。 因此，将 MaxConcurrency 调整到分区数最可能实现最高性能的查询，前提是所有其他系统条件保持不变。 如果不知道分区数，可以将 MaxConcurrency （或旧版 sdk 版本中的 MaxDegreesOfParallelism）设置为较高的数值，系统会选择最小值（分区数、用户提供的输入）作为最大并行度。

## <a name="increase-maxbuffereditemcount"></a>增加 MaxBufferedItemCount

查询旨在预提取结果，而客户端正在处理当前批结果。 预提取帮助改进查询中的的总体延迟。 设置 MaxBufferedItemCount 将限制预提取结果的数量。 通过将此值设置为预期返回的结果数（或较高的数值），查询可以从预提取获得最大的收益。 如果将此值设置为-1，则系统可以自动确定要缓冲的项目数。

## <a name="next-steps"></a>后续步骤
请参阅以下文档，了解如何根据查询度量每个查询，获取执行统计信息来优化查询，以及执行其他操作：

* [使用 .NET SDK 获取 SQL 查询执行指标](profile-sql-api-query.md)
* [优化 Azure Cosmos DB 的查询性能](sql-api-sql-query-metrics.md)
* [.NET SDK 性能提示](performance-tips.md)
