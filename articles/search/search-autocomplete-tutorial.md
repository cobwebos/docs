---
title: 在搜索框中添加自动完成和建议
titleSuffix: Azure Cognitive Search
description: 通过创建建议器并使用已完成的字词或短语来自动完成搜索框，来启用 Azure 认知搜索中的搜索即类型查询操作。 你还可以返回建议的匹配项。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 60e9a435d705ee0fee6509e92cdcb056ac7ab609
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81758106"
---
# <a name="add-autocomplete-and-suggestions-to-client-apps"></a>向客户端应用添加自动完成和建议

"搜索类型" 是一种用于提高用户启动的查询的工作效率的常用方法。 在 Azure 认知搜索中，通过 "*自动完成" 功能*支持此体验，该功能根据部分输入完成一个词或短语（使用 "microsoft" 完成 "微"）。 另一种形式是*建议*：匹配文档的简短列表（使用 ID 返回书名，以便可以链接到详细信息页）。 自动完成和建议依据索引中的匹配项。 服务不会提供返回零结果的查询。

若要在 Azure 认知搜索中实现这些体验，你将需要：

+ 后端的*建议器*。
+ 一个*查询*，用于指定请求的[自动完成](https://docs.microsoft.com/rest/api/searchservice/autocomplete)或[建议](https://docs.microsoft.com/rest/api/searchservice/suggestions)API。
+ 用于在客户端应用程序中处理搜索即类型交互的*UI 控件*。 出于此目的，我们建议使用现有的 JavaScript 库。

在 Azure 认知搜索中，自动完成查询和建议的结果是从搜索索引中检索的，从已向建议器注册的选定字段中进行检索。 建议器是索引的一部分，它指定哪些字段将提供完成查询、建议结果或同时执行这两项的内容。 创建并加载索引后，会在内部创建建议器数据结构来存储用于在部分查询上进行匹配的前缀。 对于建议，选择唯一或至少不重复的合适字段对于体验是必不可少的。 有关详细信息，请参阅[创建建议器](index-add-suggesters.md)。

本文的其余部分重点介绍查询和客户端代码。 它使用 JavaScript 和 c # 来说明要点。 REST API 示例用于简洁演示每个操作。 有关端到端代码示例的链接，请参阅[后续步骤](#next-steps)。

## <a name="set-up-a-request"></a>设置请求

请求的元素包括一个搜索即类型 Api、一个部分查询和一个建议器。 下面的脚本使用 "自动完成" REST API 作为示例来说明了请求的组件。

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2019-05-06
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

**SuggesterName**提供了用于完成条款或建议的建议器感知字段。 对于特别建议，字段列表应由在匹配结果中提供清晰选择的字段组成。 在销售计算机游戏的站点上，字段可能是游戏标题。

**Search**参数提供了部分查询，其中，字符通过 JQuery 自动完成控件送到查询请求。 在上面的示例中，"minecraf" 是控件可能传入的内容的静态图例。

Api 不会对部分查询施加最小长度要求;它可以只是一个字符。 但是，jQuery 自动完成功能提供最小长度。 通常，最少包含两个或三个字符。

匹配项在输入字符串中的任何位置的开头。 给定 "快速棕色的 fox"，自动完成和建议将在部分版本的 "the"、"quick"、"棕色" 或 "fox" 中匹配，但不匹配 "rown" 或 "ox" 等部分中缀术语。 而且，每个匹配项设置下游扩展的作用域。 "Quick br" 的部分查询将与 "quick 棕色" 或 "quick 面包" 匹配，但其本身不会匹配 "棕色" 或 "面包"，除非它们的前面有 "quick"。

### <a name="apis-for-search-as-you-type"></a>作为 "搜索类型" 的 Api

对于 REST 和 .NET SDK 参考页面，请单击以下链接：

+ [建议 REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [自动完成 REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [AutocompleteWithHttpMessagesAsync 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

## <a name="structure-a-response"></a>构造响应

"自动完成" 和 "建议" 的回答是您对模式的期望：[自动完成](https://docs.microsoft.com/rest/api/searchservice/autocomplete#response)返回一系列条款，[建议](https://docs.microsoft.com/rest/api/searchservice/suggestions#response)返回条款和文档 ID，以便您可以提取文档（使用[查找文档](https://docs.microsoft.com/rest/api/searchservice/lookup-document)API 提取详细信息页的特定文档）。

响应由请求中的参数整形。 对于自动完成，请设置[**autocompleteMode**](https://docs.microsoft.com/rest/api/searchservice/autocomplete#autocomplete-modes) ，以确定文本是否在一个或两个字词上发生。 对于建议，你选择的字段将确定响应的内容。

对于建议，您应该进一步优化响应以避免重复，或显示为不相关的结果。 若要控制结果，请在请求中包括更多参数。 以下参数适用于 "自动完成" 和 "建议"，但对于建议更是必需的，尤其是在建议器包含多个字段的情况下。

| 参数 | 使用情况 |
|-----------|-------|
| **$select** | 如果在建议器中具有多个**sourceFields** ，请使用 **$select**选择提供值（`$select=GameTitle`）的字段。 |
| **searchFields** | 将查询限制为特定的字段。 |
| **$filter** | 对结果集（`$filter=Category eq 'ActionAdventure'`）应用 match 条件。 |
| **$top** | 将结果限制为特定数量（`$top=5`）。|

## <a name="add-user-interaction-code"></a>添加用户交互代码

自动填充查询项或删除匹配链接的列表需要用户交互代码（通常为 JavaScript），这些代码可使用来自外部源的请求，例如自动完成或针对 Azure 搜索认知索引的建议查询。

虽然您可以在本机编写此代码，但使用现有 JavaScript 库中的函数会更容易。 本文介绍两个示例，一个用于建议，另一个用于自动完成。 

+ 建议示例中使用了[自动完成小组件（JQUERY UI）](https://jqueryui.com/autocomplete/) 。 您可以创建搜索框，然后在使用自动完成小组件的 JavaScript 函数中引用它。 小组件上的属性设置源（自动完成或建议函数）、执行操作前的输入字符的最小长度和定位。

+ [XDSoft 自动完成插件](https://xdsoft.net/jqplugins/autocomplete/)使用自动完成示例。

我们将使用这些库来生成支持建议和自动完成的搜索框。 搜索框中收集的输入与建议和自动完成操作配对。

## <a name="suggestions"></a>建议

本部分将指导你实现建议的结果，从搜索框定义开始。 它还显示了如何和脚本来调用本文中引用的第一个 JavaScript 自动完成库。

### <a name="create-a-search-box"></a>创建搜索框

假设[JQUERY UI 自动完成库](https://jqueryui.com/autocomplete/)和使用 c # 编写的 MVC 项目，则可以在**索引 cshtml**文件中使用 JavaScript 定义搜索框。 库通过对 MVC 控制器进行异步调用来检索建议，从而向搜索框添加 "搜索即类型" 交互。

在**文件夹 \Views\Home 下的**中，创建搜索框的行可能如下所示：

```html
<input class="searchBox" type="text" id="searchbox1" placeholder="search">
```

本示例是一个简单的输入文本框，包含用于设置样式的类、JavaScript 引用的 ID，以及占位符文本。  

在同一文件中，嵌入引用搜索框的 JavaScript。 以下函数调用建议 API，该 API 根据部分术语输入请求建议的匹配文档：

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

`source`指示 JQuery UI 自动完成功能，其中获取要在搜索框下显示的项的列表。 由于此项目是 MVC 项目，因此它将调用**HomeController.cs**中的**建议**函数，其中包含用于返回查询建议的逻辑。 此函数还会传递一些参数来控制突出显示内容、模糊匹配项和字词。 自动完成 JavaScript API 会添加字词参数。

`minLength: 3`确保只在搜索框中有至少三个字符时才会显示建议。

### <a name="enable-fuzzy-matching"></a>启用模糊匹配

使用模糊搜索可以根据接近的匹配项获取结果，即使用户在搜索框中拼错了单词。 编辑距离为1，这意味着用户输入和匹配项之间可能存在一个字符的最大差异。 

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

### <a name="enable-highlighting"></a>启用突出显示

突出显示会将字体样式应用于结果中对应于输入的字符。 例如，如果部分输入为 "微"，则结果将显示为**微****软范围等**。 突出显示基于 HighlightPreTag 和 HighlightPostTag 参数，该参数与建议函数内联定义。

```javascript
source: "/home/suggest?highlights=true&fuzzy=true&",
```

### <a name="suggest-function"></a>建议函数

如果使用 c # 和 MVC 应用程序，则可以在 "控制器" 目录下创建一个**HomeController.cs**文件，以便为建议的结果创建一个类。 在 .NET 中，建议函数基于[DocumentsOperationsExtensions 方法](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet)。

`InitSearch`方法为 Azure 认知搜索服务创建经过身份验证的 HTTP 索引客户端。 有关 .NET SDK 的详细信息，请参阅[如何通过 .Net 应用程序使用 Azure 认知搜索](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)。

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

到目前为止，搜索 UX 代码已经过了建议。 下一个代码块显示自动完成，使用 XDSoft jQuery UI 自动完成功能，传入 Azure 认知搜索自动完成的请求。 与建议一样，在 c # 应用程序中，支持用户交互的代码将进入**索引 cshtml**。

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

自动完成功能基于[DocumentsOperationsExtensions 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet)。 与建议一样，此代码块会出现在**HomeController.cs**文件中。

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

请访问以下链接，获取端到端说明或演示这两种搜索方式体验的代码。 这两个代码示例都同时包含建议的混合实现和自动完成功能。

+ [教程：在 c # 中创建第一个应用（第3课）](tutorial-csharp-type-ahead-and-suggestions.md)
+ [C # 代码示例： dotnet/创建-第一-应用/3-typeahead/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/3-add-typeahead)
+ [使用 REST 并行代码的 c # 和 JavaScript 示例](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)