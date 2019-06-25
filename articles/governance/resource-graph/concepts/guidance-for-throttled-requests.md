---
title: 限制的请求的指南
description: 了解如何创建更好的查询，以避免对 Azure 资源 Graph 的请求，从受限制。
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: c644d230846d9c644c3845348431eef36c8279c8
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276894"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>限制的请求数在 Azure 资源图表中的指南

在创建时以编程方式和频繁使用的 Azure 资源的图形数据，应考虑如何限制影响的查询的结果。 请求更改数据的方式，可帮助你和你的组织避免被限制和维护的有关 Azure 资源的及时数据的流。

本文介绍了四个区域和与创建 Azure 资源 Graph 中查询相关的模式：

- 了解限制的标头
- 批处理查询
- 交错安排查询
- 分页的影响

## <a name="understand-throttling-headers"></a>了解限制的标头

Azure 资源图形会为基于时间窗口上每个用户分配配额数量。 例如，用户可以发送最多 15 个查询每隔 5 秒时间范围内不受限制的情况下。 配额值由许多因素，并且可能会发生更改。

在每个查询的响应，Azure 资源图形添加两个限制的标头：

- `x-ms-user-quota-remaining` (int)：用户的剩余资源配额。 此值映射到查询计数。
- `x-ms-user-quota-resets-after` (hh:mm:ss)：时间之前的持续时间重置用户的配额使用情况。

为了说明标头的工作原理，让我们看看将标头和值的查询响应`x-ms-user-quota-remaining: 10`和`x-ms-user-quota-resets-after: 00:00:03`。

- 在下一步的 3 秒内，最多 10 个查询可能不受限制的情况下提交。
- 在 3 秒、 的值`x-ms-user-quota-remaining`并`x-ms-user-quota-resets-after`将重置为`15`和`00:00:05`分别。

若要使用到的标头的示例，请参阅_退避算法_查询请求，请参阅中的示例[并行查询](#query-in-parallel)。

## <a name="batching-queries"></a>批处理查询

批处理按订阅、 资源组或单个资源的查询是对查询进行并行化比效率更高。 更大的配额开销通常是查询的小于许多小型和目标查询的配额成本。 建议的批大小为少于_300_。

- 优化不良的方法的示例

  ```csharp
  // NOT RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  foreach (var subscriptionId in subscriptionIds)
  {
      var userQueryRequest = new QueryRequest(
          subscriptions: new[] { subscriptionId },
          query: "project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

  // ...
  }
  ```

- 示例 #1 的优化的批处理方法

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
          query: "project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- 示例 #2 的优化的批处理方法

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
          query: $"where id in~ ({resourceIds}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>交错安排查询

由于的方式强制实施限制，我们建议查询，以交错。 也就是说，而不是在同一时间发送 60 次查询，错开查询到四个 5 秒窗口：

- 非错开查询计划

  | 查询计数         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | 时间间隔 （秒） | 0-5 | 5-10 | 10-15 | 15-20 |

- 错开查询计划

  | 查询计数         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | 时间间隔 （秒） | 0-5 | 5-10 | 10-15 | 15-20 |

下面是查询 Azure 资源图形时遵循限制的标头的一个示例：

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

即使通过并行化建议批处理，有些时候查询无法以轻松地批处理。 在这些情况下，你可能想要通过以并行方式发送多个查询来查询 Azure 资源的图形。 下面是一个示例如何_退避算法_基于限制此类方案中的标头：

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

由于 Azure 资源图表在单个查询响应中返回最多 1000 个项，则可能需要[分页](./work-with-data.md#paging-results)查询以获取所需的完整数据集。 但是，某些 Azure 资源 Graph 客户端不同的方式处理分页。

- C# SDK

  使用 ResourceGraph SDK 时，您需要通过将传递到下一个分页查询前一查询响应中返回的跳过标记处理分页。 这种设计意味着您需要从已分页的所有调用收集结果并将它们组合在一起的末尾。 在这种情况下，你将发送每个分页的查询采用一个查询配额：

  ```csharp
  var results = new List<object>();
  var queryRequest = new QueryRequest(
      subscriptions: new[] { mySubscriptionId },
      query: "project id, name, type | top 5000");
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

- Azure CLI / Azure PowerShell

  使用 Azure CLI 或 Azure PowerShell 时，对 Azure 资源 Graph 查询是自动分页来提取最多 5000 个条目。 查询结果分页的所有调用从返回项的组合的列表。 在这种情况下，具体取决于查询结果中的条目数，单个的分页的查询可能会占用多个查询配额。 例如，下面的示例中，在单次运行的查询可能会占用最多五个查询配额：

  ```azurecli-interactive
  az graph query -q 'project id, name, type' -top 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'project id, name, type' -Top 5000
  ```

## <a name="still-get-throttled"></a>仍会受到限制？

如果受到了限制执行上述建议后，与在团队联系[ resourcegraphsupport@microsoft.com ](mailto:resourcegraphsupport@microsoft.com)。

提供这些详细信息：

- 特定用例和业务驱动程序需要更高版本的限制。
- 你有权访问的资源数量？ 从单个查询均对其进行多少返回？
- 类型的资源是您对感兴趣？
- 什么是查询模式？ 每 Y 秒等查询 x。

## <a name="next-steps"></a>后续步骤

- 请参阅在中使用的语言[初学者查询](../samples/starter.md)。
- 使用中的高级，请参阅[高级查询](../samples/advanced.md)。
- 了解如何[浏览资源](explore-resources.md)。