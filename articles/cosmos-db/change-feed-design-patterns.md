---
title: Azure Cosmos DB 中的更改源设计模式
description: 常用更改源设计模式概述
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: abbf5d79da033a696890566d85bd24bb54577d2c
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715692"
---
# <a name="change-feed-design-patterns-in-azure-cosmos-db"></a>Azure Cosmos DB 中的更改源设计模式

Azure Cosmos DB 更改源可以高效处理具有大量写入的大型数据集。 更改源还提供用于查询整个数据集以确定更改内容的替代方法。 本文档重点介绍常用的更改源设计模式、设计优缺点和更改源的限制。

Azure Cosmos DB 非常适合用于 IoT、游戏、零售和操作日志记录应用程序。 这些应用程序中的一种常见设计模式是使用数据更改来触发附加的操作。 附加操作的示例包括：

* 插入或更新项时触发通知或 API 调用。
* 对 IoT 的实时流式处理或对运营数据的实时分析处理。
* 数据移动，例如与缓存、搜索引擎、数据仓库或冷存储进行同步。

使用 Azure Cosmos DB 中的更改源，可针对每种模式构建高效、可缩放的解决方案，如下图所示：

![使用 Azure Cosmos DB 更改源促成实时分析和事件驱动的计算方案](./media/change-feed/changefeedoverview.png)

## <a name="event-computing-and-notifications"></a>事件计算和通知

Azure Cosmos DB 更改源可以简化需要基于特定事件触发通知或发送对 API 的调用的方案。 可以使用[更改源进程库](change-feed-processor.md)自动轮询容器的更改，并在每次发生写入或更新操作时调用外部 API。

还可以基于特定的条件，有选择性地触发通知或发送对 API 的调用。 例如，如果你要使用 [Azure Functions](change-feed-functions.md) 从更改源中读取数据，可以在函数中放置逻辑，仅在满足特定条件的情况下发送通知。 尽管在每次发生写入和更新操作期间 Azure 函数代码都会执行，但只有在满足特定的条件时才会发送通知。

## <a name="real-time-stream-processing"></a>实时流处理

Azure Cosmos DB 更改源可用于 IoT 的实时流处理，或者基于操作数据进行实时分析处理。
例如，可以接收和存储来自设备、传感器、基础结构和应用程序的事件数据，并使用 [Spark](../hdinsight/spark/apache-spark-overview.md) 实时处理这些事件。 下图显示了如何通过更改源使用 Azure Cosmos DB 实现 lambda 体系结构：

![用于引入和查询的基于 Azure Cosmos DB 的 lambda 管道](./media/change-feed/lambda.png)

在许多情况下，流处理实现首先会将大量传入数据接收到 Azure 事件中心或 Apache Kafka 等临时消息队列中。 由于 Azure Cosmos DB 能够支持持续较高的数据引入速率，并保证较低的读取和写入延迟，因此，更改源是极佳的替代方案。 基于消息队列的 Azure Cosmos DB 更改源的优势包括：

### <a name="data-persistence"></a>数据持久性

写入到 Azure Cosmos DB 的数据将显示在更改源中，并在删除之前一直保留。 消息队列通常具有最长的保留期。 例如，[Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)提供的最长数据保留期为 90 天。

### <a name="querying-ability"></a>查询功能

除了能够从 Cosmos 容器的更改源中读取数据以外，你还可以对 Azure Cosmos DB 中存储的数据运行 SQL 查询。 更改源不是对容器中已有的数据进行复制，它只是一种不同的数据读取机制。 因此，如果从更改源中读取数据，读取结果始终与同一 Azure Cosmos DB 容器的查询一致。

### <a name="high-availability"></a>高可用性

