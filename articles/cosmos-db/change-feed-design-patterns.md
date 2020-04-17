---
title: 更改 Azure Cosmos DB 中的源设计模式
description: 常见更改源设计模式概述
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 012d27b44ecfbdd460adf241742df397880f78c6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450345"
---
# <a name="change-feed-design-patterns-in-azure-cosmos-db"></a>更改 Azure Cosmos DB 中的源设计模式

Azure Cosmos DB 更改源支持高效处理具有大量写入量的大型数据集。 更改源还提供用于查询整个数据集以确定更改内容的替代方法。 本文档重点介绍常见的更改源设计模式、设计权衡和更改源限制。

Azure Cosmos DB 非常适合用于 IoT、游戏、零售和操作日志记录应用程序。 这些应用程序中的一种常见设计模式是使用数据更改来触发附加的操作。 附加操作的示例包括：

* 插入或更新项时触发通知或 API 调用。
* 对 IoT 的实时流式处理或对运营数据的实时分析处理。
* 数据移动，如与缓存、搜索引擎、数据仓库或冷存储同步。

使用 Azure Cosmos DB 中的更改源，可针对每种模式构建高效、可缩放的解决方案，如下图所示：

![使用 Azure Cosmos DB 更改源促成实时分析和事件驱动的计算方案](./media/change-feed/changefeedoverview.png)

## <a name="event-computing-and-notifications"></a>事件计算和通知

Azure Cosmos DB 更改源可以简化需要根据特定事件触发通知或调用 API 的方案。 您可以使用[更改源过程库](change-feed-processor.md)自动轮询容器以进行更改，并在每次写入或更新时调用外部 API。

您还可以根据特定条件有选择地触发通知或向 API 发送调用。 例如，如果使用[Azure 函数](change-feed-functions.md)从更改源读取 ，则可以将逻辑放入函数中，以便仅在满足特定条件时发送通知。 虽然 Azure 函数代码将在每次写入和更新期间执行，但只有满足特定条件时才会发送通知。

## <a name="real-time-stream-processing"></a>实时流处理

Azure Cosmos DB 更改源可用于 IoT 的实时流处理或操作数据的实时分析处理。
例如，您可能会从设备、传感器、基础设施和应用程序接收和存储事件数据，并使用[Spark](../hdinsight/spark/apache-spark-overview.md)实时处理这些事件。 下图显示了如何通过更改源使用 Azure Cosmos DB 实现 lambda 体系结构：

![用于引入和查询的基于 Azure Cosmos DB 的 lambda 管道](./media/change-feed/lambda.png)

在许多情况处理实现首先接收大量传入数据到临时消息队列（如 Azure 事件中心或 Apache Kafka ）。 更改源是一个伟大的替代方法，因为 Azure Cosmos DB 能够支持持续高速率的数据引入，并且保证低读取和写入延迟。 Azure Cosmos DB 更改馈送相对于消息队列的优点包括：

### <a name="data-persistence"></a>数据暂留

写入 Azure Cosmos DB 的数据将显示在更改源中，并保留到删除。 消息队列通常具有最大保留期。 例如[，Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)提供最多 90 天的数据保留时间。

### <a name="querying-ability"></a>查询能力

除了从 Cosmos 容器的更改源中读取外，还可以对存储在 Azure Cosmos DB 中的数据运行 SQL 查询。 更改源不是容器中已有数据的重复，而只是读取数据的不同机制。 因此，如果从更改源读取数据，它将始终与同一 Azure Cosmos DB 容器的查询一致。

### <a name="high-availability"></a>高可用性

