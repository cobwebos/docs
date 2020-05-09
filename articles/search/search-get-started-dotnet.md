---
title: 快速入门：通过 .NET 在 C# 中创建搜索索引
titleSuffix: Azure Cognitive Search
description: 此 C# 快速入门介绍如何使用 Azure 认知搜索 .NET SDK 创建索引、加载数据以及运行查询。
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 02/10/2020
ms.openlocfilehash: 3d0006a3c77050c1bb21a0da8d6be51e659f933d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "77589209"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-c-using-the-net-sdk"></a>快速入门：使用 C# 通过 .NET SDK 创建 Azure 认知搜索索引
> [!div class="op_single_selector"]
> * [C#](search-get-started-dotnet.md)
> * [门户](search-get-started-portal.md)
> * [PowerShell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)
>*

创建 .NET Core C# 控制台应用程序，该应用程序使用 Visual Studio 和 [Azure 认知搜索 .NET SDK](https://aka.ms/search-sdk) 创建、加载和查询 Azure 认知搜索索引。 本文逐步介绍如何创建该应用程序。 此外，还可以[下载并运行完整的应用程序](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/Quickstart)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!NOTE]
> 为简单起见，本文中的演示代码使用 Azure 认知搜索 .NET SDK 的同步方法。 但是，对于生产场景，建议你在自己的应用程序中使用异步方法，使应用程序保持可缩放且响应迅速。 例如，可以使用 `CreateAsync` 和 `DeleteAsync`，而不是 `Create` 和 `Delete`。

## <a name="prerequisites"></a>先决条件

本快速入门需要以下服务和工具。

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)（版本不限）。 示例代码和说明已在免费社区版上进行了测试。

