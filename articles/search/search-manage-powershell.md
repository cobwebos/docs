---
title: 使用 Powershell 脚本管理 Azure 搜索 | Microsoft Docs
description: 使用 PowerShell 脚本管理 Azure 搜索服务。 创建或更新 Azure 搜索服务和管理 Azure 搜索管理密钥
author: HeidiSteen
manager: cgronlun
tags: azure-resource-manager
ms.service: search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 08/15/2016
ms.author: heidist
ms.openlocfilehash: fc9d23b0c11484c7fec575c411f32522b6e46e28
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="manage-your-azure-search-service-with-powershell"></a>使用 PowerShell 管理 Azure 搜索服务
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> 
> 

本主题介绍针对 Azure 搜索服务执行许多管理任务的 PowerShell 命令。 我们将演练如何创建搜索服务、对其缩放以及管理其 API 密钥。
这些命令与 [Azure 搜索管理 REST API](http://msdn.microsoft.com/library/dn832684.aspx) 中的可用管理选项并行使用。

## <a name="prerequisites"></a>先决条件
* 必须安装 Azure PowerShell 1.0 或更高版本。 有关说明，请参阅[安装和配置 Azure PowerShell](/powershell/azure/overview)。
* 必须按如下所述，在 PowerShell 中登录到 Azure 订阅。

首先，必须使用以下命令登录到 Azure：

    Connect-AzureRmAccount

在 Microsoft Azure 登录对话框中指定 Azure 帐户的电子邮件地址及其密码。

或者，可以[使用服务主体采用非交互方式登录](../azure-resource-manager/resource-group-authenticate-service-principal.md)。

如果有多个 Azure 订阅，则需要设置 Azure 订阅。 若要查看当前订阅的列表，请运行以下命令。

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

若要指定订阅，请运行以下命令。 在以下示例中，订阅名为 `ContosoSubscription`。

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

## <a name="commands-to-help-you-get-started"></a>帮助入门的命令
    $serviceName = "your-service-name-lowercase-with-dashes"
    $sku = "free" # or "basic" or "standard" for paid services
    $location = "West US"
    # You can get a list of potential locations with
    # (Get-AzureRmResourceProvider -ListAvailable | Where-Object {$_.ProviderNamespace -eq 'Microsoft.Search'}).Locations
    $resourceGroupName = "YourResourceGroup" 
    # If you don't already have this resource group, you can create it with 
    # New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Register the ARM provider idempotently. This must be done once per subscription
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Search"

    # Create a new search service
    # This command will return once the service is fully created
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateUri "https://gallery.azure.com/artifact/20151001/Microsoft.Search.1.0.9/DeploymentTemplates/searchServiceDefaultTemplate.json" `
        -NameFromTemplate $serviceName `
        -Sku $sku `
        -Location $location `
        -PartitionCount 1 `
        -ReplicaCount 1

    # Get information about your new service and store it in $resource
    $resource = Get-AzureRmResource `
        -ResourceType "Microsoft.Search/searchServices" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19

    # View your resource
    $resource

    # Get the primary admin API key
    $primaryKey = (Invoke-AzureRmResourceAction `
        -Action listAdminKeys `
        -ResourceId $resource.ResourceId `
        -ApiVersion 2015-08-19).PrimaryKey

    # Regenerate the secondary admin API Key
    $secondaryKey = (Invoke-AzureRmResourceAction `
        -ResourceType "Microsoft.Search/searchServices/regenerateAdminKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action secondary).SecondaryKey

    # Create a query key for read only access to your indexes
    $queryKeyDescription = "query-key-created-from-powershell"
    $queryKey = (Invoke-AzureRmResourceAction `
        -ResourceType "Microsoft.Search/searchServices/createQueryKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action $queryKeyDescription).Key

    # View your query key
    $queryKey

    # Delete query key
    Remove-AzureRmResource `
        -ResourceType "Microsoft.Search/searchServices/deleteQueryKey/$($queryKey)" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19

    # Scale your service up
    # Note that this will only work if you made a non "free" service
    # This command will not return until the operation is finished
    # It can take 15 minutes or more to provision the additional resources
    $resource.Properties.ReplicaCount = 2
    $resource | Set-AzureRmResource

    # Delete your service
    # Deleting your service will delete all indexes and data in the service
    $resource | Remove-AzureRmResource

## <a name="next-steps"></a>后续步骤
至此，已创建服务，接下来可以执行下面的步骤：生成[索引](search-what-is-an-index.md)，[查询索引](search-query-overview.md)，最后创建和管理自己的使用 Azure 搜索的搜索应用程序。

* [使用 Azure 门户创建 Azure 搜索索引](search-create-index-portal.md)
* [使用搜索资源管理器在 Azure 门户中查询 Azure 搜索索引](search-explorer.md)
* [设置索引器以从其他服务加载数据](search-indexer-overview.md)
* [如何在 .NET 中使用 Azure 搜索](search-howto-dotnet-sdk.md)
* [分析 Azure 搜索流量](search-traffic-analytics.md)

