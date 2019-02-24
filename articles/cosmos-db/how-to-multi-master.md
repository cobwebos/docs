---
title: 如何在 Azure Cosmos DB 中配置多主数据库
description: 了解如何在 Azure Cosmos DB 中配置应用程序中的多主数据库
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 2/12/2019
ms.author: mjbrown
ms.openlocfilehash: 84c8e2921602bb653c0b1ef0adffd3d89e91bd78
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312134"
---
# <a name="how-to-configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>如何在使用 Azure Cosmos DB 的应用程序配置多主数据库

要在应用程序中使用多主数据库功能，需要启用多区域写入并配置多宿主功能。 通过设置部署应用程序的当前区域来配置多宿主功能。

## <a id="netv2"></a>.NET SDK v2

若要在应用程序中启用多主数据库，请将 `UseMultipleWriteLocations` 设置为 true，并将 `SetCurrentLocation` 配置为在其中部署应用程序并复制 Azure Cosmos DB 的区域。

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a id="netv3"></a>.NET SDK v3（预览版）

若要在应用程序中启用多主数据库，请将 `UseCurrentRegion` 配置为在其中部署应用程序并复制 Cosmos DB 的区域。

```csharp
CosmosConfiguration config = new CosmosConfiguration("endpoint", "key");
config.UseCurrentRegion("West US");
CosmosClient client = new CosmosClient(config);
```

## <a id="java"></a>Java 异步 SDK

若要在应用程序中启用多主数据库，请将 `policy.setUsingMultipleWriteLocations(true)` 设置为 true，并将 `policy.setPreferredLocations` 配置为在其中部署应用程序并复制 Cosmos DB 的区域。

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

## <a id="javascript"></a>Node.js、JavaScript、TypeScript SDK

若要在应用程序中启用多主数据库，请将 `connectionPolicy.UseMultipleWriteLocations` 设置为 true，并将 `connectionPolicy.PreferredLocations` 配置为在其中部署应用程序并复制 Cosmos DB 的区域。

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

## <a id="python"></a>Python SDK

若要在应用程序中启用多主数据库，请将 `connection_policy.UseMultipleWriteLocations` 设置为 true，并将 `connection_policy.PreferredLocations` 配置为在其中部署应用程序并复制 Cosmos DB 的区域。

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>后续步骤

详细了解 Azure Cosmos DB 中的多主数据库、全局分发和一致性。 请参阅以下文章：

* [利用可以在 Azure Cosmos DB 中管理一致性的会话令牌](how-to-manage-consistency.md#utilize-session-tokens)

* [Azure Cosmos DB 中的冲突类型和解决策略](conflict-resolution-policies.md)

* [Azure Cosmos DB 中的高可用性](high-availability.md)

* [在 Azure Cosmos DB 中选择适当的一致性级别](consistency-levels-choosing.md)

* [Azure Cosmos DB 中的一致性、可用性和性能权衡](consistency-levels-tradeoffs.md)
