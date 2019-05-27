---
title: 如何将 Azure Cosmos DB 更改源与 Azure Functions 配合使用
description: 将 Azure Cosmos DB 更改源与 Azure Functions 配合使用
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 08429ca76823b9e6c80a197cc390a5964c4198e6
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969004"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>使用 Azure Cosmos DB 和 Azure Functions 无服务器基于事件的体系结构

Azure Functions 提供连接到的最简单办法[更改源](change-feed.md)。 Azure Cosmos 容器的更改源中的每个新事件，可以创建将自动触发的小型反应的 Azure 函数。

![使用 Azure Cosmos DB 触发器的无服务器基于事件的函数](./media/change-feed-functions/functions.png)

与[Azure Cosmos DB 触发器](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger)，可以利用[更改源处理器](./change-feed-processor.md)的缩放和可靠的事件检测功能，而无需维护任何[辅助角色基础结构](./change-feed-processor.md#implementing-the-change-feed-processor-library)。 只需专注 Azure 函数的逻辑而无需担心事件溯源管道的其余部分。 你甚至可以混合触发器和任何其他[Azure Functions 绑定](../azure-functions/functions-triggers-bindings.md#supported-bindings)。

> [!NOTE]
> 目前，使用与核心 (SQL) API 仅支持 Azure Cosmos DB 触发器。

## <a name="requirements"></a>要求

若要实现无服务器的基于事件的流，需要：

* **受监视的容器**:受监视的容器是受监视的 Azure Cosmos 容器并将存储在从其生成更改源的数据。 任何插入和对受监视的容器的更改 (例如，CRUD) 将反映在该容器的更改源。
* **租赁容器**:租赁容器维护跨多个状态和动态的无服务器 Azure 函数实例，并使动态缩放。 此租约容器可以手动或自动创建 Azure Cosmos DB Trigger.To 自动创建租用容器，将设置*CreateLeaseCollectionIfNotExists*标记中[配置](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration). 已分区的租约容器所需具有`/id`分区键定义。

## <a name="create-your-azure-cosmos-db-trigger"></a>创建 Azure Cosmos DB 触发器

在所有 Azure Functions IDE 和 CLI 集成现在支持使用 Azure Cosmos DB 触发器创建 Azure 函数：

* [Visual Studio 扩展](../azure-functions/functions-develop-vs.md)对于 Visual Studio 用户。
* [Visual Studio 核心扩展](https://code.visualstudio.com/tutorials/functions-extension/create-function)为 Visual Studio Code 用户。
* 最后[Core CLI 工具](../azure-functions/functions-run-local.md#create-func)为用户提供跨平台 IDE 不可知的体验。

## <a name="run-your-azure-cosmos-db-trigger-locally"></a>在本地运行 Azure Cosmos DB 触发器

你可以运行你[本地 Azure Function](../azure-functions/functions-develop-local.md)与[Azure Cosmos DB 模拟器](./local-emulator.md)来创建和开发无服务器而无需 Azure 订阅或不会产生任何费用基于事件的流。

如果你想要在云中测试实时方案，可以[免费试用 Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)而无需任何信用卡或所需的 Azure 订阅。

## <a name="next-steps"></a>后续步骤

你现在可以继续若要详细了解更改源中的以下文章：

* [更改源概述](change-feed.md)
* [读取更改源的方式](read-change-feed.md)
* [使用更改源处理器库](change-feed-processor.md)
* [如何使用更改源处理器库](change-feed-processor.md)
* [使用 Azure Cosmos DB 和 Azure Functions 进行无服务器数据库计算](serverless-computing-database.md)
