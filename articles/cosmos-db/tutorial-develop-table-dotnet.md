---
title: Azure Cosmos DB：在 .NET 中使用表 API 进行开发 | Microsoft Docs
description: 了解如何通过 .NET 使用 Azure Cosmos DB 的表 API 进行开发
services: cosmos-db
author: SnehaGunda
manager: kfile
editor: ''
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/18/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: e6511b9511d2598b58fd3afee34803ceb09ac5ce
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38582846"
---
# <a name="azure-cosmos-db-develop-with-the-table-api-in-net"></a>Azure Cosmos DB：在 .NET 中使用表 API 进行开发

Azure Cosmos DB 是 Microsoft 提供的全球分布式多模型数据库服务。 可快速创建和查询文档、键/值和图形数据库，所有这些都受益于 Azure Cosmos DB 核心的全球分布和水平缩放功能。

本教程涵盖以下任务： 

> [!div class="checklist"] 
> * 创建 Azure Cosmos DB 帐户 
> * 启用 app.config 文件中的功能 
> * 使用[表 API](table-introduction.md) 创建表
> * 将实体添加到表 
> * 插入一批实体 
> * 检索单个实体 
> * 使用自动辅助索引查询实体 
> * 替换实体 
> * 删除实体 
> * 删除表
 
## <a name="tables-in-azure-cosmos-db"></a>Azure Cosmos DB 中的表 

Azure Cosmos DB 为有某类需求的应用程序提供[表 API](table-introduction.md)，这些应用程序需要采用无架构设计的键-值存储。 Azure Cosmos DB 表 API 和 [Azure 表存储](../storage/common/storage-introduction.md)现在都支持相同的 SDK 和 REST API。 可以使用 Azure Cosmos DB 创建具有高吞吐量要求的表。

本教程供熟悉 Azure 表存储 SDK 并想要通过 Azure Cosmos DB 使用高级功能的开发人员使用。 本教程基于[通过 .NET 开始使用 Azure 表存储](table-storage-how-to-use-dotnet.md)，并演示如何利用辅助索引、预配的吞吐量和多宿主等附加功能。 本教程说明如何使用 Azure 门户创建 Azure Cosmos DB 帐户，生成并部署表 API 应用程序。 还将演练用于创建和删除表，以及插入、更新、删除和查询表数据的 .NET 示例。 

如果当前使用 Azure 表存储，可以通过 Azure Cosmos DB 表 API 获得以下好处：

