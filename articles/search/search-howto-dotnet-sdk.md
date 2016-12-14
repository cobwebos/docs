---
title: "如何使用 .NET 应用程序中的 Azure 搜索 | Microsoft Docs"
description: "如何使用 .NET 应用程序中的 Azure 搜索"
services: search
documentationcenter: 
author: brjohnstmsft
manager: pablocas
editor: 
ms.assetid: 93653341-c05f-4cfd-be45-bb877f964fcb
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/06/2016
ms.author: brjohnst
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 21bd4f05eabfd685cb87b819621fe8d826f209b5


---
# <a name="how-to-use-azure-search-from-a-net-application"></a>如何使用 .NET 应用程序中的 Azure 搜索
文本介绍了如何使用 [Azure 搜索 .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)。 可以使用 .NET SDK，在你的应用程序中使用 Azure 搜索实现丰富的搜索体验。

## <a name="whats-in-the-azure-search-sdk"></a>什么是 Azure 搜索 SDK
该 SDK 包含一个客户端库 `Microsoft.Azure.Search`。 它使你能够管理索引、数据源和索引器，以及上传和管理文档及执行查询，而无需处理 HTTP 和 JSON 的详细信息。

客户端库会定义 `Index`、`Field` 和 `Document` 之类的类，还会定义 `SearchServiceClient` 和 `SearchIndexClient` 类中的 `Indexes.Create` 和 `Documents.Search` 之类的操作。 这些类已组织成以下命名空间：

