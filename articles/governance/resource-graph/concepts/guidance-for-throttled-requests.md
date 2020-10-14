---
title: 针对受限制请求的指南
description: 了解如何分组、错开、分页以及并行查询，以避免 Azure Resource Graph 限制请求。
ms.date: 10/14/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 4a8ba991d13b9be221e67f2ff1e393fb01f8a2d4
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92056168"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>有关 Azure Resource Graph 中的受限制请求的指南

创建编程和频繁使用 Azure Resource Graph 数据时，应考虑限制会如何影响查询的结果。 更改请求数据的方式可帮助你和你的组织避免受到限制并维护有关 Azure 资源的及时数据流。

本文涵盖与在 Azure Resource Graph 中创建查询相关的四个领域和模式：

- 了解限制标头
- 对查询分组
- 错开查询
- 分页的影响

## <a name="understand-throttling-headers"></a>了解限制标头

Azure Resource Graph 基于时段为每个用户分配配额数量。 例如，用户可以在每 5 秒的时段内最多发送 15 个查询，而不会受到限制。 配额值由许多因素确定，可能会发生更改。

在每个查询响应中，Azure Resource Graph 会添加两个限制标头：

- `x-ms-user-quota-remaining` (int)：用户的剩余资源配额。 此值映射到查询计数。
- `x-ms-user-quota-resets-after` (hh:mm:ss)：在用户的配额消耗量重置之前的持续时间。

当安全主体有权访问租户或管理组[查询范围](./query-language.md#query-scope)中 5000 个以上的订阅时，响应仅限于前 5000 个订阅，`x-ms-tenant-subscription-limit-hit` 标头将返回为 `true`。

为了说明标头的工作方式，我们来看看具有标头并且值为 `x-ms-user-quota-remaining: 10` 和 `x-ms-user-quota-resets-after: 00:00:03` 查询响应。

- 在接下来的 3 秒内，最多可以提交 10 个查询，而不会受到限制。
- 在 3 秒后，`x-ms-user-quota-remaining` 和 `x-ms-user-quota-resets-after` 的值将分别重置为 `15` 和 `00:00:05`。

若要查看使用标头在查询请求中进行回退的示例，请参阅[并行查询](#query-in-parallel)中的示例。

## <a name="grouping-queries"></a>对查询分组

按订阅、资源组或单个资源对查询分组比并行查询更加高效。 较大查询的配额成本通常低于许多小型定向查询的配额成本。 组大小建议小于 300。

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

- 优化分组方法的示例 #1

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int groupSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / groupSize; ++i)
  {
      var currSubscriptionGroup = subscriptionIds.Skip(i * groupSize).Take(groupSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionGroup,
          query: "Resources | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- 用于在一个查询中获取多个资源的优化分组方法的示例 #2

  ```kusto
  Resources | where id in~ ({resourceIdGroup}) | project name, type
  ```

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int groupSize = 100;
  for (var i = 0; i <= resourceIds.Count / groupSize; ++i)
  {
      var resourceIdGroup = string.Join(",",
          resourceIds.Skip(i * groupSize).Take(groupSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"Resources | where id in~ ({resourceIdGroup}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>错开查询

由于强制实施限制的方式，建议错开查询。 也就是说，在四个 5 秒时段内错开查询，而不是同时发送 60 个查询：

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

虽然建议进行分组而不是采用并行，不过有时候无法轻松地对查询分组。 在这些情况下，可能需要通过并行发送多个查询来查询 Azure Resource Graph。 下面的示例演示如何在这种情况下基于限制标头进行 _回退_ ：

```csharp
IEnumerable<IEnumerable<string>> queryGroup = /* Groups of queries  */
// Run groups in parallel.
await Task.WhenAll(queryGroup.Select(ExecuteQueries)).ConfigureAwait(false);

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

由于 Azure Resource Graph 在单个查询响应中最多返回 1000 个条目，因此可能需要对查询[分页](./work-with-data.md#paging-results)，以获取所查找的完整数据集。 但是，某些 Azure Resource Graph 客户端处理分页的方式与其他客户端不同。

- C# SDK

  使用 ResourceGraph SDK 时，需要通过将从上一个查询响应返回的跳过标记传递到下一个分页查询来处理分页。 这种设计意味着需要从所有分页调用收集结果，最后将它们合并在一起。 在这种情况下，发送的每个分页查询都采用一个查询配额：

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

  使用 Azure CLI 或 Azure PowerShell 时，对 Azure Resource Graph 进行的查询会自动分页，以便最多提取 5000 个条目。 查询结果会返回来自所有分页调用的条目的合并列表。 在这种情况下，根据查询结果中的条目数，单个分页查询可能会消耗多个查询配额。 例如，在下面的示例中，一次运行查询可能会消耗最多五个查询配额：

  ```azurecli-interactive
  az graph query -q 'Resources | project id, name, type' --first 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'Resources | project id, name, type' -First 5000
  ```

## <a name="still-get-throttled"></a>仍受到限制？

如果在执行以上建议后仍受到限制，请通过 [resourcegraphsupport@microsoft.com](mailto:resourcegraphsupport@microsoft.com) 与团队联系。

请提供以下详细信息：

- 对更高限制的特定用例和业务驱动因素需求。
- 你可以访问多少资源？ 从单个查询返回多少资源？
- 你对哪些类型的资源感兴趣？
- 你的查询模式是什么？ 每 Y 秒 X 个查询，等等。

## <a name="next-steps"></a>后续步骤

- 请参阅[初学者查询](../samples/starter.md)中使用的语言。
- 请参阅[高级查询](../samples/advanced.md)中的高级用法。
- 详细了解如何[浏览资源](explore-resources.md)。