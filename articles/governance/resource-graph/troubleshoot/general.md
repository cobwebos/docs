---
title: 排查常见错误
description: 了解如何通过 Azure 资源关系图对 Azure 资源进行查询进行故障排除。
author: DCtheGeek
ms.author: dacoulte
ms.date: 07/24/2019
ms.topic: troubleshooting
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 511d170f90e8ed34b00a3960d084223ec73d99dd
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480548"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>使用 Azure 资源图排查错误

在通过 Azure 资源关系图查询 Azure 资源时, 可能会遇到错误。 本文描述可能会发生的各种错误及其解决方法。

## <a name="finding-error-details"></a>查找错误详细信息

大多数错误是在使用 Azure 资源关系图运行查询时出现问题的结果。 当查询失败时, SDK 会提供有关失败查询的详细信息。 此信息指明了问题, 以便修复该问题, 并使之后的查询成功。

## <a name="general-errors"></a>常规错误

### <a name="toomanysubscription"></a>场景：订阅过多

#### <a name="issue"></a>问题

如果客户有权访问1000多个订阅 (包括使用[Azure Lighthouse](../../../lighthouse/overview.md)的跨租户订阅), 则不能在对 Azure 资源关系图的单个调用中跨所有订阅提取数据。

#### <a name="cause"></a>原因

Azure CLI 和 PowerShell 仅转发 Azure 资源关系图的前1000订阅。 Azure 资源关系图的 REST API 接受对执行查询的最大订阅数。

#### <a name="resolution"></a>解决

具有订阅子集的查询的批处理请求保持在1000订阅限制的范围之外。 解决方案在 PowerShell 中使用**订阅**参数。

```azurepowershell-interactive
# Replace this query with your own
$query = 'project type'

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

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

- 通过[Azure 论坛](https://azure.microsoft.com/support/forums/)获取 azure 专家的解答。
- 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。
- 如需更多帮助，可以提交 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。