---
title: 使用 Azure CLI 管理 Azure Cosmos DB 核心 (SQL) API 资源
description: 使用 Azure CLI 管理 Azure Cosmos DB 核心 (SQL) API 资源。
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/07/2020
ms.author: mjbrown
ms.openlocfilehash: dce041a46f173216844322b5a8985acbdfb86f26
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2020
ms.locfileid: "91840585"
---
# <a name="manage-azure-cosmos-core-sql-api-resources-using-azure-cli"></a>使用 Azure CLI 管理 Azure Cosmos Core (SQL) API 资源

以下指南介绍了使用 Azure CLI 自动管理 Azure Cosmos DB 帐户、数据库和容器的常见命令。 [Azure CLI 参考](https://docs.microsoft.com/cli/azure/cosmosdb)中收录了所有 Azure Cosmos DB CLI 命令的参考页。 还可以在[针对 Azure Cosmos DB 的 Azure CLI 示例](cli-samples.md)中找到更多示例，包括如何为 MongoDB、Gremlin、Cassandra 和表 API 创建和管理 Cosmos DB 帐户、数据库和容器。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本主题要求运行 Azure CLI 版本2.12.1 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

> [!IMPORTANT]
> 不能重命名 Azure Cosmos DB 资源，因为这违反了 Azure 资源管理器使用资源 Uri 的方式。

## <a name="azure-cosmos-accounts"></a>Azure Cosmos 帐户

以下部分演示如何管理 Azure Cosmos 帐户，包括：

* [创建 Azure Cosmos 帐户](#create-an-azure-cosmos-db-account)
* [添加或删除区域](#add-or-remove-regions)
* [启用多区域写入](#enable-multiple-write-regions)
* [设置区域性故障转移优先级](#set-failover-priority)
* [启用自动故障转移](#enable-automatic-failover)
* [触发手动故障转移](#trigger-manual-failover)
* [列出帐户密钥](#list-account-keys)
* [列出只读帐户密钥](#list-read-only-account-keys)
* [列出连接字符串](#list-connection-strings)
* [重新生成帐户密钥](#regenerate-account-key)

### <a name="create-an-azure-cosmos-db-account"></a>创建 Azure Cosmos DB 帐户

使用 SQL API 创建 Azure Cosmos DB 帐户，在美国西部2和美国东部2区域创建会话一致性：

> [!IMPORTANT]
> Azure Cosmos 帐户名称必须为小写且小于 44 个字符。

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount' #needs to be lower case and less than 44 characters

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --default-consistency-level Session \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --locations regionName='East US 2' failoverPriority=1 isZoneRedundant=False
```

### <a name="add-or-remove-regions"></a>添加或删除区域

创建包含两个区域的 Azure Cosmos 帐户，添加一个区域，并删除一个区域。

> [!NOTE]
> 不能同时添加或删除区域 `locations` 并更改 Azure Cosmos 帐户的其他属性。 修改区域的操作必须作为单独的操作与任何其他对帐户资源的更改操作分开执行。
> [!NOTE]
> 此命令可添加和删除区域，但不可修改故障转移优先级或触发手动故障转移。 请参阅[设置故障转移优先级](#set-failover-priority)和[触发手动故障转移](#trigger-manual-failover)。

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Create an account with 2 regions
az cosmosdb create --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False

# Add a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False \
    --locations regionName="South Central US" failoverPriority=2 isZoneRedundant=False

# Remove a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False
```

### <a name="enable-multiple-write-regions"></a>启用多个写入区域

启用 Cosmos 帐户的多区域写入

```azurecli-interactive
# Update an Azure Cosmos account from single write region to multiple write regions
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-multiple-write-locations true
```

### <a name="set-failover-priority"></a>设置故障转移优先级

为已为自动故障转移而配置的 Azure Cosmos 帐户设置故障转移优先级

```azurecli-interactive
# Assume region order is initially 'West US 2'=0 'East US 2'=1 'South Central US'=2 for account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Make South Central US the next region to fail over to instead of East US 2
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'West US 2=0' 'South Central US=1' 'East US 2=2'
```

### <a name="enable-automatic-failover"></a>启用自动故障转移

```azurecli-interactive
# Enable automatic failover on an existing account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-automatic-failover true
```

### <a name="trigger-manual-failover"></a>触发手动故障转移

> [!CAUTION]
> 在 priority = 0 的情况下更改区域会为 Azure Cosmos 帐户触发手动故障转移。 任何其他优先级更改都不会触发故障转移。

```azurecli-interactive
# Assume region order is initially 'West US 2=0' 'East US 2=1' 'South Central US=2' for account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Trigger a manual failover to promote East US 2 as new write region
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'East US 2=0' 'South Central US=1' 'West US 2=2'
```

### <a name="list-all-account-keys"></a><a id="list-account-keys"></a> 列出所有帐户密钥

获取 Cosmos 帐户的所有密钥。

```azurecli-interactive
# List all account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
   -n $accountName \
   -g $resourceGroupName
```

### <a name="list-read-only-account-keys"></a>列出只读帐户密钥

获取 Cosmos 帐户的只读密钥。

```azurecli-interactive
# List read-only account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type read-only-keys
```

### <a name="list-connection-strings"></a>列出连接字符串

获取 Cosmos 帐户的连接字符串。

```azurecli-interactive
# List connection strings
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type connection-strings
```

### <a name="regenerate-account-key"></a>重新生成帐户密钥

重新生成 Cosmos 帐户的新密钥。

```azurecli-interactive
# Regenerate secondary account keys
# key-kind values: primary, primaryReadonly, secondary, secondaryReadonly
az cosmosdb keys regenerate \
    -n $accountName \
    -g $resourceGroupName \
    --key-kind secondary
```

## <a name="azure-cosmos-db-database"></a>Azure Cosmos DB 数据库

以下部分演示了如何管理 Azure Cosmos DB 数据库，具体包括：

* [创建数据库](#create-a-database)
* [创建具有共享吞吐量的数据库](#create-a-database-with-shared-throughput)
* [将数据库迁移到自动缩放吞吐量](#migrate-a-database-to-autoscale-throughput)
* [更改数据库吞吐量](#change-database-throughput)
* [阻止数据库被删除](#prevent-a-database-from-being-deleted)

### <a name="create-a-database"></a>创建数据库

创建 Cosmos 数据库。

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName
```

### <a name="create-a-database-with-shared-throughput"></a>创建具有共享吞吐量的数据库

创建具有共享吞吐量的 Cosmos 数据库。

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
throughput=400

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $throughput
```

### <a name="migrate-a-database-to-autoscale-throughput"></a>将数据库迁移到自动缩放吞吐量

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'

# Migrate to autoscale throughput
az cosmosdb sql database throughput migrate \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    -t 'autoscale'

# Read the new autoscale max throughput
az cosmosdb sql database throughput show \
    -g $resourceGroupName \
    -a $accountName \
    -n $databaseName \
    --query resource.autoscaleSettings.maxThroughput \
    -o tsv
```

### <a name="change-database-throughput"></a>更改数据库吞吐量

将 Cosmos 数据库的吞吐量增加 1000 RU/s。

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
newRU=1000

# Get minimum throughput to make sure newRU is not lower than minRU
minRU=$(az cosmosdb sql database throughput show \
    -g $resourceGroupName -a $accountName -n $databaseName \
    --query resource.minimumThroughput -o tsv)

if [ $minRU -gt $newRU ]; then
    newRU=$minRU
fi

az cosmosdb sql database throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $newRU
```

### <a name="prevent-a-database-from-being-deleted"></a>阻止数据库被删除

将 Azure 资源删除锁定置于数据库上，以防止删除该数据库。 此功能要求锁定 Cosmos 帐户，防止数据平面 Sdk 更改。 若要了解详细信息，请参阅 [阻止 sdk 中的更改](role-based-access-control.md#prevent-sdk-changes)。 Azure 资源锁还可以通过指定锁定类型防止更改资源 `ReadOnly` 。 对于 Cosmos 数据库，可以使用它来防止更改吞吐量。

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'

lockType='CanNotDelete' # CanNotDelete or ReadOnly
databaseParent="databaseAccounts/$accountName"
databaseLockName="$databaseName-Lock"

# Create a delete lock on database
az lock create --name $databaseLockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/sqlDatabases \
    --lock-type $lockType \
    --parent $databaseParent \
    --resource $databaseName

# Delete lock on database
lockid=$(az lock show --name $databaseLockName \
        --resource-group $resourceGroupName \
        --resource-type Microsoft.DocumentDB/sqlDatabases \
        --resource $databaseName \
        --parent $databaseParent \
        --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="azure-cosmos-db-container"></a>Azure Cosmos DB 容器

以下部分演示了如何管理 Azure Cosmos DB 容器，具体包括：

* [创建容器](#create-a-container)
* [使用自动缩放创建容器](#create-a-container-with-autoscale)
* [创建启用了 TTL 的容器](#create-a-container-with-ttl)
* [使用自定义索引策略创建容器](#create-a-container-with-a-custom-index-policy)
* [更改容器吞吐量](#change-container-throughput)
* [将容器迁移到自动缩放吞吐量](#migrate-a-container-to-autoscale-throughput)
* [阻止删除容器](#prevent-a-container-from-being-deleted)

### <a name="create-a-container"></a>创建容器

创建带有默认索引策略、分区键且 RU/s 为 400 的 Cosmos 容器。

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput
```

### <a name="create-a-container-with-autoscale"></a>使用自动缩放创建容器

使用默认索引策略、分区键且自动缩放 RU/s 为 4000 的 Cosmos 容器。

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
maxThroughput=4000

az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --max-throughput $maxThroughput
```

### <a name="create-a-container-with-ttl"></a>创建带有 TTL 的容器

创建启用了 TTL 的 Cosmos 容器。

```azurecli-interactive
# Create an Azure Cosmos container with TTL of one day
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'

az cosmosdb sql container update \
    -g $resourceGroupName \
    -a $accountName \
    -d $databaseName \
    -n $containerName \
    --ttl=86400
```

### <a name="create-a-container-with-a-custom-index-policy"></a>创建带有自定义索引策略的容器

创建带有自定义索引策略、空间索引、组合索引、分区键且 RU/s 为 400 的 Cosmos 容器。

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

# Generate a unique 10 character alphanumeric string to ensure unique resource names
uniqueId=$(env LC_CTYPE=C tr -dc 'a-z0-9' < /dev/urandom | fold -w 10 | head -n 1)

# Define the index policy for the container, include spatial and composite indexes
idxpolicy=$(cat << EOF
{
    "indexingMode": "consistent",
    "includedPaths": [
        {"path": "/*"}
    ],
    "excludedPaths": [
        { "path": "/headquarters/employees/?"}
    ],
    "spatialIndexes": [
        {"path": "/*", "types": ["Point"]}
    ],
    "compositeIndexes":[
        [
            { "path":"/name", "order":"ascending" },
            { "path":"/age", "order":"descending" }
        ]
    ]
}
EOF
)
# Persist index policy to json file
echo "$idxpolicy" > "idxpolicy-$uniqueId.json"


az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput \
    --idx @idxpolicy-$uniqueId.json

# Clean up temporary index policy file
rm -f "idxpolicy-$uniqueId.json"
```

### <a name="change-container-throughput"></a>更改容器吞吐量

将 Cosmos 容器的吞吐量增加 1000 RU/s。

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
newRU=1000

# Get minimum throughput to make sure newRU is not lower than minRU
minRU=$(az cosmosdb sql container throughput show \
    -g $resourceGroupName -a $accountName -d $databaseName \
    -n $containerName --query resource.minimumThroughput -o tsv)

if [ $minRU -gt $newRU ]; then
    newRU=$minRU
fi

az cosmosdb sql container throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -d $databaseName \
    -n $containerName \
    --throughput $newRU
```

### <a name="migrate-a-container-to-autoscale-throughput"></a>将容器迁移到自动缩放吞吐量

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'

# Migrate to autoscale throughput
az cosmosdb sql container throughput migrate \
    -a $accountName \
    -g $resourceGroupName \
    -d $databaseName \
    -n $containerName \
    -t 'autoscale'

# Read the new autoscale max throughput
az cosmosdb sql container throughput show \
    -g $resourceGroupName \
    -a $accountName \
    -d $databaseName \
    -n $containerName \
    --query resource.autoscaleSettings.maxThroughput \
    -o tsv
```

### <a name="prevent-a-container-from-being-deleted"></a>阻止删除容器

将 Azure 资源删除锁定置于容器上，以防止删除该容器。 此功能要求锁定 Cosmos 帐户，防止数据平面 Sdk 更改。 若要了解详细信息，请参阅 [阻止 sdk 中的更改](role-based-access-control.md#prevent-sdk-changes)。 Azure 资源锁还可以通过指定锁定类型防止更改资源 `ReadOnly` 。 对于 Cosmos 容器，这可用于阻止更改吞吐量或其他任何属性。

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'

lockType='CanNotDelete' # CanNotDelete or ReadOnly
databaseParent="databaseAccounts/$accountName"
containerParent="databaseAccounts/$accountName/sqlDatabases/$databaseName"
containerLockName="$containerName-Lock"

# Create a delete lock on container
az lock create --name $containerLockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/containers \
    --lock-type $lockType \
    --parent $containerParent \
    --resource $containerName

# Delete lock on container
lockid=$(az lock show --name $containerLockName \
        --resource-group $resourceGroupName \
        --resource-type Microsoft.DocumentDB/containers \
        --resource-name $containerName \
        --parent $containerParent \
        --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅：

* [安装 Azure CLI](/cli/azure/install-azure-cli)
* [Azure CLI 参考](https://docs.microsoft.com/cli/azure/cosmosdb)
* [针对 Azure Cosmos DB 的其他 Azure CLI 示例](cli-samples.md)
