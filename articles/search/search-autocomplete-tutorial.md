---
title: 在搜索框中添加自动完成和建议
titleSuffix: Azure Cognitive Search
description: 通过创建建议器并构建可以使用已完成的字词或短语自动完成搜索框的请求，在 Azure 认知搜索中启用“边键入边搜索”查询操作。 你还可以返回建议的匹配项。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/08/2020
ms.custom: devx-track-javascript, devx-track-csharp
ms.openlocfilehash: 8be53838f6262eaafc643bc78fd08b6f02d9bac6
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89660268"
---
# <a name="add-autocomplete-and-suggestions-to-client-apps"></a>向客户端应用添加自动完成和建议

“边键入边搜索”是提高用户发起的查询的工作效率的一种常用技术。 在 Azure 认知搜索中，此体验是通过“自动完成”支持的。自动完成可根据部分输入来完成某个字词或短语（使用“microsoft”来补全“micro”）**。 另一种形式是“建议”：匹配文档的简短列表（返回具有某个 ID 的书名，以便可以链接到详细信息页）**。 自动完成和建议都是根据索引中的匹配项预测的。 服务不会提供返回零个结果的查询。

若要在 Azure 认知搜索中实现这些体验，需要：

+ 在后端上有一个建议器**。
+ 一个在请求中指定[自动完成](/rest/api/searchservice/autocomplete)或[建议](/rest/api/searchservice/suggestions) API 的查询**。
+ 一个用于在客户端应用中处理“边键入边搜索”交互的 UI 控件**。 对于此目的，我们建议使用现有的 JavaScript 库。

在 Azure 认知搜索中，自动完成的查询和建议的结果是在搜索索引中从已向建议器注册的选定字段中检索的。 建议器是索引的一部分，它指定哪些字段将提供完成查询和/或建议结果的内容。 创建并加载索引后，将在内部创建建议器数据结构，以存储用于对部分查询进行匹配的前缀。 对于建议，选择唯一或者至少不重复的适当字段对于实现该体验至关重要。 有关详细信息，请参阅[创建建议器](index-add-suggesters.md)。

本文的剩余内容将重点介绍查询和客户端代码。 其中使用了 JavaScript 和 C# 来演示关键点。 使用了 REST API 示例来简要演示每个操作。 有关端到端代码示例的链接，请参阅[后续步骤](#next-steps)。

## <a name="set-up-a-request"></a>设置请求

请求的元素包括一个“边键入边搜索”API、一个部分查询和一个建议器。 以下脚本使用“自动完成 REST API”作为示例来演示请求的组成部分。

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

suggesterName 提供建议器感知的字段，用以完成字词或建议****。 具体对于建议而言，字段列表应该由那些在匹配结果之间中提供明确选择的字段构成。 在销售电脑游戏的站点上，该字段可能是游戏名称。

search 参数提供了部分查询，其中的字符通过 jQuery 自动完成控件馈送到查询请求****。 在上面的示例中，“minecraf”静态演示了控件可以传入的内容。

API 不会对部分查询施加最小长度要求；查询长度可以短至一个字符。 但是，jQuery 自动完成提供了一个最小长度。 通常最少包含两到三个字符。

匹配项出现在输入字符串中任意位置的字词的开头。 假如输入字符串为“the quick brown fox”，则自动完成和建议都会匹配部分字词“the”、“quick”、“brown”或“fox”，但不匹配字词中包含的部分字符，例如“rown”或“ox”。 此外，每次匹配都会设置下游扩展的范围。 部分查询“quick br”将匹配“quick brown”或“quick bread”，但“brown”或“bread”本身都不是匹配项，除非它们前面带有“quick”。

### <a name="apis-for-search-as-you-type"></a>用于“边键入边搜索”的 API

请参考 REST 和 .NET SDK 参考页的以下链接：

