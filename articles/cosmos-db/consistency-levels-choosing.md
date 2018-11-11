---
title: 为你的应用程序选择适当的一致性级别 | Microsoft Docs
description: 在 Azure Cosmos DB 中为你的应用程序选择适当的一致性级别。
keywords: consistency, performance, azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: mjbrown
ms.openlocfilehash: 4b438320ac75918d10880a4bcdccadca4eebec32
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243982"
---
# <a name="choose-the-right-consistency-level-for-your-application"></a>为你的应用程序选择适当的一致性级别

依赖于复制以实现高可用性、低延迟或这两者的分布式数据库在读取一致性与可用性、延迟和吞吐量之间进行基本权衡。 大多数商用分布式数据库都要求开发人员在两种极端一致性模型之间进行选择：非常一致和最终一致。 开发人员使用 Azure Cosmos DB 可在五个妥善定义的一致性模型（非常、有限过期、会话、一致前缀和最终）之间进行选择。 这些一致性模型中的每一种都具有明确的定义并直观呈现，可用于特定的真实场景。 五种一致性模型中的每一种都具有明确的可用性和性能权衡，并由全面的 SLA 提供支持。 以下简单的注意事项将有助于你在许多常见方案中做出正确的选择。

## <a name="sql-api-or-table-api"></a>SQL API 或表 API

- 对于许多真实场景中，会话一致性是最佳也是推荐的选项。 有关详细信息，请参阅[如何管理应用程序的会话令牌](how-to-manage-consistency.md#utilize-session-tokens)。
- 如果应用程序需要非常一致，建议使用有限过期一致级别。
- 如果需要比会话一致性和写入的个位数毫秒延迟所提供的更严格的一致性保证，建议使用有限过期一致性级别。  
- 如果应用程序需要最终一致，建议使用一致前缀一致性级别。
- 如果需要的一致性不如会话一致性提供的那么严格的话，建议使用一致前缀一致性级别。
- 如果需要最高可用性和最低延迟，请使用最终一致级别。

## <a name="cassandra-mongodb-or-gremlin-api"></a>Cassandra、MongoDB 或 Gremlin API

- 有关 Apache Cassandra 的“读取一致性级别”与 Cosmos DB 一致性级别之间的映射的详细信息，请参阅[一致性级别和 Cosmos DB API ](consistency-levels-across-apis.md#cassandra-mapping)。
- 有关 MongoDB 的“读取问题”与 Cosmos DB 一致性级别之间的映射的详细信息，请参阅[一致性级别和 Cosmos DB API ](consistency-levels-across-apis.md#mongo-mapping)。

## <a name="you-may-get-stronger-consistency-guarantees-in-practice"></a>可在实践中获得更强的一致性保证

读取操作的一致性保证对应于所请求的数据库状态的刷新度和顺序。 读取一致性与写入（更新）操作的排序和传播有关。  

当一致性级别设置为“有限过期”时，Cosmos DB 保证客户端始终读取前一次写入的值，伴有一个受到过期窗口限制的延迟。

当一致性级别设置为“强”时，过期窗口等于零，并且保证客户端读取写入的最新提交值。

对于剩余的三个一致性级别，过期窗口在很大程度上取决于你的工作负载。 例如，如果未在数据库上执行任何写入操作，具有“最终”、“会话”或“一致前缀”一致性级别的读取操作可能产生与具有非常一致级别的读取操作相同的结果。

如果使用非常一致以外的任何一致性级别配置你的 Cosmos DB 帐户，则可通过查看在 Azure 门户[请参阅此处了解如何使用 PBS 指标](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)公开的概率有限过期 (PBS) 指标找到客户端获得工作负载的非常一致（线性化）读取的概率。 概率有限过期表明了最终一致的最终程度。 通过此指标可深入了解在 Cosmos DB 帐户中配置的一致性级别之间获得更强一致性的频率。 换句话说，可看到获得写入和读取区域组合的非常一致读取的概率（以毫秒为单位）。

## <a name="next-steps"></a>后续步骤

阅读以下文章中有关一致性级别的详细信息：

* [跨 Cosmos DB API 的一致性级别映射](consistency-levels-across-apis.md)
* [各种一致性级别的可用性和性能权衡](consistency-levels-tradeoffs.md)
* [如何管理应用程序的会话令牌](how-to-manage-consistency.md#utilize-session-tokens)
* [如何监视概率有限过期 (PBS) 指标](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)