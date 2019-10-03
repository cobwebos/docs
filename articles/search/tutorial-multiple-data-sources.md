---
title: C# 教程：为多个数据源编制索引 - Azure 搜索
description: 了解如何将多个数据源的数据导入单个 Azure 搜索索引。
author: RobDixon22
manager: nitinme
services: search
ms.service: search
ms.topic: tutorial
ms.date: 06/21/2019
ms.author: heidist
ms.openlocfilehash: d55a586d3dfb22b5dad377ff656b8d6a6c940bdb
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241835"
---
# <a name="c-tutorial-combine-data-from-multiple-data-sources-in-one-azure-search-index"></a>C# 教程：将多个数据源的数据合并到一个 Azure 搜索

Azure 搜索可导入、分析多个数据源的数据并将其编入单个合并的搜索索引。 这适合以下情况：其中结构化数据是使用来自文本、HTML 或 JSON 文档等其他源的结构化程度较低或甚至纯文本数据来聚合的。

本教程介绍如何为来自 Azure Cosmos DB 数据源的酒店数据编制索引并将其与来自 Azure Blob 存储文档的酒店房间详细信息整合。 其结果将是包含复杂数据类型的合并的酒店搜索索引。

本教程使用 C#、适用于 Azure 搜索的 .NET SDK 以及 Azure 门户执行以下任务：

> [!div class="checklist"]
> * 上传示例数据和创建数据源
> * 标识文档密钥
> * 定义和创建索引
> * 为来自 Azure Cosmos DB 的酒店数据编制索引
> * 合并来自 Blob 存储的酒店房间数据

## <a name="prerequisites"></a>先决条件

本快速入门使用以下服务、工具和数据。 

