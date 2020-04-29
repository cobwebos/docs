---
title: 更改 Azure Cosmos DB 中的源设计模式
description: 常见更改源设计模式概述
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 012d27b44ecfbdd460adf241742df397880f78c6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81450345"
---
# <a name="change-feed-design-patterns-in-azure-cosmos-db"></a>更改 Azure Cosmos DB 中的源设计模式

利用 Azure Cosmos DB 更改源，可以在大量写入操作的情况中有效处理大型数据集。 更改源还提供用于查询整个数据集以确定更改内容的替代方法。 本文档重点介绍常见的更改源设计模式、设计折衷和更改源限制。

Azure Cosmos DB 非常适合用于 IoT、游戏、零售和操作日志记录应用程序。 这些应用程序中的一种常见设计模式是使用数据更改来触发附加的操作。 附加操作的示例包括：

* 插入或更新项时触发通知或 API 调用。
* 对 IoT 的实时流式处理或对运营数据的实时分析处理。
* 数据移动，例如与缓存、搜索引擎、数据仓库或冷存储同步。

使用 Azure Cosmos DB 中的更改源，可针对每种模式构建高效、可缩放的解决方案，如下图所示：

![使用 Azure Cosmos DB 更改源促成实时分析和事件驱动的计算方案](./media/change-feed/changefeedoverview.png)

## <a name="event-computing-and-notifications"></a>事件计算和通知

Azure Cosmos DB 更改源可以简化需要根据某个事件触发通知或对 API 的调用的方案。 您可以使用 "[更改源进程库](change-feed-processor.md)" 自动轮询容器的更改，并在每次写入或更新时调用外部 API。

还可以根据特定条件有选择地触发通知或发送对 API 的调用。 例如，如果要使用[Azure Functions](change-feed-functions.md)从更改源中读取数据，则可以在函数中放置逻辑，以便仅在满足特定条件时才发送通知。 尽管 Azure 函数代码会在每次写入和更新期间执行，但只有在满足特定条件时才会发送通知。

## <a name="real-time-stream-processing"></a>实时流处理

Azure Cosmos DB 更改源可用于实时流处理，用于实时处理操作数据的 IoT 或实时分析。
例如，你可能会接收并存储设备、传感器、基础结构和应用程序中的事件数据，并使用[Spark](../hdinsight/spark/apache-spark-overview.md)实时处理这些事件。 下图显示了如何通过更改源使用 Azure Cosmos DB 来实现 lambda 体系结构：

![用于引入和查询的基于 Azure Cosmos DB 的 lambda 管道](./media/change-feed/lambda.png)

在许多情况下，流处理实现首先会将大量传入数据接收到临时消息队列，如 Azure 事件中心或 Apache Kafka。 更改源是一种很好的选择，因为 Azure Cosmos DB 能够以保证的读取和写入延迟来支持持续的数据引入速率。 通过消息队列 Azure Cosmos DB 更改源的优点包括：

### <a name="data-persistence"></a>数据暂留

写入 Azure Cosmos DB 的数据将显示在更改源中，并保留到 "已删除"。 消息队列通常有最长的保留期。 例如， [Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)提供的最大数据保持期为90天。

### <a name="querying-ability"></a>查询功能

除了从 Cosmos 容器的更改源中读取以外，还可以对存储在 Azure Cosmos DB 中的数据运行 SQL 查询。 更改源不是已在容器中复制的数据，而是只是一种不同的读取数据机制。 因此，如果从更改源中读取数据，则该数据将始终与相同 Azure Cosmos DB 容器的查询一致。

### <a name="high-availability"></a>高可用性

