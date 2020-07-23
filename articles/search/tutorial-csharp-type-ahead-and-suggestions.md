---
title: 有关自动完成和建议功能的 C# 教程
titleSuffix: Azure Cognitive Search
description: 添加自动完成和建议功能，以便使用下拉列表从用户收集搜索词输入。 本教程在现有的酒店项目的基础上编写。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: 63c098ccd42a438f8daab787afb54cf13cd053c3
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780549"
---
# <a name="tutorial-add-autocomplete-and-suggestions-using-the-net-sdk"></a>教程：使用 .NET SDK 添加自动完成和建议功能

了解如何在用户开始在搜索框中键入内容时实现自动完成功能（提前键入查询和建议的文档）。 在本教程中，我们先分别显示自动完成的查询和建议结果，然后将它们一起显示。 用户可能只需键入两个或三个字符就能找到所有可用的结果。

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> * 添加建议
> * 为建议添加突出显示
> * 添加自动完成功能
> * 结合使用自动完成功能和建议

## <a name="prerequisites"></a>先决条件

本教程是一系列教程的一部分，是在 [C# 教程：搜索结果分页 - Azure 认知搜索](tutorial-csharp-paging.md)中创建的分页项目的基础上编写的。

也可下载并运行此特定教程的解决方案：[3-add-typeahead](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/3-add-typeahead)。

## <a name="add-suggestions"></a>添加建议

让我们从为用户提供候选产品/服务的最简单情况开始：下拉建议列表。

1. 在 index.cshtml 文件中，将 TextBoxFor  语句的 `@id` 更改为“azureautosuggest”  。

    ```cs
     @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautosuggest" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

2. 在此语句后面，在结尾的 &lt;/div&gt;  后面，输入此脚本。 此脚本利用开源 jQuery UI 库中的[自动完成小组件](https://api.jqueryui.com/autocomplete/)来提供建议结果的下拉列表。 

    ```javascript
    <script>
        $("#azureautosuggest").autocomplete({
            source: "/Home/Suggest?highlights=false&fuzzy=false",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

    ID“azureautosuggest”将上述脚本连接到搜索框。 小组件的 source 选项设置为 Suggest 方法，该方法使用两个查询参数（highlights  和 fuzzy  ，在本例中都设为 false）调用建议 API。 此外，触发搜索至少需要两个字符。

### <a name="add-references-to-jquery-scripts-to-the-view"></a>在视图中添加对 jQuery 脚本的引用

1. 若要访问 jQuery 库，请将视图文件的 &lt;head&gt; 部分更改为以下代码：

    ```cs
    <head>
        <meta charset="utf-8">
        <title>Typeahead</title>
        <link href="https://code.jquery.com/ui/1.12.1/themes/start/jquery-ui.css"
              rel="stylesheet">
        <script src="https://code.jquery.com/jquery-1.10.2.js"></script>
        <script src="https://code.jquery.com/ui/1.12.1/jquery-ui.js"></script>

        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

2. 因为我们要引入一个新的 jQuery 引用，所以还需要删除或注释掉 _Layout.cshtml 文件（在“Views/Shared”  文件夹中）中的默认 jQuery 引用。 找到以下行，并按所示那样注释掉第一行脚本。 此更改可避免与 jQuery 引用产生冲突。

    ```html
    <environment include="Development">
        <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

    现在，可以使用预定义的自动完成 jQuery 函数了。

### <a name="add-the-suggest-action-to-the-controller"></a>添加对控制器的 Suggest 操作

1. 在主控制器中，添加 **Suggest** 操作（即添加在 **Page** 操作之后）。

    ```cs
        public async Task<ActionResult> Suggest(bool highlights, bool fuzzy, string term)
        {
            InitSearch();

            // Setup the suggest parameters.
            var parameters = new SuggestParameters()
            {
                UseFuzzyMatching = fuzzy,
                Top = 8,
            };

            if (highlights)
            {
                parameters.HighlightPreTag = "<b>";
                parameters.HighlightPostTag = "</b>";
            }

            // Only one suggester can be specified per index. It is defined in the index schema.
            // The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and simply searches the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", parameters);

            // Convert the suggest query results to a list that can be displayed in the client.
            List<string> suggestions = suggestResult.Results.Select(x => x.Text).ToList();

            // Return the list of suggestions.
            return new JsonResult(suggestions);
        }
    ```

    Top  参数指定要返回的结果数（如果未指定，默认值为 5）。 Azure 索引上指定了一个_建议器_，该索引在数据设置完毕后完成，但不是由类似于本教程中的客户端应用来完成。 在此例中，建议器称名为“sg”，它搜索 HotelName  字段 - 别无其他了。 

    模糊匹配允许输出中包含“近似结果”（最多差一个编辑距离）。 如果 highlights  参数设置为 true，则输出中会添加粗体的 HTML 标记。 在下一节中，我们会将这两个参数设置为 true。

2. 这样可能会收到一些语法错误。 如果收到错误，则将以下两个 using  语句添加到文件顶部。

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

3. 运行应用。 输入“po”（例如）时是否会显示一系列选项？ 现在尝试输入“pa”。

    ![键入“po”后显示了两条建议](./media/tutorial-csharp-create-first-app/azure-search-suggest-po.png)

    请注意，输入的字母_必须_是某个词的起始部分，而不只是这个词的一部分。

4. 在视图脚本中，将 &fuzzy  设置为 true，并再次运行该应用。 现在，输入“po”。 请注意，这时搜索会假定你写错了一个字母！
 
    ![在将 fuzzy 设置为 true 后，键入“pa”](./media/tutorial-csharp-create-first-app/azure-search-suggest-fuzzy.png)

    如果感兴趣，可阅读 [Azure 认知搜索中的 Lucene 查询语法](https://docs.microsoft.com/azure/search/query-lucene-syntax)，其中详细介绍了模糊搜索使用的逻辑。

## <a name="add-highlighting-to-the-suggestions"></a>为建议添加突出显示

可以通过将 highlights  参数设为“true”来改进向用户提供的建议的外观。 但是，需要先将一些代码添加到视图中来显示粗体文本。

1. 在视图中 (index.cshtml)，在上面输入的 azureautosuggest  脚本后面添加以下脚本。

    ```javascript
    <script>
        var updateTextbox = function (event, ui) {
            var result = ui.item.value.replace(/<\/?[^>]+(>|$)/g, "");
            $("#azuresuggesthighlights").val(result);
            return false;
        };

        $("#azuresuggesthighlights").autocomplete({
            html: true,
            source: "/home/suggest?highlights=true&fuzzy=false&",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },
            select: updateTextbox,
            focus: updateTextbox
        }).data("ui-autocomplete")._renderItem = function (ul, item) {
            return $("<li></li>")
                .data("item.autocomplete", item)
                .append("<a>" + item.label + "</a>")
                .appendTo(ul);
        };
    </script>
    ```

2. 现在将文本框的 ID 更改为以下内容。

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresuggesthighlights" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. 再次运行应用，应会看到输入的文本在建议中显示为粗体。 例如，请尝试键入“pa”。
 
    ![键入突出显示的“pa”](./media/tutorial-csharp-create-first-app/azure-search-suggest-highlight.png)

4. 上面突出显示的脚本中使用的逻辑并不能做到万无一失。 如果输入一个术语，它在相同的名称中出现两次，那么粗体结果与预期不会完全一致。 请尝试键入“mo”。

    开发人员需要回答的一个问题是，如何界定脚本的工作效果，什么情况算是“效果足够好”，什么情况需要修正其结果。 本教程未深入探讨突出显示，但如果突出显示对你的数据无效，则需要考虑使用精确的算法。 有关详细信息，请参阅[命中项突出显示](search-pagination-page-layout.md#hit-highlighting)。

## <a name="add-autocomplete"></a>添加自动完成功能

与建议略有不同的另一个变体是用于完成查询词的自动完成功能（有时称为“提前键入”）。 同样，我们将从最简单的实现开始，然后再探讨如何改善用户体验。

1. 在视图中前述脚本后面输入以下脚本。

    ```javascript
    <script>
        $("#azureautocompletebasic").autocomplete({
            source: "/Home/Autocomplete",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

2. 现在将文本框的 ID 更改为以下内容。

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocompletebasic" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

3. 在主控制器中，需要在 Suggest  操作下方输入 Autocomplete  操作。

    ```cs
        public async Task<ActionResult> AutoComplete(string term)
        {
            InitSearch();

            // Setup the autocomplete parameters.
            var ap = new AutocompleteParameters()
            {
                AutocompleteMode = AutocompleteMode.OneTermWithContext,
                Top = 6
            };
            AutocompleteResult autocompleteResult = await _indexClient.Documents.AutocompleteAsync(term, "sg", ap);

            // Convert the results to a list that can be displayed in the client.
            List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();

            // Return the list.
            return new JsonResult(autocomplete);
        }
    ```

    请注意，在自动完成功能搜索中使用的函数是名为“sg”的 suggester  函数，与为建议用的函数一样（仅尝试自动完成酒店名称）。

    有一系列 AutocompleteMode  设置，这里使用 OneTermWithContext  。 有关其他选项的说明，请参阅[自动完成 API](https://docs.microsoft.com/rest/api/searchservice/autocomplete)。

4. 运行应用。 请注意，下拉列表中显示的一系列选项都是单个词语。 请尝试键入“re”开头的单词。 请注意选项数是如何随键入字母的增加而减少的。

    ![键入时应用基本的自动完成功能](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocompletebasic.png)

    目前来看，之前运行的建议脚本可能比此自动完成脚本更有用。 要使自动完成功能更便于用户使用，最好将其添加到建议搜索中。

## <a name="combine-autocompletion-and-suggestions"></a>结合使用自动完成功能和建议

将自动完成功能和建议结合使用，是最复杂的选项，但却可能提供最佳的用户体验。 我们想要的是根据正在键入的文本进行显示，这是 Azure 认知搜索在自动完成文本方面的第一选择。 我们还需要一系列以下拉列表形式显示的建议。

有提供此功能的库 - 库名通常为“内联自动完成”或类似名称。 但我们将以本机方式实现此功能，以便可以展示其工作原理。 在此示例中，我们从控制器上的工作着手。

1. 需要在控制器中添加一个操作，它只返回一个自动完成结果和指定数量的建议。 将此操作命名为 AutocompleteAndSuggest  。 在主控制器中的其他新操作后面添加以下操作。

    ```cs
        public async Task<ActionResult> AutocompleteAndSuggest(string term)
        {
            InitSearch();

            // Setup the type-ahead search parameters.
            var ap = new AutocompleteParameters()
            {
                AutocompleteMode = AutocompleteMode.OneTermWithContext,
                Top = 1,
            };
            AutocompleteResult autocompleteResult = await _indexClient.Documents.AutocompleteAsync(term, "sg", ap);

            // Setup the suggest search parameters.
            var sp = new SuggestParameters()
            {
                Top = 8,
            };

            // Only one suggester can be specified per index. The name of the suggester is set when the suggester is specified by other API calls.
            // The suggester for the hotel database is called "sg", and it searches only the hotel name.
            DocumentSuggestResult<Hotel> suggestResult = await _indexClient.Documents.SuggestAsync<Hotel>(term, "sg", sp);

            // Create an empty list.
            var results = new List<string>();

            if (autocompleteResult.Results.Count > 0)
            {
                // Add the top result for type-ahead.
                results.Add(autocompleteResult.Results[0].Text);
            }
            else
            {
                // There were no type-ahead suggestions, so add an empty string.
                results.Add("");
            }
            for (int n = 0; n < suggestResult.Results.Count; n++)
            {
                // Now add the suggestions.
                results.Add(suggestResult.Results[n].Text);
            }

            // Return the list.
            return new JsonResult(results);
        }
    ```

    “结果”  列表的顶部返回了一个自动完成选项，后跟全部建议。

2. 在视图中，我们先实现一个小技巧，在用户输入的粗体文本正下方显示一个浅灰色的自动完成词语。 HTML 中包含用于实现此目的的相对定位功能。 将 TextBoxFor  语句（和其周围的 &lt;div&gt; 语句）更改为以下内容，注意，常规搜索框正下方又显示了一个标识为“underneath”  的搜索框，使常规框偏离其默认位置 39 个像素！

    ```cs
    <div id="underneath" class="searchBox" style="position: relative; left: 0; top: 0">
    </div>

    <div id="searchinput" class="searchBoxForm" style="position: relative; left: 0; top: -39px">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocomplete" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

    请注意，在这种情况下，我们再次将 ID 更改为 azureautocomplete  。

3. 并在视图中到目前为止输入的所有脚本后面输入以下脚本。 脚本量较大。

    ```javascript
    <script>
        $('#azureautocomplete').autocomplete({
            delay: 500,
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },

            // Use Ajax to set up a "success" function.
            source: function (request, response) {
                var controllerUrl = "/Home/AutoCompleteAndSuggest?term=" + $("#azureautocomplete").val();
                $.ajax({
                    url: controllerUrl,
                    dataType: "json",
                    success: function (data) {
                        if (data && data.length > 0) {

                            // Show the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = data[0];

                            // Remove the top suggestion as it is used for inline autocomplete.
                            var array = new Array();
                            for (var n = 1; n < data.length; n++) {
                                array[n - 1] = data[n];
                            }

                            // Show the drop-down list of suggestions.
                            response(array);
                        } else {

                            // Nothing is returned, so clear the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }
                });
            }
        });

        // Complete on TAB.
        // Clear on ESC.
        // Clear if backspace to less than 2 characters.
        // Clear if any arrow key hit as user is navigating the suggestions.
        $("#azureautocomplete").keydown(function (evt) {

            var suggestedText = document.getElementById("underneath").innerHTML;
            if (evt.keyCode === 9 /* TAB */ && suggestedText.length > 0) {
                $("#azureautocomplete").val(suggestedText);
                return false;
            } else if (evt.keyCode === 27 /* ESC */) {
                document.getElementById("underneath").innerHTML = "";
                $("#azureautocomplete").val("");
            } else if (evt.keyCode === 8 /* Backspace */) {
                if ($("#azureautocomplete").val().length < 2) {
                    document.getElementById("underneath").innerHTML = "";
                }
            } else if (evt.keyCode >= 37 && evt.keyCode <= 40 /* Any arrow key */) {
                document.getElementById("underneath").innerHTML = "";
            }
        });

        // Character replace function.
        function setCharAt(str, index, chr) {
            if (index > str.length - 1) return str;
            return str.substr(0, index) + chr + str.substr(index + 1);
        }

        // This function is needed to clear the "underneath" text when the user clicks on a suggestion, and to
        // correct the case of the autocomplete option when it does not match the case of the user input.
        // The interval function is activated with the input, blur, change, or focus events.
        $("#azureautocomplete").on("input blur change focus", function (e) {

            // Set a 2 second interval duration.
            var intervalDuration = 2000, 
                interval = setInterval(function () {

                    // Compare the autocorrect suggestion with the actual typed string.
                    var inputText = document.getElementById("azureautocomplete").value;
                    var autoText = document.getElementById("underneath").innerHTML;

                    // If the typed string is longer than the suggestion, then clear the suggestion.
                    if (inputText.length > autoText.length) {
                        document.getElementById("underneath").innerHTML = "";
                    } else {

                        // If the strings match, change the case of the suggestion to match the case of the typed input.
                        if (autoText.toLowerCase().startsWith(inputText.toLowerCase())) {
                            for (var n = 0; n < inputText.length; n++) {
                                autoText = setCharAt(autoText, n, inputText[n]);
                            }
                            document.getElementById("underneath").innerHTML = autoText;

                        } else {
                            // The strings do not match, so clear the suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }

                    // If the element loses focus, stop the interval checking.
                    if (!$input.is(':focus')) clearInterval(interval);

                }, intervalDuration);
        });
    </script>
    ```

    请注意，这里灵活使用了 interval  函数同时清除不再与用户正在键入的内容匹配的基础文本，该函数还将用户输入的字母视为相同的大小写（即搜索时“pa”等同于“PA”、“pA”、“Pa”），以便去除重复的结果。

    通读脚本中的注释，获得更全面的理解。

4. 最后，需要稍微调整两个 HTML 类，使其透明。 将下面的代码行添加到 hotels.css 文件中的 searchBoxForm 和 searchBox 类中   。

    ```html
        background: rgba(0,0,0,0);
    ```

5. 现在运行应用。 在搜索框中输入“pa”。 得到的自动完成建议是否为“palace”，以及两个包含“pa”的酒店？

    ![键入时应用内联自动完成功能和建议](./media/tutorial-csharp-create-first-app/azure-search-suggest-autocomplete.png)

6. 尝试按 tab 键来接受自动完成建议，并尝试使用箭头键和 tab 键来选择建议，然后使用鼠标和单击方式再试一次。 验证脚本是否能很好地处理所有这些情况。

    你可能会认为从提供此功能的库中加载该功能会更简单一些，但现在你至少了解了一种实现内联自动完成的方法！

## <a name="takeaways"></a>要点

请考虑此项目中的以下要点：

* 通过自动完成功能（也称为“提前键入”）和建议，用户只需按几个键就能精确获得要查找的内容。
* 自动完成功能和建议协同工作可提供丰富的用户体验。
* 始终用所有形式的输入来测试自动完成函数。
* 可使用 setInterval  函数验证和更正 UI 元素。

## <a name="next-steps"></a>后续步骤

下一个教程介绍改进用户体验的另一种方法，即使用 facet 通过一次单击来缩小搜索范围。

> [!div class="nextstepaction"]
> [C# 教程：使用 facet 辅助导航 - Azure 认知搜索](tutorial-csharp-facets.md)


