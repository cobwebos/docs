---
title: 快速入门：第一个 REST API 查询
description: 在此快速入门中，将按照以下步骤调用 REST API 的 Resource Graph 终结点并运行第一个查询。
ms.date: 06/29/2020
ms.topic: quickstart
ms.openlocfilehash: 8776a107484691ffab72f2e1622ed5837375b7fb
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "85802541"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-rest-api"></a>快速入门：使用 REST API 运行你的第一个 Resource Graph 查询

将 Azure Resource Graph 与 REST API 搭配使用的第一步是，检查是否有用于调用 REST API 的工具。 然后，此快速入门将引导你完成运行查询和通过调用 Azure Resource Graph REST API 终结点检索结果的过程。

在此过程结束时，你将拥有调用 REST API 终结点的工具，并运行第一个 Resource Graph 查询。

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="getting-started-with-rest-api"></a>REST API 入门

如果不熟悉 REST API，请首先查看 [Azure REST API 参考](/rest/api/azure/)，大致了解 REST API，尤其是请求 URI 和请求正文。 本文使用这些概念来提供有关如何使用 Azure Resource Graph 的说明，并假定具有相关的实践经验。 [ARMClient](https://github.com/projectkudu/ARMClient) 和其他工具可自动处理授权，建议初学者使用。

有关 Azure Resource Graph 规范，请参阅 [Azure Resource Graph REST API](/rest/api/azure-resourcegraph/)。

### <a name="rest-api-and-powershell"></a>REST API 和 PowerShell

如果尚无用于进行 REST API 调用的工具，请考虑使用 PowerShell 获取说明。 以下代码示例获取用于使用 Azure 进行身份验证的标头。 生成身份验证标头，有时称为持有者令牌，然后向要连接到的 REST API URI 提供任何参数或请求正文 ：

```azurepowershell-interactive
# Log in first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Invoke the REST API
$restUri = 'https://management.azure.com/subscriptions/{subscriptionId}?api-version=2020-01-01'
$response = Invoke-RestMethod -Uri $restUri -Method Get -Headers $authHeader
```

替换上面 $restUri 变量中的 `{subscriptionId}`，以获取订阅的相关信息。 $Response 变量可保留 `Invoke-RestMethod` cmdlet 的结果，后者可使用 [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json) 之类的 cmdlet 进行分析。 如果 REST API 服务终结点需要请求正文，请向 `Invoke-RestMethod` 的 `-Body` 参数提供 JSON 格式的变量。

## <a name="run-your-first-resource-graph-query"></a>运行首个 Resource Graph 查询

将 REST API 工具添加到所选环境中后，即可尝试一个简单的 Resource Graph 查询。 该查询返回前五个 Azure 资源，以及每个资源的名称和资源类型 。

在每个 REST API 调用的请求正文中，都包含在替换为自己的值时需要使用的变量：

- `{subscriptionID}` - 替换为订阅 ID

1. 使用 REST API 和 `resources` 终结点运行第一个 Azure Resource Graph 查询：

   - REST API URI

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - 请求正文

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | limit 5"
         }
     }
     ```

   > [!NOTE]
   > 由于此查询示例未提供排序修饰符（例如 `order by`），因此多次运行此查询可能会为每个请求生成一组不同的资源。

1. 更新对 `resouces` 终结点的调用，并将查询更改为按 Name 属性应用 `order by` ：

   - REST API URI

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - 请求正文

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | limit 5 | order by name asc"
         }
     }
     ```

   > [!NOTE]
   > 与第一个查询一样，多次运行此查询可能会为每个请求生成一组不同的资源。 查询命令的顺序非常重要。 在本例中，`order by` 位于 `limit` 之后。 命令按此顺序执行，首先会限制查询结果，然后对它们进行排序。

1. 更新对 `resources` 终结点的调用，并将查询更改为先按 Name 属性应用 `order by`，然后对前五个结果应用 `limit` ：

   - REST API URI

     ```http
     POST https://management.azure.com/providers/Microsoft.ResourceGraph/resources?api-version=2019-04-01
     ```

   - 请求正文

     ```json
     {
         "subscriptions": [
             "{subscriptionID}"
         ],
         "query": "Resources | project name, type | order by name asc | limit 5"
         }
     }
     ```

假设环境中没有任何变化，则多次运行最后一个查询时，返回的结果将是一致的且按 Name 属性排序，但仍限制为前五个结果。

有关 Azure Resource Graph REST API 调用的更多示例，请参阅 [Azure Resource Graph REST 示例](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources#examples)。

## <a name="clean-up-resources"></a>清理资源

REST API 没有要卸载的库或模块。 如果你安装了某个工具（例如 ARMClient 或 Postman）以进行调用，并且不再需要它，现在可以卸载该工具 。

## <a name="next-steps"></a>后续步骤

在此快速入门中，你调用了 Resource Graph REST API 终结点并运行了第一个查询。 若要详细了解 Resource Graph 语言，请继续阅读查询语言详细信息页。

> [!div class="nextstepaction"]
> [获取有关查询语言的详细信息](./concepts/query-language.md)
