---
title: 通过 .NET 开始使用 Azure 表存储和 Azure Cosmos DB 表 API | Microsoft Docs
description: 使用 Azure 表存储或 Azure Cosmos DB 表 API 将结构化数据存储在云中。
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: dotnet
ms.topic: sample
ms.date: 03/14/2018
ms.author: sngun
ms.openlocfilehash: 927a734b288f5bb0082e77be15ae540702fe4e8b
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808275"
---
# <a name="get-started-with-azure-table-storage-and-the-azure-cosmos-db-table-api-using-net"></a>通过 .NET 开始使用 Azure 表存储和 Azure Cosmos DB 表 API
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

可以使用 Azure 表存储或 Azure Cosmos DB 表 API 将结构化 NoSQL 数据存储在云中，以便通过无架构设计提供键/属性存储。 由于表存储和 Azure Cosmos DB 表 API 是无架构的，因此随着应用程序需求的发展，可以轻松调整数据。 对于许多类型的应用程序来说，访问表存储数据和 Azure Cosmos DB 表 API 速度快且经济高效，在数据量相似的情况下，其成本通常比传统 SQL 要低。

可以使用表存储或 Azure Cosmos DB 表 API 来存储灵活的数据集，例如 Web 应用程序的用户数据、通讯簿、设备信息，或者服务需要的其他类型的元数据。 可以在表中存储任意数量的实体，并且一个存储帐户或表 API 帐户可以包含任意数量的表，直至达到存储帐户或表 API 帐户的容量限制。

### <a name="about-this-sample"></a>关于此示例
本示例介绍如何在常见的 Azure 表存储和表 API 方案中使用[适用于 .NET 的 Microsoft Azure CosmosDB 表库](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)。 该包的名称表示它适合与 Azure Cosmos DB 配合使用，但其实该包既适合与 Azure Cosmos DB 表 API，也适合与 Azure 表存储配合使用，只不过每个服务具有唯一的终结点。 本文使用 C# 探索这些方案，演示如何：
* 创建和删除表
* 插入、更新和删除行
* 查询表

## <a name="prerequisites"></a>先决条件

