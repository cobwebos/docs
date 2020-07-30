---
title: 在 Azure Cosmos DB 中创建容器
description: 了解如何使用 Azure 门户、.NET、Java、Python、Node.js 和其他 Sdk 在 Azure Cosmos DB 中创建容器。
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/29/2020
ms.author: mjbrown
ms.openlocfilehash: 761789b36e85b16f0b4482cef12645e673c73be7
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2020
ms.locfileid: "87429702"
---
# <a name="create-an-azure-cosmos-container"></a>创建 Azure Cosmos 容器

本文介绍使用 Azure 门户、Azure CLI、PowerShell 或受支持的 Sdk 创建 Azure Cosmos 容器（集合、表或图形）的不同方式。 本文演示如何创建容器、指定分区键和预配吞吐量。

> [!NOTE]
> 创建容器时，请确保不创建名称相同但大小写不同的两个容器。 这是因为 Azure 平台的某些部分不区分大小写，这可能会对具有此类名称的容器导致遥测和操作混乱/冲突。

## <a name="create-a-container-using-azure-portal"></a>使用 Azure 门户创建容器

### <a name="sql-api"></a><a id="portal-sql"></a>SQL API

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. [创建新的 Azure Cosmos 帐户](create-sql-api-dotnet.md#create-account)或选择现有的帐户。

1. 打开“数据资源管理器”窗格，然后选择“新建容器” 。 接下来，请提供以下详细信息：

   * 表明要创建新数据库还是使用现有数据库。
   * 输入容器 ID。
   * 输入分区键。
   * 输入要进行预配的吞吐量（例如，1000 RU）。
   * 选择“确定” 。

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-sql.png" alt-text="“数据资源管理器”窗格的屏幕截图，其中突出显示了“新建容器”":::

### <a name="azure-cosmos-db-api-for-mongodb"></a><a id="portal-mongodb"></a>用于 MongoDB 的 Azure Cosmos DB API

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. [创建新的 Azure Cosmos 帐户](create-mongodb-dotnet.md#create-a-database-account)或选择现有的帐户。

1. 打开“数据资源管理器”窗格，然后选择“新建容器” 。 接下来，请提供以下详细信息：

   * 表明要创建新数据库还是使用现有数据库。
   * 输入容器 ID。
   * 输入分片键。
   * 输入要进行预配的吞吐量（例如，1000 RU）。
   * 选择“确定” 。

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-mongodb.png" alt-text="Azure Cosmos DB API for MongoDB“添加容器”对话框的屏幕截图":::

### <a name="cassandra-api"></a><a id="portal-cassandra"></a>Cassandra API

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. [创建新的 Azure Cosmos 帐户](create-cassandra-dotnet.md#create-a-database-account)或选择现有的帐户。

1. 打开“数据资源管理器”窗格，然后选择“新建表” 。 接下来，请提供以下详细信息：

   * 表明要创建新密钥空间还是使用现有密钥空间。
   * 输入表名称。
   * 输入属性并指定一个主键。
   * 输入要进行预配的吞吐量（例如，1000 RU）。
   * 选择“确定” 。

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-cassandra.png" alt-text="Cassandra API 的屏幕截图，突出显示“添加表”对话框":::

> [!NOTE]
> Cassandra API 的主键用作分区键。

### <a name="gremlin-api"></a><a id="portal-gremlin"></a>Gremlin API

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. [创建新的 Azure Cosmos 帐户](create-graph-dotnet.md#create-a-database-account)或选择现有的帐户。

1. 打开“数据资源管理器”窗格，然后选择“新建图形” 。 接下来，请提供以下详细信息：

   * 表明要创建新数据库还是使用现有数据库。
   * 输入图形 ID。
   * 对于“无限制”存储容量。
   * 输入顶点的分区键。
   * 输入要进行预配的吞吐量（例如，1000 RU）。
   * 选择“确定” 。

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-gremlin.png" alt-text="Gremlin API 的屏幕截图，突出显示“添加图形”对话框":::

### <a name="table-api"></a><a id="portal-table"></a>表 API

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. [创建新的 Azure Cosmos 帐户](create-table-dotnet.md#create-a-database-account)或选择现有的帐户。

1. 打开“数据资源管理器”窗格，然后选择“新建表” 。 接下来，请提供以下详细信息：

   * 输入表 ID。
   * 输入要进行预配的吞吐量（例如，1000 RU）。
   * 选择“确定” 。

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-table.png" alt-text="表 API 的屏幕截图，突出显示“添加表”对话框":::

> [!Note]
> 就表 API 来说，每次添加新行时，都会指定分区键。

## <a name="create-a-container-using-azure-cli"></a>使用 Azure CLI 创建容器<a id="cli-sql"></a><a id="cli-mongodb"></a><a id="cli-cassandra"></a><a id="cli-gremlin"></a><a id="cli-table"></a>

下面的链接说明如何使用 Azure CLI 为 Azure Cosmos DB 创建容器资源。

有关所有 Azure Cosmos DB Api 的所有 Azure CLI 示例的列表，请参阅[Azure Cosmos DB Azure CLI 示例](cli-samples.md)。

* [使用 Azure CLI 创建容器](manage-with-cli.md#create-a-container)
* [使用 Azure CLI 为 Azure Cosmos DB for MongoDB API 创建集合](./scripts/cli/mongodb/create.md)
* [使用 Azure CLI 创建 Cassandra 表](./scripts/cli/cassandra/create.md)
* [使用 Azure CLI 创建 Gremlin 图](./scripts/cli/gremlin/create.md)
* [使用 Azure CLI 创建表 API 表](./scripts/cli/table/create.md)

## <a name="create-a-container-using-powershella-idps-mongodba-idps-gremlin"></a>使用 PowerShell 创建容器<a id="ps-sql"></a><a id="ps-mongodb"><a id="ps-cassandra"></a><a id="ps-gremlin"><a id="ps-table"></a>

下面的链接说明如何使用 PowerShell 为 Azure Cosmos DB 创建容器资源。

有关所有 Azure Cosmos DB Api 中所有 PowerShell 示例的列表，请参阅、 [SQL API](powershell-samples-sql.md)、 [CASSANDRA API](powershell-samples-cassandra.md)、 [MongoDB api](powershell-samples-mongodb.md)、 [Gremlin api](powershell-samples-gremlin.md)和[表 API](powershell-samples-table.md)

* [使用 PowerShell 创建容器](manage-with-powershell.md#create-container)
* [使用 PowerShell 为 MongoDB API Azure Cosmos DB 创建集合](./scripts/powershell/mongodb/ps-mongodb-create.md)
* [使用 PowerShell 创建 Cassandra 表](./scripts/powershell/cassandra/ps-cassandra-create.md)
* [使用 PowerShell 创建 Gremlin 图](./scripts/powershell/gremlin/ps-gremlin-create.md)
* [使用 PowerShell 创建表 API 表](./scripts/powershell/table/ps-table-create.md)

## <a name="create-a-container-using-net-sdk"></a>使用 .NET SDK 创建容器

如果在创建集合时遇到超时异常，请执行读取操作来验证是否已成功创建集合。 在成功完成集合创建操作之前，读取操作将引发异常。 有关创建操作支持的状态代码的列表，请参阅 Azure Cosmos DB 文章的[HTTP 状态代码](/rest/api/cosmos-db/http-status-codes-for-cosmosdb)。

### <a name="sql-api-and-gremlin-api"></a><a id="dotnet-sql-graph"></a>SQL API 和 Gremlin API

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

### <a name="azure-cosmos-db-api-for-mongodb"></a><a id="dotnet-mongodb"></a>用于 MongoDB 的 Azure Cosmos DB API

```csharp
// Create a collection with a partition key by using Mongo Shell:
db.runCommand( { shardCollection: "myDatabase.myCollection", key: { myShardKey: "hashed" } } )
```

> [!Note]
> MongoDB 网络协议不能理解[请求单位](request-units.md)的概念。 要创建一个新集合，并在其上提供吞吐量，请使用 Azure 门户或用于 SQL API 的 Cosmos DB SDK。

### <a name="cassandra-api"></a><a id="dotnet-cassandra"></a>Cassandra API

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

## <a name="next-steps"></a>后续步骤

* [Azure Cosmos DB 中的分区](partitioning-overview.md)
* [Azure Cosmos DB 中的请求单位](request-units.md)
* [在容器和数据库上预配吞吐量](set-throughput.md)
* [使用 Azure Cosmos 帐户](account-overview.md)
