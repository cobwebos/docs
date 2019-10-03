---
title: 如何创建多个独立的适用于 Cosmos DB 的 Azure Functions 触发器
description: 了解如何配置多个独立的适用于 Cosmos DB 的 Azure Functions 触发器以创建事件驱动的体系结构。
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 987136bf8aba1313e1bef21f58691bf9a860ea32
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093382"
---
# <a name="create-multiple-azure-functions-triggers-for-cosmos-db"></a>创建多个适用于 Cosmos DB 的 Azure Functions 触发器

本文介绍如何配置多个适用于 Cosmos DB 的 Azure Functions 触发器，以并行工作并独立地对更改做出反应。

![基于事件的无服务器 Functions 使用适用于 Cosmos DB 的 Azure Functions 触发器并共享租用容器](./media/change-feed-functions/multi-trigger.png)

## <a name="event-based-architecture-requirements"></a>基于事件的体系结构要求

使用 [Azure Functions](../azure-functions/functions-overview.md) 生成无服务器体系结构时，[建议](../azure-functions/functions-best-practices.md#avoid-long-running-functions)创建协同工作的小型函数集，而不是长时间运行的大型函数。

在使用[适用于 Cosmos DB 的 Azure Functions 触发器](./change-feed-functions.md)生成基于事件的无服务器流时，只要特定 [Azure Cosmos 容器](./databases-containers-items.md#azure-cosmos-containers)中存在新事件，就会遇到要执行多项操作的方案。 如果要触发的操作彼此独立，理想的解决方案是为每个要执行的操作创建一个适用于 Cosmos DB 的 Azure Functions 触发器，所有触发器侦听同一 Azure Cosmos 容器上的更改。

## <a name="optimizing-containers-for-multiple-triggers"></a>优化多个触发器的容器

鉴于适用于 Cosmos DB 的 Azure Functions 触发器的要求，我们需要第二个容器来存储状态，也称为租用容器。 这是否意味着每个 Azure 函数需要一个单独的租用容器？

可以使用以下两个选项：

* 为每个函数创建一个租用容器：除非使用[共享吞吐量数据库](./set-throughput.md#set-throughput-on-a-database)，否则此方法可能会转化为其他成本。 请记住，容器级别的最小吞吐量是 400 个[请求单位](./request-units.md)，对于租用容器，它仅用于检查进度和维护状态。
* 创建一个租用容器并为所有函数共享：第二个选项更好地利用了容器上预配的请求单元，因为它允许多个 Azure Functions 共享和使用相同的预配吞吐量。

本文的目的是指导你完成第二个选项。

## <a name="configuring-a-shared-leases-container"></a>配置共享的租用容器

若要配置共享的租用容器，需要在触发器上进行的唯一额外配置是在使用 C# 时添加 `LeaseCollectionPrefix` [属性](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---c-attributes)，或在使用 JavaScript 时添加 `leaseCollectionPrefix` [属性](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---javascript-example)。 属性的值应是特定触发器的逻辑描述符。

例如，如果有三个触发器：一个发送电子邮件，一个执行聚合以创建具体化视图，一个将更改发送到另一个存储，供以后分析，那么可以将“电子邮件”的 `LeaseCollectionPrefix` 分配到第一个触发器，“具体化”分配到第二个触发器，“分析”分配到第三个触发器。

重要的是，所有三个触发器都可以使用相同的租用容器配置（帐户、数据库和容器名称）。

请参阅以下非常简单的代码示例，该示例使用 C# 中的 `LeaseCollectionPrefix` 属性：

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

[FunctionName("SendEmails")]
public static void SendEmails([CosmosDBTrigger(
    databaseName: "ToDoItems",
    collectionName: "Items",
    ConnectionStringSetting = "CosmosDBConnection",
    LeaseCollectionName = "leases",
    LeaseCollectionPrefix = "emails")]IReadOnlyList<Document> documents,
    ILogger log)
{
    ...
}

[FunctionName("MaterializedViews")]
public static void MaterializedViews([CosmosDBTrigger(
    databaseName: "ToDoItems",
    collectionName: "Items",
    ConnectionStringSetting = "CosmosDBConnection",
    LeaseCollectionName = "leases",
    LeaseCollectionPrefix = "materialized")]IReadOnlyList<Document> documents,
    ILogger log)
{
    ...
}
```

对于 JavaScript，可以使用 `leaseCollectionPrefix` 属性在 `function.json` 文件上应用配置：

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "CosmosDBConnection",
    "databaseName": "ToDoItems",
    "collectionName": "Items",
    "leaseCollectionPrefix": "emails"
},
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "CosmosDBConnection",
    "databaseName": "ToDoItems",
    "collectionName": "Items",
    "leaseCollectionPrefix": "materialized"
}
```

> [!NOTE]
> 始终监视共享租用容器上预配的请求单元。 共享租用容器的每个触发器都将增加吞吐量平均消耗，因此可能需要在增加使用它的 Azure Functions 的数量时增加预配的吞吐量。

## <a name="next-steps"></a>后续步骤

* 请参阅[适用于 Cosmos DB 的 Azure Functions 触发器](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration)的完整配置
* 检查所有语言的扩展[示例列表](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---example)。
* 使用 Azure Cosmos DB 和 Azure Functions [GitHub 存储库](https://github.com/ealsur/serverless-recipes/tree/master/cosmosdbtriggerscenarios)访问无服务器方案以获取更多示例。