---
title: 针对受限制请求的指南
description: 进一步了解如何对 Azure 资源关系图限制的请求进行批处理、交错、分页和并行查询。
ms.date: 11/21/2019
ms.topic: conceptual
ms.openlocfilehash: 4405cce567a75f83823cc2d441b2a59985c196ad
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74304674"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>有关 Azure Resource Graph 中受限请求的指南

创建编程代码和频繁使用 Azure Resource Graph 数据时，应考虑到限制对查询结果的影响。 更改请求数据的方式可能有助于你和你的组织避免受到限制，并在 Azure 资源方面保持及时的数据流。

本文介绍与在 Azure Resource Graph 中创建查询相关的四个方面和模式：

- 了解限制标头
- 批处理查询
- 错开查询
- 分页的影响

## <a name="understand-throttling-headers"></a>了解限制标头

Azure Resource Graph 根据时间范围为每个用户分配配额数。 例如，某个用户可以每隔 5 秒最多发送 15 个查询，而不会受到限制。 配额值由多种因素决定，并可能会发生更改。

在每个查询响应中，Azure Resource Graph 会添加两个限制标头：

- `x-ms-user-quota-remaining` （int）：用户的剩余资源配额。 此值映射到查询计数。
- `x-ms-user-quota-resets-after` （hh： mm： ss）：重置用户的配额消耗之前的持续时间。

为了演示标头的工作原理，让我们看看一个包含标头以及值 `x-ms-user-quota-remaining: 10` 和 `x-ms-user-quota-resets-after: 00:00:03` 的查询响应。

- 在接下来的 3 秒内，最多可以提交 10 个查询，这不会受到限制。
- 3 秒后，`x-ms-user-quota-remaining` 和 `x-ms-user-quota-resets-after` 的值将分别重置为 `15` 和 `00:00:05`。

