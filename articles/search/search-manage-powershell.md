---
title: 使用 Az.Search 模块的 PowerShell 脚本 - Azure 搜索
description: 使用 PowerShell 创建和配置 Azure 搜索服务。 可以纵向扩展或缩减服务，对管理和查询 API 密钥进行管理，以及查询系统信息。
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: heidist
ms.openlocfilehash: 6090881cc2b94fa42fdac22220c858a0153ccc5c
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648093"
---
# <a name="manage-your-azure-search-service-with-powershell"></a>使用 PowerShell 管理 Azure 搜索服务
> [!div class="op_single_selector"]
> * [门户](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [REST API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

可以在 Windows、Linux 或 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) 中运行 PowerShell cmdlet 和脚本，以创建和配置 Azure 搜索。 **Az.Search** 模块扩展了 Azure PowerShell，并且完全可与 [Azure 搜索管理 REST API](https://docs.microsoft.com/rest/api/searchmanagement) 搭配使用。 使用 Azure PowerShell 和 **Az.Search** 可执行以下任务：

> [!div class="checklist"]
> * [列出订阅中的所有搜索服务](#list-search-services)
> * [获取有关特定搜索服务的信息](#get-search-service-information)
> * [创建或删除服务](#create-or-delete-a-service)
> * [重新生成管理 API 密钥](#regenerate-admin-keys)
> * [创建或删除查询 API 密钥](#create-or-delete-query-keys)
> * [通过增加或减少副本与分区来缩放服务](#scale-replicas-and-partitions)

无法使用 PowerShell 更改服务的名称、区域或层级。 创建服务时，会分配专用资源。 更改底层硬件（位置或节点类型）需要新的服务。 没有任何工具或 API 可将一个服务中的内容传输到另一个服务。 所有内容管理都是通过 [REST](https://docs.microsoft.com/rest/api/searchservice/) 或 [.NET](https://docs.microsoft.com/dotnet/api/?term=microsoft.azure.search) API 进行的，若要移动索引，需要在新的服务中重新创建并重新加载这些索引。 

尽管没有专用的 PowerShell 命令可用于内容管理，但你可以编写 PowerShell 脚本来调用 REST 或 .NET 以创建并加载索引。 **Az.Search** 模块本身不提供这些操作。

不支持通过 PowerShell 或任何其他 API（仅限门户）完成的其他任务包括：
+ 为 [AI 扩充的索引](cognitive-search-concept-intro.md)[附加认知服务资源](cognitive-search-attach-cognitive-services.md)。 认知服务将附加到技能集，而不是附加到订阅或服务。
+ 用于监视 Azure 搜索的[附加监视解决方案](search-monitor-usage.md#add-on-monitoring-solutions)或[搜索流量分析](search-traffic-analytics.md)。

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>检查版本并加载模块

本文中的示例是交互式，需要提升的权限。 必须安装 Azure PowerShell（**Az** 模块）。 有关详细信息，请参阅[安装 Azure PowerShell](/powershell/azure/overview)。

### <a name="powershell-version-check-51-or-later"></a>PowerShell 版本检查（5.1 或更高版本）

本地 PowerShell 必须是在任何受支持操作系统上运行的 5.1 或更高版本。

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>加载 Azure PowerShell

如果你不确定是否已安装 **Az**，请运行以下命令作为验证步骤。 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

某些系统不会自动加载模块。 如果运行上述命令时出错，请尝试加载该模块，如果失败，请重新查看安装说明，以检查是否遗漏了某个步骤。

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>使用浏览器登录令牌连接到 Azure

可以在 PowerShell 中使用门户登录凭据连接到订阅。 或者，可以[使用服务主体以非交互方式进行身份验证](../active-directory/develop/howto-authenticate-service-principal-powershell.md)。

```azurepowershell-interactive
Connect-AzAccount
```

如果你持有多个 Azure 订阅，请设置 Azure 订阅。 若要查看当前订阅的列表，请运行以下命令。

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

若要指定订阅，请运行以下命令。 在以下示例中，订阅名为 `ContosoSubscription`。

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-all-azure-search-services-in-your-subscription"></a>列出订阅中的所有 Azure 搜索服务

以下命令摘自 [**Az.Resources**](https://docs.microsoft.com/powershell/module/az.resources/?view=azps-1.4.0#resources)，它会返回有关订阅中已预配的现有资源和服务的信息。 如果你不知道已经创建了多少个搜索服务，则这些命令会返回该信息，省得要在门户中查找。

第一个命令返回所有搜索服务。

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

在服务列表中返回有关特定资源的信息。

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

结果应类似于以下输出。

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alpha-numeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>导入 Az.Search

[**Az.Search**](https://docs.microsoft.com/powershell/module/az.search/?view=azps-1.4.0#search) 中的命令只有在加载该模块之后才可用。

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>列出所有 Az.Search 命令

作为验证步骤，返回模块中提供的命令列表。

```azurepowershell-interactive
Get-Command -Module Az.Search
```

结果应类似于以下输出。

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

导入 **Az.Search** 之后，如果你知道哪个资源组包含你的搜索服务，请运行 [Get-AzSearchService](https://docs.microsoft.com/powershell/module/az.search/get-azsearchservice?view=azps-1.4.0) 返回服务定义，包括名称、区域、层级、副本计数和分区计数。

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

结果应类似于以下输出。

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

[**New-AzSearchService**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) 用于[创建新的搜索服务](search-create-service-portal.md)。

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName "demo-westus" -Name "my-demo-searchapp" -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3
``` 
结果应类似于以下输出。

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

[**New-AzSearchAdminKey**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) 用于滚动更新管理 [API 密钥](search-security-api-keys.md)。 两个管理密钥是使用每个服务创建的，用于进行身份验证访问。 需要在每个请求中提供密钥。 这两个管理密钥在功能上是等效的，授予对搜索服务的完全写入访问权限，并可以检索任何信息，或创建和删除任何对象。 这两个密钥可以换用。 

一次只能重新生成其中的一个密钥（指定为 `primary` 或 `secondary` 密钥）。 为避免服务中断，在滚动更新主密钥时，请记得将所有客户端代码更新为使用辅助密钥。 请避免在操作是正在进行时更改密钥。

如果在不更新客户端代码的情况下重新生成密钥，使用旧密钥的请求预期将会失败。 重新生成所有新密钥不会永久性地将你锁定在服务之外，你仍可以通过门户访问服务。 重新生成主密钥和辅助密钥后，可将客户端代码更新为使用新密钥，而操作也会相应地恢复。

API 密钥的值由服务生成。 无法提供自定义密钥供 Azure 搜索使用。 同样，管理 API 密钥没有用户定义的名称。 对密钥的引用是固定的字符串：`primary` 或 `secondary`。 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

结果应类似于以下输出。 即使每次只更改一个，也会同时返回两个密钥。

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>创建或删除查询密钥

使用 [**New-AzSearchQueryKey**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchquerykey?view=azps-1.4.0) 创建查询 [API 密钥](search-security-api-keys.md)，用于从客户端应用对 Azure 搜索索引进行只读访问。 查询密钥用于对特定的索引进行身份验证，以检索搜索结果。 查询密钥不授予对服务中其他项（例如索引、数据源或索引器）的只读访问权限。

无法提供密钥供 Azure 搜索使用。 API 密钥由服务生成。

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>缩放副本和分区

[**Set-AzSearchService**](https://docs.microsoft.com/powershell/module/az.search/set-azsearchservice?view=azps-1.4.0) 用于[增加或减少副本与分区](search-capacity-planning.md)，以调整服务中的可计费资源。 增加副本或分区会增大费用，两者都提供固定和可变的费率。 如果你暂时需要更大的处理能力，可以增加副本和分区来处理工作负荷。 “概述”门户页中的监视区域提供有关查询延迟、每秒查询数和限制的磁贴，指示当前容量是否足够。

添加或删除资源可能需要一段时间。 容量调整在后台发生，使现有工作负荷能够继续运行。 附加容量准备就绪后，将立即用于处理传入的请求，无需进行额外的配置。 

删除容量可能会造成中断。 建议在减少容量之前先停止所有索引和索引器作业，以免请求遭到丢弃。 如果无法做到这一点，可以考虑以增量方式减少容量（每次删除一个副本和分区），直至达到新的目标级别。

提交命令后，没有任何办法可以中途终止该命令。 必须等到该命令完成才能修改计数。

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

结果应类似于以下输出。

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

使用门户、REST API 或 .NET SDK 生成[索引](search-what-is-an-index.md)和[查询索引](search-query-overview.md)。

* [使用 Azure 门户创建 Azure 搜索索引](search-create-index-portal.md)
* [设置索引器以从其他服务加载数据](search-indexer-overview.md)
* [使用搜索资源管理器在 Azure 门户中查询 Azure 搜索索引](search-explorer.md)
* [如何在 .NET 中使用 Azure 搜索](search-howto-dotnet-sdk.md)