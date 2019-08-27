---
title: 使用更改源估算器 - Azure Cosmos DB
description: 了解如何使用更改源估算器分析更改源处理器的进度
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 08/15/2019
ms.author: maquaran
ms.openlocfilehash: f72a3bfcfd6b4f480977510963f49f73a7207e1f
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615288"
---
# <a name="use-the-change-feed-estimator"></a>使用更改源估算器

本文介绍如何监视[更改源处理器](./change-feed-processor.md)实例在读取更改源时的进度。

## <a name="why-is-monitoring-progress-important"></a>为什么监视进度很重要？

更改源处理器充当一个指针，它会在[更改源](./change-feed.md)中向前移动，并将更改传递给委托实现。 

更改源处理器部署可以按特定速率处理更改，具体取决于可用的资源，例如 CPU、内存、网络，等等。

如果该速率低于更改在 Azure Cosmos 容器中的发生速率，则处理器就会开始出现滞后现象。

确定此情况后，我们就可以决定是否需要缩放更改源处理器部署。

## <a name="implement-the-change-feed-estimator"></a>实现更改源估算器

与[更改源处理器](./change-feed-processor.md)一样，更改源估算器充当一个推送模型。 此估算器会度量上一个处理项（按租用容器的状态定义）和容器中的最新更改之间的差异，并将该值推送到某个委托。 进行度量的时间间隔也可以自定义，默认值为 5 秒。

例如，如果更改源处理器定义如下：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartProcessorEstimator)]

那么，若要初始化某个估算器来度量该处理器，正确的方式是使用 `GetChangeFeedEstimatorBuilder`，如下所示：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartEstimator)]

其中的处理器和估算器共享同一 `leaseContainer` 和同一名称。

另外有两个参数，一个是委托，其接收的数字表示**有多少更改待处理器读取**，另一个是需要进行该度量的时间间隔。

例如，下面是用于接收估算的委托：

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=EstimationDelegate)]

可以将此估算发送给监视解决方案，并通过它了解进度随时间的变化情况。

> [!NOTE]
> 更改源估算器不需部署在更改源处理器中，也不需部署在同一项目中。 它可以是独立的，可以运行在完全不同的实例中。 它只需使用同一名称和租用配置。

## <a name="additional-resources"></a>其他资源

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [GitHub 上的用法示例](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/usage/changefeed)
* [GitHub 上的其他示例](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>后续步骤

现在，可以通过以下文章继续详细了解更改源处理器：

* [更改源处理器概述](change-feed-processor.md)
* [更改源处理器开始时间](how-to-configure-change-feed-start-time.md)