若要成功完成此示例，需要以下项：

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [适用于 .NET 的 Azure 存储通用库（预览版）](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)。 这是受生产环境支持的必需预览包。 
* [适用于 .NET 的 Microsoft Azure CosmosDB 表库](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)
* [适用于 .NET 的 Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
* [Azure 存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>更多示例
有关使用表存储的其他示例，请参阅 [在 .NET 中使用 Azure 表存储入门](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/)。 可以下载示例应用程序并运行，或在 GitHub 上浏览代码。

## <a name="create-an-azure-service-account"></a>创建 Azure 服务帐户
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>创建 Azure 存储帐户
创建第一个 Azure 存储帐户的最简单方法是使用 [Azure 门户](https://portal.azure.com)。 若要了解更多信息，请参阅 [创建存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)。

还可使用 [Azure PowerShell](../storage/common/storage-powershell-guide-full.md)、[Azure CLI](../storage/common/storage-azure-cli.md) 或[适用于 .NET 的存储资源提供程序客户端库](/dotnet/api/microsoft.azure.management.storage)创建 Azure 存储帐户。

如果暂时不想创建存储帐户，也可以使用 Azure 存储模拟器在本地环境中运行和测试代码。 有关详细信息，请参阅 [使用 Azure 存储模拟器进行开发和测试](../storage/common/storage-use-emulator.md)。

### <a name="create-an-azure-cosmos-db-table-api-account"></a>创建 Azure Cosmos DB 表 API 帐户
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="set-up-your-development-environment"></a>设置开发环境
接下来在 Visual Studio 中设置开发环境，即可试用本指南中的代码示例。

### <a name="create-a-windows-console-application-project"></a>创建 Windows 控制台应用程序项目
在 Visual Studio 中创建新的 Windows 控制台应用程序。 以下步骤演示了如何在 Visual Studio 2017 中创建控制台应用程序。 在其他版本的 Visual Studio 中，这些步骤是类似的。

1. 选择“文件” > “新建” > “项目”。
2. 选择“已安装” > “Visual C#” > “Windows 经典桌面”。
3. 选择“控制台应用(.NET Framework)”。
4. 在“名称”字段中输入应用程序的名称。
5. 选择“确定”。

本示例中的所有代码示例都可以添加到控制台应用程序的 `Program.cs` 文件的 `Main()` 方法。

可以在任意类型的 .NET 应用程序（包括 Azure 云服务或 Web 应用，以及桌面和移动应用程序）中使用 Azure CosmosDB 表库。 为简单起见，我们在本指南中使用控制台应用程序。

### <a name="use-nuget-to-install-the-required-packages"></a>使用 NuGet 安装所需包
若要完成此示例，需要在项目中引用三个建议使用的包：

* [适用于 .NET 的 Azure 存储通用库（预览版）](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common)。 
* [适用于 .NET 的 Microsoft Azure Cosmos DB 表库](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)。 使用此包能够以编程方式访问 Azure 表存储帐户或 Azure Cosmos DB 表 API 帐户中的数据资源。
* [适用于 .NET 的 Microsoft Azure Configuration Manager 库](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)：此包提供用于分析配置文件中连接字符串的类，而不考虑应用程序在何处运行。

可以使用 NuGet 获取这两个包。 执行以下步骤:

1. 在“解决方案资源管理器”中，右键单击项目并选择“管理 NuGet 包”。
2. 在线搜索“Microsoft.Azure.Storage.Common”，并选择“安装”以安装适用于 .NET 的 Azure 存储通用库（预览版）及其依赖项。 由于这是一个预览包，因此请确保选中“包括预发行版”。
3. 在线搜索“Microsoft.Azure.CosmosDB.Table”，并选择“安装”以安装 Microsoft Azure CosmosDB 表库。
4. 在线搜索“WindowsAzure.ConfigurationManager”，并选择“安装”以安装 Microsoft Azure 配置管理器库。

> [!NOTE]
> 适用于 .NET 的存储通用库中的 ODataLib 依赖项通过 NuGet（而不是 WCF 数据服务）上提供的 ODataLib 包来解析。 ODataLib 库可直接下载或者通过 NuGet 由代码项目引用。 存储空间客户端库使用的具体 ODataLib 包是 [OData](http://nuget.org/packages/Microsoft.Data.OData/)、[Edm](http://nuget.org/packages/Microsoft.Data.Edm/) 和 [Spatial](http://nuget.org/packages/System.Spatial/)。 尽管这些库由 Azure 表存储类使用，但是使用存储通用库编程时，它们是必需的依赖项。
> 
> 

> [!TIP]
> 已熟悉 Azure 表存储的开发人员过去可能使用了 [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) 包。 建议所有新的表应用程序使用 [Azure 存储通用库](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)和 [Azure Cosmos DB 表库](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)，但是 WindowsAzure.Storage 包仍受支持。 如果使用 WindowsAzure.Storage 库，请在 using 语句中包括 Microsoft.WindowsAzure.Storage.Table。
>
>

### <a name="determine-your-target-environment"></a>确定目标环境
可从三个环境中选择用于运行本指南中示例的环境：

* 可针对云中的 Azure 存储帐户运行代码。 
* 可针对云中的 Azure Cosmos DB 帐户运行代码。
* 可针对 Azure 存储模拟器运行代码。 存储模拟器是模拟云中 Azure 存储帐户的本地环境。 应用程序处于开发阶段时，可以选择使用模拟器免费测试和调试代码。 模拟器使用已知帐户和密钥。 有关详细信息，请参阅[使用 Azure 存储模拟器进行开发和测试](../storage/common/storage-use-emulator.md)。

如果以云中的存储帐户为目标，请从 Azure 门户复制存储帐户的主访问密钥。 有关详细信息，请参阅 [查看和复制存储访问密钥](../storage/common/storage-create-storage-account.md#view-and-copy-storage-access-keys)。

> [!NOTE]
> 可以指向存储模拟器以避免引发与 Azure 存储有关的任何费用。 但是，如果确实选择针对云中的 Azure 存储帐户，则执行此示例的费用会忽略不计。
> 
> 

如果以 Azure Cosmos DB 帐户为目标，请从 Azure 门户复制表 API 帐户的主访问密钥。 有关详细信息，请参阅[更新连接字符串](create-table-dotnet.md#update-your-connection-string)。

### <a name="configure-your-storage-connection-string"></a>配置存储连接字符串
适用于 .NET 的 Azure 存储通用库支持使用存储连接字符串来配置终结点和用于访问存储服务的凭据。 维护存储连接字符串的最佳方法在配置文件中。 

有关连接字符串的详细信息，请参阅[配置 Azure 存储的连接字符串](../storage/common/storage-configure-connection-string.md)。

> [!NOTE]
> 帐户密钥类似于存储帐户的 root 密码。 始终要小心保护存储帐户密钥。 避免将其分发给其他用户、对其进行硬编码或将其保存在其他人可以访问的纯文本文件中。 如果认为密钥可能已泄漏，请使用 Azure 门户重新生成密钥。
> 
> 

若要配置连接字符串，请从 Visual Studio 中的解决方案资源管理器打开 `app.config` 文件。 添加 `<appSettings>` 元素的内容，如下所示。 请将 `account-name` 替换为帐户名称，将 `account-key` 替换为帐户访问密钥。

```xml
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.6.1" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
    </appSettings>
</configuration>
```

例如，如果使用的是 Azure 存储帐户，则配置设置应类似于：

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=<account-key>" />
```

如果使用的是 Azure Cosmos DB 帐户，则配置设置应类似于：

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=tableapiacct;AccountKey=<account-key>;TableEndpoint=https://tableapiacct.table.cosmosdb.azure.com:443/;" />
```

若要以存储模拟器为目标，可使用映射到已知帐户名称和密钥的快捷方式。 在这种情况下，连接字符串设置如下所示：

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />
```

### <a name="add-using-directives"></a>添加 using 指令
将以下 **using** 指令添加到 `Program.cs` 文件顶部：

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for StorageAccounts
using Microsoft.Azure.CosmosDB.Table; // Namespace for Table storage types
```

### <a name="parse-the-connection-string"></a>解析连接字符串
[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-table-service-client"></a>创建表服务客户端
[CloudTableClient][dotnet_CloudTableClient] 类用于检索存储在表存储中的表和实体。 下面是创建表服务客户端的一种方法：

```csharp
// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
```

现在，已准备好编写从表存储读取数据并将数据写入表存储的代码。

## <a name="create-a-table"></a>创建表
此示例演示如何创建表（如果表已经不存在）：

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Retrieve a reference to the table.
CloudTable table = tableClient.GetTableReference("people");

// Create the table if it doesn't exist.
table.CreateIfNotExists();
```

## <a name="add-an-entity-to-a-table"></a>将实体添加到表
实体使用派生自 [TableEntity][dotnet_TableEntity] 的自定义类映射到 C# 对象。 要将实体添加到表，请创建用于定义实体的属性的类。 以下代码定义将客户的名字和姓氏分别用作行键和分区键的实体类。 实体的分区键和行键共同唯一地标识表中的实体。 查询分区键相同的实体的速度快于查询分区键不同的实体的速度，但使用不同的分区键可实现更高的并行操作可伸缩性。 需要存储在表中的实体必须是受支持的类型，例如，必须派生自 [TableEntity][dotnet_TableEntity] 类。 要存储在表中的实体属性必须是相应类型的公共属性，并且允许获取和设置值。 此外，实体类型*必须*公开不带参数的构造函数。

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

涉及实体的表操作通过先前在“创建表”部分中创建的 [CloudTable][dotnet_CloudTable] 对象执行。 用一个 [TableOperation][dotnet_TableOperation] 对象表示要执行的操作。 以下代码示例演示如何创建 [CloudTable][dotnet_CloudTable] 对象以及 **CustomerEntity** 对象。 为准备此操作，会创建一个 [TableOperation][dotnet_TableOperation] 对象以将客户实体插入该表中。 最后，通过调用 [CloudTable][dotnet_CloudTable].[Execute][dotnet_CloudTable_Execute] 执行此操作。

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

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
可以通过一次写入操作将一批实体插入表中。 批处理操作的一些其他注意事项：

* 可以在同一批处理操作中执行更新、删除和插入操作。
* 单个批处理操作最多可包含 100 个实体。
* 单次批处理操作中的所有实体都必须具有相同的分区键。
* 虽然可以将某个查询作为批处理操作执行，但该操作必须是批处理中仅有的操作。

以下代码示例创建两个实体对象，并使用 [Insert][dotnet_TableBatchOperation_Insert] 方法将其中每个对象都添加到 [TableBatchOperation][dotnet_TableBatchOperation] 中。 然后调用 [CloudTable][dotnet_CloudTable].[ExecuteBatch][dotnet_CloudTable_ExecuteBatch] 以执行此操作。

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

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

## <a name="retrieve-all-entities-in-a-partition"></a>检索分区中的所有实体
若要查询表以获取分区中的所有实体，请使用 [TableQuery][dotnet_TableQuery] 对象。 以下代码示例指定了一个筛选器，以筛选分区键为“Smith”的实体。 此示例会将查询结果中每个实体的字段输出到控制台。

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

// Print the fields for each customer.
foreach (CustomerEntity entity in table.ExecuteQuery(query))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>检索分区中的一部分实体
如果不想查询分区中的所有实体，则可以通过结合使用分区键筛选器与行键筛选器来指定一个范围。 以下代码示例使用两个筛选器来获取分区“Smith”中的、行键（名字）以字母“E”前面的字母开头的所有实体，并输出查询结果。

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create the table query.
TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

// Loop through the results, displaying information about the entity.
foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

## <a name="retrieve-a-single-entity"></a>检索单个实体
可以编写查询以检索单个特定实体。 以下代码使用 [TableOperation][dotnet_TableOperation] 来指定客户“Ben Smith”。 此方法仅返回一个实体，而不是一个集合，并且 [TableResult][dotnet_TableResult].[Result][dotnet_TableResult_Result] 中的返回值是一个 **CustomerEntity** 对象。 在查询中指定分区键和行键是从表服务中检索单个实体的最快方法。

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
{
    Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
}
else
{
    Console.WriteLine("The phone number could not be retrieved.");
}
```

## <a name="replace-an-entity"></a>替换实体
要更新实体，请从表服务中检索它，修改实体对象，然后将更改保存回表服务。 以下代码将更改现有客户的电话号码。 此代码使用 [Replace][dotnet_TableOperation_Replace]，而不是调用 [Insert][dotnet_TableOperation_Insert]。 [Replace][dotnet_TableOperation_Replace] 将导致在服务器上完全替换该实体，除非服务器上的该实体自检索到它以后发生更改，在此情况下，该操作会失败。 操作失败将防止应用程序无意中覆盖应用程序的其他组件在检索与更新之间所做的更改。 正确处理此失败问题的方法是再次检索实体，进行更改（如果仍有效），然后再次执行 [Replace][dotnet_TableOperation_Replace] 操作。 下一节将演示如何重写此行为。

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

if (updateEntity != null)
{
    // Change the phone number.
    updateEntity.PhoneNumber = "425-555-0105";

    // Create the Replace TableOperation.
    TableOperation updateOperation = TableOperation.Replace(updateEntity);

    // Execute the operation.
    table.Execute(updateOperation);

    Console.WriteLine("Entity updated.");
}
else
{
    Console.WriteLine("Entity could not be retrieved.");
}
```

## <a name="insert-or-replace-an-entity"></a>插入或替换实体
如果该实体自从服务器中检索到它以后发生更改，则 [Replace][dotnet_TableOperation_Replace] 操作会失败。 此外，必须首先从服务器中检索该实体，[Replace][dotnet_TableOperation_Replace] 操作才能成功。 但是，有时你不知道服务器上是否存在该实体以及存储在其中的当前值是否无关。 更新操作会将其全部覆盖。 为此，应使用 [InsertOrReplace][dotnet_TableOperation_InsertOrReplace] 操作。 如果该实体不存在，此操作将插入它，如果存在，则替换它，而不管上次更新是何时进行的。

在下面的代码示例中，创建了“Fred Jones”的客户实体并将其插入了“people”表中。 接下来，我们使用 [InsertOrReplace][dotnet_TableOperation_InsertOrReplace] 操作通过相同的分区键 (Jones) 和行键 (Fred) 将实体保存到服务器，此次为 PhoneNumber 属性设置了其他值。 我们使用 [InsertOrReplace][dotnet_TableOperation_InsertOrReplace]，因此其所有属性值被替换。 但是，如果表中部不存在“Fred Jones”实体，则会将其插入。

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a customer entity.
CustomerEntity customer3 = new CustomerEntity("Jones", "Fred");
customer3.Email = "Fred@contoso.com";
customer3.PhoneNumber = "425-555-0106";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer3);

// Execute the operation.
table.Execute(insertOperation);

// Create another customer entity with the same partition key and row key.
// We've already created a 'Fred Jones' entity and saved it to the
// 'people' table, but here we're specifying a different value for the
// PhoneNumber property.
CustomerEntity customer4 = new CustomerEntity("Jones", "Fred");
customer4.Email = "Fred@contoso.com";
customer4.PhoneNumber = "425-555-0107";

// Create the InsertOrReplace TableOperation.
TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(customer4);

// Execute the operation. Because a 'Fred Jones' entity already exists in the
// 'people' table, its property values will be overwritten by those in this
// CustomerEntity. If 'Fred Jones' didn't already exist, the entity would be
// added to the table.
table.Execute(insertOrReplaceOperation);
```

## <a name="query-a-subset-of-entity-properties"></a>查询一部分实体属性
表查询可以只检索实体中的少数几个属性而不是所有实体属性。 此方法称为“投影”，可减少带宽并提高查询性能，尤其适用于大型实体。 以下代码中的查询只返回表中实体的电子邮件地址。 这可通过使用 [DynamicTableEntity][dotnet_DynamicTableEntity] 和 [EntityResolver][dotnet_EntityResolver] 的查询来实现。 可以在[“Upsert 和查询投影介绍”博客文章][blog_post_upsert]中更加详细地了解投影。 存储模拟器不支持投影，因此，此代码仅在使用表服务中的帐户时才能运行。

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Define the query, and select only the Email property.
TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

// Define an entity resolver to work with the entity after retrieval.
EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
{
    Console.WriteLine(projectedEmail);
}
```

## <a name="delete-an-entity"></a>删除实体
在检索实体之后，可使用更新实体的相同演示模式轻松删除该实体。 以下代码检索并删除一个客户实体。

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Assign the result to a CustomerEntity.
CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

// Create the Delete TableOperation.
if (deleteEntity != null)
{
    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

    // Execute the operation.
    table.Execute(deleteOperation);

    Console.WriteLine("Entity deleted.");
}
else
{
    Console.WriteLine("Could not retrieve the entity.");
}
```

## <a name="delete-a-table"></a>删除表
最后，以下代码示例将从存储帐户中删除表。 在删除表之后的一段时间内无法重新创建它。

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Delete the table it if exists.
table.DeleteIfExists();
```

## <a name="retrieve-entities-in-pages-asynchronously"></a>以异步方式检索页中的实体
如果正在读取大量实体，并且想要在检索进行时处理/显示实体，而非等待返回全部实体，则可以通过使用分段查询检索实体。 此示例显示如何通过 Async-Await 模式以页面形式返回结果，这样就不会在等待返回大量结果时阻止操作的执行。 有关在 .NET 中使用 Async-Await 模式的详细信息，请参阅 [使用 Async 和 Await 进行异步编程（C# 和 Visual Basic）](https://msdn.microsoft.com/library/hh191443.aspx)。

```csharp
// Initialize a default TableQuery to retrieve all the entities in the table.
TableQuery<CustomerEntity> tableQuery = new TableQuery<CustomerEntity>();

// Initialize the continuation token to null to start from the beginning of the table.
TableContinuationToken continuationToken = null;

do
{
    // Retrieve a segment (up to 1,000 entities).
    TableQuerySegment<CustomerEntity> tableQueryResult =
        await table.ExecuteQuerySegmentedAsync(tableQuery, continuationToken);

    // Assign the new continuation token to tell the service where to
    // continue on the next iteration (or null if it has reached the end).
    continuationToken = tableQueryResult.ContinuationToken;

    // Print the number of rows retrieved.
    Console.WriteLine("Rows retrieved {0}", tableQueryResult.Results.Count);

// Loop until a null continuation token is received, indicating the end of the table.
} while(continuationToken != null);
```

## <a name="next-steps"></a>后续步骤
现在，已了解有关表存储的基础知识，请按照下面的链接了解更复杂的存储任务：

* [Microsoft Azure 存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md)是 Microsoft 免费提供的独立应用，适用于在 Windows、macOS 和 Linux 上以可视方式处理 Azure 存储数据。
* 有关表存储的更多示例，请参阅 [在 .NET 中使用 Azure 表存储入门](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/)
* 查看表服务参考文档，了解有关可用 API 的完整详情：
* [.NET 存储客户端库参考](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
* [REST API 参考](http://msdn.microsoft.com/library/azure/dd179355)
* 了解如何使用 [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) 简化要为与 Azure 存储配合使用而编写的代码。
* 查看更多功能指南，以了解在 Azure 中存储数据的其他方式。
* [通过 .NET 开始使用 Azure Blob 存储](../storage/blobs/storage-dotnet-how-to-use-blobs.md) 来存储非结构化数据。
* [使用.NET (C#) 连接到 SQL 数据库](../sql-database/sql-database-develop-dotnet-simple.md)，存储关系数据。

[Download and install the Azure SDK for .NET]: /develop/net/
[Creating an Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx

[blog_post_upsert]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx

[dotnet_api_ref]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[dotnet_CloudTableClient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtableclient.aspx
[dotnet_CloudTable]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.aspx
[dotnet_CloudTable_Execute]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.execute.aspx
[dotnet_CloudTable_ExecuteBatch]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.executebatch.aspx
[dotnet_DynamicTableEntity]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.dynamictableentity.aspx
[dotnet_EntityResolver]: https://msdn.microsoft.com/library/jj733144.aspx
[dotnet_TableBatchOperation]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx
[dotnet_TableBatchOperation_Insert]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.insert.aspx
[dotnet_TableEntity]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableentity.aspx
[dotnet_TableOperation]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.aspx
[dotnet_TableOperation_Insert]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.insert.aspx
[dotnet_TableOperation_InsertOrReplace]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.insertorreplace.aspx
[dotnet_TableOperation_Replace]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.replace.aspx
[dotnet_TableQuery]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablequery.aspx
[dotnet_TableResult]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableresult.aspx
[dotnet_TableResult_Result]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableresult.result.aspx
