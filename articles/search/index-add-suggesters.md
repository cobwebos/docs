---
title: 将自动提示查询添加到索引 - Azure 搜索
description: 通过创建建议器并构建调用自动完成或自动建议的查询字词的请求，在 Azure 搜索中启用自动提示查询操作。
ms.date: 05/02/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: 73cfdb6a4185689a6485f55a4f6bdd1e7e3b14be
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648844"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>将建议器添加到 Azure 搜索中的自动提示索引

**建议器**是 [Azure 搜索索引](search-what-is-an-index.md)中支持“一边键入，一边搜索”体验的构造。 它包含你要对其启用自动提示查询输入的字段列表。 在索引中，同一个建议器支持以下两种自动提示变体中的一种或两种：“自动完成”可以完成你正在键入的字词或短语，“建议”提供简短的结果列表。 

以下屏幕截图演示了这两种自动提示功能。 在此 Xbox 搜索页中，自动完成项可将你转到该查询的新搜索结果页，其中的建议是可将你转到该特定游戏的页面的实际结果。 可将自动完成限制为搜索栏中的一个项，或提供如下所示的列表。 对于建议，可以显示文档中最恰当地描述了结果的任何部分。

![自动完成与建议的查询的直观比较](./media/index-add-suggesters/visual-comparison-suggest-complete.png "自动完成与建议的查询的直观比较")

有一个索引和查询组件可在 Azure 搜索中实现这些行为。 

+ 索引组件是建议器。 可以使用门户、REST API 或.NET SDK 创建建议器。 

+ 查询组件是在查询请求中指定的操作（建议或自动完成操作）。 

可按字段启用“一边键入，一边搜索”支持。 若要获得屏幕截图中所示的类似体验，可以在同一搜索解决方案中实现这两种自动提示行为。 这两个请求针对特定索引的文档集合，在用户提供至少包含三个字符的输入字符串后，将返回响应。

## <a name="create-a-suggester"></a>创建建议器

虽然建议器有多个属性，但它主要是你要为其启用自动提示体验的字段集合。 例如，旅行应用可能需要针对目的地、城市和旅游胜地启用提前键入。 因此，所有三个字段都会进入字段集合中。

若要创建建议器，请将一个建议器添加到索引架构。 一个索引只能包含一个建议器（具体而言，建议器集合中只能有一个建议器）。 

### <a name="use-the-rest-api"></a>使用 REST API

在 REST API 中，可以通过[创建索引](https://docs.microsoft.com/rest/api/searchservice/create-index)或[更新索引](https://docs.microsoft.com/rest/api/searchservice/update-index)添加建议器。 

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
创建建议器后，在查询逻辑中添加[建议 API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 或[自动完成 API](https://docs.microsoft.com/rest/api/searchservice/autocomplete)，以调用该功能。

### <a name="use-the-net-sdk"></a>使用 .NET SDK

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

## <a name="property-reference"></a>属性参考

有关建议器的要点在于，必须提供一个名称（按请求中的名称引用建议器）、一个 searchMode（当前只有一个，即“analyzingInfixMatching”），以及为其启用了自动提示的字段列表。 

定义建议器的属性包括以下内容：

|属性      |描述      |
|--------------|-----------------|
|`name`        |建议器的名称。 调用时[建议 REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 或[自动完成 REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 时将使用该建议器名称。|
|`searchMode`  |用于搜索候选短语的策略。 当前唯一受支持的模式是 `analyzingInfixMatching`，此模式在句子开头或中间执行短语的灵活匹配。|
|`sourceFields`|作为建议内容源的一个或多个字段的列表。 只有 `Edm.String` 和 `Collection(Edm.String)` 类型的字段可作为建议的源。 只能使用没有自定义语言分析器的字段。<p/>请仅指定有助于生成预期相应响应的字段，无论该响应是搜索栏还是下拉列表中的已完成字符串。<p/>酒店名称就是一很好的候选项，因为它很精确。 说明和注释等详细字段过于密集。 同样，类别和标记等重复性字段的效率较低。 在示例中，我们仍然包含了“category”来演示可以包含多个字段。 |

#### <a name="analysis-of-sourcefields-in-a-suggester"></a>建议器中的 SourceFields 分析

Azure 搜索将分析字段内容, 以便能够对单个字词进行查询。 除了需要对源字段进行额外分析的建议器外, 还需要为前缀编制索引。 自定义分析器配置可以组合任何不同的 tokenizer 和筛选器, 通常以生成不可能的建议所需的前缀的方式进行组合。 出于此原因, **Azure 搜索会阻止包含自定义分析器的字段包含在建议器中**。

> [!NOTE] 
>  解决上述限制的建议方法是对同一内容使用2个不同的字段。 这将允许其中一个字段具有建议器, 而另一个字段可使用自定义分析器配置进行设置。

## <a name="when-to-create-a-suggester"></a>何时创建建议器

为了避免重建索引，必须同时创建 `sourceFields` 中指定的建议器和字段。

如果将建议器添加到现有索引，并且该索引中的现有字段已包含在 `sourceFields` 中，则字段定义会发生根本性的变化，并且需要重建。 有关详细信息，请参阅[如何重建 Azure 搜索索引](search-howto-reindex.md)。

## <a name="how-to-use-a-suggester"></a>如何使用建议器

如前所述，可将建议器用于建议的查询和/或自动完成。 

建议器在请求中连同操作一起引用。 例如，在 GET REST 调用中，针对文档集合指定 `suggest` 或 `autocomplete`。 对于 REST，请在创建建议器后，在查询逻辑中使用[建议 API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 或[自动完成 API](https://docs.microsoft.com/rest/api/searchservice/autocomplete)。

对于 .NET，请使用 [SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet) 或 [AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)。

有关演示这两种请求的示例，请参阅[有关在 Azure 搜索中添加自动完成和建议的示例](search-autocomplete-tutorial.md)。

## <a name="sample-code"></a>示例代码

[DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) 示例包含 C# 和 Java 代码，演示了建议器构造、建议的查询，自动完成和分面导航。 

它使用沙盒 Azure 搜索服务和预先加载的索引，因此只需按 F5 即可运行。 无需提供订阅或登录。

## <a name="next-steps"></a>后续步骤

建议查看以下示例来了解如何构建请求。

> [!div class="nextstepaction"]
> [建议和自动完成示例](search-autocomplete-tutorial.md) 
