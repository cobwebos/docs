---
title: 使用 Azure Cosmos DB 中的更改源支持
description: 使用 Azure Cosmos DB 的更改源支持来跟踪文档中发生的更改、执行基于事件的处理（例如触发器），使缓存和分析系统保持最新状态
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: e36e95aeb25c83ccd94f11e25bfe9f1b8f7bfdad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80984855"
---
# <a name="change-feed-in-azure-cosmos-db"></a>Azure Cosmos DB 中的更改源

Azure Cosmos DB 中更改源支持的工作原理是侦听 Azure Cosmos 容器中发生的任何更改。 然后，它会按照所更改文档的修改顺序输出这些文档的排序列表。 这些更改将会持久保留且能以异步和增量方式进行处理。可将输出分配到一个或多个使用者供并行处理。

了解有关[更改源设计模式](change-feed-design-patterns.md)的详细信息。

## <a name="supported-apis-and-client-sdks"></a>支持的 API 和客户端 SDK

目前，以下 Azure Cosmos DB API 和客户端 SDK 支持此功能。

| **客户端驱动程序** | **SQL API** | **用于 Cassandra 的 Azure Cosmos DB API** | **Azure Cosmos DB 的用于 MongoDB 的 API** | **Gremlin API**|**表 API** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | 是 | 是 | 是 | 是 | 否 |
|Java|是|是|是|是|否|
|Python|是|是|是|是|否|
|Node/JS|是|是|是|是|否|

## <a name="change-feed-and-different-operations"></a>更改源和不同操作

现在，可以在更改源中看到所有插入和更新。 不能针对特定类型的操作来筛选的更改源。 一种可能的替代方法是在项上添加 "软标记"，以便在处理更改源中的项时基于该项进行更新和筛选。

目前更改源不会记录删除操作。 与前面的示例类似，您可以对要删除的项添加软标记。 例如，你可以在名为 "deleted" 的项中添加一个属性，并将其设置为 "true" 并在该项上设置 TTL，以便可以自动删除它。 可以读取历史项的更改源（与该项相对应的最新更改，不包括中间更改），例如，在五年前添加的项。 你可以将更改源视为容器的源，但是如果删除了某个项，则会将其从更改源中删除。

### <a name="sort-order-of-items-in-change-feed"></a>更改源中项的排序顺序

更改源项按其修改时间排序。 每个逻辑分区键均可保证这种排序顺序。

### <a name="consistency-level"></a>一致性级别

在最终一致性级别使用更改源时，可能会在后续更改源读取操作之间发生重复的事件（一个读取操作的最后一个事件显示为下一个读取操作的最后一个事件）。

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>多区域 Azure Cosmos 帐户中的更改流

在多区域 Azure Cosmos 帐户中，如果写入区域进行故障转移，则更改源将在整个手动故障转移操作中运作，并且是连续的。

### <a name="change-feed-and-time-to-live-ttl"></a>更改源和生存时间 (TTL)

如果某个项的 TTL（生存时间）属性设置为 -1，则将永久保留更改源。 如果数据未被删除，它将会保留在更改源中。  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>更改源和 _etag、_lsn 或 _ts

_etag 属于内部格式，请不要依赖它，因为它随时可能更改。 _ts 是修改或创建时间戳。 可以使用 _ts 进行时间顺序比较。 _lsn 是仅为更改源添加的批 ID，它表示事务 ID。 许多项可能具有相同的 _lsn。 FeedResponse 上的 ETag 不同于项上看到的 _etag。 _etag 是用于并发控制的内部标识符，它告知项的版本，而 ETag 用于将源定序。

## <a name="working-with-change-feed"></a>使用更改源

可通过以下选项使用更改源：

* [将更改源与 Azure Functions 配合使用](change-feed-functions.md)
* [将更改源与更改源处理器配合使用](change-feed-processor.md) 

更改源适用于容器中的每个逻辑分区键，它可以分配给一个或多个使用者进行并行处理，如下图所示。

![Azure Cosmos DB 更改源的分布式处理](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>更改源的功能

* 默认情况下，所有 Azure Cosmos 帐户中都启用了更改源。

* 就像执行任何其他 Azure Cosmos DB 操作一样，可使用[预配吞吐量](request-units.md)在与 Azure Cosmos 数据库关联的任何区域中从更改源读取数据。

* 更改源包括针对容器中的项所执行的插入和更新操作。 在项（如文档）中的删除位置设置“软删除”标志，可以捕获删除操作。 此外，也可以使用 [TTL 功能](time-to-live.md)为项设置有限的过期时段。 例如，24 小时，可使用该属性的值来捕获删除操作。 使用此解决方案时，处理更改的时间间隔必须比 TTL 过期时段要短。

* 在更改源中，对项的每个更改都将显示一次，且客户端必须管理其检查点逻辑。 如果想要避免管理检查点的复杂性，更改源处理器提供了自动检查点和“至少一次”语义。 请参阅[将更改源与更改源处理器配合使用](change-feed-processor.md)。

* 更改日志中仅包含最近对给定项所做的更改。 而不包含中途的更改。

* 更改源按照每个逻辑分区键值中的修改顺序排序。 无法保证各分区键值中的顺序一致。

* 可从任意时间点同步更改，也就是说，发生更改的数据没有固定的数据保留期。

* 对于 Azure Cosmos 容器的所有逻辑分区键，可以并行发生更改。 多个使用者可以使用此功能并行处理大型容器中发生的更改。

* 应用程序可针对同一容器同时请求多个更改源。 可以使用 ChangeFeedOptions.StartTime 提供初始的起点。 例如，查找对应于给定时钟时间的继续令牌。 ContinuationToken （如果已指定）优先于 StartTime 和 StartFromBeginning 值。 ChangeFeedOptions.StartTime 的精度是 ~5 秒。

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>用于 Cassandra 和 MongoDB 的 API 中的更改源

更改源功能在 MongoDB API 中作为更改流出现，在 Cassandra API 中作为带有谓词的查询出现。 若要了解有关 MongoDB API 的实现细节的详细信息，请参阅[用于 MongoDB 的 Azure Cosmos DB API 中的更改流](mongodb-change-streams.md)。

本机 Apache Cassandra 提供了变更数据捕获 (CDC)，这是一种机制，用于标记要存档的特定表，并在达到 CDC 日志的可配置磁盘大小时拒绝写入这些表。 用于 Cassandra 的 Azure Cosmos DB API 中的更改源功能增强了通过 CQL 使用谓词查询更改的功能。 若要了解有关实现细节的详细信息，请参阅[用于 Cassandra 的 Azure Cosmos DB API 中的更改源](cassandra-change-feed.md)。

## <a name="next-steps"></a>后续步骤

接下来，请通过以下文章继续详细了解更改源：

* [读取更改源的选项](read-change-feed.md)
* [将更改源与 Azure Functions 配合使用](change-feed-functions.md)
* [使用更改源处理器](change-feed-processor.md)