Azure Cosmos DB 提供高达 99.999% 的读取和写入可用性。 可以轻松地对 Azure Cosmos DB 数据进行全局分布操作，并为其配置零 [RTO（恢复时间目标）](consistency-levels-tradeoffs.md#rto)，这与许多消息队列不同。

处理更改源中的项后，可以生成具体化视图，并将聚合值存回到 Azure Cosmos DB 中。 例如，若要使用 Azure Cosmos DB 构建游戏，可使用更改源，根据已完成的游戏的分数实时更新排行榜。

## <a name="data-movement"></a>数据移动

还可以从更改源中读取数据，以实现实时数据移动。

例如，更改源可帮助你有效执行以下任务：

* 使用 Azure Cosmos DB 中存储的数据更新缓存、搜索索引或数据仓库。

* 使用不同的逻辑分区键零停机迁移到其他 Azure Cosmos 帐户或其他 Azure Cosmos 容器。

* 实现应用程序级数据分层和存档。 例如，可将“热数据”存储在 Azure Cosmos DB 中，并将陈旧的“冷数据”存储在 [Azure Blob 存储](../storage/common/storage-introduction.md)等其他存储系统中。

如果必须[反规范化各个分区和容器中的数据](how-to-model-partition-example.md#v2-introducing-denormalization-to-optimize-read-queries
)，可以从容器的更改源（用作此数据复制操作的源）中读取数据。 使用更改源的实时数据复制只能保证最终一致性。 在 Cosmos 容器中处理更改时，可以[监视更改源处理器的滞后程度](how-to-use-change-feed-estimator.md)。

## <a name="event-sourcing"></a>事件溯源

[事件溯源模式](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)涉及到使用仅限追加的存储来记录对该数据执行的整个操作系列。 在所有数据引入都建模为写入（无更新或删除）的事件溯源体系结构中，Azure Cosmos DB 的更改源非常适合用作中心数据存储。 在这种情况下，对 Azure Cosmos DB 的每次写入都是一个“事件”，你可以在更改源中获得以往事件的完整记录。 中心事件存储发布的事件的典型用途是维护具体化视图或者与外部系统集成。 由于更改源中不存在保留时间限制，因此可以通过从 Cosmos 容器更改源的开头部分进行读取，来重放所有以往的事件。

可以让[多个更改源使用者订阅同一个容器的更改源](how-to-create-multiple-cosmos-db-triggers.md#optimizing-containers-for-multiple-triggers)。 使用更改源只需支付[租用容器](change-feed-processor.md#components-of-the-change-feed-processor)的预配吞吐量费用，此外不会产生其他费用。 不管是否使用更改源，都会在每个容器中提供更改源。

由于 Azure Cosmos DB 在横向可伸缩性和高可用性方面具有优势，因此在事件溯源模式中，它是极佳的仅限追加的中心持久数据存储。 此外，更改源处理器库提供[“至少一次”](change-feed-processor.md#error-handling)保证，确保不会遗漏任何事件的处理。

## <a name="current-limitations"></a>当前限制

必须知道更改源的几项重要限制。 尽管 Cosmos 容器中的项始终会保留在更改源中，但更改源并非完整的操作日志。 设计某个利用更改源的应用程序时，需要考虑到一些重要方面。

### <a name="intermediate-updates"></a>中间更新

更改源中仅包含最近对给定项所做的更改。 处理更改时，将会读取最新可用的项版本。 如果在短时间内对同一项进行了多次更新，可能会遗漏中间更新的处理。 若要跟踪更新并重放以往对某个项的更新，建议你改将这些更新建模为一系列写入操作。

### <a name="deletes"></a>Deletes

更改源不会捕获删除操作。 如果删除容器中的某个项，也会从更改源中删除该项。 处理此操作的最常用方法是在要删除的项中添加一个软标记。 可以添加名为“deleted”的属性，并在执行删除操作时将其设置为“true”。 此文档更新将显示在更改源中。 可以在此项中设置 TTL，以后就会自动删除此项。

### <a name="guaranteed-order"></a>保证顺序

更改源中提供顺序保证，这种保证是在某个分区键值（而不是在各个分区键值）中提供的。 应该选择可以提供有意义的顺序保证的分区键。

例如，假设某个零售应用程序使用事件溯源设计模式。 在此应用程序中，每个不同的用户操作都是“事件”，这些事件建模为对 Azure Cosmos DB 的写入。 假设按以下顺序发生了一些示例事件：

1. 客户将商品 A 添加到其购物车
2. 客户将商品 B 添加到其购物车
3. 客户从购物车中删除商品 A
4. 客户结帐，然后卖家交付购物车内容

将为每个客户保留当前购物车内容的具体化视图。 此应用程序必须确保按事件的发生顺序处理这些事件。 例如，如果在删除商品 A 之前处理了购物车结帐，则卖家可能已经为客户交付了商品 A，而不是所需的商品 B。为了保证按发生顺序处理这四个事件，这些事件应该位于同一个分区键值中。 如果选择**用户名**（每个客户都有唯一的用户名）作为分区键，则可以保证这些事件按照它们写入到 Azure Cosmos DB 的顺序显示在更改源中。

## <a name="examples"></a>示例

下面是一些真实的更改源代码示例，这些示例超出了 Microsoft 文档中提供的示例的范围：

- [更改源简介](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html)
- [以更改源为中心实现的 IoT 用例](https://github.com/AzureCosmosDB/scenario-based-labs)
- [以更改源为中心实现的零售用例](https://github.com/AzureCosmosDB/scenario-based-labs)

## <a name="next-steps"></a>后续步骤

* [更改源概述](change-feed.md)
* [读取更改源的选项](read-change-feed.md)
* [将更改源与 Azure Functions 配合使用](change-feed-functions.md)
