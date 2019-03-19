---
title: 使用 Az.Search 模块-Azure 搜索的 PowerShell 脚本
description: 创建并使用 PowerShell 配置 Azure 搜索服务。 您可以向上或向下缩放服务，管理管理员和查询 api 密钥和查询系统信息。
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: heidist
ms.openlocfilehash: 541feee2005428226b3f46927bc0e4bfb53cc98d
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2019
ms.locfileid: "57781708"
---
# <a name="manage-your-azure-search-service-with-powershell"></a>使用 PowerShell 管理 Azure 搜索服务
> [!div class="op_single_selector"]
> * [门户](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [REST API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

您可以在 Windows，Linux，或在运行 PowerShell cmdlet 和脚本[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)创建和配置[Azure 搜索](https://docs.microsoft.com/azure/search/)。 [ **Az.Search** ](https://docs.microsoft.com/powershell/module/az.search/?view=azps-1.4.0#search)模块扩展[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.4.0)具有到完整奇偶校验[Azure 搜索管理 REST Api](https://docs.microsoft.com/rest/api/searchmanagement)。 使用 Azure PowerShell 并**Az.Search**，可以执行以下任务：

> [!div class="checklist"]
> * [列出所有订阅中的搜索服务](#list-search-services)
> * [获取有关特定的搜索服务的信息](#get-search-service-information)
> * [创建或删除服务](#create-or-delete-a-service)
> * [重新生成管理员 API 密钥](#regenerate-admin-api-keys)
> * [创建或删除查询 api 密钥](#create-or-delete-query-keys)
> * [通过增加或减少副本和分区缩放服务](#scale-replicas-and-partitions)

无法使用 PowerShell，若要更改名称、 区域或你的服务层。 创建服务时，将分配专用的资源。 更改基础硬件 （位置或节点类型） 需要新的服务。 没有任何工具或 Api 来传输内容。 所有内容管理是通过[REST](https://docs.microsoft.com/rest/api/searchservice/)或[.NET](https://docs.microsoft.com/dotnet/api/?term=microsoft.azure.search) Api，并且如果你想要移动的索引，则需要重新创建，并重新加载这些新的服务上。 

尽管有关内容管理无专用的 PowerShell 命令，可以编写 PowerShell 脚本中调用 REST 或.NET 创建和加载索引。 **Az.Search**模块本身不提供这些操作。

不支持通过 PowerShell 或任何其他 API （仅限门户） 的其他任务包括：
+ [附加认知服务资源](cognitive-search-attach-cognitive-services.md)有关[AI 丰富索引](cognitive-search-concept-intro.md)。 认知服务附加到技能组合，而不是订阅或服务。
+ [外接程序的监视解决方案](search-monitor-usage.md#add-on-monitoring-solutions)或[搜索流量分析](search-traffic-analytics.md)用来监视 Azure 搜索。

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>检查版本和加载的模块

这篇文章中的示例是交互式的并且需要提升的权限。 Azure PowerShell ( **Az**模块) 必须安装。 有关详细信息，请参阅[安装 Azure PowerShell](/powershell/azure/overview)。

### <a name="powershell-version-check-51-or-later"></a>PowerShell 版本检查 （5.1 或更高版本）

5.1 或更高版本，在任何受支持的操作系统必须是本地 PowerShell。

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>加载 Azure PowerShell

如果你不确定是否**Az**验证步骤的形式运行以下命令的安装。 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

某些系统中执行操作不是自动加载模块。 如果将发生错误前一个命令，尝试加载该模块，和如果失败，请返回到的安装说明以查看是否丢失一个步骤。

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>使用浏览器登录令牌连接到 Azure

可以使用你的门户登录凭据以连接到 PowerShell 中的订阅。 或者，可以[使用服务主体以非交互方式进行身份验证](../active-directory/develop/howto-authenticate-service-principal-powershell.md)。

```azurepowershell-interactive
Connect-AzAccount
```

如果具有多个 Azure 订阅，设置你的 Azure 订阅。 若要查看当前订阅的列表，请运行以下命令。

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

若要指定订阅，请运行以下命令。 在以下示例中，订阅名为 `ContosoSubscription`。

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-all-azure-search-services-in-your-subscription"></a>列出订阅中的所有 Azure 搜索服务

以下命令是从[ **Az.Resources**](https://docs.microsoft.com/powershell/module/az.resources/?view=azps-1.4.0#resources)，返回有关现有资源和你的订阅中预配服务的信息。 如果不知道已经创建了搜索服务数目，这些命令将返回该信息保存到门户行程。

第一个命令将返回所有搜索服务。

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

从服务列表中，返回特定资源的相关信息。

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

从命令[ **Az.Search** ](https://docs.microsoft.com/powershell/module/az.search/?view=azps-1.4.0#search)之前加载该模块将不可用。

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>列出所有 Az.Search 命令

作为验证步骤，返回的模块中提供的命令的列表。

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

之后**Az.Search**导入，并且您知道包含您的搜索服务，运行的资源组[Get AzSearchService](https://docs.microsoft.com/powershell/module/az.search/get-azsearchservice?view=azps-1.4.0)返回服务定义，包括名称、 区域、 层和副本和分区计数。

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

[**新 AzSearchService** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0)用于[创建新的搜索服务](search-create-service-portal.md)。

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

[**新 AzSearchAdminKey** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0)用于滚动更新管理员[API 密钥](search-security-api-keys.md)。 使用已经过身份验证访问每个服务创建两个管理密钥。 每个请求需要密钥。 这两个管理密钥在功能上等效，能够检索任何信息，或创建和删除任何对象授予对搜索服务的完全写入访问权限。 两个注册表项存在，以便您可以使用时替换为另一个。 

您可以仅重新生成一次一个地指定为`primary`或`secondary`密钥。 不间断的服务，请记住要更新所有客户端代码，以滚动更新主密钥时使用辅助密钥。 避免更改密钥时，操作是正在进行。

如您所料，而不更新客户端代码重新生成密钥，使用旧密钥的请求将失败。 重新生成所有新密钥不会不永久锁定你的服务，并仍可以通过门户访问该服务。 重新生成主密钥和辅助密钥后，可以更新客户端代码以使用新密钥并将相应地继续操作。

由服务生成的 API 密钥值。 不能提供 Azure 搜索使用的自定义密钥。 同样，是没有用户定义的管理 API 密钥名称。 对密钥的引用固定字符串，要么`primary`或`secondary`。 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

结果应类似于以下输出。 即使您只更改一次一个地返回这两个密钥。

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>创建或删除查询密钥

[**新 AzSearchQueryKey** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchquerykey?view=azps-1.4.0)用来创建查询[API 密钥](search-security-api-keys.md)进行只读访问从客户端应用到 Azure 搜索索引。 查询密钥用于对特定索引以便检索搜索结果进行身份验证。 查询密钥不授予对该服务，如索引、 数据源或索引器上的其他项的只读访问权限。

不能提供 Azure 搜索使用的密钥。 由服务生成 API 密钥。

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>缩放副本和分区

[**集 AzSearchService** ](https://docs.microsoft.com/powershell/module/az.search/set-azsearchservice?view=azps-1.4.0)用于[增加或减少副本和分区](search-capacity-planning.md)重新调整你的服务中的可计费资源。 增加副本或分区将添加到你的帐单，同时具有固定和可变的费用。 如果必须临时需要额外的处理能力，则可以增加副本和分区才能处理工作负荷。 监视区域中概述的门户页上查询延迟，每秒，和限制、，该值指示当前容量是否足够查询具有磁贴。

可能需要一段时间才能添加或删除资源。 在后台，允许现有工作负荷以继续进行容量所做的调整。 更多的容量用于传入请求，只要它已准备就绪，且无需其他配置。 

删除容量可能会造成干扰。 停止所有索引和索引器作业，才能降低了容量建议以避免丢弃的请求。 如果这不可行，则可以考虑减少容量以增量方式，一个副本和分区时，直至达到新的目标级别。

一旦提交命令时，没有方法以将其终止的中间完成了。 必须将等待命令完成之前修改计数。

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

构建[索引](search-what-is-an-index.md)，[查询索引](search-query-overview.md)使用门户、 REST Api 或.NET SDK。

* [使用 Azure 门户创建 Azure 搜索索引](search-create-index-portal.md)
* [设置索引器将数据从其他服务加载](search-indexer-overview.md)
* [使用搜索资源管理器在 Azure 门户中查询 Azure 搜索索引](search-explorer.md)
* [如何在 .NET 中使用 Azure 搜索](search-howto-dotnet-sdk.md)