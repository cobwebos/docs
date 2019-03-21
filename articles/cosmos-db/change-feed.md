---
title: 使用 Azure Cosmos DB 中的更改源支持
description: 使用 Azure Cosmos DB 的更改源支持跟踪文档中发生的更改，执行基于事件的处理（例如触发器），使缓存和分析系统保持最新状态。
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: e21058d47f554ca4a057ab90433895800fb17dd9
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2019
ms.locfileid: "56886717"
---
# <a name="change-feed-in-azure-cosmos-db---overview"></a>Azure Cosmos DB 中的更改源 - 概述

Azure Cosmos DB 中更改源支持的工作原理是侦听 Azure Cosmos DB 容器中发生的任何更改。 然后，它会按照所更改文档的修改顺序输出这些文档的排序列表。 这些更改将会持久保留且能以异步和增量方式进行处理。可将输出分配到一个或多个使用者供并行处理。 

Azure Cosmos DB 非常适合用于 IoT、游戏、零售和操作日志记录应用程序。 这些应用程序中的一种常见设计模式是使用数据更改来触发附加的操作。 附加操作的示例包括：

* 插入或更新项时触发通知或 API 调用。
* 对 IoT 的实时流式处理或对运营数据的实时分析处理。
* 通过与缓存、搜索引擎或数据仓库同步，或者将数据存档到冷存储，进行附加的数据移动。

使用 Azure Cosmos DB 中的更改源，可针对每种模式构建高效、可缩放的解决方案，如下图所示：

![使用 Azure Cosmos DB 更改源促成实时分析和事件驱动的计算方案](./media/change-feed/changefeedoverview.png)

## <a name="supported-apis-and-client-sdks"></a>支持的 API 和客户端 SDK

目前，以下 Azure Cosmos DB API 和客户端 SDK 支持此功能。

| **客户端驱动程序** | **Azure CLI** | **SQL API** | **Cassandra API** | **Azure Cosmos DB 的 API for MongoDB** | **Gremlin API**|**表 API** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | NA | 是 | 否 | 否 | 是 | 否 |
|Java|NA|是|否|否|是|否|
|Python|NA|是|否|否|是|否|
|Node/JS|NA|是|否|否|是|否|

## <a name="change-feed-and-different-operations"></a>更改源和不同操作

如今，在更改流中可以看到所有操作。 针对只更新和不插入等特定操作的功能（可控制更改源）尚不可用。 可以在更新项上添加“软标记”，并在更改流中处理项时根据标记进行筛选。 目前更改源不会记录日志删除操作。 与前面的示例类似，可在要删除的项上添加软标记，例如，可在名为“已删除”的项中添加属性并将其设置为“true”，然后在该项上设置 TTL，这样系统就可将其自动删除。 可以阅读历史项的更改流（例如，五年前添加的项）。 如果未删除该项，则可以读取不超过容器原始时间的更改源。

### <a name="sort-order-of-items-in-change-feed"></a>更改源中项的排序顺序

更改源项按其修改时间排序。 按逻辑分区键保证这种排序顺序。

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>多区域 Azure Cosmos 帐户中的更改流

在多区域 Azure Cosmos 帐户中，如果写入区域进行故障转移，则更改源将在整个手动故障转移操作中运作，并且是连续的。

### <a name="change-feed-and-time-to-live-ttl"></a>更改源和生存时间 (TTL)

如果某个项的 TTL（生存时间）属性设置为 -1，则将永久保留更改源。 如果数据未被删除，它将会保留在更改源中。  

### <a name="change-feed-and-etag-lsn-or-ts"></a>更改源和 _etag、_lsn 或 _ts

_etag 属于内部格式，请不要依赖它，因为它随时可能更改。 _ts 是修改或创建时间戳。 可以使用 _ts 进行时间顺序比较。 _lsn 是仅为更改源添加的批 ID，它表示事务 ID。许多项可能具有相同的 _lsn。 FeedResponse 上的 ETag 不同于项上看到的 _etag。 _etag 是用于并发控制的内部标识符，它告知项的版本，而 ETag 用于将源定序。

## <a name="change-feed-use-cases-and-scenarios"></a>更改源用例和方案

更改源可以高效处理具有大量写入的大型数据集。 更改源还提供用于查询整个数据集以确定更改内容的替代方法。

