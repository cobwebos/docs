---
title: "Azure Cosmos DB：在 .NET 中使用表 API 进行开发 | Microsoft Docs"
description: "了解如何通过 .NET 使用 Azure Cosmos DB 的表 API 进行开发"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 4b22cb49-8ea2-483d-bc95-1172cd009498
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: arramac
ms.custom: mvc
ms.openlocfilehash: 2189dc7900f03a45c360fceffbcd7c1ff36f7e48
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2017
---
# <a name="azure-cosmos-db-develop-with-the-table-api-in-net"></a>Azure Cosmos DB：在 .NET 中使用表 API 进行开发

Azure Cosmos DB 由 Microsoft 提供，是全球分布的多模型数据库服务。 可快速创建和查询文档、键/值，及图形数据库，它们均受益于 Azure Cosmos DB 最核心的全球分布和水平缩放功能。

本教程涵盖以下任务： 

> [!div class="checklist"] 
> * 创建 Azure Cosmos DB 帐户 
> * 启用 app.config 文件中的功能 
> * 使用[表 API](table-introduction.md)（预览）创建表
> * 将实体添加到表 
> * 插入一批实体 
> * 检索单个实体 
> * 使用自动辅助索引查询实体 
> * 替换实体 
> * 删除实体 
> * 删除表
 
## <a name="tables-in-azure-cosmos-db"></a>Azure Cosmos DB 中的表 

Azure Cosmos DB 为有某类需求的应用程序提供[表 API](table-introduction.md)（预览），这些应用程序需要采用无架构设计的键-值存储。 [Azure 表存储](../storage/common/storage-introduction.md) SDK 和 REST API 可用于处理 Azure Cosmos DB。 可以使用 Azure Cosmos DB 创建具有高吞吐量要求的表。 Azure Cosmos DB 当前在公共预览版中支持吞吐量优化表（非正式地称为“高级表”）。 

可以继续对具有高存储和低吞吐量要求的表使用 Azure 表存储。

如果当前使用 Azure 表存储，可以通过“高级表”预览获得以下好处：

