---
title: 读取 Azure Cosmos DB 更改源
description: 本文介绍可用于读取和访问 Azure Cosmos DB 中的更改源的各种选项。
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.reviewer: sngun
ms.openlocfilehash: d7408f3b3e955d397ba4a54d07323f80dd72c3d3
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83697345"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>读取 Azure Cosmos DB 更改源

可以使用推送模型或拉取模型来处理 Azure Cosmos DB 更改源。 如果使用推送模型，客户端将从服务器请求工作，并且可以获得用于处理更改的业务逻辑。 但是，在检查更改以及存储上次已处理更改的状态时，所遇到的复杂情况将在服务器上进行处理。

如果使用拉取模型，服务器会请求工作（通常从中心工作队列请求工作）。 在这种情况下，客户端不仅可以获得用于处理更改的业务逻辑，而且还能存储上次已处理更改的状态，这样，在多个客户端并行处理更改时可以处理负载均衡和处理错误。

从 Azure Cosmos DB 更改源读取时，我们通常建议使用推送模型，因为这样就无需考虑以下事项：

- 在更改源中轮询以后的更改。
- 存储上次已处理更改的状态。 从更改源读取时，此信息会自动存储在[租约容器](change-feed-processor.md#components-of-the-change-feed-processor)中。
- 在使用更改的多个客户端之间进行负载均衡。 例如，如果某个客户端无法赶上处理更改的进度，而另一个客户端具有可用的容量。
- [处理错误](change-feed-processor.md#error-handling)。 例如，在代码中出现未经处理的异常或者发生暂时性网络问题后，自动重试未正确处理的已失败更改。

使用 Azure Cosmos DB 更改源的大多数方案都会使用推送模型选项之一。 但在某些情况下，你可能想要对拉取模型进行更低级别的控制。 其中包括：

- 从特定的分区键读取更改
- 控制客户端接收要处理的更改的速度
- 对更改源中的现有数据执行一次性读取（例如，执行数据迁移）

## <a name="reading-change-feed-with-a-push-model"></a>使用推送模型读取更改源

使用推送模型是从更改源读取数据的最简单方法。 可通过两种方式使用推送模型从更改源读取数据：[Azure Functions Cosmos DB 触发器](change-feed-functions.md)和[更改源处理器库](change-feed-processor.md)。 Azure Functions 在后台使用更改源处理器，因此这两种读取更改源的方式非常类似。 可将 Azure Functions 简单地视为更改源处理器的托管平台，两者并非完全不同的更改源读取方式。

### <a name="azure-functions"></a>Azure Functions

如果你对更改源还不太了解，则 Azure Functions 是最简单的选项。 由于其简易性，它也是大多数更改源用例的建议选项。 为 Azure Cosmos DB 创建 Azure Functions 触发器时，请选择要连接的容器，每当该容器中发生更改时，都会触发 Azure 函数。 由于 Azure Functions 在后台使用更改源处理器，因此它会自动在容器的[分区](partition-data.md)之间并行化更改处理操作。

使用 Azure Functions 进行开发是一种简单体验，可能比在你自己的平台上部署更改源处理器更快。 可以使用 Azure Functions 门户来创建触发器，也可以使用 SDK 以编程方式这样做。 Visual Studio 和 VS Code 支持编写 Azure 函数，你甚至可以使用 Azure Functions CLI 进行跨平台开发。 可以在桌面上编写和调试代码，然后单击一下鼠标部署函数。 有关详细信息，请参阅[使用 Azure Functions 进行无服务器数据库计算](serverless-computing-database.md)和[将更改源与 Azure Functions 配合使用](change-feed-functions.md)。

### <a name="change-feed-processor-library"></a>更改源处理器库

更改源处理器库可让你更好地控制更改源，同时还能消除大部分复杂性。 更改源处理器库遵循观察程序模式，在此模式中，处理函数由库调用。 更改源处理器库将自动检查更改，如果找到更改，则将这些更改“推送”到客户端。 如果有高吞吐量的更改源，可以实例化多个客户端来读取更改源。 更改源处理器库自动在不同客户端之间划分负载。 你不需要实现任何用于在多个客户端之间进行负载均衡的逻辑，或者任何用于维护租约状态的逻辑。

更改源处理器库保证传送所有更改“至少一次”。 换言之，如果你使用更改源处理器库，系统会针对更改源中的每个项成功调用处理函数。 如果处理函数中的业务逻辑发生未经处理的异常，则会不断重试已失败的更改，直到成功处理这些更改。 若要防止更改源处理器陷入不断重试相同更改的状态，请在处理函数中添加逻辑，以便在出现异常时将文档写入死信队列。 详细了解[错误处理](change-feed-processor.md#error-handling)。

在 Azure Functions 中，有关如何处理错误的建议是相同的。 仍应在委托代码中添加逻辑，以便在出现异常时将文档写入死信队列。 但是，如果 Azure 函数中发生未经处理的异常，系统不会自动重试生成异常的更改。 如果业务逻辑中发生未经处理的异常，Azure 函数会继续处理下一项更改。 Azure 函数不会重试相同的已失败更改。

与使用 Azure Functions 时一样，使用更改源处理器库进行开发也很简单。 但是，你要负责为更改源处理器部署一个或多个主机。 主机是使用更改源处理器侦听更改的应用程序实例。 尽管 Azure Functions 提供自动缩放功能，但你要负责缩放你的主机。 有关详细信息，请参阅[使用更改源处理器](change-feed-processor.md#dynamic-scaling)。 更改源处理器库是 [Azure Cosmos DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3) 的一部分。

## <a name="reading-change-feed-with-a-pull-model"></a>使用拉取模型读取更改源

[更改源拉取模型](change-feed-pull-model.md)可让你按自己的步调使用更改源。 更改必须由客户端请求，系统不会自动轮询更改。 若要将上次已处理的更改永久“加入书签”（类似于推送模型的租约容器），需要[保存一个继续标记](change-feed-pull-model.md#saving-continuation-tokens)。

使用更改源拉取模型可以对更改源进行更低级别的控制。 使用拉取模型读取更改源时，有三个选项：

- 读取整个容器的更改
- 读取特定 [FeedRange](change-feed-pull-model.md#using-feedrange-for-parallelization) 的更改
- 读取特定分区键值的更改

就像使用更改源处理器时一样，可以在多个客户端之间并行化更改处理操作。 但是，拉取模型不会自动处理客户端之间的负载均衡。 使用拉取模型并行化更改源的处理时，首先需要获取 FeedRange 列表。 FeedRange 跨越一系列分区键值。 需要通过一个业务流程协调程序进程来获取 FeedRange 并在计算机之间分配这些 FeedRange。 然后，可以使用这些 FeedRange 让多台计算机并行读取更改源。

拉取模型不提供内置的“至少一次”传送保证。 拉取模型允许进行较低级别的控制，可让你确定如何处理错误。

> [!NOTE]
> 更改源拉取模型当前[仅在 Azure Cosmos DB .NET SDK 中提供了预览版](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.9.0-preview)。 该预览版尚不可用于其他 SDK 版本。

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>适用于 Cassandra 和 MongoDB 的 API 中的更改源

在 MongoDB API 中，更改源功能显示为更改流；在 Cassandra API 中，它是以包含谓词的查询提供的。 若要详细了解 MongoDB API 的实现细节，请参阅 [Azure Cosmos DB API for MongoDB 中的更改流](mongodb-change-streams.md)。

本机 Apache Cassandra 提供变更数据捕获 (CDC)。CDC 是一种机制，用于标记要存档的特定表，并在达到 CDC 日志的可配置磁盘空间大小时拒绝写入这些表。 Azure Cosmos DB API for Cassandra 中的更改源功能增强了通过 CQL 使用谓词查询更改的功能。 若要详细了解实现细节，请参阅 [Azure Cosmos DB API for Cassandra 中的更改源](cassandra-change-feed.md)。

## <a name="next-steps"></a>后续步骤

现在，可以通过以下文章继续详细了解更改源：

* [更改源概述](change-feed.md)
* [将更改源与 Azure Functions 配合使用](change-feed-functions.md)
* [使用更改源处理器库](change-feed-processor.md)
