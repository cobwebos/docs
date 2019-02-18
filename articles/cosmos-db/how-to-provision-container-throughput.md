---
title: 在 Azure Cosmos DB 中预配容器吞吐量
description: 了解如何在 Azure Cosmos DB 中预配容器级别的吞吐量
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 4df8a12581b5d71a76964ca1e3d40c6c53185f67
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55860314"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>在 Azure Cosmos 容器上预配吞吐量

本文介绍如何在 Azure Cosmos DB 中为容器（集合、图形或表）预配吞吐量。 可以为单个容器预配吞吐量，也可以[为数据库预配](how-to-provision-database-throughput.md)吞吐量，并在数据库中的容器之间共享。 可以使用 Azure 门户、Azure CLI 或 Azure CosmosDB SDK 为容器预配吞吐量。

## <a name="provision-throughput-by-using-azure-portal"></a>使用 Azure 门户预配吞吐量

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. [创建新的 Azure Cosmos DB 帐户](create-sql-api-dotnet.md#create-a-database-account)或选择现有的帐户。

1. 打开“数据资源管理器”窗格，然后选择“新建集合”。 接下来，请提供以下详细信息：

   * 表明要创建新数据库还是使用现有数据库。
   * 输入集合 ID（或者表或图形）。
   * 输入分区键值（例如 `/userid`）。
   * 输入吞吐量（例如 1000 RU）。
   * 选择“确定”。

![数据资源管理器的屏幕截图，突出显示“新建集合”](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="provision-throughput-by-using-azure-cli"></a>使用 Azure CLI 预配吞吐量

```azurecli-interactive
# Create a container with a partition key and provision throughput of 1000 RU/s
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

若要为 Azure Cosmos DB 帐户（使用用于 MongoDB 的 Azure Cosmos DB API 配置）预配吞吐量，请使用 `/myShardKey` 作为分区键路径。 若要为针对 Cassandra API 配置的 Azure Cosmos DB 帐户预配吞吐量，请使用 `/myPrimaryKey` 作为分区键路径。

## <a name="provision-throughput-by-using-net-sdk"></a>使用 .NET SDK 预配吞吐量

> [!Note]
> 使用 SQL API 为除 Cassandra API 之外的所有 API 预配吞吐量。

### <a id="dotnet-most"></a>SQL、MongoDB、Gremlin 和表 API

```csharp
// Create a container with a partition key and provision throughput of 1000 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a id="dotnet-cassandra"></a>Cassandra API

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 1000 RU/s
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解如何在 Azure Cosmos DB 中预配吞吐量：

* [如何为数据库预配吞吐量](how-to-provision-database-throughput.md)
* [Azure Cosmos DB 中的请求单位和吞吐量](request-units.md)
