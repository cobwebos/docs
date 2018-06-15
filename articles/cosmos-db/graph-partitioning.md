---
title: 图形 API 分区 | Microsoft Docs
description: 了解如何在 Azure Cosmos DB 中使用分区图形。
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.devlang: na
ms.topic: conceptual
ms.date: 02/28/2018
ms.author: lbosq
ms.openlocfilehash: e2658c01ea3a31efdcf2fef3bb9b56df536de295
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34795646"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中使用分区图形

Azure Cosmos DB 中图形 API 的重要功能之一是通过横向可缩放性处理大规模图形。 此过程是通过 [Azure Cosmos DB 中的分区功能](partition-data.md#how-does-partitioning-work)实现的。该功能利用在存储和吞吐量方面可独立缩放的集合（也称为容器）。 Azure Cosmos DB 支持跨所有 API 的以下容器类型：

- **固定集合**：这些集合可以存储大小高达 10 GB、每秒最多可分配 10,000 个请求单位的图形数据库。 若要创建固定集合，不需要在数据中指定分区键属性。

- **无限制集合**：这些集合可以自动扩展，以通过水平分区存储超出 10-GB 限制的图形。 每个分区将存储 10 GB 数据，而数据将会根据**指定的分区键**（使用无限制集合时必需的参数）自动均衡。 此类容器几乎可以存储无限的数据大小，最多允许每秒 100,000 个请求单位，甚至可以[联系支持人员](https://aka.ms/cosmosdbfeedback?subject=Cosmos%20DB%20More%20Throughput%20Request)请求提高吞吐量。

本文档将会介绍有关图形数据库分区方式的具体信息，以及分区对顶点（或节点）和边缘的影响。

## <a name="requirements-for-partitioned-graph"></a>分区图形的要求

下面是在创建分区图形容器时需要了解的详细信息：
- 如果集合的大小预期会超过 10 GB 和/或分配的每秒请求单位数 (RU/s) 需要超过 10,000，则**必须设置分区**。
- **顶点和边缘作为 JSON 文档存储**在 Azure Cosmos DB 图形 API 容器的后端中。
- **顶点需要分区键**。 此键通过哈希算法确定在哪个分区中存储顶点。 此分区键的名称是不包含空格或特殊字符的单字字符串，是创建新集合时在门户中使用 `/partitioning-key-name` 格式定义的。
- **边缘连同其源顶点一起存储**。 换而言之，对于每个顶点，其分区键将该顶点要连同其传出边缘一起存储在哪个位置。 这样做是为了避免在图形查询中使用 `out()` 基数时出现跨分区查询。
- **图形查询需要指定分区键**。 若要充分利用 Azure Cosmos DB 中的水平分区，选择单个顶点时应尽量指定分区键。 下面是用于在分区图形中选择一个或多个顶点的查询：

    - 根据 ID 选择顶点，然后**使用 `.has()` 步骤指定分区键属性**： 
    
        ```
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - 通过**指定包含分区键值和 ID 的元组**选择顶点： 
    
        ```
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - 指定**分区键值和 ID 的元组数组**：
    
        ```
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - 选择一组顶点并**指定分区键值列表**： 
    
        ```
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>使用分区图形时的最佳做法

下面是在无限制集合中使用分区图形时为了确保获得最高效性能和可伸缩性而应该遵守的准则：
- **查询顶点时始终指定分区键值**。 从性能上讲，从已知的分区获取顶点是最有效的方法。
- **查询边缘时尽量使用传出方向**。 如上所述，边缘连同其源顶点一起朝传出方向存储。 这意味着，如果在设计数据和查询时考虑了此模式，则可以最大程度地减少出现跨分区查询的可能性。
- **选择可将数据均匀分配到不同分区的分区键**。 此决策在很大程度上取决于解决方案的数据模型。 请在 [Azure Cosmos DB 中的分区和缩放](partition-data.md)中详细了解如何创建适当的分区键。
- **尽量优化查询，以获取分区边界内的数据**。 最佳的分区策略与查询模式相符。 从单个分区获取数据的查询可提供最佳性能。

## <a name="next-steps"></a>后续步骤
本文概述了有关使用 Azure Cosmos DB 图形 API 进行分区的概念和最佳做法。 

* 了解 [Azure Cosmos DB 中的分区和缩放](partition-data.md)。
* 了解[图形 API 中的 Gremlin 支持](gremlin-support.md)。
* 了解[图形 API 简介](graph-introduction.md)。
