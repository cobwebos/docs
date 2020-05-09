---
title: Azure Cosmos DB 更改源处理器
description: 了解如何使用 Azure Cosmos DB 更改源处理器来读取更改源（更改源处理器的组件）
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/06/2020
ms.reviewer: sngun
ms.openlocfilehash: aa9b090627b6f27a54b67c361b45b6f99e3a6338
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982371"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Azure Cosmos DB 更改源处理器

更改源处理器是 [Azure Cosmos DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3) 的一部分。 它可以简化读取更改源的过程，并有效地在多个使用者之间分配事件处理负载。

更改源处理器库的主要优势在于它的容错行为，保证传送更改源中的所有事件“至少一次”。

## <a name="components-of-the-change-feed-processor"></a>更改源处理器的组件

实现更改源处理器需要四个主要组件：

1. **监视的容器：** 监视的容器是用于生成更改源的数据。 对监视容器的任何插入和更新都会反映在容器的更改源中。

1. **租约容器：** 租约容器充当状态存储，协调处理跨多个辅助角色的更改源。 租约容器可以存储在受监视容器所在的同一个帐户中，也可以存储在单独的帐户中。

1. **主机：** 主机是使用更改源处理器侦听更改的应用程序实例。 采用相同租约配置的多个实例可以并行运行，但每个实例应使用不同的**实例名称**。

1. **委托：** 委托是定义开发人员要对更改源处理器读取的每批更改执行的操作的代码。 

若要进一步了解更改源处理器的四个元素是如何协同工作的，请看下图中的一个示例。 受监视容器存储文档，并将“City”用作分区键。 可以看到，分区键值分布在包含项的范围内。 有两个主机实例以及更改源处理器正在向每个实例分配不同的分区键值范围，以最大程度地提高计算分布率。 每个范围都是并行读取的，其进度与租赁容器中的其他范围分开维护。

![更改源处理器示例](./media/change-feed-processor/changefeedprocessor.png)

## <a name="implementing-the-change-feed-processor"></a>实现更改源处理器

入口点始终是受监视容器。从 `Container` 实例调用 `GetChangeFeedProcessorBuilder`：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

其中，第一个参数是描述此处理器的目标的唯一名称，第二个参数是要处理更改的委托实现。 

委托的示例如下：


[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

最后，使用 `WithInstanceName` 定义此处理器实例的名称，该实例是用于维护包含 `WithLeaseContainer` 的状态租约的容器。

调用 `Build` 将会获得可以通过调用 `StartAsync` 启动的处理器实例。

## <a name="processing-life-cycle"></a>处理生命周期

主机实例的正常生命周期如下：

1. 读取更改源。
1. 如果没有任何更改，则休眠预定义的时间（可在生成器中使用 `WithPollInterval` 自定义）并转到 #1。
1. 如果有更改，则将更改发送到**委托**。
1. 委托**成功**处理更改后，使用最新的处理时间点更新租约存储，然后转到 #1。

## <a name="error-handling"></a>错误处理。

更改源处理器能够弹性应对用户代码错误。 这意味着，如果委托实现出现未经处理的异常（步骤 #4），则处理该特定更改批的线程将会停止，并创建一个新线程。 新线程将检查租约存储中该分区键值范围的最新时间点是什么，从该时间点处重启，并有效地将同一批更改发送到委托。 此行为将继续运行，直到你的委托正确地处理更改，这是因为如果委托代码引发异常，则会重试该批处理。

若要防止更改源处理器不断地重试相同的更改批，应在委托代码中添加逻辑，以便在出现异常时将文档写入死信队列。 此设计可确保跟踪未处理的更改，同时仍然能够继续处理将来的更改。 死信队列可能只是另一个 Cosmos 容器。 确切的数据存储并不重要，只是保留未处理的更改。

此外，可以使用[更改源估计器](how-to-use-change-feed-estimator.md)来监视更改源处理器实例读取更改源时的进度。 除了监视更改源处理器是否 "停滞" 持续重试相同批次更改外，您还可以了解更改源处理器是否滞后，因为可用资源（如 CPU、内存和网络带宽）。

## <a name="dynamic-scaling"></a>动态缩放

如简介中所述，更改源处理器可以自动在多个实例之间分配计算资源。 可以使用更改源处理器部署应用程序的多个实例并利用这些实例。唯一的关键要求是：

1. 所有实例应采用相同的租约容器配置。
1. 所有实例应使用相同的工作流名称。
1. 每个实例需要使用不同的实例名称 (`WithInstanceName`)。

如果符合这三个条件，则更改源处理器将使用均等分配算法，将租约容器中的所有租约分配到所有正在运行的实例，并将计算并行化。 在给定的时间，一个租约只能由一个实例拥有，因此，最大实例数等于租约数。

实例的数量可以增加和减少，更改源处理器会通过相应地重新分配来动态调整负载。

而且，由于吞吐量或存储量的增加，更改源处理器可以动态调整以适应容器规模。 当容器增长时，更改源处理器通过动态增加租约并在现有实例之间分配新的租约来透明地处理这些情况。

## <a name="change-feed-and-provisioned-throughput"></a>更改源和预配吞吐量

消耗的 RU 会产生费用，将数据移入和移出 Cosmos 容器始终会消耗 RU。 租约容器消耗的 RU 也会产生费用。

## <a name="additional-resources"></a>其他资源

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [GitHub 上的用法示例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [GitHub 上的其他示例](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>后续步骤

现在，可以通过以下文章继续详细了解更改源处理器：

* [更改源概述](change-feed.md)
* [更改源请求模型](change-feed-pull-model.md)
* [如何从更改源处理器库迁移](how-to-migrate-from-change-feed-library.md)
* [使用更改源估算器](how-to-use-change-feed-estimator.md)
* [更改源处理器开始时间](how-to-configure-change-feed-start-time.md)
