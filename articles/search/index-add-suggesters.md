---
title: 将 typeahead 查询添加到索引
titleSuffix: Azure Cognitive Search
description: 通过创建调用 "自动完成" 或 "autosuggested" 查询词的建议器并构建请求来在 Azure 认知搜索中启用预先键入的查询操作。
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
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790107"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-cognitive-search"></a>将建议器添加到 Azure 中的 typeahead 索引认知搜索

在 Azure 认知搜索中，"搜索类型" 或 typeahead 功能基于添加到[搜索索引](search-what-is-an-index.md)的**建议器**构造。 它是要为其启用 typeahead 的一个或多个字段的列表。

建议器支持两个 typeahead 变量： *"自动完成*"，这将完成你键入的字词或短语，并提供返回匹配文档的简短列表的*建议*。  

下面从在示例中[创建第一个应用程序C# ](tutorial-csharp-type-ahead-and-suggestions.md)中的屏幕截图说明了 typeahead。 自动完成将预测用户可能在搜索框中键入的内容。 实际输入为 "幼圆"，自动完成功能以 "in" 结束，作为预期搜索词解决。 建议在下拉列表中进行可视化。 对于建议，您可以呈现最能描述结果的文档的任何部分。 在此示例中，建议是宾馆名称。 

![自动完成和建议的查询的直观比较](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "自动完成和建议的查询的直观比较")

若要在 Azure 认知搜索中实现这些行为，有一个索引和查询组件。 

+ 在索引中，将建议器添加到索引。 可以使用门户、 [REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)或[.net SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)。 本文的其余部分重点介绍如何创建建议器。 

+ 在查询请求中，调用[以下列出的 api](#how-to-use-a-suggester)之一。

根据每个字段启用 "搜索即类型" 支持。 如果你希望体验与屏幕截图中所示的类似，则可以在同一搜索解决方案中实现这两个 typeahead 行为。 两个请求都将在用户至少提供三个字符输入字符串后返回特定索引的*文档*集合和响应。

## <a name="create-a-suggester"></a>创建建议器

尽管建议器具有多个属性，但它主要是要为其启用 typeahead 体验的字段集合。 例如，旅行应用可能需要针对目的地、城市和旅游胜地启用提前键入。 因此，所有三个字段都将放入字段集合中。

若要创建建议器，请将其添加到索引架构。 索引中可以有一个建议器（具体而言，是建议器集合中的一个建议器）。 

### <a name="when-to-create-a-suggester"></a>何时创建建议器

创建建议器的最佳时间是在同时创建字段定义。

如果尝试使用预先存在的字段创建建议器，则 API 会禁止使用。 在编制索引期间，如果两个或更多字符组合中的部分术语被标记为完整的词，则会创建 Typeahead 文本。 如果现有字段已标记为已标记，则如果要将其添加到建议器中，则必须重新生成索引。 有关重建索引的详细信息，请参阅[如何重建 Azure 认知搜索索引](search-howto-reindex.md)。

### <a name="create-using-the-rest-api"></a>使用 REST API 创建

在 REST API 中，通过 "[创建索引](https://docs.microsoft.com/rest/api/searchservice/create-index)" 或 "[更新索引](https://docs.microsoft.com/rest/api/searchservice/update-index)" 添加建议器。 

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

在C#中，定义一个[建议器对象](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)。 `Suggesters` 是一个集合，但它只能采用一个项。 

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
|`sourceFields`|作为建议内容源的一个或多个字段的列表。 字段的类型必须是 `Edm.String` 和 `Collection(Edm.String)`。 如果对该字段指定了分析器，则它必须是[此列表](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet)中的命名分析器（而不是自定义分析器）。<p/>作为最佳方案，请仅指定那些仅适用于预期和适当响应的字段，无论是搜索栏中的已完成字符串还是下拉列表。<p/>宾馆名称是一个很好的候选项，因为它具有精度。 描述和注释等详细字段过于密集。 同样，重复字段（如类别和标记）的效率较低。 在示例中，我们包含 "category"，但仍表明可以包含多个字段。 |

### <a name="analyzer-restrictions-for-sourcefields-in-a-suggester"></a>建议器中 sourceFields 的分析器限制

Azure 认知搜索会分析字段内容，以便能够按单个术语进行查询。 除了需要对源字段进行额外分析的建议器外，还需要为前缀编制索引。 自定义分析器配置可以组合任何不同的 tokenizer 和筛选器，通常以生成不可能的建议所需的前缀的方式进行组合。 出于此原因，Azure 认知搜索会阻止包含自定义分析器的字段包含在建议器中。

> [!NOTE] 
>  如果需要解决上述限制，请对同一内容使用两个不同的字段。 这将允许其中一个字段具有建议器，而另一个字段可使用自定义分析器配置进行设置。

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester-in-a-query"></a>在查询中使用建议器

创建建议器后，在查询逻辑中调用相应的 API 以调用该功能。 

+ [建议 REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [自动完成 REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [AutocompleteWithHttpMessagesAsync 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

以下对 "自动完成" REST API 的调用演示了 API 使用情况。 此示例有两个优点。 首先，与所有查询一样，操作针对索引的文档集合。 其次，你可以添加查询参数。 尽管这两个 Api 共用许多查询参数，但每个 Api 的列表都是不同的。

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs/autocomplete?[query parameters]  
api-key: [admin or query key]
```

如果索引中未定义建议器，则对自动完成或建议的调用将失败。

## <a name="sample-code"></a>代码示例

+ [在示例中C#创建第一个应用程序](tutorial-csharp-type-ahead-and-suggestions.md)演示了建议器构造、建议的查询、自动完成和分面导航。 此代码示例在沙箱 Azure 认知搜索服务上运行，并使用预加载的酒店索引，因此，只需按 F5 运行该应用程序即可。 无需订阅或登录。

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)是包含C#和 Java 代码的较旧示例。 它还演示了建议器构造、建议的查询、自动完成和分面导航。 此代码示例使用寄宿的[NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)示例数据。 


## <a name="next-steps"></a>后续步骤

建议采用以下示例来了解如何表述请求。

> [!div class="nextstepaction"]
> [建议和自动完成示例](search-autocomplete-tutorial.md) 