### <a name="use-cases"></a>用例

例如，使用更改源可以有效地执行以下任务：

* 使用 Azure Cosmos DB 中存储的数据更新缓存、搜索索引或数据仓库。

* 实现应用程序级别的数据分层和存档，例如，将“热数据”存储在 Azure Cosmos DB 中，将“冷数据”搁置在其他存储系统中（如 [Azure Blob 存储](../storage/common/storage-introduction.md)）。

* 使用不同的逻辑分区键零停机迁移到其他 Azure Cosmos 帐户或其他 Azure Cosmos 容器。

* 使用 Azure Cosmos DB 实现 [lambda 体系结构](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/)，其中 Azure Cosmos DB 支持实时、批处理和查询服务层，因此实现的 lambda 体系结构 TCO 较低。

* 接收和存储设备、传感器、基础架构和应用程序发出的事件数据，并实时处理这些事件（例如，使用 [Spark](../hdinsight/spark/apache-spark-overview.md)）。  下图显示了如何通过更改源使用 Azure Cosmos DB 实现 lambda 体系结构：

![用于引入和查询的基于 Azure Cosmos DB 的 lambda 管道](./media/change-feed/lambda.png)

### <a name="scenarios"></a>方案

以下是一些可通过更改源轻松实现的方案：

* 在[无服务器](https://azure.microsoft.com/en-us/solutions/serverless/) Web 应用或移动应用中，可以跟踪各种事件（例如，对客户配置文件、首选项或其位置的更改），并触发特定的操作（例如，使用 [Azure Functions](change-feed-functions.md) 向客户的设备发送推送通知）。

* 例如，若要使用 Azure Cosmos DB 构建游戏，可使用更改源，根据已完成的游戏的分数实时更新排行榜。


## <a name="working-with-change-feed"></a>使用更改源

可通过以下选项使用更改源：

* [将更改源与 Azure Functions 配合使用](change-feed-functions.md)
* [将更改源与更改源处理器库配合使用](change-feed-processor.md) 

更改源适用于容器中的每个逻辑分区键，它可以分配给一个或多个使用者进行并行处理，如下图所示。

![Azure Cosmos DB 更改源的分布式处理](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>更改源的功能

* 默认情况下，所有 Azure Cosmos 帐户中都启用了更改源。

* 就像执行任何其他 Azure Cosmos DB 操作一样，可使用[预配吞吐量](request-units.md)在与 Azure Cosmos 数据库关联的任何区域中从更改源读取数据。

* 更改源包括针对容器中的项所执行的插入和更新操作。 在项（如文档）中的删除位置设置“软删除”标志，可以捕获删除操作。 此外，也可以使用 [TTL 功能](time-to-live.md)为项设置有限的过期时段。 例如，24 小时，可使用该属性的值来捕获删除操作。 使用此解决方案时，处理更改的时间间隔必须比 TTL 过期时段要短。 

* 在更改源中，对项的每个更改都将显示一次，且客户端必须管理其检查点逻辑。 如果想要避免复杂的检查点管理过程，更改源处理器库提供了自动检查点和“至少一次”语义。 请参阅[将更改源与更改源处理器库配合使用](change-feed-processor.md)。

* 更改日志中仅包含最近对给定项所做的更改。 而不包含中途的更改。

* 更改源按照每个逻辑分区键值中的修改顺序排序。 无法保证各分区键值中的顺序一致。

* 可从任意时间点同步更改，也就是说，发生更改的数据没有固定的数据保留期。

* 对于 Azure Cosmos 容器的所有逻辑分区键，可以并行发生更改。 多个使用者可以使用此功能并行处理大型容器中发生的更改。

* 应用程序可针对同一容器同时请求多个更改源。 可以使用 ChangeFeedOptions.StartTime 提供初始的起点。 例如，查找对应于给定时钟时间的继续令牌。 ContinuationToken（如果指定）优先于 StartTime 和 StartFromBeginning 值。 ChangeFeedOptions.StartTime 的精度是 ~5 秒。 

## <a name="next-steps"></a>后续步骤

接下来，请通过以下文章继续详细了解更改源：

* [读取更改源的选项](read-change-feed.md)
* [将更改源与 Azure Functions 配合使用](change-feed-functions.md)
* [使用更改源处理器库](change-feed-processor.md)
