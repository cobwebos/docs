---
title: 在 Azure Cosmos DB 中创建容器
description: 了解如何在 Azure Cosmos DB 中创建容器
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 8ce890500c31c1966254e5bca9d23c8fcdd7bb67
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258286"
---
# <a name="create-an-azure-cosmos-container"></a>创建 Azure Cosmos 容器

本文介绍如何通过不同方式来创建容器（集合、表或图形）。 用户可以使用 Azure 门户、Azure CLI 或支持的 SDK。 本文演示如何创建容器、指定分区键和预配吞吐量。

## <a name="create-a-container-by-using-azure-portal"></a>使用 Azure 门户创建容器

### <a id="portal-sql"></a>SQL API

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. [创建新的 Azure Cosmos DB 帐户](create-sql-api-dotnet.md#create-account)或选择现有的帐户。

1. 打开“数据资源管理器”窗格，然后选择“新建集合”。 接下来，请提供以下详细信息：

   * 表明要创建新数据库还是使用现有数据库。
   * 输入集合 ID。
   * 输入分区键。
   * 输入吞吐量（例如 1000 RU）。
   * 选择“确定”。

![“数据资源管理器”窗格的屏幕截图，突出显示“新建集合”](./media/how-to-create-container/partitioned-collection-create-sql.png)

### <a id="portal-mongodb"></a>用于 MongoDB 的 Azure Cosmos DB API

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. [创建新的 Azure Cosmos DB 帐户](create-mongodb-dotnet.md#create-a-database-account)或选择现有的帐户。

1. 打开“数据资源管理器”窗格，然后选择“新建集合”。 接下来，请提供以下详细信息：

   * 表明要创建新数据库还是使用现有数据库。
   * 输入集合 ID。
   * 对于“无限制”存储容量。
   * 输入分片键。
   * 输入吞吐量（例如 1000 RU）。
   * 选择“确定”。

![用于 MongoDB 的 Azure Cosmos DB API 的屏幕截图，突出显示“添加集合”对话框](./media/how-to-create-container/partitioned-collection-create-mongodb.png)

### <a id="portal-cassandra"></a>Cassandra API

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. [创建新的 Azure Cosmos DB 帐户](create-cassandra-dotnet.md#create-a-database-account)或选择现有的帐户。

1. 打开“数据资源管理器”窗格，然后选择“新建表”。 接下来，请提供以下详细信息：

   * 表明要创建新密钥空间还是使用现有密钥空间。
   * 输入表名称。
   * 输入属性并指定一个主键。
   * 输入吞吐量（例如 1000 RU）。
   * 选择“确定”。

![Cassandra API 的屏幕截图，突出显示“添加表”对话框](./media/how-to-create-container/partitioned-collection-create-cassandra.png)

> [!NOTE]
> Cassandra API 的主键用作分区键。

### <a id="portal-gremlin"></a>Gremlin API

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. [创建新的 Azure Cosmos DB 帐户](create-graph-dotnet.md#create-a-database-account)或选择现有的帐户。

1. 打开“数据资源管理器”窗格，然后选择“新建图形”。 接下来，请提供以下详细信息：

   * 表明要创建新数据库还是使用现有数据库。
   * 输入图形 ID。
   * 对于“无限制”存储容量。
   * 输入顶点的分区键。
   * 输入吞吐量（例如 1000 RU）。
   * 选择“确定”。

![Gremlin API 的屏幕截图，突出显示“添加图形”对话框](./media/how-to-create-container/partitioned-collection-create-gremlin.png)

### <a id="portal-table"></a>表 API

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. [创建新的 Azure Cosmos DB 帐户](create-table-dotnet.md#create-a-database-account)或选择现有的帐户。

1. 打开“数据资源管理器”窗格，然后选择“新建表”。 接下来，请提供以下详细信息：

   * 输入表 ID。
   * 对于“无限制”存储容量。
   * 输入吞吐量（例如 1000 RU）。
   * 选择“确定”。

![表 API 的屏幕截图，突出显示“添加表”对话框](./media/how-to-create-container/partitioned-collection-create-table.png)

> [!Note]
> 就表 API 来说，每次添加新行时，都会指定分区键。

## <a name="create-a-container-by-using-azure-cli"></a>使用 Azure CLI 创建容器

### <a id="cli-sql"></a>SQL API

```azurecli-interactive
# Create a container with a partition key and provision 1000 RU/s throughput.

az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $containerName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

### <a id="cli-mongodb"></a>用于 MongoDB 的 Azure Cosmos DB API

```azurecli-interactive
# Create a collection with a shard key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $collectionName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myShardKey \
    --throughput 1000
```

### <a id="cli-cassandra"></a>Cassandra API

```azurecli-interactive
# Create a table with a partition/primary key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $tableName \
    --name $accountName \
    --db-name $keyspaceName \
    --partition-key-path /myPrimaryKey \
    --throughput 1000
```

### <a id="cli-gremlin"></a>Gremlin API

```azurecli-interactive
# Create a graph with a partition key and provision 1000 RU/s throughput.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $graphName \
    --name $accountName \
    --db-name $databaseName \
    --partition-key-path /myPartitionKey \
    --throughput 1000
```

### <a id="cli-table"></a>表 API

```azurecli-interactive
# Create a table with 1000 RU/s
# Note: you don't need to specify partition key in the following command because the partition key is set on each row.
az cosmosdb collection create \
    --resource-group $resourceGroupName \
    --collection-name $tableName \
    --name $accountName \
    --db-name $databaseName \
    --throughput 1000
```

## <a name="create-a-container-by-using-net-sdk"></a>使用 .NET SDK 创建容器

### <a id="dotnet-sql-graph"></a>SQL API 和 Gremlin API

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

### <a id="dotnet-mongodb"></a>用于 MongoDB 的 Azure Cosmos DB API

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> MongoDB 网络协议不需请求单位。 若要创建新的包含吞吐量的集合，请使用 Azure 门户或 SQL API。

### <a id="dotnet-cassandra"></a>Cassandra API

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>后续步骤

- [Azure Cosmos DB 中的分区](partitioning-overview.md)
