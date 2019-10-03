---
title: Azure Cosmos DB Gremlin 的限制
description: 图形引擎运行时限制的参考文档
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: olignat
ms.openlocfilehash: f1fe4cfac22a651f44338986d0a767fe3e1a360b
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70911040"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Azure Cosmos DB Gremlin 限制
本文介绍 Azure Cosmos DB Gremlin 引擎的限制，并说明他们可能会如何影响客户遍历。

Cosmos DB Gremlin 是在 Cosmos DB 基础结构之上构建的，这就是[Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/concepts-limits)的所有限制仍适用的原因。 

## <a name="limits"></a>限制

达到 Gremlin 限制时，将取消遍历，其中**x 毫秒-状态代码**= 429 指示限制错误。

**资源**    | **默认限制** | **解释**
--- | --- | ---
*每个请求的内存* | **2 GB** | 请求在处理过程中可以占用的最大内存。 需要计算大数据集的请求会消耗额外的内存。 考虑将请求划分为较小的数据集，以避免超出此限制或使用 OLAP 解决方案。
*脚本长度* | **64 KB** | 每个请求的 Gremlin 遍历脚本的最大长度。
*运算符深度* | **400** |  遍历中的唯一步骤的总数。 例如， ```g.V().out()```的运算符计数为2：V （）和 out （）， ```g.V('label').repeat(out()).times(100)```运算符深度为3：V （）、repeat （）和 out （），因为```.times(100)```是```.repeat()```运算符的参数。
*并行度* | **32** | 发往存储层的单个请求中查询的存储分区的最大数。 具有数百个分区的图形将受到此限制的影响。
*重复限制* | **32** | ```.repeat()``` 运算符可执行的最大迭代次数。 大多数情况下```.repeat()``` ，每次执行步骤迭代都将运行广度优先遍历，这意味着任何遍历最多只能有32个顶点之间的跃点。
*遍历超时* | **30 秒** | 当遍历超过此时间时，将取消该遍历。 Cosmos DB Graph 是一种 OLTP 数据库，其中绝大部分遍历都在几毫秒内完成。 若要在 Cosmos DB 关系图上运行 OLAP 查询，请将[Apache Spark](https://azure.microsoft.com/services/cosmos-db/)与[Graph 数据帧](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes)和[Cosmos DB Spark 连接器](https://github.com/Azure/azure-cosmosdb-spark)一起使用。
*谓词限制* | **20** | 应用于单个顶点或边的 ```.has()``` 或 ```.hasNot()``` 步骤的计数。 当达到此限制时，显示给应用程序的错误为 ```The SQL query exceeded the maximum number of joins. The allowed limit is 20```。 由于团队正在努力提升此限制，这是一种暂时性的麻烦。 
*空闲连接超时* | **5小时** | Graph 服务器将使 websocket 连接保持打开状态，而不会在其上传输流量。 TCP keep-alive 数据包或 HTTP keep-alive 请求不会将连接寿命扩展到超过此限制，但是，如果未发送，则底层 Azure 基础结构可能会更快地关闭连接。 如果未在 Gremlin 上运行遍历，Cosmos DB Graph 引擎会将其视为空闲。
*每小时资源令牌* | **100** | Gremlin 客户端用于连接到某个区域中的 Gremlin 帐户的唯一资源令牌数。 当应用程序超过每小时唯一令牌`"Exceeded allowed resource token limit of 100 that can be used concurrently"`限制时，将在下一次身份验证请求时返回。

## <a name="next-steps"></a>后续步骤
* [Azure Cosmos DB Gremlin 响应标头](gremlin-headers.md) 
* [用 Gremlin Azure Cosmos DB 资源令牌](how-to-use-resource-tokens-gremlin.md)
