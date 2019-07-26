---
title: 如何将 Azure Cosmos DB 更改源与 Azure Functions 配合使用
description: 将 Azure Cosmos DB 更改源与 Azure Functions 配合使用
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 299799566f0d54cc3fa017b032e15b387fe8cdd5
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467924"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Azure Cosmos DB 和 Azure Functions 的基于事件的无服务器体系结构

Azure Functions 提供连接到[更改源](change-feed.md)的最简单方法。 可以创建小型的反应式 Azure Functions，每当 Azure Cosmos 容器的更改源中出现新事件时，就会将其触发。

![使用 Cosmos DB 的 Azure Functions 触发器的基于无服务器事件的函数](./media/change-feed-functions/functions.png)

使用[Cosmos DB 的 Azure Functions 触发器](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger), 可以利用[更改源处理器](./change-feed-processor.md)的缩放和可靠的事件检测功能, 而无需维护任何[辅助角色基础结构](./change-feed-processor.md)。 只需专注于 Azure 函数的逻辑，而无需考虑事件溯源管道的剩余。 甚至可以将触发器和任何其他 [Azure Functions 绑定](../azure-functions/functions-triggers-bindings.md#supported-bindings)搭配使用。

> [!NOTE]
> 目前, 仅支持将 Cosmos DB Azure Functions 触发器用于核心 (SQL) API。

## <a name="requirements"></a>要求

若要实现基于事件的无服务器流，需要：

* **受监视的容器**：受监视的容器是正在受到监视的 Azure Cosmos 容器，它存储生成了更改源的数据。 对受监视容器的任何插入和更改（例如 CRUD）都会反映在容器的更改源中。
* **租约容器**：租约容器维护多个动态无服务器 Azure 函数实例的状态，并启用动态缩放。 此租用容器可由 Cosmos DB Azure Functions 触发器手动或自动创建。 若要自动创建租约容器, 请在[配置](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration)中设置*CreateLeaseCollectionIfNotExists*标志。 分区的租约容器需要包含 `/id` 分区键定义。

## <a name="create-your-azure-functions-trigger-for-cosmos-db"></a>为 Cosmos DB 创建 Azure Functions 触发器

现在跨所有 Azure Functions IDE 和 CLI 集成支持创建包含 Cosmos DB 的 Azure Functions 触发器的 Azure 函数:

* 面向 Visual Studio 用户的 [Visual Studio 扩展](../azure-functions/functions-develop-vs.md)。
* 面向 Visual Studio Code 用户的 [Visual Studio Core 扩展](https://code.visualstudio.com/tutorials/functions-extension/create-function)。
* 最后是适用于跨平台 IDE 不可知体验的 [Core CLI 工具](../azure-functions/functions-run-local.md#create-func)。

## <a name="run-your-trigger-locally"></a>在本地运行触发器

可以使用 [Azure Cosmos DB 模拟器](./local-emulator.md)[在本地运行 Azure 函数](../azure-functions/functions-develop-local.md)，以创建和开发基于事件的无服务器流，而无需使用 Azure 订阅，也不会产生任何费用。

如果要测试云中的实时方案, 可以[免费试用 Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) , 无需任何信用卡或 Azure 订阅。

## <a name="next-steps"></a>后续步骤

接下来，请通过以下文章继续详细了解更改源：

* [更改源概述](change-feed.md)
* [读取更改源的方式](read-change-feed.md)
* [使用更改源处理器库](change-feed-processor.md)
* [如何使用更改源处理器库](change-feed-processor.md)
* [使用 Azure Cosmos DB 和 Azure Functions 进行无服务器数据库计算](serverless-computing-database.md)
