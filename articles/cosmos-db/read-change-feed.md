---
title: 访问 Azure Cosmos DB 中的更改源
description: 本文介绍了可用来读取和访问 Azure Cosmos DB 中的更改源的各种选项。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 3d30c9f946f97f06c1a3ba1cd2e77e1ab151a572
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754873"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>读取 Azure Cosmos DB 更改源

可以使用下列任意选项来处理 Azure Cosmos DB 更改源：

* 使用 Azure Functions
* 使用更改源处理器库
* 使用 Azure Cosmos DB SQL API SDK

## <a name="using-azure-functions"></a>使用 Azure Functions

Azure Functions 是最简单且建议使用的选项。 为 Cosmos DB 创建 Azure Functions 触发器时，可以选择要连接的容器，并在容器发生更改时触发 Azure 函数。 可在 Azure Functions 门户、Azure Cosmos DB 门户中或以编程方式使用 SDK 创建触发器。 Visual Studio 和 VS Code 支持编写 Azure 函数，你甚至可以使用 Azure Functions CLI 进行跨平台开发。 可以在桌面上编写和调试代码，然后单击一下鼠标部署函数。 若要了解详细信息，请参阅[使用 Azure Functions 进行无服务器数据库计算](serverless-computing-database.md)和[将更改源与 Azure Functions 配合使用](change-feed-functions.md)。

## <a name="using-the-change-feed-processor-library"></a>使用更改源处理器库

更改源处理器库隐藏了复杂性并且还允许你完全控制更改源。 此库遵循观察程序模式，在此模式中，处理函数由库调用。 如果有高吞吐量的更改源，可以实例化多个客户端来读取更改源。 由于使用的是更改源处理器库，因此它会自动在不同客户端之间分配负载，不需要由你实施此逻辑。 此库会处理所有复杂性。 如果希望使用自己的负载均衡器，可以针对自定义的分区策略实现 `IPartitionLoadBalancingStrategy` 来处理更改源。 若要了解详细信息，请参阅[使用更改源处理器库](change-feed-processor.md)。

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>使用 Azure Cosmos DB SQL API SDK

使用 SDK，你可以对更改源进行低层控制。 您可以管理检查点、访问特定逻辑分区键等。如果有多个读取器，则可以使用 `ChangeFeedOptions` 将读取负载分配给不同的线程或不同的客户端。 

## <a name="next-steps"></a>后续步骤

现在，可以在以下文章中了解有关更改流的详细信息：

* [更改源概述](change-feed.md)
* [将更改源与 Azure Functions 配合使用](change-feed-functions.md)
* [使用更改源处理器库](change-feed-processor.md)
