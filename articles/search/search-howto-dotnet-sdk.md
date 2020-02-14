---
title: 在 .NET 中使用 Azure 认知搜索
titleSuffix: Azure Cognitive Search
description: 了解如何在 .NET 应用程序中使用C#和 .net SDK 使用 Azure 认知搜索。 基于代码的任务包括连接到服务，为内容编制索引，以及查询索引。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: dotnet
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b31a4e40c1e9095499faf265673ab4213ad6bde0
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190971"
---
# <a name="how-to-use-azure-cognitive-search-from-a-net-application"></a>如何从 .NET 应用程序使用 Azure 认知搜索

本文介绍了如何使用[Azure 认知搜索 .NET SDK](https://aka.ms/search-sdk)启动和运行。 使用 Azure 认知搜索，可以使用 .NET SDK 在应用程序中实现丰富的搜索体验。

## <a name="whats-in-the-azure-cognitive-search-sdk"></a>Azure 认知搜索 SDK 中的内容
SDK 包括一些客户端库。借助它，不仅可以管理索引、数据源、索引器和同义词映射，还能上传和管理文档并执行查询，所有这些操作都无需处理 HTTP 和 JSON 的详细信息。 这些客户端库全部作为 NuGet 包进行分发。

主 NuGet 包是 `Microsoft.Azure.Search`，它是一个元包，包括所有作为依赖关系的其他程序包。 如果你刚开始使用，或者知道你的应用程序将需要 Azure 认知搜索的所有功能，请使用此包。

SDK 中的其他 NuGet 程序包有：
 
  - `Microsoft.Azure.Search.Data`：如果要使用 Azure 认知搜索开发 .NET 应用程序，并且只需查询或更新索引中的文档，请使用此包。 如果还需要创建或更新索引、同义词映射或其他服务级资源，请改用 `Microsoft.Azure.Search` 包。
  - `Microsoft.Azure.Search.Service`：如果你要在 .NET 中开发自动化来管理 Azure 认知搜索索引、同义词映射、索引器、数据源或其他服务级别资源，请使用此包。 如果只需要查询或更新索引中的文档，请改用 `Microsoft.Azure.Search.Data` 包。 如果需要 Azure 认知搜索的所有功能，请改用 `Microsoft.Azure.Search` 包。
  - `Microsoft.Azure.Search.Common`： Azure 认知搜索 .NET 库所需的通用类型。 无需在应用程序中直接使用此包。 它仅用于作为依赖项。

各种客户端库定义 `Index`、`Field` 和 `Document` 等类，以及 `Indexes.Create` 和 `Documents.Search` 类中的 `SearchServiceClient` 和 `SearchIndexClient` 等操作。 这些类已组织成以下命名空间：

* [Microsoft.Azure.Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

如果你想要为 SDK 的未来更新提供反馈，请参阅我们的[反馈页面](https://feedback.azure.com/forums/263029-azure-search/)或在[GitHub](https://github.com/azure/azure-sdk-for-net/issues)上创建问题，并在问题标题中提到 "Azure 认知搜索"。

.NET SDK 支持[Azure 认知搜索 REST API](https://docs.microsoft.com/rest/api/searchservice/)`2019-05-06` 版本。 此版本包括对 Azure Blob 编制索引时对[复杂类型](search-howto-complex-data-types.md)、 [AI 扩充](cognitive-search-concept-intro.md)、[自动完成](https://docs.microsoft.com/rest/api/searchservice/autocomplete)和[JsonLines 分析模式](search-howto-index-json-blobs.md)的支持。 

此 SDK 不支持[管理操作](https://docs.microsoft.com/rest/api/searchmanagement/)（如创建和缩放搜索服务以及管理 API 密钥）。 如果需要从 .NET 应用程序管理搜索资源，可以使用[Azure 认知搜索 .Net 管理 SDK](https://aka.ms/search-mgmt-sdk)。

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>升级到最新版本的 SDK
如果你已在使用较旧版本的 Azure 认知搜索 .NET SDK，并且想要升级到最新的正式发布版本，则[本文](search-dotnet-sdk-migration-version-9.md)介绍了如何操作。

## <a name="requirements-for-the-sdk"></a>SDK 的要求
1. Visual Studio 2017 或更高版本。
2. 你自己的 Azure 认知搜索服务。 要使用 SDK，需要服务的名称以及一个或多个 API 密钥。 [在门户中创建服务](search-create-service-portal.md)将帮助你完成这些步骤。
3. 在 Visual Studio 中使用 "管理 NuGet 包" 下载 Azure 认知搜索 .NET SDK [NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.Search)。 只需在 NuGet.org 上搜索程序包名称 `Microsoft.Azure.Search`（或者如果你只需要其中一部分功能，则可以搜索上述其中一个其他程序包名称）。

Azure 认知搜索 .NET SDK 支持面向 .NET Framework 4.5.2 和更高版本的应用程序，以及 .NET Core 2.0 和更高版本。

## <a name="core-scenarios"></a>核心方案
需要在搜索应用程序中完成几项操作。 在本教程中，我们介绍以下核心方案：

* 创建索引
* 使用文档填充索引
* 使用全文搜索和筛选器搜索文档

下面的示例代码演示了每种情况。 可随意在自己的应用程序中使用代码片段。

### <a name="overview"></a>概述
我们将要探索的示例应用程序会创建一个名为“hotels”的新索引、用几个文档对该索引进行填充，并执行一些搜索查询。 以下是主程序，演示总体流程：

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    string indexName = configuration["SearchIndexName"];

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteIndexIfExists(indexName, serviceClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateIndex(indexName, serviceClient);

    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);

    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);

    ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

    RunQueries(indexClientForQueries);

    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
    Console.ReadKey();
}
```

> [!NOTE]
> 可以在 [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) 上找到本演练中所用示例应用程序的完整源代码。
> 
>

我们将逐步进行介绍。 首先，我们需要创建一个新的 `SearchServiceClient`。 使用此对象，可以管理索引。 若要构造一个，需要提供 Azure 认知搜索服务名称以及管理 API 密钥。 可以在`appsettings.json`示例应用程序[的 ](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) 文件中输入此信息。

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

> [!NOTE]
> 如果提供了不正确的密钥（例如，需要管理密钥的查询密钥），首次调用 `SearchServiceClient` 之类上的操作方法时，`CloudException` 会引发 `Indexes.Create`（错误消息为“已禁用”）。 如果遇到此情况，请仔细检查我们的 API 密钥。
> 
> 

以下几行调用方法来创建名为“hotels”的索引，如果该索引已存在，请事先删除它。 我们稍后会介绍这些方法。

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, serviceClient);
```

接下来，需要填充索引。 若要填充索引，需要 `SearchIndexClient`。 有两种方法可获取一个：构建它，或调用 `Indexes.GetClient` 上的 `SearchServiceClient`。 为方便起见，我们使用后者。

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
```

> [!NOTE]
> 在典型的搜索应用程序中，索引管理和填充可能由单独的组件与搜索查询进行处理。 `Indexes.GetClient` 可以方便地填充索引，因为这样可以节省提供额外 `SearchCredentials`的麻烦。 它通过向新 `SearchServiceClient` 传递用于创建 `SearchIndexClient` 的管理密钥来实现此目的。 但是，在执行查询的应用程序中，最好直接创建 `SearchIndexClient`，以便可以传入只允许读取数据的查询密钥，而不是管理密钥。 这与最小特权原则一致，可帮助使应用程序更安全。 可在[此处](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization)了解有关管理密钥和查询密钥的详细信息。
> 
> 

现在，我们已有 `SearchIndexClient`，可填充索引。 索引填充由我们稍后将介绍的另一种方法完成。

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

最后，我们执行一些搜索查询，并显示结果。 这次我们使用不同的 `SearchIndexClient`：

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(indexName, configuration);

RunQueries(indexClientForQueries);
```

我们会在以后详细地查看 `RunQueries` 方法。 下面是用于创建新 `SearchIndexClient` 的代码：

```csharp
private static SearchIndexClient CreateSearchIndexClient(string indexName, IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, indexName, new SearchCredentials(queryApiKey));
    return indexClient;
}
```

这次我们使用查询密钥，因为我们不需要对索引进行写访问。 可以在`appsettings.json`示例应用程序[的 ](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) 文件中输入此信息。

如果使用有效服务名称和 API 密钥运行此应用程序，输出应如下例所示：（某些控制台输出已替换为 "..."出于说明目的。）

    Deleting index...

    Creating index...

    Uploading documents...

    Waiting for documents to be indexed...

    Search the entire index for the term 'motel' and return only the HotelName field:

    Name: Secret Point Motel

    Name: Twin Dome Motel


    Apply a filter to the index to find hotels with a room cheaper than $100 per night, and return the hotelId and description:

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Times Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.

    HotelId: 2
    Description: The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.


    Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

    Name: Triple Landscape Hotel
    Last renovated on: 9/20/2015 12:00:00 AM +00:00

    Name: Twin Dome Motel
    Last renovated on: 2/18/1979 12:00:00 AM +00:00


    Search the hotel names for the term 'hotel':

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

    Complete.  Press any key to end application... 

本文末尾处提供了应用程序的完整源代码。

接下来，我们会详细介绍 `Main` 调用的每个方法。

### <a name="creating-an-index"></a>创建索引
创建 `SearchServiceClient`后，`Main` 删除 "宾馆" 索引（如果已存在）。 该删除操作通过以下方法完成：

```csharp
private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists(indexName))
    {
        serviceClient.Indexes.Delete(indexName);
    }
}
```

此方法使用给定的 `SearchServiceClient` 来检查索引是否存在，如果存在，则删除该索引。

> [!NOTE]
> 本文中的示例代码使用 Azure 认知搜索 .NET SDK 的同步方法。 建议用户在自己的应用程序中使用异步方法，使应用程序保持可缩放且响应迅速。 例如，在上述方法中，可以使用 `ExistsAsync` 和 `DeleteAsync`，而不是 `Exists` 和 `Delete`。
> 
> 

接下来，`Main` 通过调用如下方法创建新的“hotels”索引：

```csharp
private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    
    serviceClient.Indexes.Create(definition);
}
```

此方法使用用于定义新索引架构的一列 `Index` 对象创建新的 `Field` 对象。 每个字段都有名称、数据类型和数个属性（定义其搜索行为）。 `FieldBuilder` 类通过检查给定 `Field` 模型类的公共属性和特性，使用反射来为索引创建 `Hotel` 对象的列表。 我们会在以后详细地查看 `Hotel` 类。

> [!NOTE]
> 如果需要，始终可以直接创建 `Field` 对象的列表，而不是使用 `FieldBuilder`。 例如，你可能不想使用模型类，或者可能需要使用不希望通过添加属性来进行修改的现有模型类。
>
> 

除了字段外，还可以向索引中添加计分配置文件、建议器或 CORS 选项（为简洁起见，示例中省略了这些参数）。 可以在[SDK 参考](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index)中找到有关 Index 对象及其组成部分的详细信息，以及[Azure 认知搜索 REST API 参考](https://docs.microsoft.com/rest/api/searchservice/)。

### <a name="populating-the-index"></a>填充索引
`Main` 中的下一步将填充新创建的索引。 此索引填充通过以下方法完成：（某些代码替换为 "..."出于说明目的。  有关完整的数据填充代码，请参阅完整的示例解决方案。）

```csharp
private static void UploadDocuments(ISearchIndexClient indexClient)
{
    var hotels = new Hotel[]
    {
        new Hotel()
        {
            HotelId = "1",
            HotelName = "Secret Point Motel",
            ...
            Address = new Address()
            {
                StreetAddress = "677 5th Ave",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Budget Room, 1 Queen Bed (Cityside)",
                    ...
                },
                new Room()
                {
                    Description = "Budget Room, 1 King Bed (Mountain View)",
                    ...
                },
                new Room()
                {
                    Description = "Deluxe Room, 2 Double Beds (City View)",
                    ...
                }
            }
        },
        new Hotel()
        {
            HotelId = "2",
            HotelName = "Twin Dome Motel",
            ...
            {
                StreetAddress = "140 University Town Center Dr",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Suite, 2 Double Beds (Mountain View)",
                    ...
                },
                new Room()
                {
                    Description = "Standard Room, 1 Queen Bed (City View)",
                    ...
                },
                new Room()
                {
                    Description = "Budget Room, 1 King Bed (Waterfront View)",
                    ...
                }
            }
        },
        new Hotel()
        {
            HotelId = "3",
            HotelName = "Triple Landscape Hotel",
            ...
            Address = new Address()
            {
                StreetAddress = "3393 Peachtree Rd",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Standard Room, 2 Queen Beds (Amenities)",
                    ...
                },
                new Room ()
                {
                    Description = "Standard Room, 2 Double Beds (Waterfront View)",
                    ...
                },
                new Room()
                {
                    Description = "Deluxe Room, 2 Double Beds (Cityside)",
                    ...
                }
            }
        }
    };

    var batch = IndexBatch.Upload(hotels);

    try
    {
        indexClient.Documents.Index(batch);
    }
    catch (IndexBatchException e)
    {
        // Sometimes when your Search service is under load, indexing will fail for some of the documents in
        // the batch. Depending on your application, you can take compensating actions like delaying and
        // retrying. For this simple demo, we just log the failed document keys and continue.
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

    Console.WriteLine("Waiting for documents to be indexed...\n");
    Thread.Sleep(2000);
}
```

此方法有四个部分。 第一个创建一个数组，其中包含3个 `Hotel` 对象，每个对象都有3个 `Room` 对象，这些对象将用作输入数据上传到索引。 为简单起见，此数据是硬编码的。 在自己的应用程序中，数据可能来自外部数据源（如 SQL 数据库）。

第二部分创建包含文档的 `IndexBatch`。 创建 Batch 时，指定要应用到 Batch 的操作，在这种情况下应调用 `IndexBatch.Upload`。 然后，`Documents.Index` 方法将该批次上载到 Azure 认知搜索索引。

> [!NOTE]
> 在本示例中，我们只需上传文档。 如果想要将更改合并到现有文档或删除文档，可以改为调用 `IndexBatch.Merge`、`IndexBatch.MergeOrUpload` 或 `IndexBatch.Delete` 创建 Batch。 你还可以通过调用 `IndexBatch.New`来混合单个批处理中的不同操作，此操作采用 `IndexAction` 对象的集合，其中每个对象都通知 Azure 认知搜索对文档执行特定操作。 可以通过调用 `IndexAction`、`IndexAction.Merge` 之类的相应方法，创建自带操作的每个 `IndexAction.Upload`。
> 
> 

此方法的第三部分是处理索引重要错误情况的 catch 块。 如果 Azure 认知搜索服务未能为批处理中的某些文档编制索引，则 `Documents.Index`会引发 `IndexBatchException`。 如果在服务的负载较重时对文档编制索引，则会发生此异常。 **强烈建议在代码中显式处理这种情况。** 可以延迟为失败的文档编制索引，并重试，也可以像此示例一样记录并继续执行，还可以执行其他操作，具体取决于应用程序对数据一致性的要求。

> [!NOTE]
> 可以使用 [`FindFailedActionsToRetry`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception.findfailedactionstoretry) 方法来构造一个新的批处理，其中仅包含上次调用 `Index` 时失败的操作。 在 [StackOverflow](https://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry) 上有如何正确使用该方法的讨论。
>
>

最后，`UploadDocuments` 方法延迟了两秒钟。 索引在 Azure 认知搜索服务中异步执行，因此，示例应用程序需要等待一小段时间才能确保文档可用于搜索。 此类延迟通常仅在演示、测试和示例应用程序中是必需的。

<a name="how-dotnet-handles-documents"></a>

#### <a name="how-the-net-sdk-handles-documents"></a>.NET SDK 如何处理文档
你可能想知道 Azure 认知搜索 .NET SDK 如何能够将用户定义的类（如 `Hotel`）的实例上传到索引。 为了帮助回答这个问题，请看 `Hotel` 类：

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsSearchable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.EnLucene)]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("Description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    // SmokingAllowed reflects whether any room in the hotel allows smoking.
    // The JsonIgnore attribute indicates that a field should not be created 
    // in the index for this property and it will only be used by code in the client.
    [JsonIgnore]
    public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? Rating { get; set; }

    public Address Address { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }

    public Room[] Rooms { get; set; }
}
```

首先要注意的是，`Hotel` 类中的每个公共属性的名称将映射到索引定义中名称相同的字段。 如果希望每个字段都以小写字母开头（"camel 大小写"），则可以告知 SDK 自动将属性名称映射为 camel 大小写大小写，并使用类上的 `[SerializePropertyNamesAsCamelCase]` 属性。 这种情况在 .NET 应用程序中很常见，它们执行数据绑定，其中目标架构不在应用程序开发人员的控制范围内，而不违反 .NET 中的 "Pascal 大小写" 命名准则。

> [!NOTE]
> Azure 认知搜索 .NET SDK 使用[newtonsoft.json JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)库将自定义模型对象序列化和反序列化为 JSON。 如果需要，可以自定义此序列化。 有关详细信息，请参阅[JSON.NET 的自定义序列化](#JsonDotNet)。
> 
> 

要注意的第二个问题是，每个属性都由 `IsFilterable`、`IsSearchable`、`Key`和 `Analyzer`等属性修饰。 这些属性直接映射到[Azure 认知搜索索引中的相应字段属性](/rest/api/searchservice/create-index)。 `FieldBuilder` 类使用这些属性来构造索引的字段定义。

关于 `Hotel` 类的第三个重要事项是公共属性的数据类型。 这些属性的 .NET 类型映射到它们在索引定义中的等效字段类型。 例如，`Category` 字符串属性映射到 `category` 类型的 `Edm.String` 字段。 `bool?`、`Edm.Boolean`、`DateTimeOffset?`和 `Edm.DateTimeOffset` 等之间存在相似的类型映射。 类型映射的特定规则随[Azure 认知搜索 .NET SDK 参考](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get)中的 `Documents.Get` 方法一起记录。 `FieldBuilder` 类会处理此映射，但你最好还是了解此映射，以便在需要排查任何序列化问题时可以下手。

您是否曾经注意到 `SmokingAllowed` 属性？

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

此属性的 `JsonIgnore` 特性告知 `FieldBuilder` 不将其作为字段序列化到索引。  这是创建客户端计算属性的好方法，可在应用程序中将其用作帮助程序。  在这种情况下，`SmokingAllowed` 属性反映了 `Rooms` 集合中的任何 `Room` 是否允许吸烟。  如果 all 均为 false，则表示整个酒店不允许吸烟。

某些属性（如 `Address` 和 `Rooms`）是 .NET 类的实例。  这些属性表示更复杂的数据结构，因此，需要索引中包含[复杂数据类型](https://docs.microsoft.com/azure/search/search-howto-complex-data-types)的字段。

`Address` 属性表示 `Address` 类中的一组多个值，定义如下：

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
{
    public partial class Address
    {
        [IsSearchable]
        public string StreetAddress { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string City { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string StateProvince { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string PostalCode { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string Country { get; set; }
    }
}
```

此类包含用于描述美国或加拿大的地址的标准值。 您可以使用类似于这样的类型在索引中将逻辑字段组合在一起。

`Rooms` 属性表示 `Room` 对象的数组：

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
{
    public partial class Room
    {
        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrMicrosoft)]
        [JsonProperty("Description_fr")]
        public string DescriptionFr { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string Type { get; set; }

        [IsFilterable, IsFacetable]
        public double? BaseRate { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string BedOptions { get; set; }

        [IsFilterable, IsFacetable]
        public int SleepsCount { get; set; }

        [IsFilterable, IsFacetable]
        public bool? SmokingAllowed { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string[] Tags { get; set; }
    }
}
```

.NET 中的数据模型及其相应的索引架构应设计为支持你想要提供给最终用户的搜索体验。 .NET 中的每个顶层对象（即索引中的 ie 文档）都对应于你在用户界面中显示的搜索结果。 例如，在旅馆搜索应用程序中，你的最终用户可能想要按酒店名称、宾馆的功能或特定房间的特征进行搜索。 稍后我们将介绍一些查询示例。

使用自己的类与索引中的文档进行交互的这一功能在这两个方向上都有效。你还可以检索搜索结果，并让 SDK 自动将它们反序列化为你选择的类型，如下一节中所示。

> [!NOTE]
> Azure 认知搜索 .NET SDK 还支持使用 `Document` 类的动态类型化文档，这是字段名称到字段值的键/值映射。 如果在设计时不知道索引架构，或者绑定到特定模型类不太方便，这很有用。 该 SDK 中处理文档的所有方法都有使用 `Document` 类的重载，以及采用泛型类型参数的强类型重载。 本教程中的示例代码仅使用后者。 [`Document` 类](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document)继承自 `Dictionary<string, object>`。
> 
>

**为何应使用可为 null 的数据类型**

在设计自己的模型类以便映射到 Azure 认知搜索索引时，建议声明值类型的属性，例如 `bool` 和 `int` 可以为 null （例如，`bool?` 而不是 `bool`）。 如果使用不可为 null 属性，必须 **保证** 索引中的所有文档的对应字段都不包含 null 值。 SDK 和 Azure 认知搜索服务均不会帮助你强制执行此工作。

这不只是假想的问题：假设将新字段添加到 `Edm.Int32`类型的现有索引。 更新索引定义后，所有文档的新字段都将具有 null 值（因为 Azure 认知搜索中的所有类型都可以为 null）。 如果随后使用该字段具有不可为 null `int` 属性的模型类，则在尝试检索文档时会获得如下所示的 `JsonSerializationException`：

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

由于此原因，最佳做法是建议在模型类中使用可以为 null 的类型。

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>使用 JSON.NET 的自定义序列
SDK 使用 JSON.NET 对文档进行序列化和反序列化。 如果需要，可以通过定义自己的 `JsonConverter` 或 `IContractResolver`自定义序列化和反序列化。 有关详细信息，请参阅[JSON.NET 文档](https://www.newtonsoft.com/json/help/html/Introduction.htm)。 如果要将应用程序中的现有模型类调整为适用于 Azure 认知搜索和其他更高级的方案，这会很有用。 例如，使用自定义序列，可以：

* 包含或排除模型类的某些属性作为文档字段存储。
* 在代码中的属性名称与索引中的字段名称之间进行映射。
* 创建可用于将属性映射到文档字段的自定义属性。

可以在 GitHub 上的 Azure 认知搜索 .NET SDK 的单元测试中找到实现自定义序列化的示例。 一个好的起点是[此文件夹](https://github.com/Azure/azure-sdk-for-net/tree/4f6f4e4c90200c1b0621c4cead302a91e89f2aba/sdk/search/Microsoft.Azure.Search/tests/Tests/Models)。 它包含了自定义序列化测试所用的类。

### <a name="searching-for-documents-in-the-index"></a>在索引中搜索文档
示例应用程序的最后一步是在索引中搜索某些文档：

```csharp
private static void RunQueries(ISearchIndexClient indexClient)
{
    SearchParameters parameters;
    DocumentSearchResult<Hotel> results;

    Console.WriteLine("Search the entire index for the term 'motel' and return only the HotelName field:\n");

    parameters =
        new SearchParameters()
        {
            Select = new[] { "HotelName" }
        };

    results = indexClient.Documents.Search<Hotel>("motel", parameters);

    WriteDocuments(results);

    Console.Write("Apply a filter to the index to find hotels with a room cheaper than $100 per night, ");
    Console.WriteLine("and return the hotelId and description:\n");

    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(r: r/BaseRate lt 100)",
            Select = new[] { "HotelId", "Description" }
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    parameters =
        new SearchParameters()
        {
            OrderBy = new[] { "LastRenovationDate desc" },
            Select = new[] { "HotelName", "LastRenovationDate" },
            Top = 2
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.WriteLine("Search the entire index for the term 'hotel':\n");

    parameters = new SearchParameters();
    results = indexClient.Documents.Search<Hotel>("hotel", parameters);

    WriteDocuments(results);
}
```

每次执行查询时，此方法首先创建一个新的 `SearchParameters` 对象。 此对象用于为查询指定其他选项，如排序、筛选、分页和分面。 在此方法中，我们要为不同查询设置 `Filter`、`Select`、`OrderBy` 和 `Top` 属性。 所有 `SearchParameters` 属性在[此处](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters)进行了说明。

下一步是实际执行搜索查询。 使用 `Documents.Search` 方法来运行搜索。 对于每个查询，我们将要使用的搜索文本（或 `"*"`，如果没有搜索文本）作为字符串传递，还会传递先前创建的搜索参数。 此外，我们指定 `Hotel` 作为 `Documents.Search` 的类型参数，这指示 SDK 将搜索结果中的文档反序列化为类型为 `Hotel` 的对象。

> [!NOTE]
> 可在[此处](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search)找到有关搜索查询表达式语法的详细信息。
> 
> 

最后，在每个查询后，该方法循环访问搜索结果中的所有匹配项，将每个文档打印到控制台：

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

让我们依次仔细查看每个查询。 下面是用于执行第一个查询的代码：

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "HotelName" }
    };

results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

在这种情况下，我们将在任何可搜索字段中搜索 "motel" 一词的整个索引，并且我们只想检索由 `Select` 参数指定的酒店名称。 结果如下：

    Name: Secret Point Motel

    Name: Twin Dome Motel

下一个查询更有趣一些。  我们想要查找其房间低于 $100 的住宿，并仅返回酒店 ID 和说明：

```csharp
parameters =
    new SearchParameters()
    {
        Filter = "Rooms/any(r: r/BaseRate lt 100)",
        Select = new[] { "HotelId", "Description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

此查询使用 OData `$filter` 表达式 `Rooms/any(r: r/BaseRate lt 100)` 来筛选索引中的文档。 这将使用[any 运算符](https://docs.microsoft.com/azure/search/search-query-odata-collection-operators)将 "BaseRate lt 100" 应用于聊天室集合中的每个项。 可在[此处](https://docs.microsoft.com/azure/search/query-odata-filter-orderby-syntax)找到 Azure 认知搜索支持的 OData 语法的详细信息。

下面是查询的结果：

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York...

    HotelId: 2
    Description: The hotel is situated in a nineteenth century plaza, which has been expanded and renovated to...

接下来，我们想要查找最近翻修的前两个酒店，并显示酒店名称和上次翻修日期。 代码如下： 

```csharp
parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "LastRenovationDate desc" },
        Select = new[] { "HotelName", "LastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

在此示例中，我们再次使用 OData 语法来指定 `OrderBy` 参数作为 `lastRenovationDate desc`。 我们还将 `Top` 设置为 2 以确保仅获取前两个文档。 与以前一样，我们设置 `Select` 以指定应返回的字段。

结果如下：

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

最后，我们想要查找与 "宾馆" 一词匹配的所有酒店名称：

```csharp
parameters = new SearchParameters()
{
    SearchFields = new[] { "HotelName" }
};
results = indexClient.Documents.Search<Hotel>("hotel", parameters);

WriteDocuments(results);
```

以下是结果，它包含所有字段，因为我们未指定 `Select` 属性：

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

本教程到此步骤结束，但不要就此打住。 \* * 接下来的步骤提供了更多资源，用于了解有关 Azure 认知搜索的详细信息。

## <a name="next-steps"></a>后续步骤
* 浏览 [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) 和 [REST API](https://docs.microsoft.com/rest/api/searchservice/) 的参考资料。
* 查看[命名约定](https://docs.microsoft.com/rest/api/searchservice/Naming-rules)，了解命名各种对象的规则。
* 查看 Azure 认知搜索中[支持的数据类型](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types)。