- 具有多宿主的统包[全局分发](distribute-data-globally.md)与[自动和手动故障转移](regional-failover.md)
- 支持针对所有属性的自动架构不可知索引（“辅助索引”）和快速查询 
- 支持跨任意数量的区域实现[存储和吞吐量的独立缩放](partition-data.md)
- 支持[按表的专用吞吐量](request-units.md)，可以从每秒数百个请求扩展到每秒数百万个请求
- 支持[五个可调整一致性级别](consistency-levels.md)，可基于应用程序需要权衡可用性、延迟和一致性
- 单个区域内可用性达 99.99%，可添加更多区域以提高可用性，以及获得可通用的[行业领先的综合 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- 使用现有的 Azure 存储 .NET SDK，而不会更改应用程序的任何代码

预览期间，Azure Cosmos DB 使用 .NET SDK 支持表 API。 可从 NuGet 下载 [Azure 存储预览版 SDK](https://aka.ms/premiumtablenuget)，其具有与 [Azure 存储 SDK](https://www.nuget.org/packages/WindowsAzure.Storage) 相同的类和方法签名，但也可以使用表 API 连接到 Azure Cosmos DB 帐户。

若要详细了解复杂的 Azure 表存储任务，请参阅：

* [Azure Cosmos DB 简介：表 API](table-introduction.md)
* 有关可用 API 的完整详细信息的表服务参考文档[用于 .NET 的存储客户端库参考](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)

### <a name="about-this-tutorial"></a>关于本教程
本教程供熟悉 Azure 表存储 SDK 并想要通过 Azure Cosmos DB 使用高级功能的开发人员使用。 本教程基于[通过 .NET 开始使用 Azure 表存储](table-storage-how-to-use-dotnet.md)，并演示如何利用辅助索引、预配的吞吐量和多宿主等附加功能。 涵盖如何使用 Azure 门户创建 Azure Cosmos DB 帐户，生成并部署表应用程序。 还将演练用于创建和删除表，以及插入、更新、删除和查询表数据的 .NET 示例。 

如果尚未安装 Visual Studio 2017，可以下载并使用免费的 [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)。 在安装 Visual Studio 的过程中，请确保启用“Azure 开发”。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>创建数据库帐户

现在首先在 Azure 门户中创建 Azure Cosmos DB 帐户。  

> [!TIP]
> * 已有一个 Azure Cosmos DB 帐户？ 如果有，请跳到[设置 Visual Studio 解决方案](#SetupVS)。
> * 是否具有 Azure DocumentDB 帐户？ 如果有，则该帐户现为 Azure Cosmos DB 帐户，可以直接跳到[设置 Visual Studio 解决方案](#SetupVS)。  
> * 如果使用 Azure Cosmos DB Emulator，请遵循 [Azure Cosmos DB Emulator](local-emulator.md) 中的步骤设置该模拟器，并直接跳到[设置 Visual Studio 解决方案](#SetupVS)。
<!---Loc Comment: Please, check link [Set up your Visual Studio solution] since it's not redirecting to any location.---> 
>
>

[!INCLUDE [cosmosdb-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)] 

## <a name="clone-the-sample-application"></a>克隆示例应用程序

现在让我们从 github 克隆表应用、设置连接字符串，并运行。

1. 打开 git 终端窗口（例如 git bash）和指向工作目录的 `cd`。  

2. 运行下列命令以克隆示例存储库。 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-getting-started
    ```

3. 然后在 Visual Studio 中打开解决方案文件。

## <a name="update-your-connection-string"></a>更新连接字符串

现在返回到 Azure 门户，获取连接字符串信息，并将其复制到应用。

1. 在 [Azure 门户](http://portal.azure.com/)的 Azure Cosmos DB 帐户的左侧导航栏中，单击“密钥”，并单击“读写密钥”。 将在下一步使用屏幕右侧的复制按钮将连接字符串复制到 app.config 文件中。

2. 在 Visual Studio 中，打开 app.config 文件。 

3. 使用复制按钮从门户中复制 URI 值，并使其成为 app.config 中的 account-key 的值。使用 app.config 中之前为 account-name 创建的帐户名。
  
```
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key;TableEndpoint=https://account-name.documents.azure.com" />
```

> [!NOTE]
> 要将此应用与标准 Azure 表存储配合使用，需要更改 `app.config file` 中的连接字符串。 将帐户名用作 Table-account 名称，将密钥用作 Azure 存储主密钥。 <br>
>`<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key;EndpointSuffix=core.windows.net" />`
> 
>

## <a name="build-and-deploy-the-app"></a>生成并部署应用
1. 在 Visual Studio 中，右键单击解决方案资源管理器中的项目，并单击“管理 NuGet 包”。 

2. 在 NuGet“浏览”框中，键入 WindowsAzure.Storage-PremiumTable。 选中“包括预发行版本”。

3. 从结果中，安装 WindowsAzure.Storage-PremiumTable，并选择预览版 `0.0.1-preview`。 此操作会安装 Azure 表存储包和所有依赖项。

4. 单击 Ctrl+F5 运行应用程序。 

现可返回到数据资源管理器，查看查询、修改和处理此表数据。 

> [!NOTE]
> 要将此应用与 Azure Cosmos DB Emulator 配合使用，只需更改 `app.config file` 中的连接字符串。 将以下值用于模拟器。 <br>
>`<add key="StorageConnectionString" value=DefaultEndpointsProtocol=https;AccountName=localhost;AccountKey=<insertkey>==;TableEndpoint=https://localhost -->`
> 
>

## <a name="azure-cosmos-db-capabilities"></a>Azure Cosmos DB 功能
Azure Cosmos DB 支持大量 Azure 表存储 API 中不可用的功能。 可以通过以下 `appSettings` 配置值启用新功能。 我们未向预览版 Azure 存储 SDK 引入任何新签名或重载。 这样便能够同时连接到标准表和高级表，并使用其他 Azure 存储服务，如 Blob 和队列。 


| 键 | 说明 |
| --- | --- |
| TableConnectionMode  | Azure Cosmos DB 支持两种连接模式。 在`Gateway`模式下，将始终向 Azure Cosmos DB 网关发出请求，该网关会将其转发到相应的数据分区。 在`Direct`连接模式下，客户端会提取表到分区的映射，且会直接针对数据分区发出请求。 建议使用`Direct`（默认）。  |
| TableConnectionProtocol | Azure Cosmos DB 支持两种连接协议 - `Https` 和 `Tcp`。 默认为 `Tcp`，并推荐使用该协议，因为它更轻质。 |
| TablePreferredLocations | 以逗号分隔的首选（多主页）位置列表，用于读取。 每个 Azure Cosmos DB 帐户可与 1-30+ 个区域关联。 每个客户端实例可按首选顺序指定这些区域的一个子集以实现低延迟的读取。 必须使用这些区域的[显示名称](https://msdn.microsoft.com/library/azure/gg441293.aspx)命名这些区域，例如 `West US`。 另请参阅[多宿主 API](tutorial-global-distribution-table.md)。
| TableConsistencyLevel | 通过在后列五个定义完善的一致性级别之间进行选择，可在延迟、一致性和可用性之间权衡：`Strong`、`Session`、`Bounded-Staleness`、`ConsistentPrefix` 和 `Eventual`。 默认值为 `Session`。 一致性级别的选择会在多区域设置中产生显著的性能差异。 有关详细信息，请参阅[一致性级别](consistency-levels.md)。 |
| TableThroughput | 表的保留吞吐量以请求单位 (RU) /秒表示。 单个表可支持数百至数百万的 RU/s。 请参阅[请求单位](request-units.md)。 默认为 `400` |
| TableIndexingPolicy | 针对表中所有列的一致和自动辅助索引 | 符合索引策略规范的 JSON 字符串。 要查看如何将索引策略更改为包括/排除特定列，请参阅[索引策略](indexing-policies.md)。 | 所有属性的自动索引（字符串哈希、数字范围） |
| TableQueryMaxItemCount | 配置单个往返过程中按每个表查询所返回的最大项数。 默认为 `-1`，使 Azure Cosmos DB 在运行时能够动态确定该值。 |
| TableQueryEnableScan | 如果查询无法使用任何筛选器的索引，则无论如何要通过扫描运行它。 默认值为 `false`。|
| TableQueryMaxDegreeOfParallelism | 用于执行跨分区查询的并行度。 `0` 是不使用任何预提取的串行，`1` 是使用预提取的串行，更高的值会增加并行度的速率。 默认为 `-1`，使 Azure Cosmos DB 在运行时能够动态确定该值。 |

若要更改默认值，请从 Visual Studio 中的解决方案资源管理器打开 `app.config` 文件。 添加 `<appSettings>` 元素的内容，如下所示。 将 `account-name` 替换为存储帐户名称，将 `account-key` 替换为帐户访问密钥。 

```xml
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
    <appSettings>
      <!-- Client options -->
      <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key; TableEndpoint=https://account-name.documents.azure.com" />
      <add key="TableConnectionMode" value="Direct"/>
      <add key="TableConnectionProtocol" value="Tcp"/>
      <add key="TablePreferredLocations" value="East US, West US, North Europe"/>
      <add key="TableConsistencyLevel" value="Eventual"/>

      <!--Table creation options -->
      <add key="TableThroughput" value="700"/>
      <add key="TableIndexingPolicy" value="{""indexingMode"": ""Consistent""}"/>

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

table.CreateIfNotExists();
```

在表创建方式上存在一个重要差异。 与 Azure 存储的基于消耗的事务模型不同，Azure Cosmos DB 保留吞吐量。 保留模型具有两个主要优势：

* 吞吐量是专用/保留的，因此如果请求速率处于配置的吞吐量水平或其以下，则用户将不会受到限制
* 保留模型[对吞吐量大的工作负荷而言更为经济高效](key-value-store-cost.md)

可通过以 RU（请求单位）/秒的形式配置 `TableThroughput` 的设置，来配置默认吞吐量。 

对 1-KB 实体的 1 次读取规范化为 1 RU，并会根据其他操作的 CPU、内存和 IOPS 消耗量将其规范化为固定 RU 值。 详细了解 [Azure Cosmos DB 中的请求单位](request-units.md)。

> [!NOTE]
> 虽然表存储 SDK 当前不支持修改吞吐量，但可在任何时候通过 Azure 门户或 Azure CLI 即时更改吞吐量。

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

用于 Azure Cosmos DB 的表 API 处于预览状态。 在正式版中，由 SLA（其他 Azure Cosmos DB API 等）支持 p99 延迟保证。 

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
Azure 表存储支持批量操作 API，这样便能够通过单个批量操作合并更新、删除和插入操作。 Azure Cosmos DB 不具有 Azure 表存储针对批量 API 的某些限制。 例如，可以在某批中执行多个读取，可以对批中的同一实体执行多个写入，且不存在每批 100 个操作的限制。 

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

在预览版中，对于表 API，Azure Cosmos DB 支持与 Azure 表存储相同的查询功能。 Azure Cosmos DB 还支持排序、聚合、地理空间查询、层次结构和各种内置函数。 在将来的服务更新中，会在表 API 中提供更多的功能。 有关这些功能的概述，请参阅 [Azure Cosmos DB 查询](documentdb-sql-query.md)。 

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

如果不打算继续使用此应用，请使用以下步骤删除本教程在 Azure 门户中创建的所有资源。   

1. 在 Azure 门户的左侧菜单中，单击“资源组”，并单击已创建资源的名称。  
2. 在资源组页上单击“删除”，在文本框中键入要删除的资源的名称，并单击“删除”。 

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