Azure Cosmos DB 提供高达 99.999% 的读写可用性。 与许多消息队列不同，Azure Cosmos DB 数据可以轻松全局分布，并且配置的[RTO（恢复时间目标）](consistency-levels-tradeoffs.md#rto)为零。

在更改源中处理项后，可以生成具体化视图，并在 Azure Cosmos DB 中保留聚合值。 例如，若要使用 Azure Cosmos DB 构建游戏，可使用更改源，根据已完成的游戏的分数实时更新排行榜。

## <a name="data-movement"></a>数据移动

您还可以从更改源中读取实时数据移动。

例如，更改源可帮助您高效地执行以下任务：

* 使用存储在 Azure Cosmos DB 中的数据更新缓存、搜索索引或数据仓库。

* 使用不同的逻辑分区键零停机迁移到其他 Azure Cosmos 帐户或其他 Azure Cosmos 容器。

* 实现应用程序级数据分层和存档。 例如，您可以将"热数据"存储在 Azure Cosmos DB 中，并将"冷数据"老化到其他存储系统，如[Azure Blob 存储](../storage/common/storage-introduction.md)。

当必须[跨分区和容器取消数据规范化](how-to-model-partition-example.md#v2-introducing-denormalization-to-optimize-read-queries
)时，可以从容器的更改源中读取此数据复制的源。 使用更改源进行实时数据复制只能保证最终的一致性。 您可以[监视更改馈送处理器](how-to-use-change-feed-estimator.md)在处理 Cosmos 容器中的更改时落后多远。

## <a name="event-sourcing"></a>活动采购

[事件源模式](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)涉及使用仅追加存储来记录对该数据的完整系列操作。 Azure Cosmos DB 的更改源是事件源体系结构中的中央数据存储的绝佳选择，其中所有数据引入都建模为写入（不更新或删除）。 在这种情况下，每个写入 Azure Cosmos DB 都是一个"事件"，您将在更改源中记录过去的事件的完整记录。 中心事件存储发布的事件的典型用途是维护具体化视图或与外部系统集成。 由于更改源中没有保留时间限制，因此可以通过从 Cosmos 容器的更改源的开头读取来重播所有过去的事件。

您可以[让多个更改源使用者订阅同一容器的更改源](how-to-create-multiple-cosmos-db-triggers.md#optimizing-containers-for-multiple-triggers)。 除了[租用容器的](change-feed-processor.md#components-of-the-change-feed-processor)预配吞吐量外，使用更改源不花费任何成本。 无论更改源是否被使用，更改源在每个容器中都可用。

Azure Cosmos DB 是事件源模式中仅集中追加的持久数据存储，因为它具有水平可扩展性和高可用性的优势。 此外，更改源处理器库提供["至少一次"](change-feed-processor.md#error-handling)保证，确保您不会错过处理任何事件。

## <a name="current-limitations"></a>当前限制

更改源具有您应该了解的重要限制。 虽然 Cosmos 容器中的项将始终保留在更改源中，但更改源不是完整的操作日志。 在设计利用更改源的应用程序时，需要考虑一些重要方面。

### <a name="intermediate-updates"></a>中间更新

更改源中仅包含给定项的最新更改。 处理更改时，您将读取最新的可用物料版本。 如果在短时间内对同一项有多个更新，则可能会错过处理中间更新。 如果要跟踪更新并能够重播项目过去的更新，我们建议将这些更新建模为一系列写入。

### <a name="deletes"></a>Deletes

更改源不捕获删除。 如果从容器中删除项目，也会从更改源中删除该项目。 处理此操作的最常见方法是在要删除的项上添加软标记。 您可以添加名为"已删除"的属性，并在删除时将其设置为"true"。 此文档更新将显示在更改源中。 您可以在此项目上设置 TTL，以便以后可以自动删除它。

### <a name="guaranteed-order"></a>保证订单

分区键值中的更改源中有保证的顺序，但不跨分区键值。 您应该选择一个分区键，为您提供有意义的订单保证。

例如，考虑使用事件源设计模式的零售应用程序。 在此应用程序中，不同的用户操作是每个"事件"，这些"事件"建模为写入 Azure Cosmos DB。 想象一下，如果一些示例事件按以下顺序发生：

1. 客户将项目 A 添加到购物车
2. 客户将项目 B 添加到购物车
3. 客户从购物车中删除项目 A
4. 客户签出和购物车内容已发货

为每个客户维护当前购物车内容的物化视图。 此应用程序必须确保按这些事件发生的顺序处理这些事件。 例如，如果在项目 A 删除之前处理购物车结帐，则客户很可能已装运项目 A，而不是所需的项目 B。为了保证这四个事件按其发生顺序进行处理，它们应属于相同的分区键值。 如果选择**用户名**（每个客户都有唯一的用户名）作为分区键，则可以保证这些事件以写入 Azure Cosmos DB 的相同顺序显示在更改源中。

## <a name="examples"></a>示例

下面是一些超出 Microsoft 文档中示例范围的实际更改源代码示例：

- [更改源简介](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html)
- [以更改源为中心的 IoT 用例](https://github.com/AzureCosmosDB/scenario-based-labs)
- [以更改源为中心的零售用例](https://github.com/AzureCosmosDB/scenario-based-labs)

## <a name="next-steps"></a>后续步骤

* [更改源概述](change-feed.md)
* [读取更改源的选项](read-change-feed.md)
* [将更改源与 Azure Functions 配合使用](change-feed-functions.md)