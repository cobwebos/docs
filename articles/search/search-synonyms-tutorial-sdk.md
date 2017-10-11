---
title: "Azure 搜索中的同义词（预览版）教程 | Microsoft Docs"
description: "向 Azure 搜索中的索引添加同义词（预览版）功能。"
services: search
manager: jhubbard
documentationcenter: 
author: HeidiSteen
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 03/31/2017
ms.author: heidist
ms.openlocfilehash: 014959ed471f796d2184f0f8ff10d15cdc8a2ec6
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="synonym-preview-c-tutorial-for-azure-search"></a>适用于 Azure 搜索的同义词（预览版）C# 教程

使用同义词进行搜索时，在语义上被视为与输入字词等效的字词也属于匹配项，从而扩展了查询的范围。 例如，你可能想"car"来匹配包含字词"automobile"或"vehicle"的文档。

在 Azure 搜索中，同义词在“同义词映射”中通过可将等效字词关联在一起的“映射规则”进行定义。 可以创建多个同义词映射，将其发布为在服务范围内可供任何索引使用的资源，然后引用要在字段级别使用的资源。 在查询时，除了搜索索引，Azure 搜索还会查看同义词映射（如果在查询所用的字段上指定了该映射）。

> [!NOTE]
> 同义词功能目前为预览版，仅支持最新的预览版 API 和 SDK（api-version 为 2016-09-01-Preview，SDK 版本为 4.x-preview）。 目前不提供 Azure 门户支持。 预览版 API 不在 SLA 下且预览版功能可能会更改，因此不建议在生产应用程序中使用。

## <a name="prerequisites"></a>先决条件

教程要求如下：

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure 搜索服务](search-create-service-portal.md)
* [预览版 Microsoft.Azure.Search .NET 库](https://aka.ms/search-sdk-preview)
* [如何使用 .NET 应用程序中的 Azure 搜索](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

## <a name="overview"></a>概述

前后比较查询可演示同义词的值。 在本教程中，我们使用一个示例应用程序，针对示例索引执行查询并返回结果。 示例应用程序创建了一个名为“hotels”的小索引，其中填充了两个文档。 该应用程序使用未在索引中出现的字词和短语执行搜索查询，接着启用同义词功能，再次进行相同的搜索。 以下代码演示了整个流程。

```csharp
  static void Main(string[] args)
  {
      SearchServiceClient serviceClient = CreateSearchServiceClient();

      Console.WriteLine("{0}", "Cleaning up resources...\n");
      CleanupResources(serviceClient);

      Console.WriteLine("{0}", "Creating index...\n");
      CreateHotelsIndex(serviceClient);

      ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

      Console.WriteLine("{0}", "Uploading documents...\n");
      UploadDocuments(indexClient);

      ISearchIndexClient indexClientForQueries = CreateSearchIndexClient();

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Adding synonyms...\n");
      UploadSynonyms(serviceClient);
      EnableSynonymsInHotelsIndex(serviceClient);
      Thread.Sleep(10000); // Wait for the changes to propagate

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");

      Console.ReadKey();
  }
```
有关如何创建和填充示例索引的步骤，请参阅[如何使用 .NET 应用程序中的 Azure 搜索](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)。

## <a name="before-queries"></a>“启用前”查询

在 `RunQueriesWithNonExistentTermsInIndex` 中，我们使用“five star”、“internet”和“economy AND hotel”发出搜索查询。
```csharp
Console.WriteLine("Search the entire index for the phrase \"five star\":\n");
results = indexClient.Documents.Search<Hotel>("\"five star\"", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'internet':\n");
results = indexClient.Documents.Search<Hotel>("internet", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the terms 'economy' AND 'hotel':\n");
results = indexClient.Documents.Search<Hotel>("economy AND hotel", parameters);
WriteDocuments(results);
```
两个已编入索引的文档都不包含这些字词，因此首次 `RunQueriesWithNonExistentTermsInIndex` 获得的输出如下。
~~~
Search the entire index for the phrase "five star":

no document matched

Search the entire index for the term 'internet':

no document matched

Search the entire index for the terms 'economy' AND 'hotel':

no document matched
~~~

## <a name="enable-synonyms"></a>启用同义词

启用同义词是一个两步过程。 首先定义并上传同义词规则，然后将字段配置为使用这些规则。 `UploadSynonyms` 和 `EnableSynonymsInHotelsIndex` 中概述了此过程。

1. 将同义词映射添加到搜索服务。 在 `UploadSynonyms` 中，我们在同义词映射“desc-synonymmap”中定义了四条规则，并将其上传到服务。
```csharp
    var synonymMap = new SynonymMap()
    {
        Name = "desc-synonymmap",
        Format = "solr",
        Synonyms = "hotel, motel\n
                    internet,wifi\n
                    five star=>luxury\n
                    economy,inexpensive=>budget"
    };

    serviceClient.SynonymMaps.CreateOrUpdate(synonymMap);
```
同义词映射必须符合开源标准 `solr` 格式。 该格式在 [Azure 搜索中的同义词](search-synonyms.md)的`Apache Solr synonym format`部分进行了说明。

2. 将可搜索字段配置为允许在索引定义中使用同义词映射。 在 `EnableSynonymsInHotelsIndex` 中，我们对`category`和`tags`这两个字段启用了同义词功能，方法是将 `synonymMaps` 属性设置为新上传的同义词映射的名称。
```csharp
  Index index = serviceClient.Indexes.Get("hotels");
  index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
  index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };

  serviceClient.Indexes.CreateOrUpdate(index);
```
添加同义词映射时，索引重建不是必需的。 可以向服务添加同义词映射，并对任意索引中的现有字段定义进行修正，使之使用新的同义词映射。 添加新属性不影响索引可用性。 这同样适用于禁用字段同义词功能的情况。 可以直接将 `synonymMaps` 属性设置为空列表。
```csharp
  index.Fields.First(f => f.Name == "category").SynonymMaps = new List<string>();
```

## <a name="after-queries"></a>“启用后”查询

上传同义词映射并对索引进行更新，允许其使用同义词映射以后，再次调用 `RunQueriesWithNonExistentTermsInIndex` 会获得如下输出：

~~~
Search the entire index for the phrase "five star":

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the term 'internet':

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the terms 'economy' AND 'hotel':

Name: Roach Motel       Category: Budget        Tags: [motel, budget]
~~~
第一个查询根据规则 `five star=>luxury` 查找文档。 第二个查询使用 `internet,wifi` 扩展了搜索，第三个查询在查找匹配的文档时同时使用 `hotel, motel` 和 `economy,inexpensive=>budget`。

添加同义词完全改观了搜索体验。 在本教程中，初始查询并没有返回有意义的结果，虽然索引中的文档是相关的。 启用同义词可以扩展索引，使之包括常用字词，但不更改索引中的基础数据。

## <a name="sample-application-source-code"></a>示例应用程序源代码
可以在 [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms) 上找到本演练中所用示例应用程序的完整源代码。

## <a name="next-steps"></a>后续步骤

* 查看[如何在 Azure 搜索中使用同义词功能](search-synonyms.md)
* 查看[同义词 REST API 文档](https://aka.ms/rgm6rq)
* 浏览 [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) 和 [REST API](https://docs.microsoft.com/rest/api/searchservice/) 的参考资料。
