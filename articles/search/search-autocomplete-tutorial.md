---
title: 添加建议和自动完成搜索框 - Azure 搜索
description: 通过创建建议器和构建请求在 Azure 搜索中启用自动提示查询操作，以便在搜索框中填充已完成的字词。
manager: nitinme
author: mrcarter8
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: mcarter
ms.openlocfilehash: 1ec343228e32c9dd6fb126560a7a17b54c5e36cb
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183293"
---
# <a name="add-suggestions-or-autocomplete-to-your-azure-search-application"></a>添加建议或自动完成 Azure 搜索应用程序

本文介绍如何使用[建议](https://docs.microsoft.com/rest/api/searchservice/suggestions)和[自动完成](https://docs.microsoft.com/rest/api/searchservice/autocomplete)功能生成一个功能强大的，支持“一边键入，一边搜索”行为的搜索框。

+ “建议”是键入时生成的建议结果，其中的每条建议是索引中的与当前键入内容相匹配的单个结果。 

+ “自动完成”是指“完成”用户当前键入的单词或短语。 它不会返回结果，而是完成某个查询，然后你可以执行该查询来返回结果。 与建议一样，系统会根据索引中的匹配项预测查询中已完成的单词或短语。 服务不会提供返回索引中零个结果的查询。

可以下载并运行 **DotNetHowToAutocomplete** 中的示例代码来评估这些功能。 示例代码针对使用 [NYCJobs 演示数据](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)填充的预生成索引。 NYCJobs 索引包含一个[建议器构造](index-add-suggesters.md)，必须通过该构造来使用建议或自动完成。 可以使用托管在沙盒服务中的已准备好的索引，或者使用 NYCJobs 示例解决方案中的数据加载器[填充自己的索引](#configure-app)。 

**DotNetHowToAutocomplete** 示例演示了 C# 和 JavaScript 语言版本的建议和自动完成。 C# 开发人员可以逐步运行一个使用 [Azure 搜索 .NET SDK](https://aka.ms/search-sdk) 的基于 ASP.NET MVC 的应用程序。 在 HomeController.cs 文件中可以找到发出自动完成和建议的查询调用的逻辑。 JavaScript 开发人员可在 IndexJavaScript.cshtml 中找到等效的查询逻辑，该文件包含对 [Azure 搜索 REST API](https://docs.microsoft.com/rest/api/searchservice/) 的直接调用。 

对于这两个语言版本，前端用户体验基于 [jQuery UI](https://jqueryui.com/autocomplete/) 和 [XDSoft](https://xdsoft.net/jqplugins/autocomplete/) 库。 我们将使用这些库来生成支持建议和自动完成的搜索框。 在搜索框中收集的输入将与建议和自动完成操作（例如，在 HomeController.cs 或 IndexJavaScript.cshtml 中定义的建议和操作）配对。

本练习将引导你完成以下任务：

> [!div class="checklist"]
> * 以 JavaScript 实现一个搜索输入框，并针对建议的匹配项或自动完成字词发出请求
> * 在 C# 中，可在 HomeController.cs 中定义建议和自动完成操作
> * 在 JavaScript 中，可直接调用 REST API 来提供相同的功能

## <a name="prerequisites"></a>先决条件

Azure 搜索服务对于本练习是可选的，因为本解决方案使用一个托管已准备好的 NYCJobs 演示索引的实时沙盒服务。 若要在自己的搜索服务中运行此示例，请参阅[配置 NYC 作业索引](#configure-app)中的说明。

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)（版本不限）。 示例代码和说明已在免费社区版上进行了测试。

* 下载 [DotNetHowToAutoComplete 示例](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)。

该示例非常全面，涵盖了建议、自动完成、分面导航和客户端缓存。 有关该示例提供的内容的完整说明, 请查看自述文件和评论。

## <a name="run-the-sample"></a>运行示例

1. 在 Visual Studio 中打开 **AutocompleteTutorial.sln**。 该解决方案包含一个与 NYC 作业演示索引连接的 ASP.NET MVC 项目。

2. 按 F5 运行该项目，并在所选的浏览器中加载页面。

顶部显示了一个用于选择 C# 或 JavaScript 的选项。 C# 选项从浏览器调用 HomeController，并使用 Azure 搜索 .NET SDK 检索结果。 

JavaScript 选项直接从浏览器调用 Azure 搜索 REST API。 一般情况下，此选项的性能明显要好得多，因为它会使控制器与流保持隔离。 可以根据自己的需求和语言偏好选择适当的选项。 页面中提供了几个自动完成示例，为每个选项提供了一些指导。 每个示例包含了一些可供尝试的建议示例文本。  

请尝试在每个搜索框中键入几个字母，以查看结果。

## <a name="search-box"></a>搜索框

对于 C# 和 JavaScript 版本，搜索框实现是完全相同的。 

打开文件夹 Views\Home 下的 **Index.cshtml** 文件查看代码：

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

此示例是一个简单的输入文本框, 其中包含用于样式的类、JavaScript 引用的 ID 以及占位符文本。  奥秒之处在于嵌入的 JavaScript。

C# 语言示例使用 Index.cshtml 中的 JavaScript 来利用 [jQuery UI 自动完成库](https://jqueryui.com/autocomplete/)。 此库异步调用 MVC 控制器来检索建议，以此将自动完成体验添加到搜索框。 JavaScript 语言版本位于 IndexJavaScript.cshtml 中。 它包含用于搜索栏的以下脚本，以及对 Azure 搜索 REST API 的调用。

让我们看看第一个示例的 JavaScript 代码，该代码调用 jQuery UI Autocomplete 函数，并传入建议请求：

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

加载页面时，以上代码会在浏览器中运行，以便为“example1a”输入框配置 jQuery UI 自动完成。  `minLength: 3` 确保仅当搜索框中至少有三个字符时，才显示建议。  源值非常重要：

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

以上代码行告知 jQuery UI Autocomplete 函数要从何处获取显示在搜索框下的项列表。 由于此项目是 MVC 项目, 因此它将调用 HomeController.cs 中的建议函数, 其中包含用于返回查询建议的逻辑 (有关下一节中的建议的详细信息)。 此函数还会传递一些参数来控制突出显示内容、模糊匹配项和字词。 自动完成 JavaScript API 会添加字词参数。

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>扩展示例以支持模糊匹配

使用模糊搜索可以根据接近的匹配项获取结果，即使用户在搜索框中拼错了单词。 尽管不强制要求这样做，但这样可以大幅提高自动提示体验的可靠性。 让我们将源行更改为启用模糊匹配，以尝试此操作。

将以下行

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

更改为：

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

按 F5 启动应用程序。

尝试键入类似于“execative”的字母，会发现返回了包含“executive”的结果，尽管此单词与键入的字母不完全匹配。

### <a name="jquery-autocomplete--backed-by-azure-search-autocomplete"></a>基于 Azure 搜索自动完成功能的 jQuery Autocomplete

到目前为止，搜索 UX 代码以建议为中心。 下一个代码块显示在请求中为 Azure 搜索自动完成功能传入的 jQuery UI Autocomplete 函数（index.cshtml 中的第 91 行）：

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#example2").autocompleteInline({
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
    $("#example2").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#example2").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#example2").val("");
        }
    });
});
```

## <a name="c-example"></a>C# 示例

查看网页的 JavaScript 代码后，让我们看看实际使用 Azure 搜索 .NET SDK 检索建议匹配项的 C# 服务器端控制器代码。

打开 Controllers 目录下的 **HomeController.cs** 文件。 

首先会发现，名为 `InitSearch` 的 类的顶部有一个方法。 此方法创建一个经过身份验证的 HTTP 索引客户端到 Azure 搜索服务。 有关详细信息，请参阅[如何从 .NET 应用程序使用 Azure 搜索](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)。

在第 41 行可以看到 Suggest 函数。 该函数基于 [DocumentsOperationsExtensions.Suggest 方法](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet)。

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
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

在第 69 行，可以看到 Autocomplete 函数。 该函数基于 [DocumentsOperationsExtensions.Autocomplete 方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet)。

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

    // Conver the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

Autocomplete 函数采用搜索字词输入。 该方法创建 [AutoCompleteParameters 对象](https://docs.microsoft.com/rest/api/searchservice/autocomplete)。 然后，结果将转换成 JSON，以便在客户端中显示。

（可选）将断点添加到 Suggest 函数的开头，并逐步执行代码。 请注意 SDK 返回的响应，以及该响应如何转换为方法返回的结果。

页面上的其他示例遵循相同的模式来添加命中的突出显示内容，以及用于支持自动完成结果客户端缓存的分面。 请查看其中的每个示例，以了解其工作原理，以及如何在搜索体验中利用这些功能。

## <a name="javascript-example"></a>JavaScript 示例

自动完成和建议的 Javascript 实现使用 URI 作为源指定索引与操作，以此调用 REST API。 

若要查看 JavaScript 实现，请打开 **IndexJavaScript.cshtml**。 请注意，jQuery UI Autocomplete 函数还用于搜索框，它会收集搜索字词输入，并对 Azure 搜索发出异步调用，以检索建议的匹配项或已完成的字词。 

让我们看看第一个示例的 JavaScript 代码：

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: function (request, response) {
        $.ajax({
            type: "POST",
            url: suggestUri,
            dataType: "json",
            headers: {
                "api-key": searchServiceApiKey,
                "Content-Type": "application/json"
            },
            data: JSON.stringify({
                top: 5,
                fuzzy: false,
                suggesterName: "sg",
                search: request.term
            }),
                success: function (data) {
                    if (data.value && data.value.length > 0) {
                        response(data.value.map(x => x["@@search.text"]));
                    }
                }
            });
        },
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

如果将此示例与上面调用 Home 控制器的示例进行比较, 你会注意到几个相似性。  `minLength` 和 `position` 的自动完成配置完全相同。 

此处的最明显差别在于源。 此示例不是调用主控制器中的 Suggest 方法，而是在 JavaScript 函数中创建一个 REST 请求，并使用 Ajax 执行该请求。 然后，在“success”中处理响应，并将其用作源。

REST 调用使用 URI 来指定发出的是[自动完成](https://docs.microsoft.com/rest/api/searchservice/autocomplete)还是[建议](https://docs.microsoft.com/rest/api/searchservice/suggestions) API 调用。 以下 URI 分别位于第 9 和第 10 行。

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

在 148 行，可以找到调用 `autocompleteUri` 的脚本。 对 `suggestUri` 的首次调用位于第 39 行。

> [!Note]
> 此处为了方便演示 REST API 而对 JavaScript 中的服务发出了 REST 调用，但不要将此做法解释为最佳做法或建议。 在脚本中包含 API 密钥和终结点会使得有权读取脚本中的这些值的任何人都可针对你的服务发起拒绝服务攻击。 尽管可以安全地将 JavaScript 用于学习目的，但对于免费服务上托管的索引，我们建议将 Java 或 C# 用于生产代码中的索引和查询操作。 

<a name="configure-app"></a>

## <a name="configure-nycjobs-to-run-on-your-service"></a>将 NYCJobs 配置为在你的服务中运行

到目前为止，你一直在使用托管的 NYCJobs 演示索引。 若要全面深入到所有代码（包括索引），请遵照这些说明在你自己的搜索服务中创建并加载索引。

1. [创建 Azure 搜索服务](search-create-service-portal.md)或在当前订阅下[查找现有服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 可在本示例中使用免费服务。 

   > [!Note]
   > 如果使用免费的 Azure 搜索服务，则仅限使用三个索引。 NYCJobs 数据加载器将创建两个索引。 请确保服务中有足够的空间用于接受新索引。

1. 下载 [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) 示例代码。

1. 在 Visual Studio 中，打开 NYCJobs 示例代码的 DataLoader 文件夹中的 **DataLoader.sln**。

1. 添加 Azure 搜索服务的连接信息。 打开 DataLoader 项目中的 App.config，更改 TargetSearchServiceName 和 TargetSearchServiceApiKey appSettings，以反映 Azure 搜索服务和 Azure 搜索服务 API 密钥。 可在 Azure 门户中找到此信息。

1. 按 F5 启动应用程序，创建两个索引并导入 NYCJob 示例数据。

1. 打开 **AutocompleteTutorial.sln** 并编辑 **AutocompleteTutorial** 项目中的 Web.config。 将 SearchServiceName 和 SearchServiceApiKey 值更改为对搜索服务有效的值。

1. 按 F5 运行应用程序。 示例 Web 应用将在默认浏览器中打开。 体验与沙盒版本相同，只不过索引和数据托管在你的服务中。

## <a name="next-steps"></a>后续步骤

本示例演示了生成支持自动完成和建议的搜索框的基本步骤。 其中介绍了如何生成 ASP.NET MVC 应用程序，并使用 Azure 搜索 .NET SDK 或 REST API 来检索建议。

接下来，请尝试将建议和自动完成功能集成到搜索体验。 以下参考文章可提供帮助。

> [!div class="nextstepaction"]
> [自动完成 REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [建议 REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [“创建索引”REST API 中的分面索引属性](https://docs.microsoft.com/rest/api/searchservice/create-index)