+ [建议 REST API](/rest/api/searchservice/suggestions) 
+ [自动完成 REST API](/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync 方法](/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [AutocompleteWithHttpMessagesAsync 方法](/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

## <a name="structure-a-response"></a>构建响应

对自动完成和建议的响应是你可能对模式的期望：[自动完成](/rest/api/searchservice/autocomplete#response)返回字词列表，[建议](/rest/api/searchservice/suggestions#response)返回字词加上文档 ID，以便你提取文档（使用[查找文档](/rest/api/searchservice/lookup-document) API 提取详细信息页的特定文档）。

响应格式由请求中的参数确定。 对于自动完成，请设置 [autocompleteMode](/rest/api/searchservice/autocomplete#autocomplete-modes) 来确定是要对一个还是两个字词执行文本完成****。 对于建议，你选择的字段确定了响应的内容。

对于建议，应进一步优化响应，以避免重复或看似无关的结果。 若要控制结果，请在请求中包含更多参数。 以下参数适用于自动完成和建议，但对于建议（尤其是建议器包含多个字段的情况）可能更有必要。

| 参数 | 使用情况 |
|-----------|-------|
| **$select** | 如果建议器中有多个 sourceField****，请使用 $select**** 来选择由哪个字段提供值 (`$select=GameTitle`)。 |
| **searchFields** | 将查询限制为特定字段。 |
| **$filter** | 对结果集应用匹配条件 (`$filter=Category eq 'ActionAdventure'`)。 |
| **$top** | 将结果限制为特定的数量 (`$top=5`)。|

## <a name="add-user-interaction-code"></a>添加用户交互代码

自动填充查询字词或删除匹配链接的列表需要用户交互代码（通常为 JavaScript），这些代码可以使用来自外部源（例如，针对 Azure 搜索认知索引运行的自动完成或建议查询）的请求。

尽管你可以在本机编写此代码，但使用现有 JavaScript 库中的函数会方便得多。 本文演示了其中的两个函数，一个用于建议，另一个用于自动完成。 

+ 建议示例中使用了[自动完成小组件 (jQuery UI)](https://jqueryui.com/autocomplete/)。 你可以创建搜索框，然后在使用自动完成小组件的 JavaScript 函数中引用它。 该小组件中的属性设置源（自动完成或建议函数）、执行操作之前的输入字符的最小长度，以及位置。

+ 自动完成示例中使用了 [XDSoft 自动完成插件](https://xdsoft.net/jqplugins/autocomplete/)。

我们将使用这些库来生成支持建议和自动完成的搜索框。 搜索框中收集的输入将与建议和自动完成操作进行配对。

## <a name="suggestions"></a>建议

本部分逐步讲解建议的结果的一个实现，从搜索框定义开始。 此外，它还演示了如何使用脚本来调用本文中引用的第一个 JavaScript 自动完成库。

### <a name="create-a-search-box"></a>创建搜索框

假设你有 [jQuery UI Autocomplete 库](https://jqueryui.com/autocomplete/)并有一个以 C# 编写的 MVC 项目，则可以在 Index.cshtml 文件中使用 JavaScript 来定义搜索框****。 该库通过异步调用 MVC 控制器来检索建议，将“边键入边搜索”交互添加到搜索框。

在 \Views\Home 文件夹下的 Index.cshtml 中，用于创建搜索框的代码行可能如下所示****：

```html
<input class="searchBox" type="text" id="searchbox1" placeholder="search">
```

此示例是一个简单的输入文本框，包含用于设置样式的类、JavaScript 引用的 ID，以及占位符文本。  

在同一文件中，嵌入引用搜索框的 JavaScript。 以下函数调用建议 API，该 API 根据部分字词输入来请求建议的匹配文档：

```javascript
$(function () {
    $("#searchbox1").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

`source` 告知 jQuery UI Autocomplete 函数要从何处获取显示在搜索框下的项列表。 由于此项目是一个 MVC 项目，因此它将调用 HomeController.cs 中的 Suggest 函数，该函数包含用于返回查询建议的逻辑**** ****。 此函数还会传递一些参数来控制突出显示内容、模糊匹配项和字词。 自动完成 JavaScript API 会添加字词参数。

`minLength: 3` 确保仅当搜索框中至少有三个字符时，才显示建议。

### <a name="enable-fuzzy-matching"></a>启用模糊匹配

使用模糊搜索可以根据接近的匹配项获取结果，即使用户在搜索框中拼错了单词。 编辑距离为 1，这意味着，用户输入与匹配项之间的最大差异只能是一个字符。 

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

### <a name="enable-highlighting"></a>启用突出显示

突出显示将字体样式应用于结果中与输入对应的字符。 例如，如果部分输入为“micro”，则结果将显示为 microsoft、microscope 等**** ****。 突出显示基于 Suggestion 函数中内联定义的 HighlightPreTag 和 HighlightPostTag 参数。

```javascript
source: "/home/suggest?highlights=true&fuzzy=true&",
```

### <a name="suggest-function"></a>Suggest 函数

如果使用 C# 和 MVC 应用程序，可以在 Controllers 目录下的 HomeController.cs 文件中为建议的结果创建类****。 在 .NET 中，Suggest 函数基于 [DocumentsOperationsExtensions.Suggest 方法](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet)。 有关 .NET SDK 的详细信息，请参阅[如何从 .NET 应用程序使用 Azure 认知搜索](./search-howto-dotnet-sdk.md)。

`InitSearch` 方法在 Azure 认知搜索服务中创建经过身份验证的 HTTP 索引客户端。 [SuggestParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters)类的属性确定在结果中搜索和返回哪些字段、匹配项的数量，以及是否使用模糊匹配。 

对于自动完成，模糊匹配仅限于一次 (一个省略或错放字符) 的编辑距离。 请注意，自动完成查询中的模糊匹配有时可能会产生意外的结果，具体取决于索引大小及其分片的方式。 有关详细信息，请参阅 [分区和分片的概念](search-capacity-planning.md#concepts-search-units-replicas-partitions-shards)。

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        Select = HotelName,
        SearchFields = HotelName,
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult resp = _indexClient.Documents.Suggest(term, "sg", sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = resp.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

Suggest 函数采用两个参数，用于确定是要返回命中的突出显示内容，还是在返回搜索词输入的同时返回模糊匹配结果。 该方法创建 [SuggestParameters 对象](/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet)，该对象随后会传递给 Suggest API。 然后，结果将转换成 JSON，以便在客户端中显示。

## <a name="autocomplete"></a>自动完成

到目前为止，搜索 UX 代码以建议为中心。 下一个代码块演示了如何使用 XDSoft jQuery UI Autocomplete 函数实现自动完成，该函数传入一个请求来实现 Azure 认知搜索自动完成。 与建议一样，在 C# 应用程序中，支持用户交互的代码位于 index.cshtml 中****。

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#searchbox1").autocompleteInline({
        appendMethod: "replace",
        source: [
            function (text, add) {
                if (!text) {
                    return;
                }

                $.getJSON("/home/autocomplete?term=" + text, function (data) {
                    if (data && data.length > 0) {
                        currentSuggestion2 = data[0];
                        add(data);
                    }
                });
            }
        ]
    });

    // complete on TAB and clear on ESC
    $("#searchbox1").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#searchbox1").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#searchbox1").val("");
        }
    });
});
```

### <a name="autocomplete-function"></a>Autocomplete 函数

Autocomplete 基于 [DocumentsOperationsExtensions.Autocomplete 方法](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet)。 与建议一样，此代码块位于 HomeController.cs 文件中****。

```csharp
public ActionResult AutoComplete(string term)
{
    InitSearch();
    //Call autocomplete API and return results
    AutocompleteParameters ap = new AutocompleteParameters()
    {
        AutocompleteMode = AutocompleteMode.OneTermWithContext,
        UseFuzzyMatching = false,
        Top = 5
    };
    AutocompleteResult autocompleteResult = _indexClient.Documents.Autocomplete(term, "sg", ap);

    // Convert the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

Autocomplete 函数采用搜索字词输入。 该方法创建 [AutoCompleteParameters 对象](/rest/api/searchservice/autocomplete)。 然后，结果将转换成 JSON，以便在客户端中显示。

## <a name="next-steps"></a>后续步骤

访问以下链接，获取端到端的说明或演示上述两种“边键入边搜索”体验的代码。 这两个代码示例都包含建议和自动完成的混合实现。

+ [教程：使用 C# 创建你的第一个应用（第 3 课）](tutorial-csharp-type-ahead-and-suggestions.md)
+ [C# 代码示例：azure-search-dotnet-samples/create-first-app/3-add-typeahead/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10/3-add-typeahead)
+ [同时采用了 C# 和 JavaScript 以及 REST 的代码示例](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)