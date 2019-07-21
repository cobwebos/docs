---
title: 在 Azure Cosmos DB 中预配容器吞吐量
description: 了解如何在 Azure Cosmos DB 中预配容器级别的吞吐量
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/03/2019
ms.author: rimman
ms.openlocfilehash: f195eaa0f5d22160de8c1e9e2f429073de001828
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "67986028"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>在 Azure Cosmos 容器上预配吞吐量

本文介绍如何在 Azure Cosmos DB 中为容器（集合、图形或表）预配吞吐量。 可以为单个容器预配吞吐量，也可以[为数据库预配吞吐量](how-to-provision-database-throughput.md)，并在数据库中的容器之间共享。 可以使用 Azure 门户、Azure CLI 或 Azure Cosmos DB SDK 为容器预配吞吐量。

## <a name="provision-throughput-using-azure-portal"></a>使用 Azure 门户预配吞吐量

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. [创建新的 Azure Cosmos 帐户](create-sql-api-dotnet.md#create-account)，或选择现有的 Azure Cosmos 帐户。

1. 打开“数据资源管理器”窗格，然后选择“新建集合”   。 接下来，请提供以下详细信息：

   * 表明要创建新数据库还是使用现有数据库。
   * 输入容器（或表或图）ID。
   * 输入分区键值（例如 `/userid`）。
   * 输入要预配的吞吐量（例如，1000 RU）
   * 选择“确定”  。

![数据资源管理器的屏幕截图，突出显示“新建集合”](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="provision-throughput-using-azure-cli"></a>使用 Azure CLI 预配吞吐量

```azurecli-interactive
# Create a container with a partition key and provision throughput of 400 RU/s
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 400
```

## <a name="provision-throughput-using-powershell"></a>使用 PowerShell 预配吞吐量

```azurepowershell-interactive
# Create a container with a partition key and provision throughput of 400 RU/s
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
    "options"=@{ "Throughput"= 400 }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

若要在 Azure Cosmos DB 帐户（使用用于 MongoDB 的 Azure Cosmos DB API）为容器预配吞吐量，请使用 `/myShardKey` 作为分区键路径。 若要使用 Cassandra API 在 Azure Cosmos DB 帐户中为容器预配吞吐量，请使用 `/myPrimaryKey` 作为分区键路径。

## <a name="provision-throughput-by-using-net-sdk"></a>使用 .NET SDK 预配吞吐量

> [!Note]
> 使用适用于 SQL API 的 Cosmos SDK 为除 Cassandra API 之外的所有 Cosmos DB API 预配吞吐量。

### <a id="dotnet-most"></a>SQL、MongoDB、Gremlin 和表 API
### <a name="net-v2-sdk"></a>.Net V2 SDK

```csharp
// Create a container with a partition key and provision throughput of 400 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 400 });
```

### <a name="net-v3-sdk"></a>.Net V3 SDK
[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

### <a id="dotnet-cassandra"></a>Cassandra API

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400);
```

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解如何在 Azure Cosmos DB 中预配吞吐量：

* [如何预配数据库吞吐量](how-to-provision-database-throughput.md)
* [Azure Cosmos DB 中的请求单位和吞吐量](request-units.md)
