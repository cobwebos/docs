---
title: 使用 PowerShell 创建并管理 Azure Cosmos DB
description: 使用 Azure Powershell 管理 Azure Cosmos DB 帐户、数据库、容器和吞吐量。
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 08/05/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 3b5d8ff6177b4f9f397b40f50a9cc65f74460f02
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71815887"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>使用 PowerShell 管理 Azure Cosmos DB SQL API 资源

以下指南介绍了如何使用 Powershell 通过脚本来自动管理 Azure Cosmos DB 资源，其中包括帐户、数据库、容器和吞吐量。 管理 Azure Cosmos DB 是直接使用 AzResource cmdlet 通过 Azure Cosmos DB 资源提供程序进行的。 若要查看可以通过适用于 Azure Cosmos DB 资源提供程序的 PowerShell 管理的所有属性，请参阅 [Azure Cosmos DB 资源提供程序架构](/azure/templates/microsoft.documentdb/allversions)

若要对 Azure Cosmos DB 进行跨平台管理，可以使用 [Azure CLI](manage-with-cli.md)、[REST API][rp-rest-api] 或 [Azure 门户](create-sql-api-dotnet.md#create-account)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>入门

请按照[如何安装和配置 Azure PowerShell][powershell-install-configure] 中的说明安装 PowerShell 并在其中登录 Azure 帐户。

* 如果只执行以下命令（不需要用户确认），请将 `-Force` 标志附加到命令中。
* 以下所有命令都是同步的。

## <a name="azure-cosmos-accounts"></a>Azure Cosmos 帐户

以下部分演示了如何管理 Azure Cosmos 帐户，具体包括：

* [创建 Azure Cosmos 帐户](#create-account)
* [更新 Azure Cosmos 帐户](#update-account)
* [列出订阅中的所有 Azure Cosmos 帐户](#list-accounts)
* [获取 Azure Cosmos 帐户](#get-account)
* [删除 Azure Cosmos 帐户](#delete-account)
* [更新 Azure Cosmos 帐户标记](#update-tags)
* [列出 Azure Cosmos 帐户密钥](#list-keys)
* [重新生成 Azure Cosmos 帐户密钥](#regenerate-keys)
* [列出 Azure Cosmos 帐户的连接字符串](#list-connection-strings)
* [修改 Azure Cosmos 帐户的故障转移优先级](#modify-failover-priority)
* [触发 Azure Cosmos 帐户的手动故障转移](#trigger-manual-failover)

### <a id="create-account"></a> 创建 Azure Cosmos 帐户

此命令创建一个 Azure Cosmos 数据库帐户，该帐户使用[多区域][distribute-data-globally]、有限过期[一致性策略](consistency-levels.md)。

```azurepowershell-interactive
# Create an Azure Cosmos Account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US 2"
$accountName = "mycosmosaccount" # must be lowercase and < 31 characters .

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 },
    @{ "locationName"="East US 2"; "failoverPriority"=1 }
)

$consistencyPolicy = @{
    "defaultConsistencyLevel"="BoundedStaleness";
    "maxIntervalInSeconds"=300;
    "maxStalenessPrefix"=100000
}

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "enableMultipleWriteLocations"="false"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

* `$accountName` Azure Cosmos 帐户的名称。 必须为小写，接受字母数字和“-”字符，长度必须为 3 到 31 个字符。
* `$location` Azure Cosmos 帐户资源的位置。
* `$locations` 数据库帐户的副本区域。 每个数据库帐户必须有一个故障转移优先级值为 0 的写入区域。
* `$consistencyPolicy` Azure Cosmos 帐户的默认一致性级别。 有关详细信息，请参阅 [Azure Cosmos DB 中的一致性级别](consistency-levels.md)。
* `$CosmosDBProperties` 属性值，传递给 Cosmos DB Azure 资源管理器提供程序来预配帐户。

可以为 Azure Cosmos 帐户配置 IP 防火墙和虚拟网络服务终结点。 若要了解如何为 Azure Cosmos DB 配置 IP 防火墙，请参阅[配置 IP 防火墙](how-to-configure-firewall.md)。  若要详细了解如何为 Azure Cosmos DB 启用服务终结点，请参阅[配置从虚拟网络进行访问的权限](how-to-configure-vnet-service-endpoint.md)。

### <a id="list-accounts"></a> 列出订阅中的所有 Azure Cosmos 帐户

可以使用此命令列出订阅中的所有 Azure Cosmos 帐户。

```azurepowershell-interactive
# List Azure Cosmos Accounts

Get-AzResource -ResourceType Microsoft.DocumentDb/databaseAccounts | ft
```

### <a id="get-account"></a> 获取 Azure Cosmos 帐户的属性

此命令可获取现有 Azure Cosmos 帐户的属性。

```azurepowershell-interactive
# Get the properties of an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName | Select-Object Properties
```

### <a id="update-account"></a> 更新 Azure Cosmos 帐户

此命令可更新 Azure Cosmos 数据库帐户属性。 可以更新的属性包括：

* 添加或删除区域
* 更改默认一致性策略
* 更改 IP 范围筛选器
* 更改虚拟网络配置
* 启用多主数据库

> [!NOTE]
> 不能同时添加或删除区域 `locations` 并更改 Azure Cosmos 帐户的其他属性。 修改区域的操作必须作为单独的操作与任何其他对帐户资源的更改操作分开执行。
> [!NOTE]
> 此命令可添加和删除区域，但不可修改故障转移优先级或触发手动故障转移。 请参阅[修改故障转移优先级](#modify-failover-priority)和[触发手动故障转移](#trigger-manual-failover)。

```azurepowershell-interactive
# Create an account with 2 regions
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDb/databaseAccounts"
$accountName = "mycosmosaccount" # must be lower case and < 31 characters

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0, "isZoneRedundant"=false },
    @{ "locationName"="East US 2"; "failoverPriority"=1, "isZoneRedundant"=false }
)
$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }
$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy
}
New-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties

# Add a region
$account = Get-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0, "isZoneRedundant"=false },
    @{ "locationName"="East US 2"; "failoverPriority"=1, "isZoneRedundant"=false },
    @{ "locationName"="South Central US"; "failoverPriority"=2, "isZoneRedundant"=false }
)

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties

# Azure Resource Manager does not wait on the resource update
Write-Host "Confirm region added before continuing..."

# Remove a region
$account = Get-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0, "isZoneRedundant"=false },
    @{ "locationName"="East US 2"; "failoverPriority"=1, "isZoneRedundant"=false }
)

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```
### <a id="multi-master"></a> 为 Azure Cosmos 帐户启用多个写入区域

```azurepowershell-interactive
# Update an Azure Cosmos account from single to multi-master
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceType = "Microsoft.DocumentDb/databaseAccounts"

$account = Get-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$account.Properties.enableMultipleWriteLocations = "true"
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="delete-account"></a> 删除 Azure Cosmos 帐户

此命令可删除现有 Azure Cosmos 帐户。

```azurepowershell-interactive
# Delete an Azure Cosmos Account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName
```

### <a id="update-tags"></a> 更新 Azure Cosmos 帐户标记

下面的示例介绍如何设置 Azure Cosmos 帐户的 [Azure 资源标记][azure-resource-tags]。

> [!NOTE]
> 通过此将 `-Tags` 标志追加到相应的参数，可将此命令与创建或更新命令组合。

```azurepowershell-interactive
# Update tags for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$tags = @{
    "dept" = "Finance";
    "environment" = "Production"
}

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -Tags $tags
```

### <a id="list-keys"></a>列出帐户密钥

创建 Azure Cosmos DB 帐户时，服务生成两个主访问密钥，可用于访问 Azure Cosmos DB 帐户时的身份验证。 提供两个访问密钥后，Azure Cosmos DB 支持在不中断 Azure Cosmos DB 帐户连接的情况下重新生成密钥。 还可以使用只读密钥，用于对只读操作进行身份验证。 有两个读写密钥（主密钥和辅助密钥）和两个只读密钥（主密钥和辅助密钥）。

```azurepowershell-interactive
# List keys for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listKeys `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

### <a id="list-connection-strings"></a> 列出连接字符串

对于 MongoDB 帐户，可以使用以下命令检索将 MongoDB 应用连接到数据库帐户的连接字符串。

```azurepowershell-interactive
# List connection strings for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listConnectionStrings `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

### <a id="regenerate-keys"></a> 重新生成帐户密钥

应定期重新生成 Azure Cosmos 帐户访问密钥，使连接更安全。 将会为帐户分配主访问密钥和辅助访问密钥。 这样，当重新生成某个密钥时，客户端仍可以进行访问。 有四种 Azure Cosmos 帐户密钥（主密钥、辅助密钥、PrimaryReadonly 密钥和 SecondaryReadonly 密钥）

```azurepowershell-interactive
# Regenerate the primary key for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keyKind = @{ "keyKind"="Primary" }

$keys = Invoke-AzResourceAction -Action regenerateKey `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $keyKind

Select-Object $keys
```

### <a id="enable-automatic-failover"></a> 启用自动故障转移

使 Cosmos 帐户能够在主要区域不可用时故障转移到其次要区域。

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceType = "Microsoft.DocumentDb/databaseAccounts"

$account = Get-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$account.Properties.enableAutomaticFailover="true";
$CosmosDBProperties = $account.Properties;

Set-AzResource -ResourceType $resourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="modify-failover-priority"></a> 修改故障转移优先级

对于配置了自动故障转移的帐户，可以更改在主要副本不可用时 Cosmos 将次要副本提升为主要副本的顺序。

对于以下示例，假定当前的故障转移优先级为 `West US 2 = 0`、`East US 2 = 1`、`South Central US = 2`。

> [!CAUTION]
> 在 `failoverPriority=0` 的情况下更改 `locationName` 会触发 Azure Cosmos 帐户的手动故障转移。 任何其他的优先级更改不会触发故障转移。

```azurepowershell-interactive
# Change the failover priority for an Azure Cosmos Account
# Assume existing priority is "West US 2" = 0, "East US 2" = 1, "South Central US" = 2

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverRegions = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 },
    @{ "locationName"="South Central US"; "failoverPriority"=1 },
    @{ "locationName"="East US 2"; "failoverPriority"=2 }
)

$failoverPolicies = @{
    "failoverPolicies"= $failoverRegions
}

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

### <a id="trigger-manual-failover"></a> 触发手动故障转移

对于配置了手动故障转移的帐户，可以通过修改为 `failoverPriority=0` 来进行故障转移并将所有次要副本提升为主要副本。 此操作可用于启动灾难恢复演练以测试灾难恢复规划。

在下面的示例中，假设帐户的当前故障转移优先级为 `West US 2 = 0` 和 `East US 2 = 1`，然后将区域互换。

> [!CAUTION]
> 在 `failoverPriority=0` 的情况下更改 `locationName` 会触发 Azure Cosmos 帐户的手动故障转移。 任何其他优先级更改都不会触发故障转移。

```azurepowershell-interactive
# Change the failover priority for an Azure Cosmos Account
# Assume existing priority is "West US 2" = 0, "East US 2" = 1, "South Central US" = 2

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverRegions = @(
    @{ "locationName"="South Central US"; "failoverPriority"=0 },
    @{ "locationName"="East US 2"; "failoverPriority"=1 },
    @{ "locationName"="West US 2"; "failoverPriority"=2 }
)

$failoverPolicies = @{
    "failoverPolicies"= $failoverRegions
}

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a name="azure-cosmos-database"></a>Azure Cosmos 数据库

以下部分演示了如何管理 Azure Cosmos 数据库，具体包括：

* [创建 Azure Cosmos 数据库](#create-db)
* [创建共享吞吐量的 Azure Cosmos 数据库](#create-db-ru)
* [获取 Azure Cosmos 数据库的吞吐量](#get-db-ru)
* [列出帐户中的所有 Azure Cosmos 数据库](#list-db)
* [获取单个 Azure Cosmos 数据库](#get-db)
* [删除 Azure Cosmos 数据库](#delete-db)

### <a id="create-db"></a>创建 Azure Cosmos 数据库

```azurepowershell-interactive
# Create an Azure Cosmos database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{"id"=$databaseName}
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a id="create-db-ru"></a>创建共享吞吐量的 Azure Cosmos 数据库

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database2"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{ "id"=$databaseName };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a id="get-db-ru"></a>获取 Azure Cosmos 数据库的吞吐量

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$databaseThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/settings"
$databaseThroughputResourceName = $accountName + "/sql/" + $databaseName + "/throughput"

Get-AzResource -ResourceType $databaseThroughputResourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $databaseThroughputResourceName  | Select-Object Properties
```

### <a id="list-db"></a>获取帐户中的所有 Azure Cosmos 数据库

```azurepowershell-interactive
# Get all databases in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceName = $accountName + "/sql/"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName  | Select-Object Properties
```

### <a id="get-db"></a>获取单个 Azure Cosmos 数据库

```azurepowershell-interactive
# Get a single database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="delete-db"></a>删除 Azure Cosmos 数据库

```azurepowershell-interactive
# Delete a database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName
Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="azure-cosmos-container"></a>Azure Cosmos 容器

以下部分演示了如何管理 Azure Cosmos 容器，具体包括：

* [创建 Azure Cosmos 容器](#create-container)
* [使用大分区键创建 Azure Cosmos 容器](#create-container-big-pk)
* [获取 Azure Cosmos 容器的吞吐量](#get-container-ru)
* [创建共享吞吐量的 Azure Cosmos 容器](#create-container-ru)
* [使用自定义索引创建 Azure Cosmos 容器](#create-container-custom-index)
* [在索引关闭的情况下创建 Azure Cosmos 容器](#create-container-no-index)
* [创建键和 TTL 都独一无二的 Azure Cosmos 容器](#create-container-unique-key-ttl)
* [创建带冲突解决方案的 Azure Cosmos 容器](#create-container-lww)
* [列出数据库中的所有 Azure Cosmos 容器](#list-containers)
* [获取数据库中的单个 Azure Cosmos 容器](#get-container)
* [删除 Azure Cosmos 容器](#delete-container)

### <a id="create-container"></a>创建 Azure Cosmos 容器

```azurepowershell-interactive
# Create an Azure Cosmos container with default indexes and throughput at 400 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-big-pk"></a>使用大分区键大小创建 Azure Cosmos 容器

```azurepowershell-interactive
# Create an Azure Cosmos container with a large partition key value (version = 2)
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash";
            "version" = 2
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="get-container-ru"></a>获取 Azure Cosmos 容器的吞吐量

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$containerThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers/settings"
$containerThroughputResourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName + "/throughput"

Get-AzResource -ResourceType $containerThroughputResourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $containerThroughputResourceName  | Select-Object Properties
```

### <a id="create-container-ru"></a>创建共享吞吐量的 Azure Cosmos 容器

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container2"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{}
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties 
```

### <a id="create-container-custom-index"></a>使用自定义索引策略创建 Azure Cosmos 容器

```azurepowershell-interactive
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container3"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent";
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @()
            });
            "excludedPaths"= @(@{
                "path"="/myPathToNotIndex/*"
            })
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-no-index"></a>在索引关闭的情况下创建 Azure Cosmos 容器

