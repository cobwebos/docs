---
title: 添加到索引的 Azure 搜索 typeahead 查询
description: 通过创建建议器和表述请求调用记忆式键入功能或 autosuggested 查询词中启用 Azure 搜索中的提前键入查询操作。
ms.date: 03/22/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: 877294e80d655ab75be78a5aa57854a03a5f267a
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370646"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>将建议器添加到 Azure 搜索中的 typeahead 的索引

一个**建议器**是在构造[Azure 搜索索引](search-what-is-an-index.md)支持"搜索---键入时"体验。 它包含你想要启用 typeahead 查询输入的字段的列表。 有两个 typeahead 变体： [*记忆式键入功能*](search-autocomplete-tutorial.md)字词或短语键入内容时，完成[*自动推荐*](search-autosuggest-example.md)提供较短词或短语，可以选择作为输入的查询的列表。 毫无疑问，您已了解之前商业搜索引擎中的这些行为。

![记忆式键入功能直观的比较和自动建议](./media/index-add-suggesters/visual-comparison-suggest-complete.png "记忆式键入功能直观的比较和自动建议")

若要在 Azure 搜索中实现这些行为，没有索引和查询组件。 

+ 在索引中，添加建议器。 您可以使用门户、 REST API 或.NET SDK 来创建建议器。 

+ 上一个查询，指定自动建议或自动完成操作。 

> [!Important]
> 记忆式键入功能目前以预览版提供，可在预览版 REST Api 和.NET SDK，并且不支持用于生产应用程序。 

在每个字段的基础上启用 Typeahead 支持。 如果你想体验类似于屏幕截图所示，您可以实现相同的搜索解决方案中这两种 typeahead 行为。 这两个请求目标*文档*后用户已提供的至少三个字符的输入的字符串，将返回的特定索引和响应的集合。

## <a name="create-a-suggester"></a>创建建议器

尽管建议器具有多个属性，但它是主要是要为其启用 typeahead 体验的字段的集合。 例如，旅行应用可能需要针对目的地、城市和旅游胜地启用提前键入。 在这种情况下，所有三个字段会转字段集合中。

若要创建建议器，将添加到索引架构。 索引中可以有一个建议器 (具体而言，建议器集合中的一个建议器)。 

在 REST API 中，可以添加到建议器[Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index)或[更新索引](https://docs.microsoft.com/rest/api/searchservice/update-index)。 

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

在.NET SDK 中，使用[建议器类](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)。 建议器是一个集合，但该方法仅采用一个项。

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

要注意建议器的关键点是没有 （建议器请求上按名称引用） 的名称、 searchmode 设置为 （当前只是一个"analyzinginfixmatching 为"），并为其启用 typeahead 的字段的列表。 

在创建建议器以后，请在查询逻辑中添加[建议 API](https://docs.microsoft.com/rest/api/searchservice/suggestions)，以便调用该功能。

### <a name="property-reference"></a>属性参考

定义建议器的属性包括以下内容：

|属性      |描述      |
|--------------|-----------------|
|`name`        |建议器的名称。 在调用时使用的建议器名称[建议 REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions)或[记忆式键入功能 REST API （预览）](https://docs.microsoft.com/rest/api/searchservice/autocomplete)。|
|`searchMode`  |用于搜索候选短语的策略。 当前唯一受支持的模式是 `analyzingInfixMatching`，此模式在句子开头或中间执行短语的灵活匹配。|
|`sourceFields`|作为建议内容源的一个或多个字段的列表。 只有 `Edm.String` 和 `Collection(Edm.String)` 类型的字段可作为建议的源。 只能使用没有自定义语言分析器的字段。<p/>仅指定这些字段都适用于预期进行相应的响应，无论它是在搜索栏或下拉列表中完成的字符串。<p/>酒店名称是好的候选，因为它具有精度。 详细的字段，例如说明和注释都过于密集。 同样，重复性字段，例如类别和标记，将会降低。 在示例中，我们"的类别"仍要包含演示您可以包含多个字段。 |

### <a name="index-rebuilds-for-existing-fields"></a>索引重新生成现有字段

建议器包含字段，并且如果您要添加到现有索引的建议器或更改其字段组合，您很可能需要重新生成索引。

| 操作 | 影响 |
|--------|--------|
| 创建新的字段，并同时在同一更新中创建新的建议器 | 影响最小。 如果索引包含以前添加的字段，添加了新的字段和新的建议器对现有字段没有任何影响。 |
| 将预先存在的字段添加到建议器 | 高影响。 添加一个字段更改字段定义，因而需要进行[完全重新生成](search-howto-reindex.md)。|

## <a name="use-a-suggester"></a>使用建议器

如上文所述，可以自动建议和 / 或记忆式键入功能，使用建议器。 

建议器引用的操作以及请求。 例如上一个 GET REST 调用中，, 指定`suggest`或`autocomplete`documents 集合。 对于其余部分中，创建建议器后，请使用[建议 API](https://docs.microsoft.com/rest/api/searchservice/suggestions)或[记忆式键入功能 API （预览）](https://docs.microsoft.com/rest/api/searchservice/autocomplete)中查询逻辑。

对于.NET，请使用[SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet-preview)或[AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet-preview&viewFallbackFrom=azure-dotnet)。

示例来演示每个请求：

+ [添加下拉列表中的查询选择的自动建议](search-autosuggest-example.md)

+ [将记忆式键入功能添加到 Azure 搜索中的部分术语输入](search-autocomplete-tutorial.md)（处于预览状态） 

## <a name="sample-code"></a>代码示例

[DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)同时包含示例C#和 Java 代码，并演示了建议器构造中，自动建议，记忆式键入功能，以及分面导航。 

它使用沙盒 Azure 搜索服务，预加载的索引，因此您必须执行操作是按 F5 来运行它。 不到订阅或在必要时登录。

## <a name="next-steps"></a>后续步骤

请查看下面的示例，若要查看请求进行公式化表示：

+ [Autosuggested 的查询示例](search-autosuggest-example.md) 
+ [自动完成查询示例 （预览版）](search-autocomplete-tutorial.md) 
