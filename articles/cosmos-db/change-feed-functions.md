---
title: 如何将 Azure Cosmos DB 更改源与 Azure Functions 配合使用
description: 使用 Azure Functions 连接到 Azure Cosmos DB 更改源。 稍后，你可以创建在每个新事件时触发的被动 Azure 函数。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: 7a74635551d8416bf60689b1f1403f29883e81bd
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851367"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>具有 Azure Cosmos DB 和 Azure Functions 的基于事件的无服务器体系结构

Azure Functions 提供连接到[更改源](change-feed.md)的最简单方法。 你可以创建小的反应性 Azure Functions，它将在 Azure Cosmos 容器的更改源中的每个新事件上自动触发。

![使用 Cosmos DB 的 Azure Functions 触发器的基于无服务器事件的函数](./media/change-feed-functions/functions.png)

使用[Cosmos DB 的 Azure Functions 触发器](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md)，可以利用[更改源处理器](./change-feed-processor.md)的缩放和可靠的事件检测功能，而无需维护任何[辅助角色基础结构](./change-feed-processor.md)。 只需专注于 Azure 函数的逻辑，而无需担心事件源管道的其余部分。 甚至可以使用任何其他[Azure Functions 绑定](../azure-functions/functions-triggers-bindings.md#supported-bindings)来混合该触发器。

> [!NOTE]
> 目前，仅支持将 Cosmos DB Azure Functions 触发器用于核心（SQL） API。

## <a name="requirements"></a>要求

若要实现基于无服务器事件的流，需要：

* **受监视的容器**：监视容器是受监视的 Azure Cosmos 容器，它存储从中生成更改源的数据。 对受监视容器的任何插入、对所监视容器的更新都会反映在容器的更改源中。
* **租赁容器**：租赁容器在多个和动态无服务器 Azure 函数实例之间维护状态，并启用动态缩放。 此租用容器可由 Cosmos DB Azure Functions 触发器手动或自动创建。 若要自动创建租约容器，请在[配置](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)中设置*CreateLeaseCollectionIfNotExists*标志。 分区的租用容器需要具有 `/id` 分区键定义。

## <a name="create-your-azure-functions-trigger-for-cosmos-db"></a>为 Cosmos DB 创建 Azure Functions 触发器

现在跨所有 Azure Functions IDE 和 CLI 集成支持创建包含 Cosmos DB 的 Azure Functions 触发器的 Azure 函数：

* Visual studio 用户的[Visual Studio 扩展](../azure-functions/functions-develop-vs.md)。
* Visual Studio Code 用户的[Visual Studio Code 扩展](/azure/javascript/tutorial-vscode-serverless-node-01)。
* 最后是用于跨平台 IDE 不可知体验的[核心 CLI 工具](../azure-functions/functions-run-local.md#create-func)。

## <a name="run-your-trigger-locally"></a>在本地运行触发器

你可以使用[Azure Cosmos DB 模拟器](./local-emulator.md)在[本地运行 Azure 函数](../azure-functions/functions-develop-local.md)，以创建和开发无服务器事件的流，而无需 Azure 订阅或产生任何费用。

如果要测试云中的实时方案，可以[免费试用 Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) ，无需任何信用卡或 Azure 订阅。

## <a name="next-steps"></a>后续步骤

你现在可以继续在以下文章中了解有关更改源的详细信息：

* [更改源概述](change-feed.md)
* [读取更改源的方式](read-change-feed.md)
* [使用更改源处理器库](change-feed-processor.md)
* [如何使用更改源处理器库](change-feed-processor.md)
* [使用 Azure Cosmos DB 和 Azure Functions 进行无服务器数据库计算](serverless-computing-database.md)
