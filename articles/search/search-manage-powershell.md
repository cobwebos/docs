---
title: 使用 Az 进行 PowerShell 脚本搜索模块
titleSuffix: Azure Cognitive Search
description: 使用 PowerShell 创建和配置 Azure 认知搜索服务。 可以向上或向下缩放服务、管理管理员和查询 api 密钥以及查询系统信息。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: b6147e45ca686328b1702faa5a8d50d9a75e50d6
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77157833"
---
# <a name="manage-your-azure-cognitive-search-service-with-powershell"></a>通过 PowerShell 管理 Azure 认知搜索服务
> [!div class="op_single_selector"]
> * [门户](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [REST API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

可以在 Windows、Linux 或[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)上运行 PowerShell cmdlet 和脚本，以创建和配置 Azure 认知搜索。 **Az （搜索**）模块会将完全奇偶校验的[Azure PowerShell](https://docs.microsoft.com/powershell/)扩展为[搜索管理 REST api](https://docs.microsoft.com/rest/api/searchmanagement)和执行以下任务的能力：

> [!div class="checklist"]
> * [列出订阅中的搜索服务](#list-search-services)
> * [返回服务信息](#get-search-service-information)
> * [创建或删除服务](#create-or-delete-a-service)
> * [重新生成管理 API 密钥](#regenerate-admin-keys)
> * [创建或删除查询 api 密钥](#create-or-delete-query-keys)
> * [用副本和分区增加或减少](#scale-replicas-and-partitions)

偶尔，系统会询问有关以上列表中*未*列出的任务的问题。 目前，不能使用**Az**或 management REST API 来更改服务器名称、区域或层。 创建服务时，将分配专用资源。 因此，更改基础硬件（位置或节点类型）需要一个新的服务。 同样，没有用于将内容（如索引）从一个服务传输到另一个服务的工具或 Api。

在服务中，内容创建和管理通过[搜索服务 REST API](https://docs.microsoft.com/rest/api/searchservice/)或[.net SDK](https://docs.microsoft.com/dotnet/api/?term=microsoft.azure.search)来完成。 尽管没有专用于内容的 PowerShell 命令，但你可以编写用于调用 REST 或 .NET Api 的 PowerShell 脚本来创建和加载索引。

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>检查版本和加载模块

本文中的示例是交互式的，需要提升的权限。 必须安装 Azure PowerShell （ **Az** module）。 有关详细信息，请参阅[安装 Azure PowerShell](/powershell/azure/overview)。

### <a name="powershell-version-check-51-or-later"></a>PowerShell 版本检查（5.1 或更高版本）

在任何受支持的操作系统上，本地 PowerShell 都必须是5.1 或更高版本。

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>负载 Azure PowerShell

如果不确定是否安装了**Az** ，请运行以下命令作为验证步骤。 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

某些系统不会自动加载模块。 如果在前一个命令上收到错误，请尝试加载该模块，如果失败，请返回到安装说明，查看是否漏掉了某个步骤。

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>使用浏览器登录令牌连接到 Azure

可以在 PowerShell 中使用门户登录凭据连接到订阅。 此外，还可以[使用服务主体进行非交互式身份验证](../active-directory/develop/howto-authenticate-service-principal-powershell.md)。

```azurepowershell-interactive
Connect-AzAccount
```

如果保留多个 Azure 订阅，请设置 Azure 订阅。 若要查看当前订阅的列表，请运行以下命令。

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

若要指定订阅，请运行以下命令。 在以下示例中，订阅名为 `ContosoSubscription`。

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>列出订阅中的服务

以下命令来自[**Az. Resources**](https://docs.microsoft.com/powershell/module/az.resources/?view=azps-1.4.0#resources)，返回有关已在订阅中预配的现有资源和服务的信息。 如果你不知道已经创建了多少搜索服务，则这些命令会返回该信息，从而为你节省了到门户的行程。

第一个命令返回所有搜索服务。

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

从服务列表中，返回有关特定资源的信息。

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

结果应如以下输出所示。

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alpha-numeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>导入 Az. Search

Az 中的命令将不可用[ **。** ](https://docs.microsoft.com/powershell/module/az.search/?view=azps-1.4.0#search)

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>列出所有 Az 命令

作为验证步骤，返回模块中提供的命令的列表。

```azurepowershell-interactive
Get-Command -Module Az.Search
```

结果应如以下输出所示。

```
CommandType     Name                                Version    Source
-----------     ----                                -------    ------
Cmdlet          Get-AzSearchAdminKeyPair            0.7.1      Az.Search
Cmdlet          Get-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          Get-AzSearchService                 0.7.1      Az.Search
Cmdlet          New-AzSearchAdminKey                0.7.1      Az.Search
Cmdlet          New-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          New-AzSearchService                 0.7.1      Az.Search
Cmdlet          Remove-AzSearchQueryKey             0.7.1      Az.Search
Cmdlet          Remove-AzSearchService              0.7.1      Az.Search
Cmdlet          Set-AzSearchService                 0.7.1      Az.Search
```

## <a name="get-search-service-information"></a>获取搜索服务信息

在**Az**后，将导入 "搜索"，并了解包含搜索服务的资源组，运行[AzSearchService](https://docs.microsoft.com/powershell/module/az.search/get-azsearchservice?view=azps-1.4.0)以返回服务定义，包括名称、区域、层、副本和分区计数。

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

结果应如以下输出所示。

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : West US
Sku               : Standard
ReplicaCount      : 1
PartitionCount    : 1
HostingMode       : Default
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-or-delete-a-service"></a>创建或删除服务

[**AzSearchService**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0)用于[创建新的搜索服务](search-create-service-portal.md)。

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName "demo-westus" -Name "my-demo-searchapp" -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3
``` 
结果应如以下输出所示。

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Id                : /subscriptions/<alphanumeric-subscription-ID>/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 3
PartitionCount    : 3
HostingMode       : Default
Tags
```     

## <a name="regenerate-admin-keys"></a>重新生成管理员密钥

[**AzSearchAdminKey**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0)用于滚动管理[API 密钥](search-security-api-keys.md)。 为每个服务创建了两个管理密钥以进行经过身份验证的访问。 每个请求都需要密钥。 这两个管理密钥在功能上是等效的，它可以检索任何信息或创建和删除任何对象，从而授予对搜索服务的完全写入访问权限。 存在两个键，以便您可以在替换另一个键时使用。 

一次只能重新生成一个指定为 `primary` 或 `secondary` 键的。 对于不中断的服务，请记得更新所有客户端代码，以便在滚动更新主密钥时使用辅助密钥。 请避免在操作过程中更改密钥。

正如您所料，如果重新生成密钥时未更新客户端代码，使用旧密钥的请求将失败。 重新生成所有新密钥并不会将你永久锁定在你的服务中，你仍然可以通过门户访问该服务。 重新生成主密钥和辅助密钥后，可以更新客户端代码以使用新密钥，操作将相应恢复。

API 密钥的值由服务生成。 无法提供 Azure 认知搜索要使用的自定义密钥。 同样，管理员 API 密钥没有用户定义的名称。 对该密钥的引用是固定字符串，可以是 `primary` 或 `secondary`。 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

结果应如以下输出所示。 即使每次只更改一个密钥，都将返回这两个密钥。

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>创建或删除查询密钥

[**AzSearchQueryKey**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchquerykey?view=azps-1.4.0)用于创建查询[API 密钥](search-security-api-keys.md)，以用于从客户端应用到 Azure 认知搜索索引的只读访问。 查询键用于向特定索引进行身份验证，目的是检索搜索结果。 查询键不向服务中的其他项授予只读访问权限，如索引、数据源或索引器。

无法为 Azure 认知搜索提供要使用的密钥。 API 密钥由服务生成。

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>缩放副本和分区

[**AzSearchService**](https://docs.microsoft.com/powershell/module/az.search/set-azsearchservice?view=azps-1.4.0)用于[增加或减少副本和分区](search-capacity-planning.md)，以便在服务中重新调整可计费资源。 增大副本或分区会将其添加到你的帐单中，这会产生固定和可变费用。 如果您暂时需要额外的处理能力，则可以增加副本和分区来处理工作负荷。 概述门户页中的 "监视" 区域具有有关查询延迟、每秒查询数和限制的磁贴，指示当前容量是否够用。

添加或删除占用可能需要一些时间。 在后台调整容量，允许现有工作负荷继续。 当传入请求准备就绪时，还会使用额外的容量，无需进行其他配置。 

删除容量可能会造成中断。 建议在降低容量之前停止所有索引和索引器作业，以避免删除请求。 如果这不可行，可以考虑增量减少容量，一次一个副本和分区，直到达到新的目标级别。

提交该命令后，将无法在中间终止它。 在修订计数之前，必须等待命令完成。

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

结果应如以下输出所示。

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 6
PartitionCount    : 6
HostingMode       : Default
Id                : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```


## <a name="next-steps"></a>后续步骤

生成[索引](search-what-is-an-index.md)，使用门户、REST api 或 .net SDK[查询索引](search-query-overview.md)。

* [在 Azure 门户中创建 Azure 认知搜索索引](search-create-index-portal.md)
* [设置索引器以从其他服务加载数据](search-indexer-overview.md)
* [使用 Azure 门户中的搜索资源管理器查询 Azure 认知搜索索引](search-explorer.md)
* [如何在 .NET 中使用 Azure 认知搜索](search-howto-dotnet-sdk.md)