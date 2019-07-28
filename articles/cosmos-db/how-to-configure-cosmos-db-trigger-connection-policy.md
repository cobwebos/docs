---
title: 适用于 Cosmos DB 的 Azure Functions 触发器的连接策略
description: 了解如何配置适用于 Cosmos DB 的 Azure Functions 触发器使用的连接策略
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 359b6a905e64046aad62b70ae53b993c86884ad2
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335626"
---
# <a name="how-to-configure-the-connection-policy-used-by-azure-functions-trigger-for-cosmos-db"></a>如何配置适用于 Cosmos DB 的 Azure Functions 触发器使用的连接策略

本文介绍在使用适用于 Cosmos DB 的 Azure Functions 触发器连接到 Azure Cosmos 帐户时如何配置连接策略。

## <a name="why-is-the-connection-policy-important"></a>连接策略为什么很重要？

有两种连接模式 - Direct（直接）模式和 Gateway（网关）模式。 若要详细了解这些连接模式，请参阅[性能提示](./performance-tips.md#networking)一文。 默认情况下，**网关**用于在适用于 Cosmos DB 的 Azure Functions 触发器中建立所有连接。 但是，对于性能驱动的方案，它可能不是最佳选项。

## <a name="changing-the-connection-mode-and-protocol"></a>更改连接模式和协议

有两个密钥配置设置可用于配置客户端连接策略 – **连接模式**和**连接协议**。 可以更改 Cosmos DB 的 Azure Functions 触发器和所有 [Azure Cosmos DB 绑定](../azure-functions/functions-bindings-cosmosdb-v2.md#output)使用的默认连接模式与协议。 若要更改默认设置，需要在 Azure Functions 项目或 Azure Functions 应用中找到 `host.json` 文件，并添加以下[附加设置](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings)：

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

其中，`connectionMode` 必须包含所需的连接模式（Direct 或 Gateway），`protocol` 必须包含所需的连接协议（Tcp 或 Https）。 

如果 Azure Functions 项目正在使用 Azure Functions V1 运行时（配置名称略有不同），则应使用 `documentDB` 而不是 `cosmosDB`：

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> 使用 Azure Functions 消耗计划托管计划时，每个实例可以维护的套接字连接数量有限制。 使用 Direct/TCP 模式时，根据设计会创建更多的连接，这可能会达到[消耗计划限制](../azure-functions/manage-connections.md#connection-limit)，在这种情况下，可以使用 Gateway 模式，或者以[应用服务模式](../azure-functions/functions-scale.md#app-service-plan)运行 Azure Functions。

## <a name="next-steps"></a>后续步骤

* [Azure Functions 中的连接限制](../azure-functions/manage-connections.md#connection-limit)
* [Azure Cosmos DB 性能提示](./performance-tips.md)
* [代码示例](https://github.com/ealsur/serverless-recipes/tree/master/connectionmode)