- [创建 Azure 搜索服务](search-create-service-portal.md)或在当前订阅下[查找现有服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 可在本教程中使用免费服务。

- [创建一个 Cosmos DB 帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)，用于存储示例酒店数据。

- [创建一个 Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)，用于存储示例 JSON blob 数据。

- [安装 Visual Studio](https://visualstudio.microsoft.com/)，以用作 IDE。

### <a name="install-the-project-from-github"></a>从 GitHub 安装项目

1. 找到 GitHub 上的示例存储库：[azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples)。
1. 选择“克隆或下载”，制作项目的专用本地副本  。
1. 打开 Visual Studio 并安装 Microsoft Azure 搜索 NuGet 包（如果尚未安装）。 在“工具”菜单中，依次选择“NuGet 包管理器”、“管理解决方案的 NuGet 包...”    。选择“浏览”选项卡，然后在搜索框中键入“Azure 搜索”  。 当列表中显示有 Microsoft.Azure.Search 时（版本 9.0.1 或更高版本），安装它  。 需要再单击几个对话框来完成安装。

    ![使用 NuGet 添加 Azure 库](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

1. 使用 Visual Studio，导航到本地存储库，并打开解决方案文件 AzureSearchMultipleDataSources.sln  。

## <a name="get-a-key-and-url"></a>获取密钥和 URL

必须有 Azure 搜索服务 URL 和访问密钥，才能与此服务交互。 搜索服务是使用这二者创建的，因此，如果向订阅添加了 Azure 搜索，则请按以下步骤获取必需信息：

1. [登录到 Azure 门户](https://portal.azure.com/)，在搜索服务的“概述”页中获取 URL。  示例终结点可能类似于 `https://mydemo.search.windows.net`。

1. 在“设置” > “密钥”中，获取有关该服务的完全权限的管理员密钥   。 有两个可交换的管理员密钥，为保证业务连续性而提供，以防需要滚动一个密钥。 可以在请求中使用主要或辅助密钥来添加、修改和删除对象。

![获取 HTTP 终结点和访问密钥](media/search-get-started-postman/get-url-key.png "Get an HTTP endpoint and access key")

所有请求对发送到服务的每个请求都需要 API 密钥。 具有有效的密钥可以在发送请求的应用程序与处理请求的服务之间建立信任关系，这种信任关系以每个请求为基础。

## <a name="prepare-sample-azure-cosmos-db-data"></a>准备示例 Azure Cosmos DB 数据

此示例使用两个小数据集，其中包含七个虚构酒店的数据。 一个数据集描述酒店本身，将加载到 Azure Cosmos DB 数据库中。 另一个包含酒店房间的详细信息，并以七个单独的 JSON 文件的形式提供，将上传到 Azure Blob 存储中。

1. [登录到 Azure 门户](https://portal.azure.com)，然后导航到自己的 Azure Cosmos DB 帐户的“概述”页面。

1. 在菜单栏中，单击“添加容器”。 指定“新建数据库”，并使用名称“hotel-rooms-db”  。 输入“hotels”  作为集合名称，输入“/HotelId”  作为分区键。 单击“确定”创建数据库和容器  。

   ![添加 Azure Cosmos DB 容器](media/tutorial-multiple-data-sources/cosmos-add-container.png "Add an Azure Cosmos DB container")

1. 转到 Cosmos DB 数据资源管理器并选择“hotel-rooms-db”数据库中“hotels”容器下的“items”元素    。 在命令栏中，单击“上传项”  。

   ![上传到 Azure Cosmos DB 集合](media/tutorial-multiple-data-sources/cosmos-upload.png "Upload to Cosmos DB collection")

1. 在上传面板中，单击文件夹按钮，然后导航到项目文件夹中的文件 cosmosdb/HotelsDataSubset_CosmosDb.json  。 单击“确定”  以开始上传。

   ![选择要上传的文件](media/tutorial-multiple-data-sources/cosmos-upload2.png "Select file to upload")

1. 使用“刷新”按钮来刷新酒店集合中的项的视图。 此时应会列出七个新数据库文档。

## <a name="prepare-sample-blob-data"></a>准备示例 blob 数据

1. [登录到 Azure 门户](https://portal.azure.com)，导航到你的 Azure 存储帐户，单击“Blob”，然后单击“+ 容器”   。

1. [创建 blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)，名为“hotel-rooms”  ，用于存储示例酒店房间 JSON 文件。 可将“公共访问级别”设为任何有效值。

   ![创建 blob 容器](media/tutorial-multiple-data-sources/blob-add-container.png "Create a blob container")

1. 创建容器后，将其打开，然后在命令栏中选择“上传”  。

   ![在命令栏中上传](media/search-semi-structured-data/upload-command-bar.png "在命令栏中上传")

1. 导航到包含示例文件的文件夹。 选择所有这些文件，然后单击“上传”  。

   ![上传文件](media/tutorial-multiple-data-sources/blob-upload.png "上传文件")

上传完成后，这些文件应会显示在数据容器的列表中。

## <a name="set-up-connections"></a>设置连接

搜索服务和数据源的连接信息在解决方案的 appsettings.json  文件中指定。 

1. 在 Visual Studio 中打开 AzureSearchMultipleDataSources.sln  文件。

1. 在解决方案资源管理器中编辑 appsettings.json  文件。  

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "BlobStorageAccountName": "Put your Azure Storage account name here",
  "BlobStorageConnectionString": "Put your Azure Blob Storage connection string here",
  "CosmosDBConnectionString": "Put your Cosmos DB connection string here",
  "CosmosDBDatabaseName": "hotel-rooms-db"
}
```

前两个条目使用 Azure 搜索服务的 URL 和管理密钥。 如果终结点为 `https://mydemo.search.windows.net`（例如），则要提供的服务名称为 `mydemo`。

其余条目指定 Azure Blob 存储和 Azure Cosmos DB 数据源的帐户名称和连接字符串信息。

### <a name="identify-the-document-key"></a>标识文档密钥

Azure 搜索中的键字段唯一标识索引中的每个文档。 每个搜索索引必须只有一个类型为 `Edm.String` 的键字段。 必须为添加到索引中的每个数据源文档设置键字段。 （事实上，它是唯一的必填字段。）

如果要为多个数据源的数据编制索引，必须将每个数据源键值映射到合并的索引中的同一键字段。 通常需要进行一些前期规划，为索引确定有意义的文档键，并确保它存在于每个数据源中。

在索引编制过程中，Azure 搜索索引器可以使用字段映射来重命名数据字段甚至重新设置其格式，以便可以将源数据定向到正确的索引字段。

例如，在示例 Azure Cosmos DB 数据中，酒店标识符名为 HotelId  。 但在酒店房间的 JSON blob 文件中，酒店标识符则名为 Id  。程序通过将 blob 中的 Id 字段映射到索引中的 HotelId 键字段来处理这种情况   。

> [!NOTE]
> 大多数情况下，自动生成的文档键（如某些索引器默认创建的），不会为合并的索引提供有用的文档键。 一般而言，会需要使用数据源中已存在的或可轻松添加到数据源中的有意义且唯一的键值。

## <a name="understand-the-code"></a>了解代码

指定数据和配置设置后，AzureSearchMultipleDataSources.sln  中的示例程序应已可以生成并运行。

此简单的 C#/.NET 控制台应用程序执行以下任务：
* 基于 C# 酒店类的数据结构（该类还引用“地址”和“房间”类）创建新的 Azure 搜索索引。
* 创建 Azure Cosmos DB 数据源和将 Azure Cosmos DB 数据映射到索引字段的索引器。
* 运行 Azure Cosmos DB 索引器以加载“酒店”数据。
* 创建 Azure Blob 存储数据源和将 JSON blob 数据映射到索引字段的索引器。
* 运行 Azure Blob 存储索引器以加载“房间”数据。

 运行该程序之前，请抽时间研究此示例的代码、索引和索引器定义。 相关代码在两个文件中：

  + **Hotel.cs** 包含用于定义索引的架构
  + **Program.cs** 包含用于创建 Azure 搜索索引、数据源和索引器，以及将合并的结果加载到索引中的函数。

### <a name="define-the-index"></a>定义索引

此示例程序使用 .NET SDK 来定义和创建 Azure 搜索索引。 它利用 [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) 类，从 C# 数据模型类来生成索引结构。

数据模型由“酒店”类定义，该类还包含对“地址”和“房间”类的引用。 FieldBuilder 向下钻取多个类定义，从而为索引生成复杂的数据结构。 元数据标记用于定义每个字段的属性，例如字段是否可搜索或可排序。

以下 Hotel.cs  文件中的片段显示如何指定单个字段和对另一个数据模型类的引用。

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

在 Program.cs  文件中，索引是使用由 `FieldBuilder.BuildForType<Hotel>()` 方法生成的名称和字段集合来定义的，并按下示方法创建：

```csharp
private static async Task CreateIndex(string indexName, SearchServiceClient searchService)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address and Room classes are referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    await searchService.Indexes.CreateAsync(definition);
}
```

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>创建 Azure Cosmos DB 数据源和索引器

接下来，主程序包含用于为酒店数据创建 Azure Cosmos DB 数据源的逻辑。

首先，它将 Azure Cosmos DB 数据库名称连接到连接字符串。 然后，它定义数据源对象，包括特定于 Azure Cosmos DB 源的设置，如 [useChangeDetection] 属性。

  ```csharp
private static async Task CreateAndRunCosmosDbIndexer(string indexName, SearchServiceClient searchService)
{
    // Append the database name to the connection string
    string cosmosConnectString = 
        configuration["CosmosDBConnectionString"]
        + ";Database=" 
        + configuration["CosmosDBDatabaseName"];

    DataSource cosmosDbDataSource = DataSource.CosmosDb(
        name: configuration["CosmosDBDatabaseName"], 
        cosmosDbConnectionString: cosmosConnectString,
        collectionName: "hotels",
        useChangeDetection: true);

    // The Azure Cosmos DB data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await searchService.DataSources.CreateOrUpdateAsync(cosmosDbDataSource);
  ```

创建数据源后，该程序会设置一个名为 hotel-rooms-cosmos-indexer  的 Azure Cosmos DB 索引器。

```csharp
    Indexer cosmosDbIndexer = new Indexer(
        name: "hotel-rooms-cosmos-indexer",
        dataSourceName: cosmosDbDataSource.Name,
        targetIndexName: indexName,
        schedule: new IndexingSchedule(TimeSpan.FromDays(1)));
    
    // Indexers keep metadata about how much they have already indexed.
    // If we already ran this sample, the indexer will remember that it already
    // indexed the sample data and not run again.
    // To avoid this, reset the indexer if it exists.
    bool exists = await searchService.Indexers.ExistsAsync(cosmosDbIndexer.Name);
    if (exists)
    {
        await searchService.Indexers.ResetAsync(cosmosDbIndexer.Name);
    }
    await searchService.Indexers.CreateOrUpdateAsync(cosmosDbIndexer);
```
程序在创建新索引器之前会先删除具有相同名称的任何现有索引器，以应对你想要多次运行此示例的情况。

此示例为索引器定义了一个日程计划，以使其每天运行一次。 如果不希望索引器在将来再次自动运行，可以从该调用中删除该计划属性。

### <a name="index-azure-cosmos-db-data"></a>为 Azure Cosmos DB 数据编制索引

创建数据源和索引器之后，用于运行索引器的代码就很简单了：

```csharp
    try
    {
        await searchService.Indexers.RunAsync(cosmosDbIndexer.Name);
    }
    catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
    {
        Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
    }
```

此示例包含一个简单的 try-catch 块来报告执行过程中可能发生的任何错误。

Azure Cosmos DB 索引器运行后，搜索索引将包含一套完整的示例酒店文档。 但是每家酒店的房间字段将是空数组，因为 Azure Cosmos DB 数据源未包含房间详细信息。 接下来，该程序将从 Blob 存储进行拉取，来加载和合并房间数据。

### <a name="create-blob-storage-data-source-and-indexer"></a>创建 Blob 存储数据源和索引器

为获取房间详细信息，程序会先设置一个 Blob 存储数据源，用于引用一组单独的 JSON blob 文件。

```csharp
private static async Task CreateAndRunBlobIndexer(string indexName, SearchServiceClient searchService)
{
    DataSource blobDataSource = DataSource.AzureBlobStorage(
        name: configuration["BlobStorageAccountName"],
        storageConnectionString: configuration["BlobStorageConnectionString"],
        containerName: "hotel-rooms");

    // The blob data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await searchService.DataSources.CreateOrUpdateAsync(blobDataSource);
```

创建数据源后，该程序会设置一个名为 hotel-rooms-blob-indexer  的 blob 索引器。

```csharp
    // Add a field mapping to match the Id field in the documents to 
    // the HotelId key field in the index
    List<FieldMapping> map = new List<FieldMapping> {
        new FieldMapping("Id", "HotelId")
    };

    Indexer blobIndexer = new Indexer(
        name: "hotel-rooms-blob-indexer",
        dataSourceName: blobDataSource.Name,
        targetIndexName: indexName,
        fieldMappings: map,
        parameters: new IndexingParameters().ParseJson(),
        schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

    // Reset the indexer if it already exists
    bool exists = await searchService.Indexers.ExistsAsync(blobIndexer.Name);
    if (exists)
    {
        await searchService.Indexers.ResetAsync(blobIndexer.Name);
    }
    await searchService.Indexers.CreateOrUpdateAsync(blobIndexer);
```

JSON blob 包含一个名为 Id 而不是 HotelId 的键字段   。 该代码使用 `FieldMapping` 类来指示索引器将 Id 字段值定向到索引中的 HotelId 文档键   。

Blob 存储索引器可使用能标识要使用的分析模式的参数。 该分析模式不同于 blob，后者表示单个文档或相同 blob 中的多个文档。 在此示例中，每个 blob 表示单个索引文档，因此代码使用 `IndexingParameters.ParseJson()` 参数。

有关 JSON blob 的索引器分析参数的详细信息，请参阅[为 JSON blob 编制索引](search-howto-index-json-blobs.md)。 有关使用 .NET SDK 指定这些参数的详细信息，请参阅 [IndexerParametersExtension](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingparametersextensions) 类。

程序在创建新索引器之前会先删除具有相同名称的任何现有索引器，以应对你想要多次运行此示例的情况。

此示例为索引器定义了一个日程计划，以使其每天运行一次。 如果不希望索引器在将来再次自动运行，可以从该调用中删除该计划属性。

### <a name="index-blob-data"></a>为 Blob 数据编制索引

创建 Blob 存储数据源和索引器之后，用于运行索引器的代码就很简单了：

```csharp
    try
    {
        await searchService.Indexers.RunAsync(cosmosDbIndexer.Name);
    }
    catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
    {
        Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
    }
```

由于索引已使用 Azure Cosmos DB 数据库的酒店数据进行了填充，blob 索引器会更新索引中的现有文档，并添加房间的详细信息。

> [!NOTE]
> 如果有两个数据源中都有的非键字段且这些字段中的数据不匹配，则索引将包含最近一次运行的任何索引器的值。 在本示例中，这两个数据源都包含 HotelName  字段。 如果由于某种原因，对于具有相同键值的文档，此字段中的数据不同，则索引中存储的值将是最近被编入索引的数据源的 HotelName  数据。

## <a name="search-your-json-files"></a>搜索 JSON 文件

可尝试使用门户中的[搜索资源管理器](search-explorer.md)  在程序开始运行后浏览填充的搜索索引。

在 Azure 门户中，打开搜索服务的“概述”页，在“索引”列表中找到“hotel-rooms-sample”索引    。

  ![Azure 搜索索引列表](media/tutorial-multiple-data-sources/index-list.png "List of Azure Search indexes")

单击列表中的 hotel-rooms-sample 索引。 随即会显示索引的“搜索资源管理器”界面。 输入一个词（如“奢华”）进行查询。 得到的结果中至少会显示一个文档，此文档的房间数组中会显示一系列房间对象。

## <a name="clean-up-resources"></a>清理资源

完成本教程后，最快的清理方式是删除包含 Azure 搜索服务的资源组。 现在，可以删除资源组以永久删除其中的所有内容。 在门户中，资源组名称显示在 Azure 搜索服务的“概述”页上。

## <a name="next-steps"></a>后续步骤

要编制 JSON Blob 的索引，有多种方法和多个选项。 如果源数据包含 JSON 内容，可以查看这些选项，选择最适合自己方案的选项。

> [!div class="nextstepaction"]
> [如何使用 Azure 搜索 Blob 索引器为 JSON blob 编制索引](search-howto-index-json-blobs.md)

可能会需要使用来自非结构化 blob 或全文本内容的在认知层面上很丰富的数据来优化某个数据源的结构化索引数据。 以下教程介绍如何利用 .NET SDK 将认知服务和 Azure 搜索结合起来使用。

> [!div class="nextstepaction"]
> [在 Azure 搜索索引管道中调用认知服务 API](cognitive-search-tutorial-blob-dotnet.md)