+ [创建 Azure 认知搜索服务](search-create-service-portal.md)或在当前订阅下[查找现有服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 可以使用本快速入门的免费服务。

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>获取密钥和 URL

对服务的调用要求每个请求都有一个 URL 终结点和一个访问密钥。 搜索服务是使用这二者创建的，因此，如果向订阅添加了 Azure 认知搜索，则请按以下步骤获取必需信息：

1. [登录到 Azure 门户](https://portal.azure.com/)，在搜索服务的“概述”页中获取 URL。  示例终结点可能类似于 `https://mydemo.search.windows.net`。

2. 在“设置” > “密钥”中，获取有关该服务的完全权限的管理员密钥   。 有两个可交换的管理员密钥，为保证业务连续性而提供，以防需要滚动一个密钥。 可以在请求中使用主要或辅助密钥来添加、修改和删除对象。

   此外，获取查询密钥。 最好使用只读权限发出查询请求。

![获取 HTTP 终结点和访问密钥](media/search-get-started-postman/get-url-key.png "获取 HTTP 终结点和访问密钥")

所有请求对发送到服务的每个请求都需要 API 密钥。 具有有效的密钥可以在发送请求的应用程序与处理请求的服务之间建立信任关系，这种信任关系以每个请求为基础。

## <a name="set-up-your-environment"></a>设置你的环境

首先，打开 Visual Studio，并新建能在 .NET Core 上运行的控制台应用项目。

### <a name="install-nuget-packages"></a>安装 NuGet 包

[Azure 认知搜索 .NET SDK](https://aka.ms/search-sdk) 由作为 NuGet 包分发的一些客户端库组成。

对于此项目，使用 `Microsoft.Azure.Search` NuGet 包的版本 9，以及最新的 `Microsoft.Extensions.Configuration.Json` NuGet 包。

1. 在“工具” > “NuGet 包管理器”中，选择“管理解决方案的 NuGet 包...”    。 

1. 单击“浏览”  。

1. 搜索 `Microsoft.Azure.Search`，并选择 9.0.1 版或更高版本。

1. 单击右侧的“安装”，将该程序集添加到你的项目和解决方案  。

1. 为 `Microsoft.Extensions.Configuration.Json` 重复以上步骤，选择 2.2.0 版或更高版本。


### <a name="add-azure-cognitive-search-service-information"></a>添加 Azure 认知搜索服务信息

1. 在“解决方案资源管理器”中，右键单击项目，依次选择“添加” > “新建项...”   。 

1. 在“添加新项”中，搜索“JSON”，以返回与 JSON 相关的项目类型列表。

1. 选择 JSON 文件，将该文件命名为“appsettings.json”，并单击“添加”   。 

1. 将文件添加到输出目录。 右键单击 appsettings.json 并选择“属性”  。 在“复制到输出目录”中，选择“如果较新则复制”   。

1. 将以下 JSON 复制到新 JSON 文件中。 将搜索服务名称 (YOUR-SEARCH-SERVICE-NAME) 和管理员 API 密钥 (YOUR-ADMIN-API-KEY) 替换为有效值。 如果服务终结点是 `https://mydemo.search.windows.net`，则服务名称为“mydemo”。

```json
{
  "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
  "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
  "SearchIndexName": "hotels-quickstart"
}
```

### <a name="add-class-method-files-to-your-project"></a>将类“.Method”文件添加到你的项目

将结果打印到控制台窗口时，必须以字符串形式返回 Hotel 对象的各个字段。 可以通过实现 [ToString()](https://docs.microsoft.com/dotnet/api/system.object.tostring?view=netframework-4.8) 来执行此任务，将所需的代码复制到两个新文件中。

1. 将两个空的类定义添加到你的项目：Address.Methods.cs、Hotel.Methods.cs

1. 在 Address.Methods.cs 中，使用以下代码（[1-32 行](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Address.Methods.cs/#L1-L32)）覆盖默认内容。

1. 在 Hotel.Methods.cs 中，复制 [1-66 行](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Hotel.Methods.cs/#L1-L66)。


## <a name="1---create-index"></a>1 - 创建索引

酒店索引由简单和复杂字段组成，其中简单字段是“酒店名称”或“说明”，复杂字段是包含子字段的地址或房间集合。 如果索引包含复杂类型，请在单独的类中隔离复杂字段定义。

1. 将两个空的类定义添加到你的项目：Address.cs、Hotel.cs

1. 在 Address.cs 中，使用以下代码覆盖默认内容：

    ```csharp
    using System;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Newtonsoft.Json;

    namespace AzureSearchQuickstart
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

1. 在 Hotel.cs 中，类定义索引的整体结构，包括对地址类的引用。

    ```csharp
    namespace AzureSearchQuickstart
    {
        using System;
        using Microsoft.Azure.Search;
        using Microsoft.Azure.Search.Models;
        using Newtonsoft.Json;

        public partial class Hotel
        {
            [System.ComponentModel.DataAnnotations.Key]
            [IsFilterable]
            public string HotelId { get; set; }

            [IsSearchable, IsSortable]
            public string HotelName { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
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

            [IsFilterable, IsSortable, IsFacetable]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public double? Rating { get; set; }

            public Address Address { get; set; }
        }
    }
    ```

    该字段的特性决定字段在应用程序中的使用方式。 例如，`IsSearchable` 属性必须分配给每个应包含在全文搜索中的字段。 
    
    > [!NOTE]
    > 在 .NET SDK 中，必须显式将字段属性化为 [`IsSearchable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issearchable?view=azure-dotnet)、[`IsFilterable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet)、[`IsSortable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issortable?view=azure-dotnet) 和 [`IsFacetable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfacetable?view=azure-dotnet)。 此行为与 REST API 相反，后者基于数据类型隐式启用属性（例如，简单的字符串字段是自动可搜索的）。

    类型为 `string` 的索引中必须恰好有一个字段为“密钥”字段，用于唯一地标识每个文档  。 在此架构中，密钥为 `HotelId`。

    在此索引中，“说明”字段使用可选的 [`analyzer`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.analyzer?view=azure-dotnet) 属性，该属性在需要替代默认标准 Lucene 分析器时指定。 `description_fr` 字段使用法语 Lucene 分析器 ([FrLucene](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.frlucene?view=azure-dotnet))，因为该字段存储法语文本。 `description` 使用可选的 Microsoft 语言分析器 ([EnMicrosoft](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.enmicrosoft?view=azure-dotnet))。

1. 在 Program.cs 中，使用存储在应用程序配置文件 (appsettings.json) 中的值，创建 [`SearchServiceClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient?view=azure-dotnet) 类的实例，以连接到服务。 

   `SearchServiceClient` 具有 [`Indexes`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexes?view=azure-dotnet) 属性，提供创建、列出、更新或删除 Azure 认知搜索索引所需的所有方法。 

    ```csharp
    using System;
    using System.Linq;
    using System.Threading;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Extensions.Configuration;

    namespace AzureSearchQuickstart
    {
        class Program {
            // Demonstrates index delete, create, load, and query
            // Commented-out code is uncommented in later steps
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

                // Uncomment next 3 lines in "2 - Load documents"
                // ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
                // Console.WriteLine("{0}", "Uploading documents...\n");
                // UploadDocuments(indexClient);

                // Uncomment next 2 lines in "3 - Search an index"
                // Console.WriteLine("{0}", "Searching index...\n");
                // RunQueries(indexClient);

                Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
                Console.ReadKey();
            }

            // Create the search service client
            private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
            {
                string searchServiceName = configuration["SearchServiceName"];
                string adminApiKey = configuration["SearchServiceAdminApiKey"];

                SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
                return serviceClient;
            }

            // Delete an existing index to reuse its name
            private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
            {
                if (serviceClient.Indexes.Exists(indexName))
                {
                    serviceClient.Indexes.Delete(indexName);
                }
            }

            // Create an index whose fields correspond to the properties of the Hotel class.
            // The Address property of Hotel will be modeled as a complex field.
            // The properties of the Address class in turn correspond to sub-fields of the Address complex field.
            // The fields of the index are defined by calling the FieldBuilder.BuildForType() method.
            private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
            {
                var definition = new Index()
                {
                    Name = indexName,
                    Fields = FieldBuilder.BuildForType<Hotel>()
                };

                serviceClient.Indexes.Create(definition);
            }
        }
    }    
    ```

    如果可能，请在应用程序中共享单个 `SearchServiceClient` 实例，避免打开太多连接。 类方法是线程安全的，可以启用此类共享。

   该类具有一系列构造函数。 需要将搜索服务名称和 `SearchCredentials` 对象用作参数。 `SearchCredentials` 包装 API 密钥。

    在索引定义中，`Field` 对象最简单的创建方法是调用 `FieldBuilder.BuildForType` 方法，为类型参数传递模型类。 模型类具有映射到索引的字段属性。 使用此映射可将搜索索引中的文档绑定到模型类的实例。

    > [!NOTE]
    > 如果不打算使用模型类，仍可通过直接创建 `Field` 对象来定义索引。 可以向构造函数提供字段的名称以及数据类型（或分析器（用于字符串字段））。 此外可以设置其他属性，如 `IsSearchable`、`IsFilterable`，仅举几例。
    >

1. 按 F5，可生成应用并创建索引。 

    如果成功构建项目，则会打开控制台窗口，将删除和创建索引的状态消息写入屏幕。 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - 加载文档

在 Azure 认知搜索中，文档这一数据结构既是索引输入，也是查询输出。 文档输入从外部数据源获取，可能是数据库中的行、Blob 存储中的 blob 或磁盘上的 JSON 文档。 在此示例中，我们采用了快捷方式，并在代码本身中嵌入了四个酒店的 JSON 文档。 

上传文档时，必须使用 [`IndexBatch`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 对象。 `IndexBatch` 包含 [`IndexAction`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) 对象的集合，其中每个对象均包含一个文档和一个属性，该属性用于指示 Azure 认知搜索要执行什么操作（[上传、合并、删除和 mergeOrUpload](search-what-is-data-import.md#indexing-actions)）。

1. 在 Program.cs 中，创建文档和索引操作的数组，然后将该数组传递给 `IndexBatch`。 以下文档符合酒店和地址类定义的 hotel-quickstart 索引。

    ```csharp
    // Upload documents as a batch
    private static void UploadDocuments(ISearchIndexClient indexClient)
    {
        var actions = new IndexAction<Hotel>[]
        {
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "1",
                    HotelName = "Secret Point Motel",
                    Description = "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
                    DescriptionFr = "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "air conditioning", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1970, 1, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.6,
                    Address = new Address()
                    {
                        StreetAddress = "677 5th Ave",
                        City = "New York",
                        StateProvince = "NY",
                        PostalCode = "10022",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "2",
                    HotelName = "Twin Dome Motel",
                    Description = "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "free wifi", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate =  new DateTimeOffset(1979, 2, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.60,
                    Address = new Address()
                    {
                        StreetAddress = "140 University Town Center Dr",
                        City = "Sarasota",
                        StateProvince = "FL",
                        PostalCode = "34243",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "3",
                    HotelName = "Triple Landscape Hotel",
                    Description = "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Resort and Spa",
                    Tags = new[] { "air conditioning", "bar", "continental breakfast" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(2015, 9, 20, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.80,
                    Address = new Address()
                    {
                        StreetAddress = "3393 Peachtree Rd",
                        City = "Atlanta",
                        StateProvince = "GA",
                        PostalCode = "30326",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "4",
                    HotelName = "Sublime Cliff Hotel",
                    Description = "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
                    DescriptionFr = "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
                    Category = "Boutique",
                    Tags = new[] { "concierge", "view", "24-hour front desk service" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1960, 2, 06, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.6,
                    Address = new Address()
                    {
                        StreetAddress = "7400 San Pedro Ave",
                        City = "San Antonio",
                        StateProvince = "TX",
                        PostalCode = "78216",
                        Country = "USA"
                    }
                }
            ),
        };

        var batch = IndexBatch.New(actions);

        try
        {
            indexClient.Documents.Index(batch);
        }
        catch (IndexBatchException e)
        {
            // When a service is under load, indexing might fail for some documents in the batch. 
            // Depending on your application, you can compensate by delaying and retrying. 
            // For this simple demo, we just log the failed document keys and continue.
            Console.WriteLine(
                "Failed to index some of the documents: {0}",
                String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
        }

        // Wait 2 seconds before starting queries 
        Console.WriteLine("Waiting for indexing...\n");
        Thread.Sleep(2000);
    }
    ```

    初始化 `IndexBatch` 对象后，即可通过对 [`SearchIndexClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) 对象调用 [`Documents.Index`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet)，将其发送到索引。 `Documents` 是 `SearchIndexClient` 的属性，后者提供在索引中添加、修改、删除或查询文档的方法。

    `Index` 方法的调用周围的 `try`/`catch` 捕获索引失败，如果服务负载过大，可能发生索引失败。 在生产代码中，可以延迟已失败的文档索引编制并重试，也可以像示例那样记录并继续运行，还能以满足应用程序数据一致性要求的其他方式进行处理。

    2 秒的延迟可对索引编制进行补偿（这是异步操作），这样可在执行查询之前对所有文档编制索引。 以延迟方式编写代码通常仅在演示、测试和示例应用程序中是必要的。

1. 在 Program.cs 的 main 中，取消注释“2 - 加载文档”行。 

    ```csharp
    // Uncomment next 3 lines in "2 - Load documents"
    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);
    ```
1. 按 F5 可重新生成应用。 

    如果成功构建项目，则会打开控制台窗口，在其中写入状态消息，这次是有关上传文档的消息。 在 Azure 门户的搜索服务“概述”页面中，hotels-quickstart 索引现在应该具有 4 个文档  。

有关文档处理的详细信息，请参阅[“.NET SDK 如何处理文档”](search-howto-dotnet-sdk.md#how-dotnet-handles-documents)。

## <a name="3---search-an-index"></a>3 - 搜索索引

对第一个文档编制索引后，可立即获取查询结果，但索引的实际测试应等到对所有文档编制索引后进行。 

此部分添加了两个功能：查询逻辑和结果。 对于查询，请使用 [`Search`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.search?view=azure-dotnet
) 方法。 此方法采用搜索文本以及其他[参数](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters?view=azure-dotnet)。 

[`DocumentsSearchResult`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1?view=azure-dotnet) 类表示结果。


1. 在 Program.cs 中，创建 WriteDocuments 方法，该方法用于将搜索结果输出到控制台。

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

1. 创建 RunQueries 方法，该方法用于执行查询并返回结果。 结果是 Hotel 对象。 可使用 select 参数来显示各个字段。 如果 select 参数中不包含某个字段，则其对应的 Hotel 属性将为 null。

    ```csharp
    private static void RunQueries(ISearchIndexClient indexClient)
    {
        SearchParameters parameters;
        DocumentSearchResult<Hotel> results;

        // Query 1 
        Console.WriteLine("Query 1: Search for term 'Atlanta' with no result trimming");
        parameters = new SearchParameters();
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 2
        Console.WriteLine("Query 2: Search on the term 'Atlanta', with trimming");
        Console.WriteLine("Returning only these fields: HotelName, Tags, Address:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Tags", "Address" },
            };
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 3
        Console.WriteLine("Query 3: Search for the terms 'restaurant' and 'wifi'");
        Console.WriteLine("Return only these fields: HotelName, Description, and Tags:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Description", "Tags" }
            };
        results = indexClient.Documents.Search<Hotel>("restaurant, wifi", parameters);
        WriteDocuments(results);

        // Query 4 -filtered query
        Console.WriteLine("Query 4: Filter on ratings greater than 4");
        Console.WriteLine("Returning only these fields: HotelName, Rating:\n");
        parameters =
            new SearchParameters()
            {
                Filter = "Rating gt 4",
                Select = new[] { "HotelName", "Rating" }
            };
        results = indexClient.Documents.Search<Hotel>("*", parameters);
        WriteDocuments(results);

        // Query 5 - top 2 results
        Console.WriteLine("Query 5: Search on term 'boutique'");
        Console.WriteLine("Sort by rating in descending order, taking the top two results");
        Console.WriteLine("Returning only these fields: HotelId, HotelName, Category, Rating:\n");
        parameters =
            new SearchParameters()
            {
                OrderBy = new[] { "Rating desc" },
                Select = new[] { "HotelId", "HotelName", "Category", "Rating" },
                Top = 2
            };
        results = indexClient.Documents.Search<Hotel>("boutique", parameters);
        WriteDocuments(results);
    }
    ```

    有两种[方法可用于在查询中匹配术语](search-query-overview.md#types-of-queries)：全文搜索和筛选器。 全文搜索查询会在索引的 `IsSearchable` 字段中搜索一个或多个术语。 筛选器是布尔表达式，该表达式通过索引中的 `IsFilterable` 字段求值。 可以配合使用全文搜索和筛选器，也可以单独使用。

    使用 `Documents.Search` 方法可同时执行搜索和查询。 搜索查询可在`searchText` 参数中传递，而筛选表达式则可在 `SearchParameters` 类的 `Filter` 属性中传递。 若要筛选但不搜索，只需传递 `"*"` 作为 `searchText` 参数。 若要搜索但不筛选，只需不设置 `Filter` 属性，或不在 `SearchParameters` 实例中传递。

1. 在 Program.cs 的 main 中，取消注释“3 - 搜索”行。 

    ```csharp
    // Uncomment next 2 lines in "3 - Search an index"
    Console.WriteLine("{0}", "Searching documents...\n");
    RunQueries(indexClient);
    ```
1. 该解决方案现已完成。 按 F5 可重新生成应用并完整运行该程序。 

    输出包含与以前相同的消息，并添加了查询信息和结果。

## <a name="clean-up-resources"></a>清理资源

在自己的订阅中操作时，最好在项目结束时确定是否仍需要已创建的资源。 持续运行资源可能会产生费用。 可以逐个删除资源，也可以删除资源组以删除整个资源集。

可以使用左侧导航窗格中的“所有资源”或“资源组”链接   ，在门户中查找和管理资源。

如果使用的是免费服务，请记住只能设置三个索引、索引器和数据源。 可以在门户中删除单个项目，以不超出此限制。 

## <a name="next-steps"></a>后续步骤

在此 C# 快速入门中，你已完成一系列任务，即创建索引、使用文档加载索引并运行查询。 在不同的阶段，我们采用快捷方式来简化代码，从而实现可读性和可理解性。 如果你已理解这些基本概念，我们建议阅读下一篇文章，探索替代方法和概念，进一步加深你的理解。 

示例代码和索引是此快速入门的扩展版本。 下一个示例将添加“房间”集合，使用不同的类和操作，并进一步介绍处理的工作原理。

> [!div class="nextstepaction"]
> [如何在 .NET 中开发](search-howto-dotnet-sdk.md)
