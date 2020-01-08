---
title: 使用执行配置文件计算 Azure Cosmos DB Gremlin API 中的查询
description: 了解如何使用执行配置文件步骤对 Gremlin 查询进行故障排除和改进。
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: lbosq
ms.openlocfilehash: 5705ef4fb6aa895009d554617c968543cc3fcd63
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441854"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>如何使用执行配置文件步骤评估 Gremlin 查询

本文概述了如何使用 Azure Cosmos DB Gremlin API graph 数据库的执行配置文件步骤。 此步骤提供有关故障排除和查询优化的相关信息，并且它与可针对 Cosmos DB Gremlin API 帐户执行的任何 Gremlin 查询兼容。

若要使用此步骤，只需在 Gremlin 查询的末尾追加 `executionProfile()` 函数调用。 **将执行您的 Gremlin 查询**，并且该操作的结果将使用查询执行配置文件返回 JSON 响应对象。

例如：

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

调用 `executionProfile()` 步骤后，响应将为一个 JSON 对象，该对象包括已执行的 Gremlin 步骤、所用的总时间以及语句所生成的 Cosmos DB 运行时运算符的数组。

> [!NOTE]
> Apache Tinkerpop 规范中未定义执行配置文件的此实现。 它特定于 Azure Cosmos DB Gremlin API 的实现。


## <a name="response-example"></a>响应示例

下面是将返回的输出的批注示例：

> [!NOTE]
> 此示例将注释注释，说明响应的一般结构。 实际的 executionProfile 响应不会包含任何注释。

