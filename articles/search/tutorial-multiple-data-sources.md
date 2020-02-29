---
title: C#教程：为多个数据源编制索引
titleSuffix: Azure Cognitive Search
description: 了解如何使用索引器将数据从多个数据源导入单个 Azure 认知搜索索引。 本教程和示例代码位于中C#。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 6408689deec7de365ede86665a0eaeb0bd0de64b
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196563"
---
# <a name="tutorial-index-data-from-multiple-data-sources-in-c"></a>教程：从多个数据源中检索数据C#

Azure 认知搜索可导入、分析多个数据源的数据并将其编入单个合并的搜索索引。 这适合以下情况：其中结构化数据是使用来自文本、HTML 或 JSON 文档等其他源的结构化程度较低或甚至纯文本数据来聚合的。

本教程介绍如何为来自 Azure Cosmos DB 数据源的酒店数据编制索引并将其与来自 Azure Blob 存储文档的酒店房间详细信息整合。 其结果将是包含复杂数据类型的合并的酒店搜索索引。

本教程使用C#和[.net SDK](https://aka.ms/search-sdk)执行以下任务：

> [!div class="checklist"]
> * 上传示例数据和创建数据源
> * 标识文档密钥
> * 定义和创建索引
> * 为来自 Azure Cosmos DB 的酒店数据编制索引
> * 合并来自 Blob 存储的酒店房间数据

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必备条件

