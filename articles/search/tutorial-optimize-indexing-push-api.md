---
title: C# 教程：使用推送 API 优化索引编制
titleSuffix: Azure Cognitive Search
description: 了解如何使用 Azure 认知搜索的推送 API 高效地为数据编制索引。 本教程和示例代码以 C# 编写。
manager: liamca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 08/21/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: bfb2598fb3a207bbdfaade9086efd07827b077dd
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998418"
---
# <a name="tutorial-optimize-indexing-with-the-push-api"></a>教程：使用推送 API 优化索引编制

Azure 认知搜索支持采用[两种基本方法](search-what-is-data-import.md)将数据导入到搜索索引中：一种方法是以编程方式将数据推送到索引中；另一种方法是将 [Azure 认知搜索索引器](search-indexer-overview.md)指向受支持的数据源来拉取数据。

本教程介绍了如何使用[推送模型](search-what-is-data-import.md#pushing-data-to-an-index)通过分批处理请求并使用指数回退重试策略来高效地为数据编制索引。 你可以[下载并运行该应用程序](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing)。 本文介绍了该应用程序的主要方面，以及为数据编制索引时要考虑的因素。

本教程使用 C# 和 [.NET SDK](/dotnet/api/overview/azure/search) 执行以下任务：

> [!div class="checklist"]
> * 创建索引
> * 测试各种批大小以确定最高效的大小
> * 以异步方式为数据编制索引
> * 使用多个线程以提高索引编制速度
> * 使用指数回退重试策略重试失败的项

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

本教程需要以下服务和工具。

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)（版本不限）。 示例代码和说明已在免费社区版上进行了测试。