```json
[
  {
    // The Gremlin statement that was executed.
    "gremlin": "g.V('mary').out().executionProfile()",

    // Amount of time in milliseconds that the entire operation took.
    "totalTime": 28,

    // An array containing metrics for each of the steps that were executed. 
    // Each Gremlin step will translate to one or more of these steps.
    // This list is sorted in order of execution.
    "metrics": [
      {
        // This operation obtains a set of Vertex objects.
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetVertices",
        "time": 24,
        "annotations": {
          "percentTime": 85.71
        },
        "counts": {
          "resultCount": 2
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 2,
            "size": 696,
            "time": 0.4
          }
        ]
      },
      {
        // This operation obtains a set of Edge objects. 
        // Depending on the query, these might be directly adjacent to a set of vertices, 
        // or separate, in the case of an E() query.
        //
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetEdges",
        "time": 4,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 1,
            "size": 419,
            "time": 0.67
          }
        ]
      },
      {
        // This operation obtains the vertices that a set of edges point at.
        // The metrics include: time, percentTime of total execution time and resultCount.
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        // This operation represents the serialization and preparation for a result from 
        // the preceding graph operations. The metrics include: time, percentTime of total 
        // execution time and resultCount.
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

> [!NOTE]
> ExecutionProfile 步骤将执行 Gremlin 查询。 这包括 `addV` 或 `addE`步骤，这将导致创建和提交查询中指定的更改。 因此，Gremlin 查询生成的请求单位也会收取费用。

## <a name="execution-profile-response-objects"></a>执行配置文件响应对象

ExecutionProfile （）函数的响应将生成具有以下结构的 JSON 对象的层次结构：
  - **Gremlin operation 对象**：表示执行的整个 Gremlin 操作。 包含以下属性。
    - `gremlin`：已执行的显式 Gremlin 语句。
    - `totalTime`：执行中的步骤执行所用的时间（以毫秒为单位）。 
    - `metrics`：一个数组，其中包含为满足查询而执行的每个 Cosmos DB 运行时运算符。 此列表按执行顺序排序。
    
  - **Cosmos DB 运行时运算符**：表示整个 Gremlin 操作的每个组件。 此列表按执行顺序排序。 每个对象都包含以下属性：
    - `name`：操作员的名称。 这是计算和执行的步骤类型。 请参阅下表中的详细信息。
    - `time`：给定操作员花费的时间量（以毫秒为单位）。
    - `annotations`：包含特定于已执行的操作员的其他信息。
    - `annotations.percentTime`：执行特定运算符所需的总时间的百分比。
    - `counts`：通过此运算符从存储层返回的对象数。 此值包含在中的 `counts.resultCount` 标量值中。
    - `storeOps`：表示可跨一个或多个分区的存储操作。
    - `storeOps.fanoutFactor`：表示此特定存储操作访问的分区数。
    - `storeOps.count`：表示此存储操作返回的结果数。
    - `storeOps.size`：表示给定存储操作的结果的大小（以字节为单位）。

Cosmos DB Gremlin 运行时运算符|Description
---|---
`GetVertices`| 此步骤从持久性层获取一组依据的对象。 
`GetEdges`| 此步骤将获取与一组顶点相邻的边缘。 此步骤可能会导致一个或多个存储操作。
`GetNeighborVertices`| 此步骤获取连接到一组边的顶点。 边缘包含其源顶点和目标顶点的分区键和 ID。
`Coalesce`| 执行 `coalesce()` Gremlin 步骤时，此步骤将计算两个操作的计算结果。
`CartesianProductOperator`| 此步骤将计算两个数据集之间的笛卡尔积。 通常在使用 `to()` 或 `from()` 的谓词时执行。
`ConstantSourceOperator`| 此步骤将计算一个表达式以生成常量值。
`ProjectOperator`| 此步骤使用前面操作的结果准备并序列化响应。
`ProjectAggregation`| 此步骤准备并序列化聚合操作的响应。

> [!NOTE]
> 添加新的运算符时，此列表将继续更新。

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>有关如何分析执行配置文件响应的示例

下面是可以使用执行配置文件响应来找出的常见优化示例：
  - 盲扇出查询。
  - 未筛选查询。

### <a name="blind-fan-out-query-patterns"></a>盲扇出查询模式

假设已**分区关系图**中的以下执行配置文件响应：

```json
[
  {
    "gremlin": "g.V('tt0093640').executionProfile()",
    "totalTime": 46,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 46,
        "annotations": {
          "percentTime": 100
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 5,
            "count": 1,
            "size": 589,
            "time": 75.61
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

以下结论可以通过它进行：
- 查询是单个 ID 查找，因为 Gremlin 语句遵循模式 `g.V('id')`。
- 从 `time` 的指标中判断，此查询的延迟似乎很高，因为[单个点读取操作](https://docs.microsoft.com/azure/cosmos-db/introduction#guaranteed-low-latency-at-99th-percentile-worldwide)的延迟10ms。
- 如果查找 `storeOps` 对象，我们可以看到 `fanoutFactor` 是 `5`的，这意味着此操作访问了[5 个分区](https://docs.microsoft.com/azure/cosmos-db/partition-data)。

作为此分析的结论，我们可以确定第一个查询所访问的分区是否比所需的更多。 可以通过在查询中将分区键指定为谓词来解决此问题。 这将导致延迟降低，每次查询的成本更低。 有关详细信息，请参阅[图形分区](graph-partitioning.md)。 `g.V('tt0093640').has('partitionKey', 't1001')`更好的查询。

### <a name="unfiltered-query-patterns"></a>未筛选的查询模式

比较以下两个执行配置文件的响应。 为简单起见，这些示例使用单个分区关系图。

此第一个查询将检索具有标签 `tweet` 的所有顶点，然后获取其相邻顶点：

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').out().executionProfile()",
    "totalTime": 42,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 31,
        "annotations": {
          "percentTime": 73.81
        },
        "counts": {
          "resultCount": 30
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 13,
            "size": 6819,
            "time": 1.02
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 6,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 20,
            "size": 7950,
            "time": 1.98
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 11.9
        },
        "counts": {
          "resultCount": 20
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.19
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 20
        }
      }
    ]
  }
]
```

请注意相同查询的配置文件，但现在还有其他筛选器 `has('lang', 'en')`，然后浏览相邻的顶点：

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').has('lang', 'en').out().executionProfile()",
    "totalTime": 14,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 14,
        "annotations": {
          "percentTime": 58.33
        },
        "counts": {
          "resultCount": 11
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 11,
            "size": 4807,
            "time": 1.27
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 18,
            "size": 7159,
            "time": 1.7
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.01
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 18
        }
      }
    ]
  }
]
```

这两个查询的结果相同，但第一个查询需要更多请求单位，因为它需要在查询相邻项之前迭代更大的初始数据集。 从两个响应中比较以下参数时，可以看到此行为的指示器：
- 第一个响应中的 `metrics[0].time` 值较高，这表示此单个步骤需要更长的时间才能解决。
- 在第一个响应中，`metrics[0].counts.resultsCount` 值更高，这表示初始工作数据集较大。

## <a name="next-steps"></a>后续步骤
* 了解 Azure Cosmos DB 中[支持的 Gremlin 功能](gremlin-support.md)。 
* [在 Azure Cosmos DB 中了解有关 GREMLIN API](graph-introduction.md)的详细信息。