```azurepowershell-interactive
# Create an Azure Cosmos container with no indexing
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container4"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        };
        "indexingPolicy"=@{
            "indexingMode"="none"
        }
    };
    "options"=@{ "Throughput"="400" }
} 

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-unique-key-ttl"></a>创建键策略和 TTL 都独一无二的 Azure Cosmos 容器

```azurepowershell-interactive
# Create a container with a unique key policy and TTL of one day
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent";
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @()
            });
            "excludedPaths"= @()
        };
        "uniqueKeyPolicy"= @{
            "uniqueKeys"= @(@{
                "paths"= @(
                    "/myUniqueKey1";
                    "/myUniqueKey2"
                )
            })
        };
        "defaultTtl"= 86400;
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-lww"></a>创建带冲突解决方案的 Azure Cosmos 容器

若要通过创建冲突解决方案策略来使用存储过程，请设置 `"mode"="custom"`，并将解决方案路径设置为存储过程的名称，即 `"conflictResolutionPath"="myResolverStoredProcedure"`。 若要将所有冲突都写入 ConflictsFeed，然后分别进行处理，请设置 `"mode"="custom"` 和 `"conflictResolutionPath"=""`

```azurepowershell-interactive
# Create container with last-writer-wins conflict resolution policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container6"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        };
        "conflictResolutionPolicy"=@{
            "mode"="lastWriterWins";
            "conflictResolutionPath"="/myResolutionPath"
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="list-containers"></a>列出数据库中的所有 Azure Cosmos 容器

```azurepowershell-interactive
# List all Azure Cosmos containers in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="get-container"></a>获取数据库中的单个 Azure Cosmos 容器

```azurepowershell-interactive
# Get a single Azure Cosmos container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="delete-container"></a>删除 Azure Cosmos 容器

```azurepowershell-interactive
# Delete an Azure Cosmos container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="next-steps"></a>后续步骤

* [所有 PowerShell 示例](powershell-samples.md)
* [如何管理 Azure Cosmos 帐户](how-to-manage-database-account.md)
* [创建 Azure Cosmos 容器](how-to-create-container.md)
* [在 Azure Cosmos DB 中配置生存时间](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/
