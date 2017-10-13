---
title: "Azure Cosmos DB：在 .NET 中使用 DocumentDB API 进行开发 | Microsoft Docs"
description: "了解如何通过 .NET 使用 Azure Cosmos DB 的 DocumentDB API 进行开发"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/10/2017
ms.author: mimig
ms.custom: mvc
ms.openlocfilehash: 871731adc849c575d97ed6735b87457811668ed5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmosdb-develop-with-the-documentdb-api-in-net"></a>Azure Cosmos DB：在 .NET 中使用 DocumentDB API 进行开发

Azure Cosmos DB 由 Microsoft 提供，是全球分布的多模型数据库服务。 可快速创建和查询文档、键/值，及图形数据库，它们均受益于 Azure Cosmos DB 最核心的全球分布和水平缩放功能。 

本教程演示如何使用 Azure 门户创建 Azure Cosmos DB 帐户，并使用 [DocumentDB.NET API](documentdb-introduction.md) 创建具有[分区键](documentdb-partition-data.md#partition-keys)的文档数据库和集合。 通过在创建集合时定义分区键，应用程序能够随数据增加轻松进行扩展。 

本教程介绍如何使用 [DocumentDB.NET API](documentdb-sdk-dotnet.md) 完成以下任务：

> [!div class="checklist"]
> * 创建 Azure Cosmos DB 帐户
> * 创建具有分区键的数据库和集合
> * 创建 JSON 文档
> * 更新文档
> * 查询已分区集合
> * 运行存储过程
> * 删除文档
> * 删除数据库

## <a name="prerequisites"></a>先决条件
请确保具有以下内容：

* 有效的 Azure 帐户。 如果没有，可以注册 [免费帐户](https://azure.microsoft.com/free/)。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* 如果尚未安装 Visual Studio 2017，可以下载并使用**免费的** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)。 在安装 Visual Studio 的过程中，请确保启用“Azure 开发”。

## <a name="create-an-azure-cosmos-db-account"></a>创建 Azure Cosmos DB 帐户

现在首先在 Azure 门户中创建 Azure Cosmos DB 帐户。

> [!TIP]
> * 已有一个 Azure Cosmos DB 帐户？ 如果有，请跳到[设置 Visual Studio 解决方案](#SetupVS)
> * 是否具有 Azure DocumentDB 帐户？ 如果有，则该帐户现为 Azure Cosmos DB 帐户，可以直接跳到[设置 Visual Studio 解决方案](#SetupVS)。  
> * 如果使用 Azure Cosmos DB Emulator，请遵循 [Azure Cosmos DB Emulator](local-emulator.md) 中的步骤设置该模拟器，并直接跳到[设置 Visual Studio 解决方案](#SetupVS)。 
>
>

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>设置 Visual Studio 解决方案
1. 在计算机上打开 Visual Studio。
2. 在“文件”菜单中，选择“新建”，并选择“项目”。
3. 在“新建项目”对话框中，选择“模板” / “Visual C#” / “控制台应用(.NET Framework)”，为项目命名，并单击“确定”。
   ![“新建项目”窗口屏幕截图](./media/tutorial-develop-documentdb-dotnet/nosql-tutorial-new-project-2.png)

4. 在“解决方案资源管理器”中，右键单击 Visual Studio 解决方案下方的新控制台应用程序，并单击“管理 NuGet 包...”
    
    ![“项目”右键菜单屏幕截图](./media/tutorial-develop-documentdb-dotnet/nosql-tutorial-manage-nuget-pacakges.png)
5. 在“NuGet”选项卡上，单击“浏览”，并在搜索框中输入“documentdb”。
<!---stopped here--->
6. 在结果中找到 **Microsoft.Azure.DocumentDB**，并单击“安装”。
   Azure Cosmos DB 客户端库的程序包 ID 是 [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB)。
   ![用于查找 Azure Cosmos DB 客户端 SDK 的 NuGet 菜单的屏幕截图](./media/tutorial-develop-documentdb-dotnet/nosql-tutorial-manage-nuget-pacakges-2.png)

    如果获得有关查看解决方案更改的消息，请单击“确定”。 如果获得有关接受许可证的消息，请单击“我接受”。

## <a id="Connect"></a>添加对项目的引用
本教程中的剩余步骤提供在项目中创建和更新 Azure Cosmos DB 资源所需的 DocumentDB API 代码段。

首先，向应用程序添加这些引用。
<!---These aren't added by default when you install the pkg?--->

```csharp
using System.Net;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Newtonsoft.Json;
```

## <a id="add-references"></a>连接应用

下一步，在应用程序中添加这两个常量和客户端变量。

```csharp
private const string EndpointUrl = "<your endpoint URL>";
private const string PrimaryKey = "<your primary key>";
private DocumentClient client;
```

接下来，返回到 [Azure 门户](https://portal.azure.com)检索终结点 URL 和主密钥。 终结点 URL 和主密钥是必需的，可让应用程序知道要连接的对象，使 Azure Cosmos DB 信任应用程序的连接。

在 Azure 门户中，导航到 Azure Cosmos DB 帐户，单击“密钥”，并单击“读写密钥”。

从门户中复制该 URI 并将它粘贴到 program.cs 文件中的 `<your endpoint URL>`。 然后从门户中复制“主密钥”并将它粘贴到 `<your primary key>`。 请务必删除值中的 `<` 和 `>`。

![NoSQL 教程用于创建 C# 控制台应用程序的 Azure 门户的屏幕截图。 显示一个 Azure Cosmos DB 帐户，其中在 Azure Cosmos DB 帐户边栏选项卡中突出显示了“密钥”，以及在“密钥”边栏选项卡上突出显示了 URI 和 PRIMARY KEY 值](./media/tutorial-develop-documentdb-dotnet/nosql-tutorial-keys.png)

## <a id="instantiate"></a>实例化 DocumentClient

现在创建 DocumentClient 的新实例。

```csharp
DocumentClient client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
```

## <a id="create-database"></a>创建数据库

接下来，从 [DocumentDB .NET SDK](documentdb-sdk-dotnet.md) 使用 DocumentClient 类的 [CreateDatabaseAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) 方法或 [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) 方法创建一个 Azure Cosmos DB [数据库](documentdb-resources.md#databases)。 数据库是跨集合分区的 JSON 文档存储的逻辑容器。

```csharp
await client.CreateDatabaseAsync(new Database { Id = "db" });
```
## <a name="decide-on-a-partition-key"></a>确定分区键 

集合是存储文档的容器。 集合是逻辑资源，并且可以[跨一个或多个物理分区](partition-data.md)。 [分区键](documentdb-partition-data.md)是文档内用于在服务器或分区间分发数据的属性（或路径）。 具有相同分区键的所有文档都存储在同一分区中。 

确定分区键非常重要，需在创建集合前进行。 分区键是文档内可供 Azure Cosmos DB 用于在多个服务器或分区间分发数据的属性（或路径）。 Cosmos DB 对分区键值进行哈希处理，并使用经过哈希处理的结果来确定会在其中存储文档的分区。 具有相同分区键的所有文档都存储在同一分区中，创建集合后将无法更改分区键。 

本教程会分区键设置为 `/deviceId`，以便单个设备的所有数据都存储在单个分区中。 请选择具有大量值的分区键，以相同频率使用每个值，以确保 Cosmos DB 能在数据增加时保持负载均衡并达到集合的全部吞吐量。 

有关分区的详细信息，请参阅[如何在 Azure Cosmos DB 中进行分区和缩放？](partition-data.md) 

## <a id="CreateColl"></a>创建集合 

了解完分区键 `/deviceId` 后，我们使用 **DocumentClient** 的 [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) 方法或 [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) 方法创建[集合](documentdb-resources.md#collections)。 集合是 JSON 文档和任何相关联的 JavaScript 应用程序逻辑的容器。 

> [!WARNING]
> 创建集合会影响定价，因为要保留应用程序的吞吐量才能与 Azure Cosmos DB 进行通信。 有关详细信息，请访问[定价页](https://azure.microsoft.com/pricing/details/cosmos-db/)
> 
> 

```csharp
// Collection for device telemetry. Here the JSON property deviceId is used  
// as the partition key to spread across partitions. Configured for 2500 RU/s  
// throughput and an indexing policy that supports sorting against any  
// number or string property. .
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 2500 });
```

此方法可对 Azure Cosmos DB 进行 REST API 调用，且该服务基于所请求的吞吐量设置分区数。 根据性能需求的发展，可以使用 SDK 或 [Azure 门户](set-throughput.md)更改集合的吞吐量。

## <a id="CreateDoc"></a>创建 JSON 文档
现在 Azure Cosmos DB 中插入一些 JSON 文档。 可以通过使用 **DocumentClient** 类的 [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) 方法创建[文档](documentdb-resources.md#documents)。 文档是用户定义的（任意）JSON 内容。 此示例类包含设备读取和对 CreateDocumentAsync 的调用，可将新设备读数插入到集合。

```csharp
public class DeviceReading
{
    [JsonProperty("id")]
    public string Id;

    [JsonProperty("deviceId")]
    public string DeviceId;

    [JsonConverter(typeof(IsoDateTimeConverter))]
    [JsonProperty("readingTime")]
    public DateTime ReadingTime;

    [JsonProperty("metricType")]
    public string MetricType;

    [JsonProperty("unit")]
    public string Unit;

    [JsonProperty("metricValue")]
    public double MetricValue;
  }

// Create a document. Here the partition key is extracted 
// as "XMS-0001" based on the collection definition
await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "coll"),
    new DeviceReading
    {
        Id = "XMS-001-FE24C",
        DeviceId = "XMS-0001",
        MetricType = "Temperature",
        MetricValue = 105.00,
        Unit = "Fahrenheit",
        ReadingTime = DateTime.UtcNow
    });
```
## <a name="read-data"></a>读取数据

现使用 ReadDocumentAsync 方法按分区键和 ID 来读取文档。 请注意，读取包括 PartitionKey 值（对应 REST API 中的 `x-ms-documentdb-partitionkey` 请求标头）。

```csharp
// Read document. Needs the partition key and the Id to be specified
Document result = await client.ReadDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

DeviceReading reading = (DeviceReading)(dynamic)result;
```

## <a name="update-data"></a>更新数据

现使用 ReplaceDocumentAsync 方法更新某些数据。

```csharp
// Update the document. Partition key is not required, again extracted from the document
reading.MetricValue = 104;
reading.ReadingTime = DateTime.UtcNow;

await client.ReplaceDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  reading);
```

## <a name="delete-data"></a>删除数据

现在使用 DeleteDocumentAsync 方法按分区键和 ID 来删除文档。

```csharp
// Delete a document. The partition key is required.
await client.DeleteDocumentAsync(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```
## <a name="query-partitioned-collections"></a>查询已分区集合

当在已分区集合中查询数据时，Azure Cosmos DB 会自动将查询路由到筛选器（如果有）中所指定分区键值对应的分区。 例如，此查询将只路由到包含分区键“XMS-0001”的分区。

```csharp
// Query using partition key
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```
    
下面的查询在分区键 (DeviceId) 上没有筛选器，并且以扇形展开到针对分区索引执行该查询的所有分区。 请注意，必须指定 EnableCrossPartitionQuery（REST API 中的 `x-ms-documentdb-query-enablecrosspartition`）以使 SDK 跨分区执行查询。

```csharp
// Query across partition keys
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

## <a name="parallel-query-execution"></a>并行查询执行
Azure Cosmos DB DocumentDB SDK 1.9.0 及更高版本支持并行查询执行选项，这些选项可用于对已分区集合执行低延迟查询，即使在这些查询需要处理大量分区时，也是如此。 例如，以下查询配置为跨分区并行运行。

```csharp
// Cross-partition Order By queries
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("db", "coll"), 
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```
    
可以通过调整以下参数来管理并行查询执行：

* 通过设置 `MaxDegreeOfParallelism`，可以控制并行度，即，与集合的分区同时进行的网络连接的最大数量。 如果将此参数设置为 -1，则由 SDK 管理并行度。 如果 `MaxDegreeOfParallelism` 未指定或设置为 0（默认值），则与集合的分区的网络连接将有一个。
* 通过设置 `MaxBufferedItemCount`，可以权衡查询延迟和客户端内存使用率。 如果省略此参数或将此参数设置为 -1，则由 SDK 管理并行查询执行过程中缓冲的项目数。

如果给定相同状态的集合，并行查询以串行执行相同的顺序返回结果。 执行包含排序（ORDER BY 和/或 TOP）的跨分区查询时，DocumentDB SDK 跨分区发出并行查询，并合并客户端中的部分排序结果，以生成全局范围内有序的结果。

## <a name="execute-stored-procedures"></a>执行存储过程
最后，通过将以下代码添加到项目中，可以对具有相同设备 ID 的文档执行原子事务，例如，如果要在单个文档中维护聚合或设备的最新状态。

```csharp
await client.ExecuteStoredProcedureAsync<DeviceReading>(
    UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
    new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
    "XMS-001-FE24C");
```

这就是所有的操作！ 这些是 Azure Cosmos DB 应用程序的主要组件，该程序使用分区键跨分区高效缩放数据分布。  

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用，请删除本教程在 Azure 门户中创建的所有资源，步骤如下：

1. 在 Azure 门户的左侧菜单中，单击“资源组”，并单击创建的资源的唯一名称。 
2. 在资源组页上单击“删除”，在文本框中键入要删除的资源的名称，并单击“删除”。

## <a name="next-steps"></a>后续步骤

在本教程中，已完成以下内容： 

> [!div class="checklist"]
> * 创建 Azure Cosmos DB 帐户
> * 创建具有分区键的数据库和集合
> * 创建 JSON 文档
> * 更新文档
> * 查询已分区集合
> * 运行存储过程
> * 删除文档
> * 删除数据库

现在可进入下一教程，将其他数据导入 Cosmos DB 帐户。 

> [!div class="nextstepaction"]
> [将数据导入 Azure Cosmos DB](import-data.md)
