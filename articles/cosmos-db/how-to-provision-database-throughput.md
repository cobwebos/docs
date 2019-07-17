---
title: 在 Azure Cosmos DB 中预配数据库吞吐量
description: 了解如何在 Azure Cosmos DB 中预配数据库级别的吞吐量
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/03/2019
ms.author: rimman
ms.openlocfilehash: de39581f832c30c64a69797805df7e13ce47b439
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565865"
---
# <a name="provision-throughput-on-a-database-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中的数据库上预配吞吐量

本文介绍如何在 Azure Cosmos DB 中的数据库上预配吞吐量。 可以为单个[容器](how-to-provision-container-throughput.md)预配吞吐量，也可以为数据库预配吞吐量，并在数据库中的容器之间共享吞吐量。 若要了解何时使用容器级别和数据库级别吞吐量，请参阅[容器和数据库预配吞吐量的用例](set-throughput.md)一文。 可以使用 Azure 门户或 Azure Cosmos DB SDK 来预配数据库级别吞吐量。

## <a name="provision-throughput-using-azure-portal"></a>使用 Azure 门户预配吞吐量

### <a id="portal-sql"></a>SQL（核心）API

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. [创建新的 Azure Cosmos 帐户](create-sql-api-dotnet.md#create-account)，或选择现有的 Azure Cosmos 帐户。

1. 打开“数据资源管理器”窗格，然后选择“新建数据库”   。 提供以下详细信息：

   * 输入数据库 ID。 
   * 选择“预配吞吐量”。 
   * 输入吞吐量（例如 1000 RU）。
   * 选择“确定”  。

![“新建数据库”对话框屏幕截图](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-powershell"></a>使用 PowerShell 预配吞吐量

```azurepowershell-interactive
# Create a database and provision throughput of 400 RU/s
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$databaseResourceName = $accountName + "/sql/" + $databaseName

$databaseProperties = @{
    "resource"=@{ "id"=$databaseName };
    "options"=@{ "Throughput"= 400 }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $databaseResourceName -PropertyObject $databaseProperties
```

## <a name="provision-throughput-using-net-sdk"></a>使用 .NET SDK 预配吞吐量

> [!Note]
> 使用适用于 SQL API 的 Cosmos SDK 为所有 API 预配吞吐量。 也可以选择将以下示例用于 Cassandra API。

### <a id="dotnet-all"></a>所有 API

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 500
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

### <a id="dotnet-cassandra"></a>Cassandra API

```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute(CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400);
```

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解在 Azure Cosmos DB 中预配的吞吐量：

* [全局缩放预配的吞吐量](scaling-throughput.md)
* [在容器和数据库上预配吞吐量](set-throughput.md)
* [如何为容器预配吞吐量](how-to-provision-container-throughput.md)
* [Azure Cosmos DB 中的请求单位和吞吐量](request-units.md)