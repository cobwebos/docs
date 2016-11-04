---
title: 使用 NET SDK 查询 Azure 搜索 | Microsoft Docs
description: 在 Azure 搜索中生成搜索查询，并使用搜索参数对搜索结果进行筛选和排序。
services: search
documentationcenter: ''
author: brjohnstmsft

ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 08/29/2016
ms.author: brjohnst

---
# 使用 .NET SDK 查询 Azure 搜索索引
> [!div class="op_single_selector"]
> * [概述](search-query-overview.md)
> * [门户](search-explorer.md)
> * [.NET](search-query-dotnet.md)
> * [REST](search-query-rest-api.md)
> 
> 

本文介绍如何使用 [Azure 搜索 .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) 查询索引。

开始本演练前，应已[创建 Azure 搜索索引](search-what-is-an-index.md)并[填充数据](search-what-is-data-import.md)。

注意，本文中的所有示例代码均用 C# 编写。可以在 [GitHub](http://aka.ms/search-dotnet-howto) 上找到完整的源代码。

## I.标识 Azure 搜索服务的查询 API 密钥
创建好 Azure 搜索索引后，即可使用 .NET SDK 发出查询。首先，需要获取为已预配的搜索服务生成的其中一个查询 API 密钥。每次向服务发出请求时，NET SDK 都会发送这个 API 密钥。具有有效的密钥可以在发送请求的应用程序与处理请求的服务之间建立信任关系，这种信任关系以每个请求为基础。

1. 若要查找服务的 API 密钥，必须登录到 [Azure 门户](https://portal.azure.com/)
2. 转到 Azure 搜索服务的边栏选项卡
3. 单击“密钥”图标

服务将具有*管理密钥*和*查询密钥*。

* 主管理密钥和辅助*管理密钥*授予所有操作的完全控制权限，包括管理服务以及创建和删除索引、索引器与数据源的能力。有两个密钥的作用是确保在决定重新生成主密钥时可以继续使用辅助密钥，反之亦然。
* *查询密钥*授予索引和文档的只读访问权限，通常分发给发出搜索请求的客户端应用程序。

可以使用其中一个查询密钥来查询索引。查询也可使用管理密钥，但最好在应用程序代码中使用查询密钥，因为这更符合[最低特权原则](https://en.wikipedia.org/wiki/Principle_of_least_privilege)。

## II.创建 SearchIndexClient 类的实例
若要使用 Azure 搜索 .NET SDK 发出查询，需要创建 `SearchIndexClient` 类的实例。此类具有几个构造函数。需要将搜索服务名称、索引名称和 `SearchCredentials` 对象用作参数。`SearchCredentials` 包装 API 密钥。

下面的代码使用搜索服务名称值以及应用程序配置文件（`app.config` 或 `web.config`）中存储的 API 密钥值为“hotels”索引创建新的 `SearchIndexClient`（该索引在[使用 .NET SDK 创建 Azure 搜索索引](search-create-index-dotnet.md)中创建）：

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string queryApiKey = ConfigurationManager.AppSettings["SearchServiceQueryApiKey"];

SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
```

`SearchIndexClient` 具有 `Documents` 属性。此属性提供查询 Azure 搜索索引所需的全部方法。

## III.查询索引
使用 .NET SDK 进行搜索与对 `SearchIndexClient` 调用 `Documents.Search` 方法一样简单。此方法采用几个参数，包括搜索文本以及可用于进一步优化查询的 `SearchParameters` 对象。

#### 查询类型
可以使用的两个主要[查询类型](search-query-overview.md#types-of-queries)分别为 `search` 和 `filter`。`search` 查询可搜索索引中全部_可搜索_字段中的一个或多个词。`filter` 查询可对索引中的全部_可筛选_字段计算布尔表达式。

使用 `Documents.Search` 方法可同时执行搜索和查询。搜索查询可在 `searchText` 参数中传递，而筛选表达式则可在 `SearchParameters` 类的 `Filter` 属性中传递。若要筛选但不搜索，只需传递 `"*"` 作为 `searchText` 参数。若要搜索但不筛选，只需不设置 `Filter` 属性，或不在 `SearchParameters` 实例中传递。

#### 查询示例
下面的示例代码演示查询“hotels”索引的几种不同方法（该索引在[使用 .NET SDK 创建 Azure 搜索索引](search-create-index-dotnet.md#DefineIndex)中定义）。注意，随搜索结果返回的文档是 `Hotel` 类的实例（该类在[使用 .NET SDK 将数据导入 Azure 搜索](search-import-data-dotnet.md#HotelClass)中定义）。示例代码使用 `WriteDocuments` 方法将搜索结果输出到控制台。此方法在下一节介绍。

```csharp
SearchParameters parameters;
DocumentSearchResult<Hotel> results;

Console.WriteLine("Search the entire index for the term 'budget' and return only the hotelName field:\n");

parameters =
    new SearchParameters()
    {
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);

Console.Write("Apply a filter to the index to find hotels cheaper than $150 per night, ");
Console.WriteLine("and return the hotelId and description:\n");

parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
Console.Write("in descending order, take the top two results, and show only hotelName and ");
Console.WriteLine("lastRenovationDate:\n");

parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'motel':\n");

parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

## IV.处理搜索结果
`Documents.Search` 方法返回包含查询结果的 `DocumentSearchResult` 对象。上一节中的示例使用名为 `WriteDocuments` 的方法将搜索结果输出到控制台：

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

以下是上一节中的查询的结果（假定“hotels”索引填充的是[使用 .NET SDK 将数据导入 Azure 搜索](search-import-data-dotnet.md)中的示例数据）：

```
Search the entire index for the term 'budget' and return only the hotelName field:

Name: Roach Motel

Apply a filter to the index to find hotels cheaper than $150 per night, and return the hotelId and description:

ID: 2   Description: Cheapest hotel in town
ID: 3   Description: Close to town hall and the river

Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Search the entire index for the term 'motel':

ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577

```

上面的示例代码使用控制台来输出搜索结果。同样需要在自己的应用程序中显示搜索结果。请在 [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample) 上查看此示例，查看如何在基于 ASP.NET MVC 的 Web 应用程序中呈现搜索结果。

<!---HONumber=AcomDC_0921_2016-->