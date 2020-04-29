---
title: 创建建议器
titleSuffix: Azure Cognitive Search
description: 通过创建建议器并构建调用自动完成或自动建议的查询字词的请求，在 Azure 认知搜索中启用自动提示查询操作。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 7eb2988628d60fa72c7d83b81a58a1e0fae5de33
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770103"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>在查询中创建建议器以启用自动完成和建议的结果

在 Azure 认知搜索中，通过添加到[搜索索引](search-what-is-an-index.md)的**建议器**构造启用 "搜索即类型"。 建议器支持两种体验： *"自动完成*"，它完成整个术语查询的部分输入，并*建议*"邀请" 单击特定的匹配项。 自动完成将生成一个查询。 建议会生成匹配的文档。

通过[c # 创建第一个应用](tutorial-csharp-type-ahead-and-suggestions.md)的以下屏幕截图演示了这两种情况。 自动完成功能将预测潜在的术语，并使用 "in" 完成 "隶书"。 建议是小型搜索结果，其中的一个字段（如酒店名称）表示与该索引匹配的酒店搜索文档。 对于建议，可以介绍提供描述性信息的任何字段。

![自动完成和建议查询的直观比较](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "自动完成和建议查询的直观比较")

您可以单独或一起使用这些功能。 有一个索引和查询组件可在 Azure 认知搜索中实现这些行为。 

+ 在索引中，将建议器添加到索引。 可以使用门户、[REST API](https://docs.microsoft.com/rest/api/searchservice/create-index) 或 [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)。 本文的余下内容重点介绍如何创建建议器。

+ 在查询请求中，调用[下面列出的 API](#how-to-use-a-suggester) 之一。

对于字符串字段，在每个字段的基础上启用搜索即用类型支持。 若要获得屏幕截图中所示的类似体验，可以在同一搜索解决方案中实现这两种自动提示行为。 这两个请求针对特定索引的文档集合，在用户提供至少包含三个字符的输入字符串后，将返回响应。 

## <a name="what-is-a-suggester"></a>什么是建议器？

建议器是一种内部数据结构，它通过存储用于部分查询匹配的前缀来支持 "搜索即类型" 行为。 与标记字词一样，前缀存储在逆索引中，每个字段对应于建议器字段集合中指定的每个字段。

## <a name="define-a-suggester"></a>定义建议器

若要创建建议器，请将其添加到[索引架构](https://docs.microsoft.com/rest/api/searchservice/create-index)中并[设置每个属性](#property-reference)。 创建建议器的最佳时间是在同时定义将使用该的字段。

+ 仅使用字符串字段

+ 在字段上使用默认的标准`"analyzer": null`Lucene 分析器（）或[语言分析器](index-add-language-analyzers.md)（例如`"analyzer": "en.Microsoft"`，）

### <a name="choose-fields"></a>选择字段

尽管建议器具有多个属性，但它主要是要为其启用搜索即用体验的字符串字段的集合。 每个索引都有一个建议器，因此建议器列表必须包含为建议和自动完成内容提供内容的所有字段。

自动完成功能从要从中提取的更大的字段池获益，因为其他内容具有更多的术语完成可能性。

另一方面，如果字段选择是选择性的，则建议会生成更好的结果。 请记住，建议是搜索文档的代理，因此你将需要最能表示单个结果的字段。 区分多个匹配项的名称、标题或其他唯一字段最适合工作。 如果字段包含重复的值，则建议包含相同的结果，并且用户不知道要单击哪一个。

若要满足这两种搜索类型体验，请添加需要自动完成的所有字段，然后使用 **$select**、 **$top**、 **$filter**和**searchFields**来控制建议的结果。

### <a name="choose-analyzers"></a>选择分析器

您选择的分析器决定如何对字段进行标记化，并随后将其作为前缀。 例如，对于带连字符的字符串（如 "上下文相关"），使用语言分析器将导致这些标记组合： "上下文"、"敏感"、"区分上下文"。 如果你使用的是标准 Lucene 分析器，则连字符的字符串不存在。 

在评估分析器时，请考虑使用[分析文本 API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)来深入了解术语的标记方式，并随后对其进行前缀。 生成索引后，可以尝试对字符串使用各种分析器来查看标记输出。

使用[自定义分析器](index-add-custom-analyzers.md)或[预定义分析器](index-add-custom-analyzers.md#predefined-analyzers-reference)（标准为 Lucene 除外）的字段被显式禁止，以防止结果不佳。

> [!NOTE]
> 如果需要解决分析器约束（例如，如果需要对某些查询方案使用关键字或 ngram analyzer），则应将两个单独的字段用于相同的内容。 这样，就可以在其中一个字段中使用建议器，并使用自定义分析器配置来设置其他字段。

### <a name="when-to-create-a-suggester"></a>何时创建建议器

创建建议器的最佳时间是同时要创建字段定义本身时。

如果尝试使用预先存在的字段创建建议器，API 将不允许这样做。 在编制索引的过程中，如果两个或更多字符组合中的部分术语被标记为整体，则会生成前缀。 如果现有字段已标记化，而你想要将其添加到建议器，则必须重新生成索引。 有关详细信息，请参阅[如何重建 Azure 认知搜索索引](search-howto-reindex.md)。

## <a name="create-using-rest"></a>使用 REST 创建

在 REST API 中，通过[创建索引](https://docs.microsoft.com/rest/api/searchservice/create-index)或[更新索引](https://docs.microsoft.com/rest/api/searchservice/update-index)添加建议器。 

  ```json
  {
    "name": "hotels-sample-index",
    "fields": [
      . . .
          {
              "name": "HotelName",
              "type": "Edm.String",
              "facetable": false,
              "filterable": false,
              "key": false,
              "retrievable": true,
              "searchable": true,
              "sortable": false,
              "analyzer": "en.microsoft",
              "indexAnalyzer": null,
              "searchAnalyzer": null,
              "synonymMaps": [],
              "fields": []
          },
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["HotelName"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```

## <a name="create-using-net"></a>使用 .NET 创建

在 C# 中定义[建议器对象](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)。 `Suggesters` 是一个集合，但它只能采用一个项。 

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels-sample-index",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelName", "Category" }
            }}
    };

    serviceClient.Indexes.Create(definition);

}
```

## <a name="property-reference"></a>属性参考

|属性      |说明      |
|--------------|-----------------|
|`name`        |建议器的名称。|
|`searchMode`  |用于搜索候选短语的策略。 当前支持的唯一模式是`analyzingInfixMatching`，当前与某个字词的开头匹配。|
|`sourceFields`|作为建议内容源的一个或多个字段的列表。 字段的类型必须是 `Edm.String` 和 `Collection(Edm.String)`。 如果在字段中指定某个分析器，该分析器必须是[此列表](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet)中指定的分析器（而不是自定义分析器）。<p/> 作为最佳做法，请仅指定有助于生成预期相应响应的字段，无论该响应是搜索栏还是下拉列表中的已完成字符串。<p/>酒店名称就是一很好的候选项，因为它很精确。 说明和注释等详细字段过于密集。 同样，类别和标记等重复性字段的效率较低。 在示例中，我们仍然包含了“category”来演示可以包含多个字段。 |

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>使用建议器

在查询中使用建议器。 创建建议器后，调用以下 Api 之一以实现搜索即用体验：

+ [建议 REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [自动完成 REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [AutocompleteWithHttpMessagesAsync 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

在搜索应用程序中，客户端代码应利用诸如[JQUERY UI 自动完成](https://jqueryui.com/autocomplete/)这样的库来收集部分查询并提供匹配项。 有关此任务的详细信息，请参阅[将自动完成功能或建议结果添加到客户端代码](search-autocomplete-tutorial.md)。

以下对自动完成 REST API 的调用演示了 API 的用法。 此示例有两个要点。 首先，与所有查询一样，操作针对索引的文档集合，而查询包含**搜索**参数，在此示例中提供了部分查询。 其次，你必须将**suggesterName**添加到请求。 如果未在索引中定义建议器，对自动完成或建议的调用将会失败。

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2019-05-06
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

## <a name="sample-code"></a>代码示例

+ 使用[c # 创建第一个应用（第3课-添加搜索类型）](tutorial-csharp-type-ahead-and-suggestions.md)示例演示了建议器构造、建议的查询、自动完成和分面导航。 此代码示例在沙盒 Azure 认知搜索服务中运行，并使用预先加载的酒店索引，因此，只需按 F5 即可运行应用程序。 无需订阅或登录。

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) 是包含 C# 和 Java 代码的早期示例。 其中也演示了建议器的构造、建议的查询、自动完成和分面导航。 此代码示例使用托管的 [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) 示例数据。 

## <a name="next-steps"></a>后续步骤

建议阅读以下文章，了解有关请求表述的详细信息。

> [!div class="nextstepaction"]
> [向客户端代码添加自动完成和建议](search-autocomplete-tutorial.md) 
