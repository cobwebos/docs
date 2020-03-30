---
title: 将自动提示查询添加到索引
titleSuffix: Azure Cognitive Search
description: 通过创建建议器并构建调用自动完成或自动建议的查询字词的请求，在 Azure 认知搜索中启用自动提示查询操作。
manager: nitinme
author: Brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: a312068d5c8c574e7b069263cf37e3b855810e4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "72790107"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-cognitive-search"></a>将建议器添加到 Azure 认知搜索中的自动提示索引

在 Azure 认知搜索中，“一边键入，一边搜索”或自动提示功能基于添加到[搜索索引](search-what-is-an-index.md)的**建议器**构造。 该构造是你要对其启用自动提示的一个或多个字段的列表。

建议器支持两种自动提示变体：自动完成 - 完成你正在键入的字词或短语；建议 - 返回匹配文档的简短列表。****  

以下屏幕截图取自[在 C# 中创建第一个应用](tutorial-csharp-type-ahead-and-suggestions.md)示例，其中演示了自动提示。 自动完成功能将预测用户可能会在搜索框中键入的内容。 实际输入为“tw”，自动完成功能补充了“in”，因此预测的搜索词为“twin”。 建议内容在下拉列表中可视化。 对于建议，可以显示文档中最恰当地描述了结果的任何部分。 在本示例中，建议内容是酒店名称。 

![自动完成和建议查询的直观比较](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "自动完成和建议查询的直观比较")

有一个索引和查询组件可在 Azure 认知搜索中实现这些行为。 

+ 在索引中，将建议器添加到索引。 可以使用门户、[REST API](https://docs.microsoft.com/rest/api/searchservice/create-index) 或 [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)。 本文的余下内容重点介绍如何创建建议器。 

+ 在查询请求中，调用[下面列出的 API](#how-to-use-a-suggester) 之一。

可按字段启用“一边键入，一边搜索”支持。 若要获得屏幕截图中所示的类似体验，可以在同一搜索解决方案中实现这两种自动提示行为。 这两个请求针对特定索引的文档集合，在用户提供至少包含三个字符的输入字符串后，将返回响应。**

## <a name="create-a-suggester"></a>创建建议器

虽然建议器有多个属性，但它主要是你要为其启用自动提示体验的字段集合。 例如，旅行应用可能需要针对目的地、城市和旅游胜地启用提前键入。 因此，所有三个字段都会进入字段集合中。

若要创建建议器，请将一个建议器添加到索引架构。 一个索引只能包含一个建议器（具体而言，建议器集合中只能有一个建议器）。 

### <a name="when-to-create-a-suggester"></a>何时创建建议器

创建建议器的最佳时间是同时要创建字段定义本身时。

如果尝试使用预先存在的字段创建建议器，API 将不允许这样做。 在编制索引期间，如果两个或更多个字符的组合中的部分字词连同完整字词一起标记化，则会创建自动提示文本。 如果现有字段已标记化，而你想要将其添加到建议器，则必须重新生成索引。 有关重新编制索引的详细信息，请参阅[如何重新生成 Azure 认知搜索索引](search-howto-reindex.md)。

### <a name="create-using-the-rest-api"></a>使用 REST API 创建

在 REST API 中，通过[创建索引](https://docs.microsoft.com/rest/api/searchservice/create-index)或[更新索引](https://docs.microsoft.com/rest/api/searchservice/update-index)添加建议器。 

  ```json
  {
    "name": "hotels",
    "fields": [
      . . .
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["hotelName", "category"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```


### <a name="create-using-the-net-sdk"></a>使用 .NET SDK 创建

在 C# 中定义[建议器对象](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)。 `Suggesters` 是一个集合，但它只能采用一个项。 

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelId", "Category" }
            }}
    };

    serviceClient.Indexes.Create(definition);

}
```

### <a name="property-reference"></a>属性参考

|properties      |描述      |
|--------------|-----------------|
|`name`        |建议器的名称。|
|`searchMode`  |用于搜索候选短语的策略。 当前唯一受支持的模式是 `analyzingInfixMatching`，此模式在句子开头或中间执行短语的灵活匹配。|
|`sourceFields`|作为建议内容源的一个或多个字段的列表。 字段的类型必须是 `Edm.String` 和 `Collection(Edm.String)`。 如果在字段中指定某个分析器，该分析器必须是[此列表](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet)中指定的分析器（而不是自定义分析器）。<p/>作为最佳做法，请仅指定有助于生成预期相应响应的字段，无论该响应是搜索栏还是下拉列表中的已完成字符串。<p/>酒店名称就是一很好的候选项，因为它很精确。 说明和注释等详细字段过于密集。 同样，类别和标记等重复性字段的效率较低。 在示例中，我们仍然包含了“category”来演示可以包含多个字段。 |

### <a name="analyzer-restrictions-for-sourcefields-in-a-suggester"></a>建议器中 sourceFields 的分析器限制

Azure 认知搜索将分析字段内容，以支持对单个字词进行查询。 建议器除了需要完整的术语外，还需要为前缀编制索引，这需要对源字段进行额外的分析。 自定义分析器配置可以组合任何不同的 tokenizer 和筛选器，其方式常常使生成建议所需的前缀变得不可能。 因此，Azure 认知搜索会阻止将具有自定义分析器的字段包含在建议器中。

> [!NOTE] 
>  如果需要解决上述限制，请对相同的内容使用两个不同的字段。 这样，就可以在其中一个字段中使用建议器，并使用自定义分析器配置来设置其他字段。

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester-in-a-query"></a>在查询中使用建议器

创建建议器后，在查询逻辑中调用相应的 API 来调用该功能。 

+ [建议 REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [自动完成 REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [AutocompleteWithHttpMessagesAsync 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

以下对自动完成 REST API 的调用演示了 API 的用法。 此示例有两个要点。 首先，与所有查询一样，操作是针对索引的文档集合执行的。 其次，可以添加查询参数。 尽管这两个 API 具有许多通用的查询参数，但列表对于每个 API 是不同的。

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs/autocomplete?[query parameters]  
api-key: [admin or query key]
```

如果未在索引中定义建议器，对自动完成或建议的调用将会失败。

## <a name="sample-code"></a>示例代码

+ [在 C# 中创建第一个应用](tutorial-csharp-type-ahead-and-suggestions.md)示例演示了建议器的构造、建议的查询、自动完成和分面导航。 此代码示例在沙盒 Azure 认知搜索服务中运行，并使用预先加载的酒店索引，因此，只需按 F5 即可运行应用程序。 无需订阅或登录。

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) 是包含 C# 和 Java 代码的早期示例。 其中也演示了建议器的构造、建议的查询、自动完成和分面导航。 此代码示例使用托管的 [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) 示例数据。 


## <a name="next-steps"></a>后续步骤

建议查看以下示例来了解如何构建请求。

> [!div class="nextstepaction"]
> [建议和自动完成示例](search-autocomplete-tutorial.md) 
