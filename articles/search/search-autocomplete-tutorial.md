---
title: 记忆式键入功能添加到搜索框-Azure 搜索 typeahead 示例
description: 通过创建建议器和系统地阐明在具有已完成的词或短语的搜索框中填充的请求启用 typeahead Azure 搜索中的查询操作。
manager: pablocas
author: mrcarter8
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: mcarter
ms.custom: seodec2018
ms.openlocfilehash: b78fdf0c493e4631e4cdd7e26b154570b6226d1f
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369560"
---
# <a name="example-add-autocomplete-to-partial-term-inputs-in-azure-search"></a>示例：将记忆式键入功能添加到 Azure 搜索中的部分术语输入

此预览功能"完成"的部分术语输入通过提供 Azure 搜索索引中的文档中已完成的字词。 您可能已经注意到商业搜索引擎中的此功能。 向 Azure 搜索解决方案以简化查询获取，现在可以添加此功能目前处于公共预览状态。

在此示例中，您将学习如何使用[建议](https://docs.microsoft.com/rest/api/searchservice/suggestions)，[记忆式键入功能](https://docs.microsoft.com/rest/api/searchservice/autocomplete)并[方面](search-faceted-navigation.md)中[Azure 搜索 REST API](https://docs.microsoft.com/rest/api/searchservice/)和[.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions?view=azure-dotnet)构建功能强大的搜索框。 

+ “建议”根据用户键入的内容提供实际结果的建议。 
+ “自动完成”是 Azure 搜索中[新的预览版功能](search-api-preview.md)，它提供索引中的字词来补全用户当前正在键入的内容。 

我们将会比较多种技术，以改善用户工作效率，在用户键入内容时为他们提供丰富的搜索功能。

此示例将指导你完成使用的基于 ASP.NET MVC 的应用程序C#来调用[Azure 搜索.NET 客户端库](https://aka.ms/search-sdk)，并直接调用 Azure 搜索 REST API 的 JavaScript。 对于此示例应用程序针对索引填充[NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)示例数据。 可以使用 NYC Jobs 演示中已配置的索引，也可以使用 NYCJobs 示例解决方案中的数据加载器填充自己的索引。 该示例使用 [jQuery UI](https://jqueryui.com/autocomplete/) 和 [XDSoft](https://xdsoft.net/jqplugins/autocomplete/) JavaScript 库生成支持自动完成的搜索框。 将这些组件与 Azure 搜索结合使用，可以查看用于演示如何在搜索框中支持自动完成和提前键入的多个示例。

将要执行以下任务：

> [!div class="checklist"]
> * 下载并配置解决方案
> * 将搜索服务信息添加到应用程序设置
> * 实现一个搜索输入框
> * 在自动完成列表中添加从远程源提取数据的支持 
> * 检索的建议和记忆式键入功能使用.NET SDK 和 REST API
> * 支持客户端缓存以改善性能 

## <a name="prerequisites"></a>先决条件

* Visual Studio 2017。 可以使用免费的 [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)。 

* 下载示例[源代码](https://github.com/azure-samples/search-dotnet-getting-started)的示例。

* （可选）一个有效的 Azure 帐户和 Azure 搜索服务。 如果没有 Azure 帐户，可以注册[免费试用版](https://azure.microsoft.com/free/)。 有关服务预配的帮助，请参阅[创建搜索服务](search-create-service-portal.md)。 因为可以使用非托管的 NYCJobs 索引已在位置进行不同演示完成此示例中，帐户和服务就是可选的。

* （可选）下载 [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) 示例代码，以便将 NYCJobs 数据导入到自己的 Azure 搜索服务上的某个索引中。

> [!Note]
> 如果使用免费的 Azure 搜索服务，则仅限使用三个索引。 NYCJobs 数据加载器将创建两个索引。 请确保服务中有足够的空间用于接受新索引。

### <a name="set-up-azure-search-optional"></a>设置 Azure 搜索（可选）

若要将 NYCJobs 示例应用程序的数据导入自己的索引，请遵循本部分中的步骤。 此步骤是可选的。  若要使用提供的示例索引，请直接跳到有关运行示例的下一部分。

1. 在 Visual Studio 中，打开 NYCJobs 示例代码的 DataLoader 文件夹中的 DataLoader.sln 解决方案文件。

1. 更新 Azure 搜索服务的连接信息。  打开 DataLoader 项目中的 App.config，更改 TargetSearchServiceName 和 TargetSearchServiceApiKey appSettings，以反映 Azure 搜索服务和 Azure 搜索服务 API 密钥。  可在 Azure 门户中找到这些信息。

1. 按 F5 启动应用程序。  随后会创建 2 个索引，并导入 NYCJob 示例数据。

1. 在示例的示例代码中，打开 Visual Studio 中的 AutocompleteTutorial.sln 解决方案文件。  打开 AutocompleteTutorial 项目中的 Web.config，并将 SearchServiceName 和 SearchServiceApiKey 值更改为上述相同值。

### <a name="running-the-sample"></a>运行示例

现在您就可以运行示例示例应用程序。  在要运行该示例的 Visual Studio 中打开 AutocompleteTutorial.sln 解决方案文件。  该解决方案包含一个 ASP.NET MVC 项目。  按 F5 运行该项目，并在所选的浏览器中加载页面。  顶部显示了一个用于选择 C# 或 JavaScript 的选项。  C#选项从浏览器连接到了 HomeController 同时使用 Azure 搜索.NET SDK 来检索结果。  JavaScript 选项直接从浏览器调用 Azure 搜索 REST API。  一般情况下，此选项的性能明显要好得多，因为它会使控制器与流保持隔离。  可以根据自己的需求和语言偏好选择适当的选项。  页面中提供了几个自动完成示例，为每个选项提供了一些指导。  每个示例包含了一些可供尝试的建议示例文本。  请尝试在每个搜索框中键入几个字母，以查看结果。

## <a name="how-this-works-in-code"></a>代码中的工作原理

在浏览器中查看示例后，让我们详细演练第一个示例，以了解相关的组件及其工作原理。

### <a name="search-box"></a>搜索框

对于选择的任一语言，搜索框支持完全相同的语言。  打开文件夹 Views\Home 下的 Index.cshtml 文件。 搜索框本身很简单：

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

这是具有一个 ID 来引用的 JavaScript 和占位符文本的样式设置的类的简单输入的文本框。  奇妙之处在于 javascript。

### <a name="javascript-code-c"></a>JavaScript 代码 (C#)。

C# 语言示例使用 Index.cshtml 中的 JavaScript 来利用 jQuery UI 自动完成库。  此库异步调用 MVC 控制器来检索建议，以此将自动完成体验添加到搜索框。  让我们看看第一个示例的 JavaScript 代码：

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

此代码在加载页面时在浏览器中运行，以便为“example1a”输入框配置自动完成。  `minLength: 3` 确保仅当搜索框中至少有三个字符时，才显示建议。  源值非常重要：

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

此行告知自动完成 API 要从何处获取显示在搜索框下的项列表。  由于这是一个 MVC 项目，因此它会调用 HomeController.cs 中的 Suggest 函数。  下一部分将更详细地介绍该函数。  此 API 还会传递一些参数来控制突出显示内容、模糊匹配项和字词。  自动完成 JavaScript API 会添加字词参数。

#### <a name="extending-the-sample-to-support-fuzzy-matching"></a>扩展示例以支持模糊匹配

使用模糊搜索可以根据接近的匹配项获取结果，即使用户在搜索框中拼错了单词。  让我们将源行更改为启用模糊匹配，以尝试此操作。

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

### <a name="homecontrollercs-c"></a>HomeController.cs (C#)

了解示例的 JavaScript 代码后，让我们看看实际使用 Azure 搜索 .NET SDK 检索建议的 C# 控制器代码。

1. 打开 Controllers 目录下的 HomeController.cs 文件。 

1. 首先会发现，InitSearch 类的顶部有一个方法。  此方法在 Azure 搜索服务中创建经过身份验证的 HTTP 索引客户端。  如果你想要了解有关此工作原理的详细信息，请访问下面的示例：[如何使用 .NET 应用程序中的 Azure 搜索](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

1. 转到 Suggest 函数。

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

Suggest 函数采用两个参数，用于确定是要返回命中的突出显示内容，还是在返回搜索词输入的同时返回模糊匹配结果。  该方法会创建 SuggestParameters 对象，该对象随后会传递给 Suggest API。 然后，结果将转换成 JSON，以便在客户端中显示。
（可选）将断点添加到 Suggest 函数的开头，并逐步执行代码。  请注意 SDK 返回的响应，以及该响应如何转换为方法返回的结果。

页面上的其他示例遵循相同的模式来添加命中的突出显示内容、自动完成建议的提前键入内容，以及用于支持自动完成结果客户端缓存的分面。  请查看其中的每个示例，以了解其工作原理，以及如何在搜索体验中利用这些功能。

### <a name="javascript-language-example"></a>JavaScript 语言示例

对于 JavaScript 语言示例，IndexJavaScript.cshtml 页面中的 JavaScript 代码利用 jQuery UI 自动完成。  此库在提供优雅的搜索框外观方面发挥了最大作用，使用它可以轻松地异步调用 Azure 搜索以检索建议。  让我们看看第一个示例的 JavaScript 代码：

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

如果将此示例与前面调用主控制器的示例进行比较，会发现两者存在一些类似之处。  `minLength` 和 `position` 的自动完成配置完全相同。  此处的最明显差别在于源。  而不是在主控制器中调用的建议方法，REST 请求中的 JavaScript 函数创建和执行使用 Ajax。  然后，在“success”中处理响应，并将其用作源。

## <a name="takeaways"></a>要点

此示例演示用于生成支持自动完成功能和建议的搜索框中的基本步骤。  您看到了如何可以生成一个 ASP.NET MVC 应用程序和使用 Azure 搜索.NET SDK 或 REST API 来检索建议。

## <a name="next-steps"></a>后续步骤

将建议和自动完成功能集成到搜索体验中。  请考虑直接使用.NET SDK 或 REST API 可以帮助键入以使它们提高工作效率的 Azure 搜索的强大功能引入你的用户。

> [!div class="nextstepaction"]
> [自动完成 REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [建议 REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [“创建索引”REST API 中的分面索引属性](https://docs.microsoft.com/rest/api/searchservice/create-index)

