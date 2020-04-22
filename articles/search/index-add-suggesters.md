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
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770103"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>创建建议程序以在查询中启用自动完成和建议的结果

在 Azure 认知搜索中，通过添加到[搜索索引](search-what-is-an-index.md)**的建议器**构造启用"按类型搜索"。 建议器支持两种体验：*自动完成*，完成整个术语查询的部分输入，以及邀请单击到特定匹配*项的建议*。 自动完成生成查询。 建议生成匹配的文档。

以下屏幕截图在[C# 中创建第一个应用](tutorial-csharp-type-ahead-and-suggestions.md)说明了这两个示例。 自动完成预期一个潜在的术语，用"in"完成"tw"。 建议是迷你搜索结果，其中像酒店名称这样的字段表示索引中的匹配酒店搜索文档。 有关建议，可以显示任何提供描述性信息的字段。

![自动完成和建议查询的直观比较](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "自动完成和建议查询的直观比较")

您可以单独或一起使用这些功能。 有一个索引和查询组件可在 Azure 认知搜索中实现这些行为。 

+ 在索引中，将建议器添加到索引。 可以使用门户、[REST API](https://docs.microsoft.com/rest/api/searchservice/create-index) 或 [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)。 本文的余下内容重点介绍如何创建建议器。

+ 在查询请求中，调用[下面列出的 API](#how-to-use-a-suggester) 之一。

基于字符串字段的按字段启用搜索即类型支持。 若要获得屏幕截图中所示的类似体验，可以在同一搜索解决方案中实现这两种自动提示行为。 这两个请求针对特定索引的文档集合，在用户提供至少包含三个字符的输入字符串后，将返回响应。 

## <a name="what-is-a-suggester"></a>什么是建议者？

建议程序是一种内部数据结构，通过存储用于在部分查询上匹配的前缀，支持按类型搜索行为。 与标记化术语一样，前缀存储在倒置索引中，每个字段都存储在建议器字段集合中。

## <a name="define-a-suggester"></a>定义建议者

要创建建议程序，请向[索引架构](https://docs.microsoft.com/rest/api/searchservice/create-index)添加一个，并[设置每个属性](#property-reference)。 创建建议器的最佳时间是定义将使用它的字段。

+ 仅使用字符串字段

+ 在现场使用默认标准 Lucene`"analyzer": null`分析器 （ ） 或[语言分析器](index-add-language-analyzers.md)（例如`"analyzer": "en.Microsoft"`），

### <a name="choose-fields"></a>选择字段

尽管建议器具有多个属性，但它主要是字符串字段的集合，您为其启用搜索即用式体验。 每个索引都有一个建议器，因此建议者列表必须包含所有为建议和自动完成贡献内容的字段。

自动完成从更大的字段池中获益，因为附加内容具有更大的期限完成潜力。

另一方面，当字段选择是选择性的时，建议会产生更好的结果。 请记住，该建议是搜索文档的代理，因此您需要最能表示单个结果的字段。 区分多个匹配项的名称、标题或其他唯一字段最符合最佳功能。 如果字段由重复值组成，则建议由相同的结果组成，用户不知道要单击哪个字段。

为了满足"即搜索即用"体验，添加自动完成所需的所有字段，然后使用 **$select、$top、$filter**和**搜索字段**来控制 **$select**建议的结果。 **$filter**

### <a name="choose-analyzers"></a>选择分析仪

您选择的分析器决定了字段如何标记并随后预定。 例如，对于连字符字符串（如"上下文敏感"），使用语言分析器将导致这些令牌组合："上下文"、"敏感"、"上下文敏感"。 如果使用标准 Lucene 分析器，则连字符字符串将不存在。 

在评估分析器时，请考虑使用[分析文本 API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)来深入了解术语如何标记并随后预缀。 生成索引后，可以在字符串上尝试各种分析器以查看令牌输出。

使用[自定义分析器](index-add-custom-analyzers.md)或[预定义分析仪](index-add-custom-analyzers.md#predefined-analyzers-reference)（标准 Lucene 除外）的字段明确不允许防止不良结果。

> [!NOTE]
> 如果需要解决分析器约束，例如，如果需要关键字或 ngram 分析器用于某些查询方案，则应对同一内容使用两个单独的字段。 这样，就可以在其中一个字段中使用建议器，并使用自定义分析器配置来设置其他字段。

### <a name="when-to-create-a-suggester"></a>何时创建建议器

创建建议器的最佳时间是同时要创建字段定义本身时。

如果尝试使用预先存在的字段创建建议器，API 将不允许这样做。 前缀在索引期间生成，当两个或多个字符组合中的部分术语与整个术语一起标记时。 如果现有字段已标记化，而你想要将其添加到建议器，则必须重新生成索引。 有关详细信息，请参阅[如何重建 Azure 认知搜索索引](search-howto-reindex.md)。

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
|`searchMode`  |用于搜索候选短语的策略。 当前支持的唯一模式是`analyzingInfixMatching`，当前与 术语开头匹配。|
|`sourceFields`|作为建议内容源的一个或多个字段的列表。 字段的类型必须是 `Edm.String` 和 `Collection(Edm.String)`。 如果在字段中指定某个分析器，该分析器必须是[此列表](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet)中指定的分析器（而不是自定义分析器）。<p/> 作为最佳做法，请仅指定有助于生成预期相应响应的字段，无论该响应是搜索栏还是下拉列表中的已完成字符串。<p/>酒店名称就是一很好的候选项，因为它很精确。 说明和注释等详细字段过于密集。 同样，类别和标记等重复性字段的效率较低。 在示例中，我们仍然包含了“category”来演示可以包含多个字段。 |

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>使用建议器

查询中使用建议器。 创建建议程序后，调用以下 API 之一进行搜索即用式体验：

+ [建议 REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [自动完成 REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [AutocompleteWithHttpMessagesAsync 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

在搜索应用程序中，客户端代码应利用[jQuery UI 自动完成](https://jqueryui.com/autocomplete/)等库来收集部分查询并提供匹配项。 有关此任务的详细信息，请参阅[将自动完成或建议的结果添加到客户端代码](search-autocomplete-tutorial.md)。

以下对自动完成 REST API 的调用演示了 API 的用法。 此示例有两个要点。 首先，与所有查询一样，该操作针对索引的文档集合，查询包含**一个搜索**参数，在这种情况下，该参数提供部分查询。 其次，您必须向请求添加**建议名称**。 如果未在索引中定义建议器，对自动完成或建议的调用将会失败。

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2019-05-06
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

## <a name="sample-code"></a>示例代码

+ [在 C# 中创建第一个应用（第 3 课 - 添加搜索即键入）](tutorial-csharp-type-ahead-and-suggestions.md)示例演示建议器构造、建议查询、自动完成和分面导航。 此代码示例在沙盒 Azure 认知搜索服务中运行，并使用预先加载的酒店索引，因此，只需按 F5 即可运行应用程序。 无需订阅或登录。

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) 是包含 C# 和 Java 代码的早期示例。 其中也演示了建议器的构造、建议的查询、自动完成和分面导航。 此代码示例使用托管的 [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) 示例数据。 

## <a name="next-steps"></a>后续步骤

我们建议使用以下文章来详细了解请求的表述方式。

> [!div class="nextstepaction"]
> [向客户端代码添加自动完成和建议](search-autocomplete-tutorial.md) 