- 具有多宿主的统包[全局分发](distribute-data-globally.md)与[自动和手动故障转移](regional-failover.md)
- 支持针对所有属性的自动架构不可知索引（“辅助索引”）和快速查询 
- 支持跨任意数量的区域实现[存储和吞吐量的独立缩放](partition-data.md)
- 支持[按表的专用吞吐量](request-units.md)，可以从每秒数百个请求扩展到每秒数百万个请求
- 支持[五个可调整一致性级别](consistency-levels.md)，可基于应用程序需要权衡可用性、延迟和一致性
- 单个区域内可用性达 99.99%，可添加更多区域以提高可用性，以及获得可通用的[行业领先的综合 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- 使用现有的 Azure 存储 .NET SDK，而不会更改应用程序的任何代码

本教程介绍使用 .NET SDK 的 Azure Cosmos DB 表 API。 可以从 NuGet 下载 [Azure Cosmos DB 表 API .NET SDK](https://aka.ms/tableapinuget)。

若要详细了解复杂的 Azure 表存储任务，请参阅：

* [Azure Cosmos DB 表 API 简介](table-introduction.md)
* 表服务参考文档提供有关可用 API [Azure Cosmos DB 表 API .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cosmosdb/client?view=azure-dotnet) 的完整详情

### <a name="about-this-tutorial"></a>关于本教程
本教程供熟悉 Azure 表存储 SDK 并想要通过 Azure Cosmos DB 使用高级功能的开发人员使用。 本教程基于[通过 .NET 开始使用 Azure 表存储](table-storage-how-to-use-dotnet.md)，并演示如何利用辅助索引、预配的吞吐量和多宿主等附加功能。 涵盖如何使用 Azure 门户创建 Azure Cosmos DB 帐户，生成并部署表应用程序。 还将演练用于创建和删除表，以及插入、更新、删除和查询表数据的 .NET 示例。 

如果尚未安装 Visual Studio 2017，可以下载并使用免费的 [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)。 在安装 Visual Studio 的过程中，请确保启用“Azure 开发”。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>创建数据库帐户

现在首先在 Azure 门户中创建 Azure Cosmos DB 帐户。  
 
> [!IMPORTANT]  
> 必须新建表 API 帐户，才能使用正式发布的表 API SDK。 正式发布的 SDK 不支持在预览期间创建的表 API 帐户。 
>

[!INCLUDE [cosmosdb-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)] 

## <a name="clone-the-sample-application"></a>克隆示例应用程序

现在让我们从 github 克隆表应用、设置连接字符串，并运行。 会看到以编程方式处理数据是多么容易。 

1. 打开诸如 git bash 之类的 git 终端窗口，并使用 `cd` 命令更改为相应的示例应用程序安装文件夹。 

    ```bash
    cd "C:\git-samples"
    ```

2. 运行下列命令以克隆示例存储库。 此命令在计算机上创建示例应用程序的副本。 

    ```bash
    git clone https://github.com/Azure-Samples/storage-table-dotnet-getting-started.git
    ```

3. 然后在 Visual Studio 中打开解决方案文件。 

## <a name="update-your-connection-string"></a>更新连接字符串

现在返回到 Azure 门户，获取连接字符串信息，并将其复制到应用。 这样，应用程序就可以与托管的数据库进行通信。 

1. 在 [Azure 门户](http://portal.azure.com/)中，单击“连接字符串”。 

    使用屏幕右侧的复制按钮复制“主连接字符串”。

    ![在“连接字符串”窗格中查看并复制“连接字符串”](./media/create-table-dotnet/connection-string.png)

2. 在 Visual Studio 中，打开 app.config 文件。 

3. 取消注释第 8 行的 StorageConnectionString 并注释掉第 7 行的 StorageConnectionString，因为本教程不使用存储模拟器。 第 7 行和第 8 行现在应如下所示：

    ```
    <!--key="StorageConnectionString" value="UseDevelopmentStorage=true;" />-->
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
    ```

4. 将主连接字符串从门户粘贴到第 8 行的 StorageConnectionString 值。 粘贴引号内的字符串。
   
    > [!IMPORTANT]
    > 如果终结点使用 documents.azure.com，表示已有预览帐户。必须[新建表 API 帐户](#create-a-database-account)，才能使用正式版表 API SDK。 
    >

    第 8 行现在应类似于：

    ```
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=txZACN9f...==;TableEndpoint=https://<account name>.table.cosmosdb.azure.com;" />
    ```

5. 保存 app.config 文件。

现已使用与 Azure Cosmos DB 进行通信所需的所有信息更新应用。 

## <a name="azure-cosmos-db-capabilities"></a>Azure Cosmos DB 功能
Azure Cosmos DB 支持大量 Azure 表存储 API 中不可用的功能。 

某些功能通过 CreateCloudTableClient 的新重载进行访问，用户可以使用这些新重载指定连接策略和一致性级别。

| 表连接设置 | 说明 |
| --- | --- |
| 连接模式  | Azure Cosmos DB 支持两种连接模式。 在`Gateway`模式下，将始终向 Azure Cosmos DB 网关发出请求，该网关会将其转发到相应的数据分区。 在`Direct`连接模式下，客户端会提取表到分区的映射，且会直接针对数据分区发出请求。 建议使用`Direct`（默认）。  |
| 连接协议 | Azure Cosmos DB 支持两种连接协议 - `Https` 和 `Tcp`。 默认为 `Tcp`，并推荐使用该协议，因为它更轻质。 |
| 首选位置 | 以逗号分隔的首选（多主页）位置列表，用于读取。 每个 Azure Cosmos DB 帐户可与 1-30+ 个区域关联。 每个客户端实例可按首选顺序指定这些区域的一个子集以实现低延迟的读取。 必须使用这些区域的[显示名称](https://msdn.microsoft.com/library/azure/gg441293.aspx)命名这些区域，例如 `West US`。 另请参阅[多宿主 API](tutorial-global-distribution-table.md)。 |
| 一致性级别 | 通过在后列五个定义完善的一致性级别之间进行选择，可在延迟、一致性和可用性之间权衡：`Strong`、`Session`、`Bounded-Staleness`、`ConsistentPrefix` 和 `Eventual`。 默认为 `Session`。 一致性级别的选择会在多区域设置中产生显著的性能差异。 有关详细信息，请参阅[一致性级别](consistency-levels.md)。 |

可以通过以下 `appSettings` 配置值启用其他功能。

| 密钥 | 说明 |
| --- | --- |
| TableQueryMaxItemCount | 配置单个往返过程中按每个表查询所返回的最大项数。 默认为 `-1`，使 Azure Cosmos DB 在运行时能够动态确定该值。 |
| TableQueryEnableScan | 如果查询无法使用任何筛选器的索引，则无论如何要通过扫描运行它。 默认为 `false`。|
| TableQueryMaxDegreeOfParallelism | 用于执行跨分区查询的并行度。 `0` 是不使用任何预提取的串行，`1` 是使用预提取的串行，更高的值会增加并行度的速率。 默认为 `-1`，使 Azure Cosmos DB 在运行时能够动态确定该值。 |

若要更改默认值，请从 Visual Studio 中的解决方案资源管理器打开 `app.config` 文件。 添加 `<appSettings>` 元素的内容，如下所示。 将 `account-name` 替换为存储帐户名称，将 `account-key` 替换为帐户访问密钥。 

```xml
<configuration>
    ...
    <appSettings>
      <!-- Client options -->
      <add key="CosmosDBStorageConnectionString" 
        value="DefaultEndpointsProtocol=https;AccountName=MYSTORAGEACCOUNT;AccountKey=AUTHKEY;TableEndpoint=https://account-name.table.cosmosdb.azure.com" />
      <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key; TableEndpoint=https://account-name.documents.azure.com" />

      <!-- Table query options -->
      <add key="TableQueryMaxItemCount" value="-1"/>
      <add key="TableQueryEnableScan" value="false"/>
      <add key="TableQueryMaxDegreeOfParallelism" value="-1"/>
      <add key="TableQueryContinuationTokenLimitInKb" value="16"/>
            
    </appSettings>
</configuration>
```

快速查看应用中发生的情况。 打开 `Program.cs` 文件，会发现以下几行代码将创建表资源。 

## <a name="create-the-table-client"></a>创建表客户端
初始化 `CloudTableClient`，以连接到表帐户。

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
```
使用 `TableConnectionMode`、`TableConnectionProtocol`、`TableConsistencyLevel` 和 `TablePreferredLocations` 配置值初始化此客户端（如已在应用设置中指定）。

## <a name="create-a-table"></a>创建表
然后使用 `CloudTable` 创建表。 Azure Cosmos DB 中的表可就存储和吞吐量进行独立缩放，分区由服务自动处理。 Azure Cosmos DB 支持固定大小和数量不限的表。 有关详细信息，请参阅 [Azure Cosmos DB 中的分区](partition-data.md)。 

```csharp
CloudTable table = tableClient.GetTableReference("people");
400
table.CreateIfNotExists(throughput: 800);
```

在表创建方式上存在一个重要差异。 与 Azure 存储的基于消耗的事务模型不同，Azure Cosmos DB 保留吞吐量。 吞吐量是专用/保留的，因此即使请求速率处于配置的吞吐量水平或以下，用户也不会受到限制。

可以通过以 CreateIfNotExists 参数的形式提供它来配置默认吞吐量。

对 1-KB 实体的 1 次读取规范化为 1 RU，并会根据其他操作的 CPU、内存和 IOPS 消耗量将其规范化为固定 RU 值。 详细了解 [Azure Cosmos DB 中的请求单位](request-units.md)，具体说来就是[密钥值存储](key-value-store-cost.md)的请求单位。

接下来，我们将使用 Azure 表存储 SDK 逐步演练简单的读取和写入 (CRUD) 操作。 本教程演示通过 Azure Cosmos DB 实现的可预测的低至个位数的毫秒延迟和快速查询。

## <a name="add-an-entity-to-a-table"></a>将实体添加到表
Azure 表存储中的实体从 `TableEntity` 类扩展，且必须具有 `PartitionKey` 和 `RowKey` 属性。 以下是客户实体定义的示例。

```csharp
public class CustomerEntity : TableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

以下代码片段演示如何使用 Azure 存储 SDK 插入实体。 Azure Cosmos DB 的设计旨在保证针对世界范围内的任何规模实现低延迟。

对于在与 Azure Cosmos DB 帐户相同的区域中运行的应用程序，写入完成延迟为 <15 毫秒（p99）和约 6 毫秒（p50）。 这一持续时间造成后列事实：仅当写入被同步复制、持久提交以及对所有内容编制了索引后，这些写入才会被确认回客户端。


```csharp
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
table.Execute(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>插入一批实体
Azure 表存储支持批量操作 API，这样便能够通过批量操作合并更新、删除和插入操作。

```csharp
// Create the batch operation.
TableBatchOperation batchOperation = new TableBatchOperation();

// Create a customer entity and add it to the table.
CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
customer1.Email = "Jeff@contoso.com";
customer1.PhoneNumber = "425-555-0104";

// Create another customer entity and add it to the table.
CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
customer2.Email = "Ben@contoso.com";
customer2.PhoneNumber = "425-555-0102";

// Add both customer entities to the batch insert operation.
batchOperation.Insert(customer1);
batchOperation.Insert(customer2);

// Execute the batch operation.
table.ExecuteBatch(batchOperation);
```
## <a name="retrieve-a-single-entity"></a>检索单个实体
在同一 Azure 区域中，Azure Cosmos DB 中的检索 (GET) 完成延迟为 < 10 毫秒（p99）和约 1 毫秒（p50）。 通过设置 `TablePreferredLocations`，可以将任意多个区域添加到帐户中以实现低延迟读取，并部署应用程序以便从其本地区域（“多宿主”）读取。 

可以使用以下代码片段检索单个实体：

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
> [!TIP]
> 请参阅[使用多个区域进行开发](tutorial-global-distribution-table.md)，了解多宿主 API
>

## <a name="query-entities-using-automatic-secondary-indexes"></a>使用自动辅助索引查询实体
可以使用 `TableQuery` 类查询表。 Azure Cosmos DB 具有写入优化数据库引擎，可自动索引表中的所有列。 Azure Cosmos DB 中的索引是架构不可知的。 因此，即使架构在行间是不同的，或者架构随着时间的推移不断发展，仍会自动对其进行索引。 由于 Azure Cosmos DB 支持自动辅助索引，因此针对任何属性的查询均可有效使用索引。

```csharp
CloudTable table = tableClient.GetTableReference("people");

// Filter against a property that's not partition key or row key
TableQuery<CustomerEntity> emailQuery = new TableQuery<CustomerEntity>().Where(
    TableQuery.GenerateFilterCondition("Email", QueryComparisons.Equal, "Ben@contoso.com"));

foreach (CustomerEntity entity in table.ExecuteQuery(emailQuery))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

对于表 API，Azure Cosmos DB 支持与 Azure 表存储相同的查询功能。 Azure Cosmos DB 还支持排序、聚合、地理空间查询、层次结构和各种内置函数。 在将来的服务更新中，会在表 API 中提供更多的功能。 有关这些功能的概述，请参阅 [Azure Cosmos DB 查询](sql-api-sql-query.md)。 

## <a name="replace-an-entity"></a>替换实体
要更新实体，请从表服务中检索它，修改实体对象，然后将更改保存回表服务。 以下代码将更改现有客户的电话号码。 

```csharp
TableOperation updateOperation = TableOperation.Replace(updateEntity);
table.Execute(updateOperation);
```
同样，可以执行 `InsertOrMerge` 或 `Merge` 操作。  

## <a name="delete-an-entity"></a>删除实体
在检索实体之后，可使用更新实体的相同演示模式轻松删除该实体。 以下代码检索并删除一个客户实体。

```csharp
TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
table.Execute(deleteOperation);
```

## <a name="delete-a-table"></a>删除表
最后，以下代码示例将从存储帐户中删除表。 可以立即使用 Azure Cosmos DB 删除并重新创建表。

```csharp
CloudTable table = tableClient.GetTableReference("people");
table.DeleteIfExists();
```

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>后续步骤

本教程介绍如何将 Azure Cosmos DB 与表 API 配合使用，现已完成以下操作： 

> [!div class="checklist"] 
> * 创建 Azure Cosmos DB 帐户 
> * 启用 app.config 文件中的功能 
> * 创建表 
> * 将实体添加到表 
> * 插入一批实体 
> * 检索单个实体 
> * 使用自动辅助索引查询实体 
> * 替换实体 
> * 删除实体 
> * 删除表  

现可继续学习下一教程，并了解有关查询表数据的详细信息。 

> [!div class="nextstepaction"]
> [使用表 API 查询](tutorial-query-table.md)
