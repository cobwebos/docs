---
title: 为使用 Azure Cosmos DB 的应用程序选择适当的一致性级别
description: 在 Azure Cosmos DB 中为你的应用程序选择适当的一致性级别。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/24/2018
ms.reviewer: sngun
ms.openlocfilehash: f32434e5ac0cd35cf620c1589aeb441476622442
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407447"
---
# <a name="choose-the-right-consistency-level"></a>选择适当的一致性级别 

依赖于复制以实现高可用性、低延迟或这两者的分布式数据库在读取一致性与可用性、延迟和吞吐量之间进行基本权衡。 大多数商用分布式的数据库要求开发人员使用两个极端一致性模型之间进行选择：*强*一致性和*最终*一致性。 Azure Cosmos DB 允许开发人员能够在五个妥善定义的一致性模型之间进行选择：*强*，*有限过期性*，*会话*，*一致前缀*并*最终*。 这些一致性模型中的每一种都具有明确的定义并直观呈现，可用于特定的真实场景。 每个五个一致性模型提供精确[可用性和性能的权衡取舍](consistency-levels-tradeoffs.md)开发，由综合 Sla 支持。 以下简单的注意事项将有助于你在许多常见方案中做出正确的选择。

## <a name="sql-api-and-table-api"></a>SQL API 和表 API

如果使用 SQL API 或表 API 生成应用程序，请考虑以下几点：

- 对于许多真实场景中，会话一致性是最佳也是推荐的选项。 有关详细信息，请参阅[如何管理应用程序的会话令牌](how-to-manage-consistency.md#utilize-session-tokens)。

- 如果应用程序需要非常一致，建议使用有限过期一致级别。

- 如果需要比会话一致性和写入的个位数毫秒延迟所提供的更严格的一致性保证，建议使用有限过期一致性级别。  

- 如果应用程序需要最终一致，建议使用一致前缀一致性级别。

- 如果需要的一致性不如会话一致性提供的那么严格的话，建议使用一致前缀一致性级别。

- 如果你需要最高可用性和最低的延迟，然后使用最终一致性级别。

- 如果需要更高的数据持久性而不想牺牲性能，可以在应用层创建自定义一致性级别。 有关详细信息，请参阅[如何在应用程序中实现自定义同步](how-to-custom-synchronization.md)。

## <a name="cassandra-mongodb-and-gremlin-apis"></a>Cassandra、 MongoDB 和 Gremlin Api

- 有关 Apache Cassandra 提供的“读取一致性级别”与 Cosmos DB 一致性级别之间的映射的详细信息，请参阅[一致性级别和 Cosmos DB API](consistency-levels-across-apis.md#cassandra-mapping)。

- 有关 MongoDB 的“读取问题”与 Cosmos DB 一致性级别之间的映射的详细信息，请参阅[一致性级别和 Cosmos DB API ](consistency-levels-across-apis.md#mongo-mapping)。

## <a name="consistency-guarantees-in-practice"></a>一致性保证的实践

在实践中，通常可能获得更强的一致性保证。 读取操作的一致性保证对应于所请求的数据库状态的新旧程度和顺序。 读取一致性与写入/更新操作的排序和传播有关。  

* 当一致性级别设置为“有限过期”时，Cosmos DB 保证客户端始终读取前一次写入的值，伴有一个受到过期窗口限制的延迟。

* 当一致性级别设置为“强”时，过期窗口等于零，并且保证客户端读取写入操作的最新提交值。

* 对于剩余的三个一致性级别，过期窗口在很大程度上取决于你的工作负载。 例如，如果未在数据库上执行任何写入操作，具有“最终”、“会话”或“一致前缀”一致性级别的读取操作可能产生与具有非常一致级别的读取操作相同的结果。

如果你的 Azure Cosmos 帐户配置的一致性级别不强一致性，你可以通过查看找出你的客户端可能会收到强的概率和工作负荷的一致读取*概率统计有限过期性*(PBS) 指标。 此指标在 Azure 门户中公开，若要了解详细信息，请参阅[监视概率有限过期性 (PBS) 指标](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)。

概率有限过期表明了最终一致的最终程度。 此指标提供了深入分析何种频率可以获取比当前已配置你的 Azure Cosmos 帐户的一致性级别更强的一致性。 换句话说，可看到获得写入和读取区域组合的非常一致读取的概率（以毫秒计量）。

## <a name="next-steps"></a>后续步骤

阅读以下文章中有关一致性级别的详细信息：

* [跨 Cosmos DB API 的一致性级别映射](consistency-levels-across-apis.md)
* [各种一致性级别的可用性和性能权衡](consistency-levels-tradeoffs.md)
* [如何管理应用程序的会话令牌](how-to-manage-consistency.md#utilize-session-tokens)
* [监视概率有限过期性 (PBS) 指标](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)