* [Microsoft.Azure.Search](https://msdn.microsoft.com/library/azure/microsoft.azure.search.aspx)
* [Microsoft.Azure.Search.Models](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.aspx)

Azure 搜索 .NET SDK 的当前版本现已正式发布。 如果想要提供反馈，供我们包含到下一个版本中，请访问我们的[反馈页](https://feedback.azure.com/forums/263029-azure-search/)。

.NET SDK 支持版本为 `2015-02-28` 的 Azure 搜索 REST API，[MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx) 上有相关介绍。 此版本现在包含对 Lucene 查询语法和 Microsoft 语言分析器的支持。 *未*包含在此版本中的较新功能（如对 `moreLikeThis` 搜索参数的支持）已存在于[预览版](search-api-2015-02-28-preview.md)中，但尚未在 SDK 中提供。 可以返回[搜索服务版本控制](https://msdn.microsoft.com/library/azure/dn864560.aspx)，检查任一功能的状态更新。

此 SDK 中不受支持的其他功能如下所示：

* [管理操作](https://msdn.microsoft.com/library/azure/dn832684.aspx)。 管理操作包括预配 Azure 搜索服务和管理 API 密钥。 以后，会在一个单独的 Azure 搜索 .NET 管理 SDK 中提供对上述操作的支持。

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>升级到最新版本的 SDK
如果你已在使用较旧版本的 Azure 搜索 .NET SDK，并且想要升级到最新的正式发布版本，[文本](search-dotnet-sdk-migration.md)介绍了操作方法。

## <a name="requirements-for-the-sdk"></a>SDK 的要求
1. Visual Studio 2013 或 Visual Studio 2015。
2. 你自己的 Azure 搜索服务。 要使用 SDK，需要你的服务的名称以及一个或多个 API 密钥。 [在门户中创建服务](search-create-service-portal.md)将帮助你完成这些步骤。
3. 在 Visual Studio 中，通过使用“管理 NuGet 程序包”来下载 Azure 搜索 .NET SDK [NuGet 程序包](http://www.nuget.org/packages/Microsoft.Azure.Search)。 只需在 NuGet.org 上搜索程序包名称 `Microsoft.Azure.Search`。

Azure 搜索 .NET SDK 支持面向 .NET Framework 4.5 的应用程序，以及面向 Windows 8.1 和 Windows Phone 8.1 的 Windows 应用商店应用。 Silverlight 不受支持。

## <a name="core-scenarios"></a>核心方案
需要在你的搜索应用程序中完成几项操作。 在本教程中，我们将介绍以下核心方案：

* 创建索引
* 使用文档填充索引
* 使用全文搜索和筛选器搜索文档

后续示例代码说明了其中的每一个。 可随意在你自己的应用程序中使用代码片段。

### <a name="overview"></a>概述
我们将要探索的示例应用程序会创建一个名为“hotels”的新索引、用几个文档对该索引进行填充，然后执行一些搜索查询。 以下是主程序，演示总体流程：

    // This sample shows how to delete, create, upload documents and query an index
    static void Main(string[] args)
    {
        // Put your search service name here. This is the hostname portion of your service URL.
        // For example, if your service URL is https://myservice.search.windows.net, then your
        // service name is myservice.
        string searchServiceName = "myservice";

        string apiKey = "Put your API admin key here.";

        SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

        Console.WriteLine("{0}", "Deleting index...\n");
        DeleteHotelsIndexIfExists(serviceClient);

        Console.WriteLine("{0}", "Creating index...\n");
        CreateHotelsIndex(serviceClient);

        ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

        Console.WriteLine("{0}", "Uploading documents...\n");
        UploadDocuments(indexClient);

        Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
        SearchDocuments(indexClient, searchText: "fancy wifi");

        Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
        SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

        Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
        Console.ReadKey();
    }

我们将逐步进行介绍。 首先，我们需要创建一个新的 `SearchServiceClient`。 此对象使你可以管理索引。 若要构建一个，需要提供你的 Azure 搜索服务名称以及管理 API 密钥。

        // Put your search service name here. This is the hostname portion of your service URL.
        // For example, if your service URL is https://myservice.search.windows.net, then your
        // service name is myservice.
        string searchServiceName = "myservice";

        string apiKey = "Put your API admin key here.";

        SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

> [!NOTE]
> 如果提供了不正确的密钥（例如，需要管理密钥的查询密钥），首次调用 `Indexes.Create` 之类上的操作方法时，`SearchServiceClient` 会引发 `CloudException`（错误消息为“已禁用”）。 如果你遇到此情况，请仔细检查我们的 API 密钥。
> 
> 

以下几行调用方法来创建名为“hotels”的索引，如果该索引已存在，请事先删除它。 我们稍后会介绍这些方法。

        Console.WriteLine("{0}", "Deleting index...\n");
        DeleteHotelsIndexIfExists(serviceClient);

        Console.WriteLine("{0}", "Creating index...\n");
        CreateHotelsIndex(serviceClient);

接下来，需要填充索引。 为此，我们需要 `SearchIndexClient`。 有两种方法可获取一个：构建它，或调用 `SearchServiceClient` 上的 `Indexes.GetClient`。 为方便起见，我们使用后者。

        ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

> [!NOTE]
> 在典型的搜索应用程序中，索引管理和填充由搜索查询中的一个单独的组件处理。 `Indexes.GetClient` 对于填充索引很方便，因为使用它则无需提供另一个 `SearchCredentials`。 它通过向新 `SearchIndexClient` 传递用于创建 `SearchServiceClient` 的管理密钥来实现此目的。 但是，在执行查询的应用程序中，最好直接创建 `SearchIndexClient` ，这样可以传入查询密钥而不是管理密钥。 这与最小特权原则一致，可帮助使应用程序更安全。 可在[此处](https://msdn.microsoft.com/library/azure/dn798935.aspx)了解有关管理密钥和查询密钥的详细信息。
> 
> 

现在，我们已有 `SearchIndexClient`，可填充索引。 执行此操作使用的是我们稍后会介绍的另一种方法。

        Console.WriteLine("{0}", "Uploading documents...\n");
        UploadDocuments(indexClient);

最后，我们再次使用 `SearchIndexClient` 执行一些搜索查询，并显示结果：

        Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
        SearchDocuments(indexClient, searchText: "fancy wifi");

        Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
        SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

        Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
        Console.ReadKey();

如果使用有效服务名称和 API 密钥运行此应用程序，输出应如下所示：

    Deleting index...

    Creating index...

    Uploading documents...

    Searching documents 'fancy wifi'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 956-532     Name: Express Rooms     Category: Budget        Tags: [wifi, budget]

    Filter documents with category 'Luxury'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 566-518     Name: Surprisingly Expensive Suites     Category: Luxury    Tags: []
    Complete.  Press any key to end application...

本文末尾处提供了应用程序的完整源代码。

接下来，我们会详细介绍 `Main` 调用的每个方法。

### <a name="creating-an-index"></a>创建索引
创建 `SearchServiceClient` 后，`Main` 执行的下一步操作是删除“hotels”索引（如果该索引已存在）。 使用以下方法完成此操作：

    private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
    {
        if (serviceClient.Indexes.Exists("hotels"))
        {
            serviceClient.Indexes.Delete("hotels");
        }
    }

此方法使用给定的 `SearchServiceClient` 来检查索引是否存在，如果存在，则删除该索引。

> [!NOTE]
> 为简单起见，本文中的示例代码使用 Azure 搜索 .NET SDK 的同步方法。 建议用户在自己的应用程序中使用异步方法，使应用程序保持可缩放且响应迅速。 例如，在上述方法中，可以使用 `ExistsAsync` 和 `DeleteAsync`，而不是 `Exists` 和 `Delete`。
> 
> 

接下来，`Main` 通过调用如下方法创建新的“hotels”索引：

    private static void CreateHotelsIndex(SearchServiceClient serviceClient)
    {
        var definition = new Index()
        {
            Name = "hotels",
            Fields = new[]
            {
                new Field("hotelId", DataType.String)                       { IsKey = true },
                new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true },
                new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true },
                new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
                new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
                new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
            }
        };

        serviceClient.Indexes.Create(definition);
    }

此方法使用用于定义新索引架构的一列 `Field` 对象创建新的 `Index` 对象。 每个字段都有名称、数据类型和数个属性（定义其搜索行为）。 除了字段，还可以向索引添加计分配置文件、建议器或 CORS 选项（为简洁起见，示例中省略了这些项）。 可在 [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.index_members.aspx) 上的 SDK 参考以及 [Azure 搜索 REST API 参考](https://msdn.microsoft.com/library/azure/dn798935.aspx)中，找到有关 Index 对象及其组成部分的详细信息。

### <a name="populating-the-index"></a>填充索引
`Main` 中的下一步是填充新创建的索引。 在以下方法中完成此操作：

    private static void UploadDocuments(ISearchIndexClient indexClient)
    {
        var documents =
            new Hotel[]
            {
                new Hotel()
                {
                    HotelId = "1058-441",
                    HotelName = "Fancy Stay",
                    BaseRate = 199.0,
                    Category = "Luxury",
                    Tags = new[] { "pool", "view", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                    Rating = 5,
                    Location = GeographyPoint.Create(47.678581, -122.131577)
                },
                new Hotel()
                {
                    HotelId = "666-437",
                    HotelName = "Roach Motel",
                    BaseRate = 79.99,
                    Category = "Budget",
                    Tags = new[] { "motel", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                    Rating = 1,
                    Location = GeographyPoint.Create(49.678581, -122.131577)
                },
                new Hotel()
                {
                    HotelId = "970-501",
                    HotelName = "Econo-Stay",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "pool", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(46.678581, -122.131577)
                },
                new Hotel()
                {
                    HotelId = "956-532",
                    HotelName = "Express Rooms",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "wifi", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(48.678581, -122.131577)
                },
                new Hotel()
                {
                    HotelId = "566-518",
                    HotelName = "Surprisingly Expensive Suites",
                    BaseRate = 279.99,
                    Category = "Luxury",
                    ParkingIncluded = false
                }
            };

        try
        {
            var batch = IndexBatch.Upload(documents);
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

        // Wait a while for indexing to complete.
        Thread.Sleep(2000);
    }

此方法有四个部分。 第一部分创建的一个 `Hotel` 对象数组，将用作我们上传到索引的输入数据。 为简单起见，此数据是硬编码的。 在你自己的应用程序中，数据可能来自外部数据源（如 SQL 数据库）。

第二部分创建包含文档的 `IndexBatch`。 创建 Batch 时，指定要应用到 Batch 的操作，在这种情况下应调用 `IndexBatch.Upload`。 然后，使用 `Documents.Index` 方法将 Batch 上传到 Azure 搜索索引。

> [!NOTE]
> 在本示例中，我们只需上传文档。 如果想要将更改合并到现有文档或删除文档，可以改为调用 `IndexBatch.Merge`、`IndexBatch.MergeOrUpload` 或 `IndexBatch.Delete` 创建 Batch。 还可以通过调用 `IndexBatch.New` 在单个 Batch 中混用不同操作，这会用到一个 `IndexAction` 对象的集合，其中的每个对象都会指示 Azure 搜索对文档执行特定操作。 可以通过调用 `IndexAction.Merge`、`IndexAction.Upload` 之类的相应方法，创建自带操作的每个 `IndexAction`。
> 
> 

此方法的第三部分是处理索引重要错误情况的 catch 块。 如果 Azure 搜索服务无法为 Batch 中的某些文档编制索引，`Documents.Index` 将引发 `IndexBatchException`。 如果在服务负载过大时为文档编制索引，可能会发生这种情况。 **强烈建议在代码中显式处理这种情况。**  可以延迟为失败的文档编制索引，然后重试，也可以像此示例一样记录并继续执行，还可以执行其他操作，具体取决于应用程序对数据一致性的要求。

最后，该方法延迟了两秒钟。 编制索引在 Azure 搜索服务中异步进行，因此，示例应用程序需要等待很短时间，以确保文档可用于搜索。 此类延迟通常仅在演示、测试和示例应用程序中是必需的。

#### <a name="how-the-net-sdk-handles-documents"></a>.NET SDK 如何处理文档
用户可能想知道 Azure 搜索 .NET SDK 如何将用户定义的类（如 `Hotel` ）的实例上载到索引。 为了帮助回答这个问题，请看 `Hotel` 类：

    [SerializePropertyNamesAsCamelCase]
    public class Hotel
    {
        public string HotelId { get; set; }

        public string HotelName { get; set; }

        public double? BaseRate { get; set; }

        public string Category { get; set; }

        public string[] Tags { get; set; }

        public bool? ParkingIncluded { get; set; }

        public DateTimeOffset? LastRenovationDate { get; set; }

        public int? Rating { get; set; }

        public GeographyPoint Location { get; set; }

        public override string ToString()
        {
            return String.Format(
                "ID: {0}\tName: {1}\tCategory: {2}\tTags: [{3}]",
                HotelId,
                HotelName,
                Category,
                (Tags != null) ? String.Join(", ", Tags) : String.Empty);
        }
    }

要注意的第一个问题是，`Hotel` 的每个公共属性均对应于索引定义中的一个字段，但有一个重要差异：每个字段的名称均以小写字母开头（“骆驼拼写法”），而 `Hotel` 的每个公共属性名称均以大写字母开头（“帕斯卡拼写法”）。 在执行目标架构不受应用程序开发人员控制的数据绑定的 .NET 应用程序中，这种情况很常见。 不必违反 .NET 命名准则将属性名设为 camel 大小写，而可以使用 `[SerializePropertyNamesAsCamelCase]` 属性指示 SDK 将属性名自动映射到 camel 大小写。

> [!NOTE]
> Azure 搜索 .NET SDK 使用 [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) 库将自定义模型对象序列化为 JSON 和从 JSON 反序列化。 如果需要，可以自定义此序列化。 有关详细信息，请参阅[使用 JSON.NET 的自定义序列](#JsonDotNet)。
> 
> 

有关 `Hotel` 类的第二个重要问题是公共属性的数据类型。 这些属性的 .NET 类型映射到它们在索引定义中的等效字段类型。 例如，`Category` 字符串属性映射到 `Edm.String` 类型的 `category` 字段。 `bool?` 和 `Edm.Boolean`、 `DateTimeOffset?`和 `Edm.DateTimeOffset` 等之间存在相似的类型映射。[MSDN](https://msdn.microsoft.com/library/azure/dn931291.aspx) 上的 `Documents.Get` 方法中记录了类型映射的特定规则。

使用自己的类作为文档的这种功能可以在这两个方向上正常工作；此外，还可以检索搜索结果，并使用 SDK 自动将结果反序列化为所选类型，我们会在下一节中对此进行介绍。

> [!NOTE]
> Azure 搜索 .NET SDK 还使用 `Document` 类支持动态类型化文档，该类是字段名称到字段值的键/值映射。 如果在设计时不知道索引架构，或者绑定到特定模型类不太方便，这很有用。 该 SDK 中处理文档的所有方法都有使用 `Document` 类的重载，以及采用泛型类型参数的强类型重载。 本教程中的示例代码仅使用后者。 可在[此处](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.document.aspx)找到有关 `Document` 类的更多信息。
> 
> 

**有关数据类型的重要说明**

设计自己的模型类以映射到 Azure 搜索索引时，建议将值类型的属性（如 `bool` 和 `int`）声明为可以为 null（例如，`bool?` 而不是 `bool`）。 如果使用不可为 null 属性，必须 **保证** 索引中的所有文档的对应字段都不包含 null 值。 该 SDK 和 Azure 搜索服务都不会帮助强制实施此检查。

这不只是假想的问题：假设将新字段添加到 `Edm.Int32`类型的现有索引。 更新索引定义后，所有文档的该新字段都具有 null 值（因为 Azure 搜索中的所有类型都可以为 null）。 如果随后使用该字段具有不可为 null `int` 属性的模型类，则在尝试检索文档时将获得如下所示的 `JsonSerializationException`：

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

由于此原因，最佳做法是建议在模型类中使用可以为 null 的类型。

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>使用 JSON.NET 的自定义序列
SDK 使用 JSON.NET 对文档进行序列化和反序列化。 必要时可以通过定义自己的 `JsonConverter` 或 `IContractResolver` 来自定义序列化和反序列化（有关详细信息，请参阅 [JSON.NET 文档](http://www.newtonsoft.com/json/help/html/Introduction.htm)）。 当你想要使应用程序中的现有模型类适用于 Azure 搜索和其他更高级的方案时，这可能非常有用。 例如，使用自定义序列，你可以：

* 包含或排除模型类的某些属性作为文档字段存储。
* 在代码中的属性名称与索引中的字段名称之间进行映射。
* 创建可用于映射属性到文档字段以及创建相应索引定义的自定义属性。

可以在 GitHub 上的 Azure 搜索 .NET SDK 的单元测试中找到实现自定义序列化的示例。 一个好的起点是[此文件夹](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Search/Search.Tests/Tests/Models)。 它包含了自定义序列化测试所用的类。

### <a name="searching-for-documents-in-the-index"></a>在索引中搜索文档
示例应用程序中的最后一步是在索引中搜索一些文档。 执行此操作的方法如下所示：

    private static void SearchDocuments(ISearchIndexClient indexClient, string searchText, string filter = null)
    {
        // Execute search based on search text and optional filter
        var sp = new SearchParameters();

        if (!String.IsNullOrEmpty(filter))
        {
            sp.Filter = filter;
        }

        DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
        foreach (SearchResult<Hotel> result in response.Results)
        {
            Console.WriteLine(result.Document);
        }
    }

首先，此方法创建一个新的 `SearchParameters` 对象。 这用于为查询指定其他选项，如排序、筛选、分页和查找。 在本示例中，我们仅会设置 `Filter` 属性。

下一步是实际执行搜索查询。 使用 `Documents.Search` 方法完成此操作。 在这种情况下，我们将要使用的搜索文本作为字符串传递，还会传递先前创建的搜索参数。 此外，我们指定 `Hotel` 作为 `Documents.Search` 的类型参数，这指示 SDK 将搜索结果中的文档反序列化为类型为 `Hotel` 的对象。

最后，该方法遍历搜索结果中的所有匹配项，将每个文档打印到控制台。

详细了解如何调用此方法：

    SearchDocuments(indexClient, searchText: "fancy wifi");

    SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

在第一个调用中，我们查找包含查询词“fancy”或“wifi”的所有文档。 在第二个调用中个，搜索文本设置为“*”，这表示“查找所有内容”。 可在[此处](https://msdn.microsoft.com/library/azure/dn798920.aspx)找到有关搜索查询表达式语法的详细信息。

第二个调用使用 OData `$filter` 表达式（即 `category eq 'Luxury'`）。 这限制搜索仅返回 `category` 字段完全匹配字符串“Luxury”的文档。 可在[此处](https://msdn.microsoft.com/library/azure/dn798921.aspx)找到有关 Azure 搜索支持的 OData 语法的详细信息。

既然已知这两个调用执行的操作，那么弄明白其如下所示的输出的原因应该比较容易：

    Searching documents 'fancy wifi'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 956-532     Name: Express Rooms     Category: Budget        Tags: [wifi, budget]

    Filter documents with category 'Luxury'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 566-518     Name: Surprisingly Expensive Suites     Category: Luxury    Tags: []

第一个搜索返回两个文档。 第一个文档在名称中带有“Fancy”，而第二个文档在 `tags` 字段中带有“wifi”。 第二个搜索返回两个文档，这两个文档恰好是索引中将 `category` 字段设置为“Luxury”的仅有文档。

本教程到此步骤结束，但不要就此打住。 **后续步骤**提供了详细了解 Azure 搜索的其他资源。

## <a name="next-steps"></a>后续步骤
* 在 MSDN 上，浏览 [.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) 和 [REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) 的参考资料。
* 通过[视频和其他示例以及教程](search-video-demo-tutorial-list.md)加深了解。
* 阅读此版本的 Azure 搜索 SDK 中的特性和功能：[Azure 搜索概述](https://msdn.microsoft.com/library/azure/dn798933.aspx)
* 查看[命名约定](https://msdn.microsoft.com/library/azure/dn857353.aspx)，了解命名各种对象的规则。
* 查看 Azure 搜索中[受支持的数据类型](https://msdn.microsoft.com/library/azure/dn798938.aspx)。

## <a name="sample-application-source-code"></a>示例应用程序源代码
以下是本演练中所用示例应用程序的完整源代码。 请注意，如果你要生成并运行该示例，需要将 Program.cs 中的服务名称和 API 密钥占位符替换为你自己的值。

Program.cs：

```csharp
using System;
using System.Configuration;
using System.Linq;
using System.Threading;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;

namespace AzureSearch.SDKHowTo
{
    class Program
    {
        // This sample shows how to delete, create, upload documents and query an index
        static void Main(string[] args)
        {
            // Put your search service name here. This is the hostname portion of your service URL.
            // For example, if your service URL is https://myservice.search.windows.net, then your
            // service name is myservice.
            string searchServiceName = "myservice";

            string apiKey = "Put your API admin key here.";

            SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

            Console.WriteLine("{0}", "Deleting index...\n");
            DeleteHotelsIndexIfExists(serviceClient);

            Console.WriteLine("{0}", "Creating index...\n");
            CreateHotelsIndex(serviceClient);

            ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

            Console.WriteLine("{0}", "Uploading documents...\n");
            UploadDocuments(indexClient);

            Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
            SearchDocuments(indexClient, searchText: "fancy wifi");

            Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
            SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

            Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
            Console.ReadKey();
        }

        private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
        {
            if (serviceClient.Indexes.Exists("hotels"))
            {
                serviceClient.Indexes.Delete("hotels");
            }
        }

        private static void CreateHotelsIndex(SearchServiceClient serviceClient)
        {
            var definition = new Index()
            {
                Name = "hotels",
                Fields = new[]
                {
                    new Field("hotelId", DataType.String)                       { IsKey = true },
                    new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true },
                    new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true },
                    new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
                    new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
                    new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
                    new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
                    new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
                    new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
                }
            };

            serviceClient.Indexes.Create(definition);
        }

        private static void UploadDocuments(ISearchIndexClient indexClient)
        {
            var documents =
                new Hotel[]
                {
                    new Hotel()
                    {
                        HotelId = "1058-441",
                        HotelName = "Fancy Stay",
                        BaseRate = 199.0,
                        Category = "Luxury",
                        Tags = new[] { "pool", "view", "concierge" },
                        ParkingIncluded = false,
                        LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                        Rating = 5,
                        Location = GeographyPoint.Create(47.678581, -122.131577)
                    },
                    new Hotel()
                    {
                        HotelId = "666-437",
                        HotelName = "Roach Motel",
                        BaseRate = 79.99,
                        Category = "Budget",
                        Tags = new[] { "motel", "budget" },
                        ParkingIncluded = true,
                        LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                        Rating = 1,
                        Location = GeographyPoint.Create(49.678581, -122.131577)
                    },
                    new Hotel()
                    {
                        HotelId = "970-501",
                        HotelName = "Econo-Stay",
                        BaseRate = 129.99,
                        Category = "Budget",
                        Tags = new[] { "pool", "budget" },
                        ParkingIncluded = true,
                        LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                        Rating = 4,
                        Location = GeographyPoint.Create(46.678581, -122.131577)
                    },
                    new Hotel()
                    {
                        HotelId = "956-532",
                        HotelName = "Express Rooms",
                        BaseRate = 129.99,
                        Category = "Budget",
                        Tags = new[] { "wifi", "budget" },
                        ParkingIncluded = true,
                        LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                        Rating = 4,
                        Location = GeographyPoint.Create(48.678581, -122.131577)
                    },
                    new Hotel()
                    {
                        HotelId = "566-518",
                        HotelName = "Surprisingly Expensive Suites",
                        BaseRate = 279.99,
                        Category = "Luxury",
                        ParkingIncluded = false
                    }
                };

            try
            {
                var batch = IndexBatch.Upload(documents);
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

            // Wait a while for indexing to complete.
            Thread.Sleep(2000);
        }

        private static void SearchDocuments(ISearchIndexClient indexClient, string searchText, string filter = null)
        {
            // Execute search based on search text and optional filter
            var sp = new SearchParameters();

            if (!String.IsNullOrEmpty(filter))
            {
                sp.Filter = filter;
            }

            DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
            foreach (SearchResult<Hotel> result in response.Results)
            {
                Console.WriteLine(result.Document);
            }
        }
    }
}
```

Hotel.cs:

```csharp
using System;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;

namespace AzureSearch.SDKHowTo
{
    [SerializePropertyNamesAsCamelCase]
    public class Hotel
    {
        public string HotelId { get; set; }

        public string HotelName { get; set; }

        public double? BaseRate { get; set; }

        public string Category { get; set; }

        public string[] Tags { get; set; }

        public bool? ParkingIncluded { get; set; }

        public DateTimeOffset? LastRenovationDate { get; set; }

        public int? Rating { get; set; }

        public GeographyPoint Location { get; set; }

        public override string ToString()
        {
            return String.Format(
                "ID: {0}\tName: {1}\tCategory: {2}\tTags: [{3}]",
                HotelId,
                HotelName,
                Category,
                (Tags != null) ? String.Join(", ", Tags) : String.Empty);
        }
    }
}
```



<!--HONumber=Nov16_HO3-->