有关演示如何使用标头回退查询请求的示例，请参阅_并行查询_中的示例。[](#query-in-parallel)

## <a name="batching-queries"></a>批处理查询

按订阅、资源组或单个资源批处理查询比并行化查询更为有效。 较大查询的配额成本通常小于众多有针对性的小型查询的配额成本。 建议使用小于 _300_ 的批大小。

- 优化不当的方法示例

  ```csharp
  // NOT RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  foreach (var subscriptionId in subscriptionIds)
  {
      var userQueryRequest = new QueryRequest(
          subscriptions: new[] { subscriptionId },
          query: "Resoures | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

  // ...
  }
  ```

- 优化的批处理方法示例 #1

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int batchSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / batchSize; ++i)
  {
      var currSubscriptionBatch = subscriptionIds.Skip(i * batchSize).Take(batchSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionBatch,
          query: "Resources | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- 优化的批处理方法示例 #2

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int batchSize = 100;
  for (var i = 0; i <= resourceIds.Count / batchSize; ++i)
  {
      var resourceIdBatch = string.Join(",",
          resourceIds.Skip(i * batchSize).Take(batchSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"Resources | where id in~ ({resourceIds}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>错开查询

由于限制的实施方式，我们建议将查询错开。 也就是说，不要同时发送 60 个查询，而是在四个 5 秒时限内将查询错开：

- 未错开的查询计划

  | 查询计数         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | 时间间隔（秒） | 0-5 | 5-10 | 10-15 | 15-20 |

- 错开的查询计划

  | 查询计数         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | 时间间隔（秒） | 0-5 | 5-10 | 10-15 | 15-20 |

下面是查询 Azure Resource Graph 时遵从限制标头的示例：

```csharp
while (/* Need to query more? */)
{
    var userQueryRequest = /* ... */
    // Send post request to Azure Resource Graph
    var azureOperationResponse = await this.resourceGraphClient
        .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
        .ConfigureAwait(false);

    var responseHeaders = azureOperationResponse.response.Headers;
    int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
    TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
    if (remainingQuota == 0)
    {
        // Need to wait until new quota is allocated
        await Task.Delay(resetAfter).ConfigureAwait(false);
    }
}
```

### <a name="query-in-parallel"></a>并行查询

尽管我们建议使用批处理而不是并行化，但有时，并不能轻松地批处理查询。 在这些情况下，可能需要通过并行发送多个查询来查询 Azure Resource Graph。 以下示例演示在这种情况下如何基于限制标头进行回退：

```csharp
IEnumerable<IEnumerable<string>> queryBatches = /* Batches of queries  */
// Run batches in parallel.
await Task.WhenAll(queryBatches.Select(ExecuteQueries)).ConfigureAwait(false);

async Task ExecuteQueries(IEnumerable<string> queries)
{
    foreach (var query in queries)
    {
        var userQueryRequest = new QueryRequest(
            subscriptions: subscriptionList,
            query: query);
        // Send post request to Azure Resource Graph.
        var azureOperationResponse = await this.resourceGraphClient
            .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
            .ConfigureAwait(false);
        
        var responseHeaders = azureOperationResponse.response.Headers;
        int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
        TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
        if (remainingQuota == 0)
        {
            // Delay by a random period to avoid bursting when the quota is reset.
            var delay = (new Random()).Next(1, 5) * resetAfter;
            await Task.Delay(delay).ConfigureAwait(false);
        }
    }
}
```

## <a name="pagination"></a>分页

由于 Azure Resource Graph 在单个查询响应中最多返回 1000 个条目，因此你可能需要将查询[分页](./work-with-data.md#paging-results)才能获取所需的完整数据集。 但是，某些 Azure Resource Graph 客户端处理分页的方式与其他客户端不同。

- C# SDK

  使用 ResourceGraph SDK 时，需要通过将上一查询响应中返回的跳过标记传递给下一个分页查询，来处理分页。 这种设计意味着需要收集所有分页调用的结果，并最终将其组合到一起。 在这种情况下，发送的每个分页查询都会占用一个查询配额：

  ```csharp
  var results = new List<object>();
  var queryRequest = new QueryRequest(
      subscriptions: new[] { mySubscriptionId },
      query: "Resources | project id, name, type | top 5000");
  var azureOperationResponse = await this.resourceGraphClient
      .ResourcesWithHttpMessagesAsync(queryRequest, header)
      .ConfigureAwait(false);
  while (!string.Empty(azureOperationResponse.Body.SkipToken))
  {
      queryRequest.SkipToken = azureOperationResponse.Body.SkipToken;
      // Each post call to ResourceGraph consumes one query quota
      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(queryRequest, header)
          .ConfigureAwait(false);
      results.Add(azureOperationResponse.Body.Data.Rows);

      // Inspect throttling headers in query response and delay the next call if needed.
  }
  ```

- Azure CLI/Azure PowerShell

  使用 Azure CLI 或 Azure PowerShell 时，对 Azure Resource Graph 的查询将自动分页，以最多提取 5000 个条目。 查询结果将返回所有分页调用中的条目的组合列表。 在这种情况下，根据查询结果中的条目数，单个分页查询可能会消耗多个查询配额。 例如，在以下示例中，运行一次查询最多可能会消耗五个查询配额：

  ```azurecli-interactive
  az graph query -q 'Resources | project id, name, type' --first 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'Resources | project id, name, type' -First 5000
  ```

## <a name="still-get-throttled"></a>仍然受到限制？

如果在运用上述建议后仍然受到限制，请联系支持团队 ([resourcegraphsupport@microsoft.com](mailto:resourcegraphsupport@microsoft.com))。

请提供以下详细信息：

- 特定的用例，以及需要提高限制的业务驱动因素。
- 你有权访问多少资源？ 单个查询返回了多少资源？
- 你感兴趣的资源类型有哪些？
- 你的查询模式是什么？ 每 Y 秒发送 X 个查询，等等。

## <a name="next-steps"></a>后续步骤

- 在[初学者查询](../samples/starter.md)中了解使用的语言。
- 在[高级查询](../samples/advanced.md)中了解高级用法。
- 了解有关如何[浏览资源](explore-resources.md)的详细信息。