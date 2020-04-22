---
title: 在搜索框中添加自动完成和建议
titleSuffix: Azure Cognitive Search
description: 通过创建建议器并编写使用已完成的字词或短语自动完成搜索框的请求，在 Azure 认知搜索中启用"即用即用即搜索"查询操作。 您还可以返回建议的匹配项。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 60e9a435d705ee0fee6509e92cdcb056ac7ab609
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758106"
---
# <a name="add-autocomplete-and-suggestions-to-client-apps"></a>向客户端应用添加自动完成和建议

按类型搜索是提高用户启动查询的工作效率的常用技术。 在 Azure 认知搜索中，此体验通过*自动完成*支持 ，它根据部分输入完成一个术语或短语（使用"microsoft"完成"微"）。 另一种形式是*建议*：匹配文档的简短列表（返回带有 ID 的书籍标题，以便您可以链接到详细信息页）。 自动完成和建议都基于索引中的匹配项。 该服务不会提供返回零结果的查询。

要在 Azure 认知搜索中实现这些体验，您需要：

+ 后端的*推荐器*。
+ 指定[自动完成](https://docs.microsoft.com/rest/api/searchservice/autocomplete)或[建议](https://docs.microsoft.com/rest/api/searchservice/suggestions)API 的*查询*。
+ 用于处理客户端应用中的搜索类型交互的*UI 控件*。 为此，我们建议使用现有的 JavaScript 库。

在 Azure 认知搜索中，自动完成的查询和建议的结果将从已向建议程序注册的选定字段从搜索索引中检索。 建议器是索引的一部分，它指定哪些字段将提供完成查询、建议结果或同时执行两者的内容。 创建和加载索引时，将在内部创建建议器数据结构，以存储用于在部分查询上匹配的前缀。 对于建议，选择唯一或至少不重复的合适字段对于体验至关重要。 有关详细信息，请参阅[创建建议器](index-add-suggesters.md)。

本文的其余部分侧重于查询和客户端代码。 它使用 JavaScript 和 C# 来说明要点。 REST API 示例用于简明地呈现每个操作。 有关端到端代码示例的链接，请参阅[后续步骤](#next-steps)。

## <a name="set-up-a-request"></a>设置请求

请求的元素包括一种即用即用搜索 API、部分查询和建议程序。 以下脚本使用自动完成 REST API 作为示例，演示了请求的组件。

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2019-05-06
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

**建议名称**为您提供了用于完成术语或建议的暗示感知字段。 特别是对于建议，字段列表应由在匹配结果之间提供明确选择的建议组成。 在销售电脑游戏的网站上，该字段可能是游戏标题。

**搜索**参数提供部分查询，其中字符通过 jQuery 自动完成控件馈送到查询请求。 在上面的示例中，"minecraf"是控件可能传入的静态说明。

API 不对部分查询施加最小长度要求;因此，API 不会对部分查询施加最小长度要求。它可以是一个字符。 但是，jQuery 自动完成提供了最小长度。 通常至少有两到三个字符。

匹配位于输入字符串中任意位置的术语的开头。 给定"快速棕色狐狸"，自动完成和建议将匹配部分版本的""，"快速"，"棕色"，或"狐狸"，但不是部分固定术语，如"rown"或"牛"。 此外，每个匹配设置下游扩展的范围。 部分查询"快速br"将在"快速棕色"或"快速面包"上匹配，但"棕色"或"面包"本身与"快速"或"面包"不匹配，除非"快速"在它们前面。

### <a name="apis-for-search-as-you-type"></a>用于按类型搜索的 API

按照以下链接进行 REST 和 .NET SDK 参考页：

+ [建议 REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [自动完成 REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [AutocompleteWithHttpMessagesAsync 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

## <a name="structure-a-response"></a>构造响应

自动完成和建议的响应是您可能期望的模式：[自动完成](https://docs.microsoft.com/rest/api/searchservice/autocomplete#response)返回术语列表，[建议](https://docs.microsoft.com/rest/api/searchservice/suggestions#response)返回术语加上文档 ID，以便您可以获取文档（使用[查找文档](https://docs.microsoft.com/rest/api/searchservice/lookup-document)API 获取详细信息页的特定文档）。

响应由请求上的参数形状。 对于自动完成，设置[**自动完成模式**](https://docs.microsoft.com/rest/api/searchservice/autocomplete#autocomplete-modes)以确定文本完成是否发生在一个或两个术语上。 对于"建议"，您选择的字段将确定响应的内容。

有关建议，应进一步优化响应，以避免重复或看似不相关的结果。 要控制结果，请在请求上包含更多参数。 以下参数适用于自动完成和建议，但可能更需要建议，尤其是当建议器包含多个字段时。

| 参数 | 使用情况 |
|-----------|-------|
| **$select** | 如果建议器中有多个**源字段**，请使用 **$select**选择哪个字段贡献值 （`$select=GameTitle`。 |
| **searchFields** | 将查询约束到特定字段。 |
| **$filter** | 在结果集上应用匹配条件`$filter=Category eq 'ActionAdventure'`（ 。 |
| **$top** | 将结果限制为特定数字 （）。`$top=5`|

## <a name="add-user-interaction-code"></a>添加用户交互代码

自动填充查询术语或删除匹配链接列表需要用户交互代码（通常是 JavaScript）来使用来自外部源的请求，例如针对 Azure 搜索认知索引的自动完成或建议查询。

尽管可以本机编写此代码，但使用现有 JavaScript 库中的函数要容易得多。 本文演示了两个，一个用于建议，另一个用于自动完成。 

+ "建议"示例中使用了[自动完成小部件（jQuery UI）。](https://jqueryui.com/autocomplete/) 您可以创建搜索框，然后在使用"自动完成"小部件的 JavaScript 函数中引用它。 小部件上的属性设置源（自动完成或建议函数）、执行操作前的输入字符的最小长度以及定位。

+ [XDSoft 自动完成插件](https://xdsoft.net/jqplugins/autocomplete/)是使用自动完成示例。

我们将使用这些库来生成支持建议和自动完成的搜索框。 在搜索框中收集的输入与建议和自动完成操作配对。

## <a name="suggestions"></a>建议

本节将引导您从搜索框定义开始，完成建议结果的实现。 它还显示了如何调用本文中引用的第一个 JavaScript 自动完成库的脚本。

### <a name="create-a-search-box"></a>创建搜索框

假设[jQuery UI 自动完成库](https://jqueryui.com/autocomplete/)和 C# 中的 MVC 项目，则可以使用**Index.cshtml**文件中的 JavaScript 定义搜索框。 库通过对 MVC 控制器进行异步调用来检索建议，将"即用即用"的搜索交互添加到搜索框中。

在文件夹 [Views_Home] 下的**Index.cshtml**中，创建搜索框的行可能如下所示：

```html
<input class="searchBox" type="text" id="searchbox1" placeholder="search">
```

本示例是一个简单的输入文本框，包含用于设置样式的类、JavaScript 引用的 ID，以及占位符文本。  

在同一文件中，嵌入引用搜索框的 JavaScript。 以下函数调用建议 API，它请求基于部分术语输入的匹配文档建议：

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

告诉`source`jQuery UI 自动完成函数，您可以在何处获取要在搜索框下显示的项目列表。 由于此项目是 MVC 项目，因此在HomeController.cs中调用**建议**函数，该**函数**包含用于返回查询建议的逻辑。 此函数还会传递一些参数来控制突出显示内容、模糊匹配项和字词。 自动完成 JavaScript API 会添加字词参数。

`minLength: 3`确保建议仅在搜索框中至少有三个字符时显示。

### <a name="enable-fuzzy-matching"></a>启用模糊匹配

使用模糊搜索可以根据接近的匹配项获取结果，即使用户在搜索框中拼错了单词。 编辑距离为 1，这意味着用户输入和匹配项之间最大差异为一个字符。 

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

### <a name="enable-highlighting"></a>启用突出显示

突出显示将字体样式应用于结果中对应于输入的字符。 例如，如果部分输入为"微"，则结果将显示为**微**软、**微**范围等。 突出显示基于突出显示 Pretag 和高光 PostTag 参数，这些参数与"建议"功能内联定义。

```javascript
source: "/home/suggest?highlights=true&fuzzy=true&",
```

### <a name="suggest-function"></a>建议功能

如果使用 C# 和 MVC 应用程序，**则控制器**目录下的HomeController.cs文件是可能为建议的结果创建类的位置。 在 .NET 中，建议函数基于[文档操作扩展.建议方法](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet)。

该方法`InitSearch`将经过身份验证的 HTTP 索引客户端创建到 Azure 认知搜索服务。 有关 .NET SDK 的详细信息，请参阅[如何使用 .NET 应用程序中的 Azure 认知搜索](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)。

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

Suggest 函数采用两个参数，用于确定是要返回命中的突出显示内容，还是在返回搜索词输入的同时返回模糊匹配结果。 该方法创建 [SuggestParameters 对象](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet)，该对象随后会传递给 Suggest API。 然后，结果将转换成 JSON，以便在客户端中显示。

## <a name="autocomplete"></a>自动完成

到目前为止，搜索 UX 代码一直以建议为中心。 下一个代码块使用 XDSoft jQuery UI 自动完成功能自动完成，在 Azure 认知搜索自动完成请求中传递。 与建议一样，在 C# 应用程序中，支持用户交互的代码将采用**index.cshtml**。

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

### <a name="autocomplete-function"></a>自动完成功能

自动完成基于[文档操作扩展.自动完成方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet)。 与建议一样，此代码块将位于**HomeController.cs**文件中。

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

Autocomplete 函数采用搜索字词输入。 该方法创建 [AutoCompleteParameters 对象](https://docs.microsoft.com/rest/api/searchservice/autocomplete)。 然后，结果将转换成 JSON，以便在客户端中显示。

## <a name="next-steps"></a>后续步骤

请按照这些链接获得端到端说明或代码，演示两种搜索即用式体验。 这两个代码示例都包括建议的混合实现和自动完成。

+ [教程：在 C# 中创建第一个应用（第 3 课）](tutorial-csharp-type-ahead-and-suggestions.md)
+ [C# 代码示例：azure-搜索-点点-样本/创建优先应用/3 添加类型/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/3-add-typeahead)
+ [C# 和 JavaScript，带有 REST 并行代码示例](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)