---
title: 针对受限制请求的指南
description: 了解如何创建更好的查询，以避免对 Azure 资源关系图的请求受到限制。
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/18/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 1bbfd2a64de0b42da19d0a978874d564f1755c59
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387632"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Azure 资源图中的限制请求指南

创建编程和频繁使用 Azure 资源关系图数据时，应考虑限制如何影响查询的结果。 更改请求数据的方式可帮助您和您的组织避免受到限制并维护有关 Azure 资源的及时数据。

本文介绍了与在 Azure 资源关系图中创建查询相关的四个领域和模式：

- 了解阻止标头
- 批处理查询
- 惊人查询
- 分页的影响

## <a name="understand-throttling-headers"></a>了解阻止标头

Azure 资源图基于时间范围为每个用户分配配额号。 例如，用户可以在每5秒的时间段内最多发送15个查询，而不会受到限制。 配额值由许多因素决定，并可能会发生更改。

在每个查询响应中，Azure 资源关系图将添加两个限制标头：

- `x-ms-user-quota-remaining` （int）：用户的剩余资源配额。 此值映射到查询计数。
- `x-ms-user-quota-resets-after` （hh： mm： ss）：重置用户的配额消耗之前的持续时间。

为了说明标题的工作方式，让我们看看标头和值 `x-ms-user-quota-remaining: 10` 并 `x-ms-user-quota-resets-after: 00:00:03` 的查询响应。

- 在接下来的3秒内，最多可以提交10个查询，而不会受到限制。
- 3秒，@no__t 值0和 `x-ms-user-quota-resets-after` 将分别重置为 `15` 和 @no__t。

若要查看使用标头在查询请求上_回退_的示例，请参阅[并行查询中](#query-in-parallel)的示例。

## <a name="batching-queries"></a>批处理查询

订阅、资源组或单个资源的批处理查询比并行查询更有效。 较大查询的配额成本通常低于许多小型和目标查询的配额成本。 建议使用小于_300_的批大小。

- 不良优化方法的示例

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

- 优化批处理方法的示例 #1

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

- 优化批处理方法的示例 #2

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

## <a name="staggering-queries"></a>惊人查询

由于强制限制的方式，我们建议将查询错开。 也就是说，将查询分为四个5秒的窗口，而不是同时发送60查询：

- 非错开查询计划

  | 查询计数         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | 时间间隔（秒） | 0-5 | 5-10 | 10-15 | 15-20 |

- 交错查询计划

  | 查询计数         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | 时间间隔（秒） | 0-5 | 5-10 | 10-15 | 15-20 |

下面是查询 Azure 资源关系图时遵从限制标头的示例：

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

即使建议使用批处理，也不能轻松地批处理查询。 在这些情况下，可能需要通过并行发送多个查询来查询 Azure 资源关系图。 下面的示例演示如何在这种情况下基于限制标头进行_回退_：

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

## <a name="pagination"></a>标记页数

由于 Azure 资源关系图在单个查询响应中最多返回1000项，因此你可能需要对查询进行[分页](./work-with-data.md#paging-results)以获取所需的完整数据集。 但是，某些 Azure 资源关系图客户端处理分页的方式不同。

- C# SDK

  使用 ResourceGraph SDK 时，需要通过将从上一个查询响应返回的 skip 标记传递到下一个分页查询来处理分页。 这种设计意味着需要收集所有分页调用的结果，并将它们一起组合到末尾。 在这种情况下，发送的每个分页查询都采用一个查询配额：

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

  使用 Azure CLI 或 Azure PowerShell 时，会自动对 Azure 资源关系图的查询进行分页，最多可提取5000个条目。 查询结果将返回所有分页调用中的条目的组合列表。 在这种情况下，根据查询结果中的条目数，单个分页查询可能会消耗多个查询配额。 例如，在下面的示例中，一次运行查询可能会消耗最多五个查询配额：

  ```azurecli-interactive
  az graph query -q 'Resources | project id, name, type' -top 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'Resources | project id, name, type' -Top 5000
  ```

## <a name="still-get-throttled"></a>仍会受到限制？

如果在进行上述建议后受到限制，请联系[resourcegraphsupport@microsoft.com](mailto:resourcegraphsupport@microsoft.com)的团队。

提供以下详细信息：

- 特定用例和业务驱动程序需要更高的限制。
- 有多少资源可以访问？ 从单个查询返回了多少？
- 你感兴趣的资源类型有哪些？
- 你的查询模式是什么？ X 个查询/Y 秒等。

## <a name="next-steps"></a>后续步骤

- 请参阅[Starter 查询](../samples/starter.md)中使用的语言。
- 请参阅高级[查询](../samples/advanced.md)中的高级使用。
- 了解如何[探索资源](explore-resources.md)。