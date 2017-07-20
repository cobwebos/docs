---
title: "Azure Cosmos DB 自动化 — 使用 Powershell 管理 | Microsoft Docs"
description: "使用 Azure Powershell 管理 Azure Cosmos DB 帐户。"
services: cosmos-db
author: dmakwana
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2017
ms.author: dimakwan
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 1d5ea7263cc44a1873b5009edf4f106cc91e0067
ms.contentlocale: zh-cn
ms.lasthandoff: 05/31/2017


---
# <a name="create-an-azure-cosmos-db-account-using-powershell"></a>使用 PowerShell 创建 Azure Cosmos DB 帐户

以下指南介绍了使用 Azure Powershell 自动管理 Azure Cosmos DB 数据库帐户的命令。 还介绍了用于管理[多区域数据库帐户][scaling-globally]中的帐户密钥和故障转移优先级的命令。 更新数据库帐户可以修改一致性策略以及添加/删除区域。 对于 Azure Cosmos DB 帐户的跨平台管理，可使用 [Azure CLI](cli-samples.md)、[资源提供程序 REST API][rp-rest-api] 或 [Azure 门户](create-documentdb-dotnet.md#create-account)。

## <a name="getting-started"></a>入门

请按照[如何安装和配置 Azure PowerShell][powershell-install-configure] 中的说明安装 PowerShell 并在其中登录 Azure Resource Manager 帐户。

### <a name="notes"></a>说明

* 如果只执行以下命令（不需要用户确认），请将 `-Force` 标志附加到命令中。
* 以下所有命令都是同步的。

## <a id="create-documentdb-account-powershell"></a> 创建 Azure Cosmos DB 帐户

此命令可创建 Azure Cosmos DB 数据库帐户。 可以将新数据库帐户配置为具有特定[一致性策略](consistency-levels.md)的单区域或[多区域][scaling-globally]。

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name>  -Location "<resource-group-location>" -Name <database-account-name> -Properties $CosmosDBProperties
    
* `<write-region-location>` 数据库帐户的写入区域位置名称。 此位置的故障转移优先级值必须为 0。 每个数据库帐户必须只有一个写入区域。
* `<read-region-location>` 数据库帐户的读取区域位置名称。 此位置的故障转移优先级值必须大于 0。 每个数据库帐户可以有多个读取区域。
* `<ip-range-filter>` 指定 CIDR 格式的 IP 地址集或 IP 地址范围，将这些地址纳入给定数据库帐户所允许的客户端 IP 列表内。 IP 地址/范围必须以逗号分隔，且不能包含空格。 有关详细信息，请参阅 [Azure Cosmos DB 防火墙支持](firewall-support.md)
* `<default-consistency-level>` Azure Cosmos DB 帐户的默认一致性级别。 有关详细信息，请参阅 [Azure Cosmos DB 中的一致性级别](consistency-levels.md)。
* `<max-interval>` 与有限过期一致性一起使用时，此值表示允许的过期时间（以秒为单位）。 此值的接受范围为 1 - 100。
* `<max-staleness-prefix>` 与有限过期一致性一起使用时，此值表示允许的过期请求数。 此值的接受范围为 1 – 2,147,483,647。
* `<resource-group-name>` 新 Azure Cosmos DB 数据库帐户所属的 [Azure 资源组][azure-resource-groups]的名称。
* `<resource-group-location>` 新 Azure Cosmos DB 数据库帐户所属的 Azure 资源组的位置。
* `<database-account-name>` 要创建的 Azure Cosmos DB 数据库帐户的名称。 只能使用小写字母、数字及“-”字符，且长度必须为 3 到 50 个字符。

示例： 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Location "West US" -Name "docdb-test" -Properties $CosmosDBProperties

### <a name="notes"></a>说明
* 上述示例创建具有两个区域的数据库帐户。 还可能创建单区域（指定为写入区域并且故障转移优先级值为 0）或多区域数据库帐户。 有关详细信息，请参阅[多区域数据库帐户][scaling-globally]。
* 位置必须是已正式推出 Azure Cosmos DB 的区域。 [Azure 区域页面](https://azure.microsoft.com/regions/#services)提供当前的区域列表。

## <a id="update-documentdb-account-powershell"></a>更新 DocumentDB 数据库帐户

此命令可更新 Azure Cosmos DB 数据库帐户属性。 这包括一致性策略和数据库帐户所在的位置。

> [!NOTE]
> 此命令可添加和删除区域，但不可修改故障转移优先级。 若要修改故障转移优先级，请参阅[以下内容](#modify-failover-priority-powershell)。

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $iprangefilter = "<ip-range-filter>"
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name> -Name <database-account-name> -Properties $CosmosDBProperties
    
* `<write-region-location>` 数据库帐户的写入区域位置名称。 此位置的故障转移优先级值必须为 0。 每个数据库帐户必须只有一个写入区域。
* `<read-region-location>` 数据库帐户的读取区域位置名称。 此位置的故障转移优先级值必须大于 0。 每个数据库帐户可以有多个读取区域。
* `<default-consistency-level>` Azure Cosmos DB 帐户的默认一致性级别。 有关详细信息，请参阅 [Azure Cosmos DB 中的一致性级别](consistency-levels.md)。
* `<ip-range-filter>` 指定 CIDR 格式的 IP 地址集或 IP 地址范围，将这些地址纳入给定数据库帐户所允许的客户端 IP 列表内。 IP 地址/范围必须以逗号分隔，且不能包含空格。 有关详细信息，请参阅 [Azure Cosmos DB 防火墙支持](firewall-support.md)
* `<max-interval>` 与有限过期一致性一起使用时，此值表示允许的过期时间（以秒为单位）。 此值的接受范围为 1 - 100。
* `<max-staleness-prefix>` 与有限过期一致性一起使用时，此值表示允许的过期请求数。 此值的接受范围为 1 – 2,147,483,647。
* `<resource-group-name>` 新 Azure Cosmos DB 数据库帐户所属的 [Azure 资源组][azure-resource-groups]的名称。
* `<resource-group-location>` 新 Azure Cosmos DB 数据库帐户所属的 Azure 资源组的位置。
* `<database-account-name>` 要更新的 Azure Cosmos DB 数据库帐户的名称。

示例： 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $iprangefilter = ""
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $CosmosDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy; "ipRangeFilter"=$iprangefilter}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Properties $CosmosDBProperties

## <a id="delete-documentdb-account-powershell"></a>删除 DocumentDB 数据库帐户

此命令可删除现有 Azure Cosmos DB 数据库帐户。

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"
    
* `<resource-group-name>` 新 Azure Cosmos DB 数据库帐户所属的 [Azure 资源组][azure-resource-groups]的名称。
* `<database-account-name>` 要删除的 Azure Cosmos DB 数据库帐户的名称。

示例：

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="get-documentdb-properties-powershell"></a>获取 DocumentDB 数据库帐户的属性

此命令可获取现有 Azure Cosmos DB 数据库帐户的属性。

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` 新 Azure Cosmos DB 数据库帐户所属的 [Azure 资源组][azure-resource-groups]的名称。
* `<database-account-name>` Azure Cosmos DB 数据库帐户的名称。

示例：

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="update-tags-powershell"></a> 更新 Azure Cosmos DB 数据库帐户的标记

下面的示例介绍如何设置 Azure Cosmos DB 数据库帐户的 [Azure 资源标记][azure-resource-tags]。

> [!NOTE]
> 通过此将 `-Tags` 标志追加到相应的参数，可将此命令与创建或更新命令组合。

示例：

    $tags = @{"dept" = "Finance”; environment = “Production”}
    Set-AzureRmResource -ResourceType “Microsoft.DocumentDB/databaseAccounts”  -ResourceGroupName "rg-test" -Name "docdb-test" -Tags $tags

## <a id="list-account-keys-powershell"></a>列出帐户密钥

创建 Azure Cosmos DB 帐户时，服务生成两个主访问密钥，可用于访问 Azure Cosmos DB 帐户时的身份验证。 提供两个访问密钥后，Azure Cosmos DB 支持在不中断 Azure Cosmos DB 帐户连接的情况下重新生成密钥。 还可以使用只读密钥，用于对只读操作进行身份验证。 有两个读写密钥（主密钥和辅助密钥）和两个只读密钥（主密钥和辅助密钥）。

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` 新 Azure Cosmos DB 数据库帐户所属的 [Azure 资源组][azure-resource-groups]的名称。
* `<database-account-name>` Azure Cosmos DB 数据库帐户的名称。

示例：

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="list-connection-strings-powershell"></a> 列出连接字符串

对于 MongoDB 帐户，可以使用以下命令检索将 MongoDB 应用连接到数据库帐户的连接字符串。

    $keys = Invoke-AzureRmResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` 新 Azure Cosmos DB 数据库帐户所属的 [Azure 资源组][azure-resource-groups]的名称。
* `<database-account-name>` Azure Cosmos DB 数据库帐户的名称。

示例：

    $keys = Invoke-AzureRmResourceAction -Action listConnectionStrings -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a id="regenerate-account-key-powershell"></a>重新生成帐户密钥

应定期更改 Azure Cosmos DB 帐户访问密钥，使连接更安全。 将分配两个访问密钥，从而可以在使用一个访问密钥保持连接到 Azure Cosmos DB 帐户的同时，再生成另一个访问密钥。

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"keyKind"="<key-kind>"}

* `<resource-group-name>` 新 Azure Cosmos DB 数据库帐户所属的 [Azure 资源组][azure-resource-groups]的名称。
* `<database-account-name>` Azure Cosmos DB 数据库帐户的名称。
* `<key-kind>` 要重新生成的以下四种密钥类型之一：[“Primary”|“Secondary”|“PrimaryReadonly”|“SecondaryReadonly”]。

示例：

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"keyKind"="Primary"}

## <a id="modify-failover-priority-powershell"></a>修改 Azure Cosmos DB 数据库帐户的故障转移优先级

对于多区域数据库帐户，可以更改 Azure Cosmos DB 数据库帐户所在的各个区域的故障转移优先级。 有关 Azure Cosmos DB 数据库帐户中故障转移的详细信息，请参阅[使用 Azure Cosmos DB 全局分发数据][distribute-data-globally]。

    $failoverPolicies = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0},@{"locationName"="<read-region-location>"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"failoverPolicies"=$failoverPolicies}

* `<write-region-location>` 数据库帐户的写入区域位置名称。 此位置的故障转移优先级值必须为 0。 每个数据库帐户必须只有一个写入区域。
* `<read-region-location>` 数据库帐户的读取区域位置名称。 此位置的故障转移优先级值必须大于 0。 每个数据库帐户可以有多个读取区域。
* `<resource-group-name>` 新 Azure Cosmos DB 数据库帐户所属的 [Azure 资源组][azure-resource-groups]的名称。
* `<database-account-name>` Azure Cosmos DB 数据库帐户的名称。

示例：

    $failoverPolicies = @(@{"locationName"="East US"; "failoverPriority"=0},@{"locationName"="West US"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"failoverPolicies"=$failoverPolicies}

## <a name="next-steps"></a>后续步骤

* 若要使用 .NET 进行连接，请参阅[使用 .NET 进行连接和查询](create-documentdb-dotnet.md)。
* 若要使用 .NET Core 进行连接，请参阅[使用 .NET Core 进行连接和查询](create-documentdb-dotnet-core.md)。
* 若要使用 Node.js 进行连接，请参阅[使用 Node.js 和 MongoDB 应用进行连接和查询](create-mongodb-nodejs.md)。

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/documentdbresourceprovider/

