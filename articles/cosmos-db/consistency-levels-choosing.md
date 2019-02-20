---
title: 为使用 Azure Cosmos DB 的应用程序选择适当的一致性级别
description: 在 Azure Cosmos DB 中为你的应用程序选择适当的一致性级别。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/24/2018
ms.reviewer: sngun
ms.openlocfilehash: 11eb849567079bfb1293c3c1e8ce97c43b66d493
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2019
ms.locfileid: "56116837"
---
# <a name="choose-the-right-consistency-level"></a>选择适当的一致性级别 

依赖于复制以实现高可用性、低延迟或这两者的分布式数据库在读取一致性与可用性、延迟和吞吐量之间进行基本权衡。 大多数商用分布式数据库都要求开发人员在两种极端一致性模型之间进行选择：非常一致和最终一致。 开发人员使用 Azure Cosmos DB 可在五个妥善定义的一致性模型（非常、有限过期、会话、一致前缀和最终）之间进行选择。 这些一致性模型中的每一种都具有明确的定义并直观呈现，可用于特定的真实场景。 五种一致性模型中的每一种在[可用性与性能方面都进行了权衡](consistency-levels-tradeoffs.md)，并有全面的 SLA 作为保障。 以下简单的注意事项将有助于你在许多常见方案中做出正确的选择。

## <a name="sql-api-and-table-api"></a>SQL API 和表 API

如果应用程序是使用 Cosmos DB SQL API 或表 API 生成的，请注意以下几点

- 对于许多真实场景中，会话一致性是最佳也是推荐的选项。 有关详细信息，请参阅[如何管理应用程序的会话令牌](how-to-manage-consistency.md#utilize-session-tokens)。

- 如果应用程序需要非常一致，建议使用有限过期一致级别。

- 如果需要比会话一致性和写入的个位数毫秒延迟所提供的更严格的一致性保证，建议使用有限过期一致性级别。  

- 如果应用程序需要最终一致，建议使用一致前缀一致性级别。

- 如果需要的一致性不如会话一致性提供的那么严格的话，建议使用一致前缀一致性级别。

- 如果需要最高可用性和最低延迟，请使用最终一致级别。

- 如果需要更高的数据持久性而不想牺牲性能，可以在应用层创建自定义一致性级别。 有关详细信息，请参阅[如何在应用程序中实现自定义同步](how-to-custom-synchronization.md)。

## <a name="cassandra-mongodb-and-gremlin-api"></a>Cassandra、MongoDB 和 Gremlin API

- 有关 Apache Cassandra 提供的“读取一致性级别”与 Cosmos DB 一致性级别之间的映射的详细信息，请参阅[一致性级别和 Cosmos DB API](consistency-levels-across-apis.md#cassandra-mapping)。

- 有关 MongoDB 的“读取问题”与 Cosmos DB 一致性级别之间的映射的详细信息，请参阅[一致性级别和 Cosmos DB API ](consistency-levels-across-apis.md#mongo-mapping)。

## <a name="consistency-guarantees-in-practice"></a>一致性保证的实践

可在实践中获得更强的一致性保证。 读取操作的一致性保证对应于所请求的数据库状态的新旧程度和顺序。 读取一致性与写入/更新操作的排序和传播有关。  

* 当一致性级别设置为“有限过期”时，Cosmos DB 保证客户端始终读取前一次写入的值，伴有一个受到过期窗口限制的延迟。

* 当一致性级别设置为“强”时，过期窗口等于零，并且保证客户端读取写入操作的最新提交值。

* 对于剩余的三个一致性级别，过期窗口在很大程度上取决于你的工作负载。 例如，如果未在数据库上执行任何写入操作，具有“最终”、“会话”或“一致前缀”一致性级别的读取操作可能产生与具有非常一致级别的读取操作相同的结果。

如果使用非常一致性以外的一致性级别配置了 Cosmos DB 帐户，则可以通过查看概率有限过期 (PBS) 指标，找到客户端获得工作负荷的非常一致读取的概率。 此指标在 Azure 门户中公开，若要了解详细信息，请参阅[监视概率有限过期性 (PBS) 指标](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)。

概率有限过期表明了最终一致的最终程度。 通过此指标可深入了解在 Cosmos DB 帐户中目前配置的一致性级别之间获得更强一致性的频率。 换句话说，可看到获得写入和读取区域组合的非常一致读取的概率（以毫秒计量）。

## <a name="next-steps"></a>后续步骤

阅读以下文章中有关一致性级别的详细信息：

* [跨 Cosmos DB API 的一致性级别映射](consistency-levels-across-apis.md)
* [各种一致性级别的可用性和性能权衡](consistency-levels-tradeoffs.md)
* [如何管理应用程序的会话令牌](how-to-manage-consistency.md#utilize-session-tokens)
* [监视概率有限过期性 (PBS) 指标](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)