Azure Cosmos DB 提供高达99.999% 的读取和写入可用性。 与许多消息队列不同的是，可以使用零的[RTO （恢复时间目标）](consistency-levels-tradeoffs.md#rto)轻松地对数据进行全局分发和配置 Azure Cosmos DB 数据。

处理更改源中的项后，可以生成具体化视图，并将聚合值保留 Azure Cosmos DB。 例如，若要使用 Azure Cosmos DB 构建游戏，可使用更改源，根据已完成的游戏的分数实时更新排行榜。

## <a name="data-movement"></a>数据移动

你还可以从更改源中读取数据，以进行实时数据移动。

例如，更改源可帮助你有效地执行以下任务：

* 使用 Azure Cosmos DB 中存储的数据更新缓存、搜索索引或数据仓库。

* 使用不同的逻辑分区键零停机迁移到其他 Azure Cosmos 帐户或其他 Azure Cosmos 容器。

* 实现应用程序级别的数据分层和存档。 例如，你可以将 "热数据" 存储在 Azure Cosmos DB，并将 "冷数据" 保留为其他存储系统，例如[Azure Blob 存储](../storage/common/storage-introduction.md)。

如果必须[跨分区和容器非规范化数据](how-to-model-partition-example.md#v2-introducing-denormalization-to-optimize-read-queries
)，可以从容器的更改源读取作为此数据复制的源。 具有更改源的实时数据复制只能确保最终一致性。 您可以[监视更改源处理器](how-to-use-change-feed-estimator.md)在处理 Cosmos 容器中的更改之前滞后的程度。

## <a name="event-sourcing"></a>事件来源

[事件来源模式](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)涉及使用只追加存储来记录针对该数据的完整操作系列。 Azure Cosmos DB 的更改源是一个很好的选择，它是在事件源结构中的中央数据存储，其中所有数据引入都建模为写入（无更新或删除）。 在这种情况下，每次写入 Azure Cosmos DB 都是一个 "事件"，并且会在更改源中包含过去事件的完整记录。 中央事件存储发布的事件的典型用法是用于维护具体化视图或与外部系统集成。 由于更改源中没有时间限制，因此你可以通过读取 Cosmos 容器的更改源的开头来重播所有过去的事件。

可以让[多个更改源使用者订阅相同容器的更改源](how-to-create-multiple-cosmos-db-triggers.md#optimizing-containers-for-multiple-triggers)。 除了[租赁容器的](change-feed-processor.md#components-of-the-change-feed-processor)预配吞吐量，使用更改源不会产生费用。 更改源可在每个容器中使用，无论其是否被使用。

作为事件来源模式，Azure Cosmos DB 是一个非常重要的中心追加持久数据存储，因为它的水平可扩展性和高可用性。 此外，更改源处理器库提供["至少一次"](change-feed-processor.md#error-handling)保证，确保不会错过任何事件的处理。

## <a name="current-limitations"></a>当前限制

更改源有重要的限制，你应该了解这些限制。 尽管 Cosmos 容器中的项将始终保留在更改源中，但更改源不是完整的操作日志。 设计利用更改源的应用程序时，需要考虑一些重要的方面。

### <a name="intermediate-updates"></a>中间更新

在更改源中只包含给定项的最新更改。 在处理更改时，您将读取最新的可用项版本。 如果在较短的时间内有对同一项的多个更新，则可能会错过处理中间更新。 如果你想要跟踪更新并能够重播对某一项的过去更新，我们建议你将这些更新作为一系列写入进行建模。

### <a name="deletes"></a>Deletes

更改源不捕获删除操作。 如果删除容器中的项，则它也会从更改源中删除。 处理此操作的最常见方法是在要删除的项上添加一个软标记。 可以添加名为 "已删除" 的属性，并在删除时将其设置为 "true"。 此文档更新将显示在更改源中。 可以在此项上设置 TTL，以便以后可以自动删除它。

### <a name="guaranteed-order"></a>保证顺序

分区键值中的更改源有一定的顺序，而不是跨分区键值。 应选择提供有意义的顺序保证的分区键。

例如，请考虑使用事件来源设计模式的零售应用程序。 在此应用程序中，不同的用户操作是每个 "事件"，作为写入 Azure Cosmos DB 建模。 假设某些示例事件按以下顺序发生：

1. 客户将商品添加到购物车
2. 客户将项目 B 添加到其购物车
3. 客户从购物车中删除商品 A
4. 客户支票和购物车的内容已寄送

为每个客户保留当前购物车内容的具体化视图。 此应用程序必须确保按事件发生的顺序处理这些事件。 例如，如果在删除项目之前要处理购物车结帐，则客户可能会已寄送物品，而不是所需的项目 B。为了保证这四个事件按它们出现的顺序进行处理，它们应该位于相同的分区键值中。 如果选择**用户名**（每个客户都有唯一的用户名）作为分区键，则可以确保这些事件在更改源中的显示顺序与它们写入 Azure Cosmos DB 的顺序相同。

## <a name="examples"></a>示例

下面是一些真实的更改源代码示例，这些代码示例超出了 Microsoft 文档中提供的示例的范围：

- [更改源简介](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html)
- [更改源中心的 IoT 用例](https://github.com/AzureCosmosDB/scenario-based-labs)
- [围绕更改源中心的零售用例](https://github.com/AzureCosmosDB/scenario-based-labs)

## <a name="next-steps"></a>后续步骤

* [更改源概述](change-feed.md)
* [读取更改源的选项](read-change-feed.md)
* [将更改源与 Azure Functions 配合使用](change-feed-functions.md)