+ [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal)
+ [Azure 存储](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
+ [Visual Studio 2019](https://visualstudio.microsoft.com/)
+ [创建](search-create-service-portal.md)或[查找现有搜索服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> 您可以使用本教程的免费服务。 免费搜索服务限制为三个索引、三个索引器和三个数据源。 本教程每样创建一个。 在开始之前，请确保你已在服务上实现了接受新资源的空间。

## <a name="download-files"></a>下载文件

1. 找到 GitHub 上的示例存储库：[azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples)。
1. 选择“克隆或下载”，制作项目的专用本地副本。
1. 如果尚未安装，请打开 Visual Studio 2019 并安装 Microsoft Azure 认知搜索 NuGet 包。 在 "**工具**" 菜单中，选择 " **Nuget 包管理器**"，然后选择 "**管理解决方案的 nuget 包**..."。在 "**浏览**" 选项卡中，找到**并安装 ""。** 需要再单击几个对话框来完成安装。

    ![使用 NuGet 添加 Azure 库](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

1. 使用 Visual Studio，导航到本地存储库，并打开解决方案文件 AzureSearchMultipleDataSources.sln。

## <a name="get-a-key-and-url"></a>获取密钥和 URL

必须有 Azure 认知搜索服务 URL 和访问密钥，才能与此服务交互。 搜索服务是使用这二者创建的，因此，如果向订阅添加了 Azure 认知搜索，则请按以下步骤获取必需信息：

1. 登录到[Azure 门户](https://portal.azure.com/)，并在 "搜索服务**概述**" 页中获取 URL。 示例终结点可能类似于 `https://mydemo.search.windows.net`。

1. 在“设置” **“密钥”中，获取有关该服务的完全权限的管理员密钥** > 。 有两个可交换的管理员密钥，为保证业务连续性而提供，以防需要滚动一个密钥。 可以在请求中使用主要或辅助密钥来添加、修改和删除对象。

![获取 HTTP 终结点和访问密钥](media/search-get-started-postman/get-url-key.png "获取 HTTP 终结点和访问密钥")

所有请求对发送到服务的每个请求都需要 API 密钥。 具有有效的密钥可以在发送请求的应用程序与处理请求的服务之间建立信任关系，这种信任关系以每个请求为基础。

## <a name="prepare-sample-azure-cosmos-db-data"></a>准备示例 Azure Cosmos DB 数据

此示例使用两个小数据集，其中包含七个虚构酒店的数据。 一个数据集描述酒店本身，将加载到 Azure Cosmos DB 数据库中。 另一个包含酒店房间的详细信息，并以七个单独的 JSON 文件的形式提供，将上传到 Azure Blob 存储中。

1. 登录到[Azure 门户](https://portal.azure.com)，然后导航到 Azure Cosmos DB 帐户概述 "页。

1. 选择**数据资源管理器**，然后选择 "**新建数据库**"。

   ![创建新数据库](media/tutorial-multiple-data-sources/cosmos-newdb.png "新建数据库")

1. 输入名称 "**宾馆-数据库**"。 接受其余设置的默认值。

   ![配置数据库](media/tutorial-multiple-data-sources/cosmos-dbname.png "配置数据库")

1. 创建新容器。 使用刚才创建的现有数据库。 输入 "**酒店**" 作为容器名称，并使用 " **/HotelId** " 作为分区键。

   ![添加容器](media/tutorial-multiple-data-sources/cosmos-add-container.png "添加容器")

1. 选择 "**酒店**" 下的**项目**，然后单击命令栏上的 "**上传项目**"。 导航到项目文件夹中的**cosmosdb/HotelsDataSubset_CosmosDb**文件，然后选择该文件。

   ![上传到 Azure Cosmos DB 集合](media/tutorial-multiple-data-sources/cosmos-upload.png "上传到 Cosmos DB 集合")

1. 使用“刷新”按钮来刷新酒店集合中的项的视图。 此时应会列出七个新数据库文档。

## <a name="prepare-sample-blob-data"></a>准备示例 blob 数据

1. 登录到[Azure 门户](https://portal.azure.com)，导航到你的 Azure 存储帐户，单击 " **blob**"，然后单击 " **+ 容器**"。

1. [创建 blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)，名为“hotel-rooms”，用于存储示例酒店房间 JSON 文件。 可将“公共访问级别”设为任何有效值。

   ![创建一个 blob 容器](media/tutorial-multiple-data-sources/blob-add-container.png "创建 Blob 容器")

1. 创建容器后，将其打开，然后在命令栏中选择“上传”。 导航到包含示例文件的文件夹。 选择所有这些文件，然后单击“上传”。

   ![上传文件](media/tutorial-multiple-data-sources/blob-upload.png "上传文件")

上传完成后，这些文件应会显示在数据容器的列表中。

## <a name="set-up-connections"></a>设置连接

搜索服务和数据源的连接信息在解决方案的 appsettings.json 文件中指定。 

1. 在 Visual Studio 中打开 AzureSearchMultipleDataSources.sln 文件。

1. 在解决方案资源管理器中编辑 appsettings.json 文件。  

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

前两个条目使用 Azure 认知搜索服务的 URL 和管理密钥。 如果终结点为 `https://mydemo.search.windows.net`（例如），则要提供的服务名称为 `mydemo`。

其余条目指定 Azure Blob 存储和 Azure Cosmos DB 数据源的帐户名称和连接字符串信息。

### <a name="identify-the-document-key"></a>标识文档密钥

Azure 认知搜索中的键字段唯一标识索引中的每个文档。 每个搜索索引必须只有一个类型为 `Edm.String` 的键字段。 必须为添加到索引中的每个数据源文档设置键字段。 （事实上，它是唯一的必填字段。）

为来自多个数据源的数据编制索引时，使用公共文档键将两个物理上不同的源文档中的数据合并到合并索引的新搜索文档中。 它通常需要进行一些前期规划才能标识索引的有意义的文档键，并确保它存在于这两个数据源中。 在此演示中，Cosmos DB 中的每个旅馆的 HotelId 键也显示在 Blob 存储中的房间 JSON blob 中。

在索引编制过程中，Azure 认知搜索索引器可以使用字段映射来重命名数据字段甚至重新设置其格式，以便可以将源数据定向到正确的索引字段。

例如，在我们的示例 Azure Cosmos DB 数据中，酒店标识符被称为 **`HotelId`** 。 但在旅馆房间的 JSON blob 文件中，酒店标识符名为 **`Id`** 。 程序通过将 **`Id`** 字段从 blob 映射到索引中的 **`HotelId`** 键字段来处理这种情况。

> [!NOTE]
> 大多数情况下，自动生成的文档键（如某些索引器默认创建的），不会为合并的索引提供有用的文档键。 一般而言，会需要使用数据源中已存在的或可轻松添加到数据源中的有意义且唯一的键值。

## <a name="understand-the-code"></a>了解代码

指定数据和配置设置后，AzureSearchMultipleDataSources.sln 中的示例程序应已可以生成并运行。

此简单的 C#/.NET 控制台应用程序执行以下任务：
* 基于C#宾馆类的数据结构创建新的索引（也引用 Address 和聊天室类）。
* 创建新的数据源和索引器，该索引器将 Azure Cosmos DB 的数据映射到索引字段。 这些是 Azure 认知搜索中的两个对象。
* 运行索引器以从 Cosmos DB 加载宾馆数据。
* 创建第二个数据源和一个将 JSON blob 数据映射到索引字段的索引器。
* 运行第二个索引器以从 Blob 存储区加载房间数据。

 运行该程序之前，请抽时间研究此示例的代码、索引和索引器定义。 相关代码在两个文件中：

  + **Hotel.cs** 包含用于定义索引的架构
  + **Program.cs** 包含用于创建 Azure 认知搜索索引、数据源和索引器，以及将合并的结果加载到索引中的函数。

### <a name="define-the-index"></a>定义索引

此示例程序使用 .NET SDK 来定义和创建 Azure 认知搜索索引。 它利用 [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) 类，从 C# 数据模型类来生成索引结构。

数据模型由“酒店”类定义，该类还包含对“地址”和“房间”类的引用。 FieldBuilder 向下钻取多个类定义，从而为索引生成复杂的数据结构。 元数据标记用于定义每个字段的属性，例如字段是否可搜索或可排序。

以下 Hotel.cs 文件中的片段显示如何指定单个字段和对另一个数据模型类的引用。

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

在 Program.cs 文件中，索引是使用由 `FieldBuilder.BuildForType<Hotel>()` 方法生成的名称和字段集合来定义的，并按下示方法创建：

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

创建数据源后，该程序会设置一个名为 hotel-rooms-cosmos-indexer 的 Azure Cosmos DB 索引器。

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

创建数据源后，该程序会设置一个名为 hotel-rooms-blob-indexer 的 blob 索引器。

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

JSON blob 包含名为 **`Id`** 的键字段，而不是 **`HotelId`** 。 代码使用 `FieldMapping` 类通知索引器将 **`Id`** 字段值定向到索引中的 **`HotelId`** 文档键。

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
> 如果有两个数据源中都有的非键字段且这些字段中的数据不匹配，则索引将包含最近一次运行的任何索引器的值。 在本示例中，这两个数据源都包含 HotelName 字段。 如果由于某种原因，对于具有相同键值的文档，此字段中的数据不同，则索引中存储的值将是最近被编入索引的数据源的 HotelName 数据。

## <a name="search-your-json-files"></a>搜索 JSON 文件

可尝试使用门户中的[搜索资源管理器](search-explorer.md)在程序开始运行后浏览填充的搜索索引。

在 Azure 门户中，打开搜索服务的“概述”页，在“索引”列表中找到“hotel-rooms-sample”索引。

  ![Azure 认知搜索索引列表](media/tutorial-multiple-data-sources/index-list.png "Azure 认知搜索索引列表")

单击列表中的 hotel-rooms-sample 索引。 随即会显示索引的“搜索资源管理器”界面。 输入一个词（如“奢华”）进行查询。 得到的结果中至少会显示一个文档，此文档的房间数组中会显示一系列房间对象。

## <a name="reset-and-rerun"></a>重置并重新运行

在开发的早期实验阶段，设计迭代的最实用方法是从 Azure 认知搜索中删除对象，并允许你的代码重新生成它们。 资源名称是唯一的。 删除某个对象后，可以使用相同的名称重新创建它。

本教程的示例代码检查现有对象并将其删除，以便您可以重新运行代码。

还可以使用门户删除索引、索引器和数据源。

## <a name="clean-up-resources"></a>清理资源

在自己的订阅中操作时，最好在项目结束时删除不再需要的资源。 持续运行资源可能会产生费用。 可以逐个删除资源，也可以删除资源组以删除整个资源集。

使用左侧导航窗格中的 "所有资源" 或 "资源组" 链接，可以在门户中查找和管理资源。

## <a name="next-steps"></a>后续步骤

现在你熟悉了来自多个源的引入数据概念，接下来让我们详细了解索引器配置，从 Cosmos DB 开始。

> [!div class="nextstepaction"]
> [配置 Azure Cosmos DB 索引器](search-howto-index-cosmosdb.md)