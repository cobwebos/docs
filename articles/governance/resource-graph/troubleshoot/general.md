---
title: 排查常见错误
description: 了解如何通过 Azure Resource Graph 在查询 Azure 资源的同时排查各种 SDK 问题。
ms.date: 10/14/2020
ms.topic: troubleshooting
ms.openlocfilehash: 13c5d5ffde8b0b82fcafa5e8149400555a0b18a6
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92056951"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>使用 Azure Resource Graph 排查错误

使用 Azure Resource Graph 查询 Azure 资源时，可能会遇到错误。 本文描述可能会发生的各种错误及其解决方法。

## <a name="finding-error-details"></a>查找错误详细信息

大多数错误都是关于使用 Azure Resource Graph 运行查询时出现的问题。 当查询失败时，SDK 会提供有关失败查询的详细信息。 此信息会指出存在的问题，以便可以修复问题并确保后续查询成功进行。

## <a name="general-errors"></a>常规错误

### <a name="scenario-throttled-requests"></a><a name="throttled"></a>场景：限制的请求数

#### <a name="issue"></a>问题

进行大量或频繁资源查询的客户受到了请求数限制。

#### <a name="cause"></a>原因

Azure Resource Graph 基于时段为每个用户分配配额数量。 例如，用户可以在每 5 秒的时段内最多发送 15 个查询，而不会受到限制。 配额值取决于多种因素并可能会发生更改。 有关详细信息，请参阅 [Azure Resource Graph 中的限制](../overview.md#throttling)。

#### <a name="resolution"></a>解决方法

有多种方法可处理请求受限问题：

- [对查询分组](../concepts/guidance-for-throttled-requests.md#grouping-queries)
- [错开查询](../concepts/guidance-for-throttled-requests.md#staggering-queries)
- [并行查询](../concepts/guidance-for-throttled-requests.md#query-in-parallel)
- [分页](../concepts/guidance-for-throttled-requests.md#pagination)

### <a name="scenario-too-many-subscriptions"></a><a name="toomanysubscription"></a>场景：订阅过多

#### <a name="issue"></a>问题

有权访问 1000 多个订阅（包括使用 [Azure Lighthouse](../../../lighthouse/overview.md) 的跨租户订阅的客户无法通过对 Azure Resource Graph 的单个调用获取所有订阅的数据。

#### <a name="cause"></a>原因

Azure CLI 和 PowerShell 仅将前 1000 个订阅转发到 Azure Resource Graph。 Azure Resource Graph 的 REST API 接受要对其执行查询的最大订阅数。

#### <a name="resolution"></a>解决方法

将对包含订阅子集的查询的批处理请求保持在 1000 个订阅的限制以下。 解决方法是在 PowerShell 中使用 Subscription 参数。

```azurepowershell-interactive
# Replace this query with your own
$query = 'Resources | project type'

# Fetch the full array of subscription IDs
$subscriptions = Get-AzSubscription
$subscriptionIds = $subscriptions.Id

# Create a counter, set the batch size, and prepare a variable for the results
$counter = [PSCustomObject] @{ Value = 0 }
$batchSize = 1000
$response = @()

# Group the subscriptions into batches
$subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

# Run the query for each batch
foreach ($batch in $subscriptionsBatch){ $response += Search-AzGraph -Query $query -Subscription $batch.Group }

# View the completed results of the query on all subscriptions
$response
```

### <a name="scenario-unsupported-content-type-rest-header"></a><a name="rest-contenttype"></a>场景：不受支持的 Content-type REST 标头

#### <a name="issue"></a>问题

客户查询 Azure Resource Graph REST API 时，返回 500（内部服务器错误）响应。

#### <a name="cause"></a>原因

Azure Resource Graph REST API 仅支持“application/json”的 `Content-Type`。 某些 REST 工具或代理默认为“text/plain”，这不受 REST API 支持。

#### <a name="resolution"></a>解决方法

验证用于查询 Azure Resource Graph 的工具或代理是否将 REST API 标头 `Content-Type` 配置为“application/json”。

### <a name="scenario-no-read-permission-to-all-subscriptions-in-list"></a><a name="rest-403"></a>场景：没有对列表中所有订阅的读取权限

#### <a name="issue"></a>问题

客户使用 Azure Resource Graph 查询显式传递订阅列表时，获得 403（禁止）响应。

#### <a name="cause"></a>原因

如果客户没有对提供的所有订阅的读取权限，则该请求将因缺乏相应安全权限而被拒绝。

#### <a name="resolution"></a>解决方法

在订阅列表中至少包含一个订阅，运行查询的客户对其至少具有读取访问权限。 有关详细信息，请参阅 [Azure Resource Graph 中的权限](../overview.md#permissions-in-azure-resource-graph)。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

- 请通过 [Azure 论坛](https://azure.microsoft.com/support/forums/)获取 Azure 专家的解答。
- 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。
- 如需更多帮助，可以提交 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。