+ [创建 Azure 认知搜索服务](search-create-service-portal.md)或在当前订阅下[查找现有服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。

<a name="get-service-info"></a>

## <a name="download-files"></a>下载文件

本教程的源代码位于 GitHub 存储库 [Azure-Samples/azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) 的 [optimzize-data-indexing](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) 文件夹中。

## <a name="key-considerations"></a>重要注意事项

将数据推送到索引中时，有几个会影响索引编制速度的重要注意事项。 有关这些因素的详细信息，可参阅[为大型数据集编制索引](search-howto-large-index.md)一文。

要考虑的六个主要因素包括：

+ **服务层级和分区/副本数** - 添加分区和提升层级都可以提高索引编制速度。
+ **索引架构** - 添加字段和向字段添加更多属性（例如“可搜索”、“可分面”或“可筛选”）都会降低索引编制速度。
+ **批大小** - 最佳批大小因索引架构和数据集而异。
+ **线程数/工作器数** - 单个线程不能充分利用索引编制速度
+ **重试策略** - 应使用指数回退重试策略来优化索引编制。
+ **网络数据传输速度** - 数据传输速度可能是一个限制因素。 从 Azure 环境中为数据编制索引可以提高数据传输速度。


## <a name="1---create-azure-cognitive-search-service"></a>1 - 创建 Azure 认知搜索服务

若要完成本教程，你需要有一个 Azure 认知搜索服务，该服务可以[在门户中创建](search-create-service-portal.md)。 建议使用打算在生产中使用的同一层级，以便能够准确地测试和优化索引编制速度。

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>获取 Azure 认知搜索的管理 API 密钥和 URL

API 调用需要服务 URL 和访问密钥。 搜索服务是使用这二者创建的，因此，如果向订阅添加了 Azure 认知搜索，则请按以下步骤获取必需信息：

1. [登录到 Azure 门户](https://portal.azure.com/)，在搜索服务的“概述”页中获取 URL。 示例终结点可能类似于 `https://mydemo.search.windows.net`。

1. 在“设置” > “密钥”中，获取有关该服务的完全权限的管理员密钥 。 有两个可交换的管理员密钥，为保证业务连续性而提供，以防需要滚动一个密钥。 可以在请求中使用主要或辅助密钥来添加、修改和删除对象。

   ![获取 HTTP 终结点和访问密钥](media/search-get-started-postman/get-url-key.png "获取 HTTP 终结点和访问密钥")

## <a name="2---set-up-your-environment"></a>2 - 设置环境

1. 启动 Visual Studio 并打开“OptimizeDataIndexing.sln”。
1. 在解决方案资源管理器中，打开“appsettings.json”以提供连接信息。
1. 对于 `searchServiceName`，如果完整 URL 为“https://my-demo-service.search.windows.net”，则要提供的服务名称为“my-demo-service”。

```json
{
  "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
  "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
  "SearchIndexName": "optimize-indexing"
}
```

## <a name="3---explore-the-code"></a>3 - 浏览代码

更新 appsettings.json 后，**OptimizeDataIndexing.sln** 中的示例程序应当已做好了生成和运行准备工作。

此代码是从 [C# 快速入门](search-get-started-dotnet.md)派生的。 可以在那篇文章中更详细地了解使用 .NET SDK 的基础知识。

此简单的 C#/.NET 控制台应用程序执行以下任务：

+ 基于 C# Hotel 类（此类还引用 Address 类）的数据结构创建新索引。
+ 测试各种批大小以确定最高效的大小
+ 以异步方式为数据编制索引
    + 使用多个线程以提高索引编制速度
    + 使用指数回退重试策略重试失败的项

 运行该程序之前，请抽时间研究此示例的代码和索引定义。 相关代码存在于多个文件中：

  + **Hotel.cs** 和 **Address.cs** 包含用于定义索引的架构
  + **DataGenerator.cs** 包含一个简单的类，可轻松创建大量的酒店数据
  + **ExponentialBackoff.cs** 包含用于优化索引编制过程的代码，如下所述
  + **Program.cs** 包含用于创建和删除 Azure 认知搜索索引、为各批数据编制索引以及测试不同批大小的函数

### <a name="creating-the-index"></a>创建索引

此示例程序使用 .NET SDK 来定义和创建 Azure 认知搜索索引。 它利用 [FieldBuilder](/dotnet/api/microsoft.azure.search.fieldbuilder) 类，从 C# 数据模型类来生成索引结构。

数据模型由 Hotel 类定义，该类还包含对 Address 类的引用。 FieldBuilder 向下钻取多个类定义，从而为索引生成复杂的数据结构。 元数据标记用于定义每个字段的属性，例如字段是否可搜索或可排序。

以下 Hotel.cs 文件中的片段显示如何指定单个字段和对另一个数据模型类的引用。

```csharp
. . .
[IsSearchable, IsSortable]
public string HotelName { get; set; }
. . .
public Address Address { get; set; }
. . .
```

在 Program.cs 文件中，索引是使用由 `FieldBuilder.BuildForType<Hotel>()` 方法生成的名称和字段集合来定义的，并按下示方法创建：

```csharp
private static async Task CreateIndex(string indexName, SearchServiceClient searchService)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address class is referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    await searchService.Indexes.CreateAsync(definition);
}
```

### <a name="generating-data"></a>生成数据

**DataGenerator.cs** 文件中实现了一个简单的类，该类可生成用于测试的数据。 这个类的唯一用途是轻松生成具有唯一 ID 的大量文档以编制索引。

若要获取具有唯一 ID 的 100,000 家酒店的列表，请运行以下两行代码：

```csharp
DataGenerator dg = new DataGenerator();
List<Hotel> hotels = dg.GetHotels(100000, "large");
```

在此示例中，有两种大小的酒店可用于测试：**small** 和 **large**。

索引的架构可能会对索引编制速度产生重大影响。 由于存在此影响，因此，在完成本教程之后，有必要对该类进行转换以生成与预期的索引架构匹配的数据。

## <a name="4---test-batch-sizes"></a>4 - 测试批大小

Azure 认知搜索支持使用以下 API 将单个或多个文档加载到索引中：

+ [Add, Update, or Delete Documents (REST API)](/rest/api/searchservice/AddUpdate-or-Delete-Documents)（添加、更新或删除文档 (REST API)）
+ [indexAction 类](/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet)或 [indexBatch 类](/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet)

分批为文档编制索引可显著提高索引编制性能。 这些批中的每一批最多可以包含 1000 个文档或大约 16 MB。

确定数据的最佳批大小是优化索引编制速度的关键。 影响最佳批大小的两个主要因素是：

+ 索引的架构
+ 数据的大小

因为最佳批大小取决于你的索引和数据，所以最好的方法是测试不同的批大小，以确定哪个批大小可以为你的方案实现最快的索引编制速度。

以下函数演示了一种用于测试批大小的简单方法。

```csharp
public static async Task TestBatchSizes(ISearchIndexClient indexClient, int min = 100, int max = 1000, int step = 100, int numTries = 3)
{
    DataGenerator dg = new DataGenerator();

    Console.WriteLine("Batch Size \t Size in MB \t MB / Doc \t Time (ms) \t MB / Second");
    for (int numDocs = min; numDocs <= max; numDocs += step)
    {
        List<TimeSpan> durations = new List<TimeSpan>();
        double sizeInMb = 0.0;
        for (int x = 0; x < numTries; x++)
        {
            List<Hotel> hotels = dg.GetHotels(numDocs, "large");

            DateTime startTime = DateTime.Now;
            await UploadDocuments(indexClient, hotels);
            DateTime endTime = DateTime.Now;
            durations.Add(endTime - startTime);

            sizeInMb = EstimateObjectSize(hotels);
        }

        var avgDuration = durations.Average(timeSpan => timeSpan.TotalMilliseconds);
        var avgDurationInSeconds = avgDuration / 1000;
        var mbPerSecond = sizeInMb / avgDurationInSeconds;

        Console.WriteLine("{0} \t\t {1} \t\t {2} \t\t {3} \t {4}", numDocs, Math.Round(sizeInMb, 3), Math.Round(sizeInMb / numDocs, 3), Math.Round(avgDuration, 3), Math.Round(mbPerSecond, 3));

        // Pausing 2 seconds to let the search service catch its breath
        Thread.Sleep(2000);
    }
}
```

因为并非所有文档都大小相同（尽管本示例中如此），所以我们要估计发送到搜索服务的数据的大小。 我们使用下面的函数进行估计，首先将对象转换为 json，然后确定其大小（以字节为单位）。 利用此技术，我们可以根据 MB/秒的索引编制速度来确定哪些批大小是最高效的。

```csharp
public static double EstimateObjectSize(object data)
{
    // converting data to json for more accurate sizing
    var json = JsonConvert.SerializeObject(data);

    // converting object to byte[] to determine the size of the data
    BinaryFormatter bf = new BinaryFormatter();
    MemoryStream ms = new MemoryStream();
    byte[] Array;

    bf.Serialize(ms, json);
    Array = ms.ToArray();

    // converting from bytes to megabytes
    double sizeInMb = (double)Array.Length / 1000000;

    return sizeInMb;
}
```

此函数需要一个 `ISearchIndexClient` 以及要为每个批大小测试的尝试次数。 由于每个批次的索引编制时间可能会有一些变化，因此我们默认情况下会将每个批次尝试三次，以使结果更具统计意义。

```csharp
await TestBatchSizes(indexClient, numTries: 3);
```

运行此函数时，控制台中应会显示如下所示的输出：

   ![测试批大小函数的输出](media/tutorial-optimize-data-indexing/test-batch-sizes.png "测试批大小函数的输出")

确定最高效的批大小，然后在教程的下一步中使用该批大小。 你可能会发现不同批大小的索引编制速度（MB/秒）差异不大。

## <a name="5---index-data"></a>5 - 为数据编制索引

现在我们已经确定了要使用的批大小，下一步就是开始为数据编制索引。 为了高效地为数据编制索引，此示例：

* 使用了多个线程/工作器。
* 实现了指数回退重试策略。

### <a name="use-multiple-threadsworkers"></a>使用多个线程/工作器

若要充分利用 Azure 认知搜索的索引编制速度，你可能需要使用多个线程将许多批量编制索引请求并发发送到该服务。  

上面提到的几个重要注意事项会影响最佳线程数。 你可以修改此示例并测试不同的线程数，以确定适合你的方案的最佳线程数。 但是，只要有多个线程并发运行，就应该能够利用大部分提升的效率。

当你增加命中搜索服务的请求时，可能会遇到表示请求没有完全成功的 [HTTP 状态代码](/rest/api/searchservice/http-status-codes)。 在编制索引期间，有两个常见的 HTTP 状态代码：

+ **503 服务不可用** - 此错误表示系统负载过重，当前无法处理请求。
+ **207 多状态** - 此错误意味着某些文档成功，但至少一个文档失败。

### <a name="implement-an-exponential-backoff-retry-strategy"></a>实现指数回退重试策略

如果失败，则应使用[指数回退重试策略](/dotnet/architecture/microservices/implement-resilient-applications/implement-retries-exponential-backoff)来重试请求。

Azure 认知搜索的 .NET SDK 会自动重试 503 和其他失败的请求，但你需要实现自己的逻辑来重试 207。 还可以使用 [Polly](https://github.com/App-vNext/Polly) 等开源工具来实现重试策略。 

在此示例中，我们实现了自己的指数回退重试策略。 为了实现此策略，我们首先定义一些变量，包括失败请求的 `maxRetryAttempts` 和初始 `delay`：

```csharp
// Create batch of documents for indexing
IndexBatch<Hotel> batch = IndexBatch.Upload(hotels);

// Define parameters for exponential backoff
int attempts = 0;
TimeSpan delay = delay = TimeSpan.FromSeconds(2);
int maxRetryAttempts = 5;
```

请务必捕获 [IndexBatchException](/dotnet/api/microsoft.azure.search.indexbatchexception?view=azure-dotnet)，因为这些异常指示索引编制操作仅部分成功 (207)。 应使用 `FindFailedActionsToRetry` 方法重试失败的项，该方法可以轻松创建仅包含失败项的新批次。

还应当捕获 `IndexBatchException` 以外的异常，并指示请求完全失败。 这些异常不太常见，特别是在 .NET SDK 中，因为它会自动重试 503。

```csharp
// Implement exponential backoff
do
{
    try
    {
        attempts++;
        var response = await indexClient.Documents.IndexAsync(batch);
        break;
    }
    catch (IndexBatchException ex)
    {
        Console.WriteLine("[Attempt: {0} of {1} Failed] - Error: {2}", attempts, maxRetryAttempts, ex.Message);

        if (attempts == maxRetryAttempts)
            break;

        // Find the failed items and create a new batch to retry
        batch = ex.FindFailedActionsToRetry(batch, x => x.HotelId);
        Console.WriteLine("Retrying failed documents using exponential backoff...\n");

        Task.Delay(delay).Wait();
        delay = delay * 2;
    }
    catch (Exception ex)
    {
        Console.WriteLine("[Attempt: {0} of {1} Failed] - Error: {2} \n", attempts, maxRetryAttempts, ex.Message);

        if (attempts == maxRetryAttempts)
            break;

        Task.Delay(delay).Wait();
        delay = delay * 2;
    }
} while (true);
```

在这里，我们将指数回退代码包装到一个函数中，以便可以轻松调用它。

然后创建另一个函数来管理活动线程。 为简单起见，此处未包括该函数，但你可在 [ExponentialBackoff.cs](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/optimize-data-indexing/OptimizeDataIndexing/ExponentialBackoff.cs) 中找到该函数。 可以通过以下命令调用该函数，其中，`hotels` 是要上传的数据，`1000` 是批大小，`8` 是并发线程数：

```csharp
ExponentialBackoff.IndexData(indexClient, hotels, 1000, 8).Wait();
```

运行该函数时，应会显示如下所示的输出：

![数据索引编制函数的输出](media/tutorial-optimize-data-indexing/index-data-start.png "数据索引编制函数的输出")

当一批文档失败时，将会输出一条错误消息，指出已失败并且正在重试该批次：

![来自数据索引编制函数的错误](media/tutorial-optimize-data-indexing/index-data-error.png "测试批大小函数的输出")

在函数完成运行后，你可以验证是否所有文档都已添加到索引中。

## <a name="6---explore-index"></a>6 - 浏览索引

在程序运行后，你可以采用编程方式或者使用门户中的[搜索浏览器](search-explorer.md)来浏览已填充的搜索索引。

### <a name="programatically"></a>采用编程方式

可以使用两个主要选项来检查索引中的文档数：[对文档计数 API](/rest/api/searchservice/count-documents) 和[获取索引统计信息 API](/rest/api/searchservice/get-index-statistics)。 这两个路径可能都需要额外花费时间进行更新，因此，如果返回的文档数低于你最初预计的值，请不要惊慌。

#### <a name="count-documents"></a>计数文档

“对文档计数”操作检索某个搜索索引中文档的计数：

```csharp
long indexDocCount = indexClient.Documents.Count();
```

#### <a name="get-index-statistics"></a>获取索引统计信息

“获取索引统计信息”操作会返回当前索引的文档计数以及存储使用情况。 索引统计信息更新需要花费比文档计数更新更多的时间。

```csharp
IndexGetStatisticsResult indexStats = serviceClient.Indexes.GetStatistics(configuration["SearchIndexName"]);
```

### <a name="azure-portal"></a>Azure 门户

在 Azure 门户中，打开搜索服务的“概览”页，在“索引”列表中找到“optimize-indexing”索引  。

  ![Azure 认知搜索索引列表](media/tutorial-optimize-data-indexing/portal-output.png "Azure 认知搜索索引列表")

“文档计数”和“存储大小”基于[获取索引统计信息 API](/rest/api/searchservice/get-index-statistics)，可能需要花费几分钟时间进行更新。

## <a name="reset-and-rerun"></a>重置并重新运行

在开发的前期试验阶段，设计迭代的最实用方法是，删除 Azure 认知搜索中的对象，并允许代码重新生成它们。 资源名称是唯一的。 删除某个对象后，可以使用相同的名称重新创建它。

本教程的示例代码会检查现有索引并将其删除，使你能够重新运行代码。

还可以使用门户来删除索引。

## <a name="clean-up-resources"></a>清理资源

在自己的订阅中操作时，最好在项目结束时删除不再需要的资源。 持续运行资源可能会产生费用。 可以逐个删除资源，也可以删除资源组以删除整个资源集。

可以使用左侧导航窗格中的“所有资源”或“资源组”链接 ，在门户中查找和管理资源。

## <a name="next-steps"></a>后续步骤

现在你已熟悉了高效引入数据的概念，接下来让我们更详细地了解 Lucene 查询体系结构，以及在 Azure 认知搜索中全文搜索如何工作。

> [!div class="nextstepaction"]
> [Azure 认知搜索中全文搜索的工作原理](search-lucene-query-architecture.md)