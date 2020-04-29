---
title: Azure Cosmos DB Gremlin API 中的数据分区
description: 了解如何在 Azure Cosmos DB 中使用分区图形。 本文还介绍了分区图形的要求和最佳做法。
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 44d3b7c2b9e23b90f696162747d9728b18fb7d3f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "77623373"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中使用分区图形

Azure Cosmos DB 中 Gremlin API 的重要功能之一是通过横向缩放处理大规模图形。 容器可以在存储和吞吐量方面独立缩放。 可以在 Azure Cosmos DB 中创建自动缩放的容器以存储图形数据。 数据根据指定的分区键自动均衡  。

如果容器预计存储的大小超过 20 GB，或者要分配超过10000个请求单位/秒（ru），**则需要进行分区**。 [Azure Cosmos DB 分区机制](partition-data.md)中的相同常规原则也适用，下面介绍了一些特定于图形的优化。

![图形分区。](./media/graph-partitioning/graph-partitioning.png)

## <a name="graph-partitioning-mechanism"></a>图形分区机制

以下指南介绍了 Azure Cosmos DB 中分区策略的运作方式：

- 顶点和边缘作为 JSON 文档存储  。

- **顶点需要分区键**。 此键通过哈希算法确定在哪个分区中存储顶点。 创建新容器时将定义分区键属性名称，其格式为 `/partitioning-key-name`。

- **边缘连同其源顶点一起存储**。 换句话说，对于每个顶点，其分区键定义它们与其传出边缘一起存储的位置。 进行此优化是为了避免在图形查询中使用 `out()` 基数时出现跨分区查询。

- **边缘包含对其指向的顶点的引用**。 所有边缘在存储时，都有分区键以及边缘指向的顶点的 ID。 此计算使得所有 `out()` 方向查询始终是带范围的分区查询，而不是跨分区的盲查询。 

- **图形查询需要指定分区键**。 若要充分利用 Azure Cosmos DB 中的水平分区，选择单个顶点时应尽量指定分区键。 下面是用于在分区图形中选择一个或多个顶点的查询：

    - 在 Gremlin API 中，不支持将 `/id` 和 `/label` 作为容器的分区键。


    - 根据 ID 选择顶点，然后**使用 `.has()` 步骤指定分区键属性**： 
    
        ```java
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - 通过**指定包含分区键值和 ID 的元组**选择顶点： 
    
        ```java
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - 指定**分区键值和 ID 的元组数组**：
    
        ```java
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - 选择一组带 ID 的顶点并**指定分区键值列表**： 
    
        ```java
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

    - 在查询开始时使用**分区策略**，并指定一个分区，用于 Gremlin 查询余下部分的范围： 
    
        ```java
        g.withStrategies(PartitionStrategy.build().partitionKey('partitionKey').readPartitions('partitionKey_value').create()).V()
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>使用分区图形时的最佳做法

将分区图形用于无限制容器时，请使用以下指南以确保性能和可伸缩性：

- **查询顶点时始终指定分区键值**。 从已知的分区获取顶点是一种实现性能的方法。 所有后续的邻接操作将始终局限于某个分区，因为边缘包含其目标顶点的引用 ID 和分区键。

- **查询边缘时尽量使用传出方向**。 如上所述，边缘连同其源顶点一起朝传出方向存储。 在设计数据和查询时考虑了此模式时，则可以最大程度地减少出现跨分区查询的可能性。 相反，`in()` 查询将始终是开销巨大的扇出查询。

- **选择可将数据均匀分配到不同分区的分区键**。 此决策在很大程度上取决于解决方案的数据模型。 请在 [Azure Cosmos DB 中的分区和缩放](partition-data.md)中详细了解如何创建适当的分区键。

- 优化查询，以获取分区边界内的数据  。 最佳的分区策略与查询模式相符。 从单个分区获取数据的查询可提供最佳性能。

## <a name="next-steps"></a>后续步骤

接下来可以继续阅读以下文章：

* 了解 [Azure Cosmos DB 中的分区和缩放](partition-data.md)。
* 了解 [Gremlin API 中的 Gremlin 支持](gremlin-support.md)。
* 了解 [Gremlin API 简介](graph-introduction.md)。
