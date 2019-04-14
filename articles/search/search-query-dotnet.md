---
title: 将数据查询到 C# (.NET SDK) 中的 Azure 搜索索引 - Azure 搜索
description: 用于在 Azure 搜索中生成搜索查询的 C# 代码示例。 将搜索参数添加到筛选器并对搜索结果进行排序。
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/20/2019
ms.openlocfilehash: 1194407122123797c2564c96ac452b9582b017a4
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59264941"
---
# <a name="quickstart-3---query-an-azure-search-index-in-c"></a>快速入门：3 - 在 C# 中查询 Azure 搜索索引

本文介绍如何使用 C# 和 [.NET SDK](https://aka.ms/search-sdk) 查询 [Azure 搜索索引](search-what-is-an-index.md)。 执行以下任务可在索引中搜索文档：

> [!div class="checklist"]
> * 创建 [`SearchIndexClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) 对象以使用只读权限连接到搜索索引。
> * 创建包含搜索或筛选器定义的 [`SearchParameters`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters?view=azure-dotnet) 对象。
> * 对 `SearchIndexClient` 调用 `Documents.Search` 方法以将查询发送到索引。

## <a name="prerequisites"></a>先决条件

使用 hotels 示例数据[加载 Azure 搜索索引](search-import-data-dotnet.md)。

获取用于对文档进行只读访问的查询密钥。 到目前为止，使用了管理员 API 密钥，以便可以在搜索服务上创建对象和内容。 但对于应用中的查询支持，强烈建议使用查询密钥。 有关说明，请参阅[创建查询密钥](search-security-api-keys.md#create-query-keys)。

## <a name="create-a-client"></a>创建客户端
创建 `SearchIndexClient` 类的实例，以便可以为它提供只读访问权限的查询密钥（而不是对上一课中使用的 `SearchServiceClient` 授予的写访问权限）。

此类具有几个构造函数。 需要将搜索服务名称、索引名称和 [`SearchCredentials`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchcredentials?view=azure-dotnet) 对象用作参数。 `SearchCredentials` 包装 API 密钥。

以下代码使用应用程序配置文件（在使用[示例应用程序](https://aka.ms/search-dotnet-howto)时为 `appsettings.json`）中存储的搜索服务名称和 API 密钥的值，为“hotels”索引创建一个新 `SearchIndexClient`：

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
    return indexClient;
}
```

`SearchIndexClient` 具有一个 [`Documents`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient.documents?view=azure-dotnet) 属性。 此属性提供查询 Azure 搜索索引所需的全部方法。

## <a name="construct-searchparameters"></a>构造 SearchParameters
使用 .NET SDK 进行搜索与对 `SearchIndexClient` 调用 `Documents.Search` 方法一样简单。 此方法采用几个参数，包括搜索文本以及可用于进一步优化查询的 `SearchParameters` 对象。

### <a name="types-of-queries"></a>查询类型
可以使用的两个主要[查询类型](search-query-overview.md#types-of-queries)分别为 `search` 和 `filter`。 `search` 查询可搜索索引中全部可搜索 字段中的一个或多个词。 `filter` 查询可对索引中的全部可筛选字段计算布尔表达式。 可单独使用 search 和 filter，也可一起使用。

使用 `Documents.Search` 方法可同时执行搜索和查询。 搜索查询可在`searchText` 参数中传递，而筛选表达式则可在 `SearchParameters` 类的 `Filter` 属性中传递。 若要筛选但不搜索，只需传递 `"*"` 作为 `searchText` 参数。 若要搜索但不筛选，只需不设置 `Filter` 属性，或不在 `SearchParameters` 实例中传递。

### <a name="example-queries"></a>查询示例
下面的示例代码演示查询“hotels”索引的几种不同方法（该索引在 [使用 C# 创建 Azure 搜索索引](search-create-index-dotnet.md#DefineIndex)中定义）。 请注意，随搜索结果返回的文档是 `Hotel` 类的实例（该类在 [使用 C# 将数据导入 Azure 搜索索引](search-import-data-dotnet.md#construct-indexbatch)中定义）。 示例代码使用 `WriteDocuments` 方法将搜索结果输出到控制台。 此方法在下一节介绍。

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

## <a name="handle-search-results"></a>处理搜索结果
`Documents.Search` 方法返回包含查询结果的 `DocumentSearchResult` 对象。 上一节中的示例使用名为 `WriteDocuments` 的方法将搜索结果输出到控制台：

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

以下是上一节中的查询的结果（假定“hotels”索引填充的是示例数据）：

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

上面的示例代码使用控制台来输出搜索结果。 同样需要在自己的应用程序中显示搜索结果。 有关如何在基于 ASP.NET MVC 的 Web 应用程序中呈现搜索结果的示例，请参阅 GitHub 上的 [DotNetSample 项目](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample)。

## <a name="next-steps"></a>后续步骤

如果尚未这样做，请查看 GitHub 上的 [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) 中的示例代码，以及[如何从 .NET 应用程序使用 Azure 搜索](search-howto-dotnet-sdk.md)，以了解有关示例代码的更多详细说明。 