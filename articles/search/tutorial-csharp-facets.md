---
title: 有关使用分面辅助导航的 C# 教程
titleSuffix: Azure Cognitive Search
description: 从“分页结果”继续添加分面导航。 了解如何使用分面来轻松缩小搜索范围。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 10/01/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ab15af07c5f63d375d8fdb4fc38e0853e207a0be
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2020
ms.locfileid: "91667276"
---
# <a name="tutorial-add-faceted-navigation-using-the-net-sdk"></a>教程：使用 .NET SDK 添加分面导航

Facet 通过提供一组用于筛选结果的链接来启用自定向导航。 在本教程中，Facet 导航结构位于页面左侧，并带有用于剪裁结果的标签和可单击文本。

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> * 将模型属性设置为 _IsFacetable_
> * 将分面导航添加到应用

## <a name="overview"></a>概述

Facet 基于搜索索引中的字段。 包含 facet=[string] 的查询请求提供用作 Facet 依据的字段。 通常包含多个Facet（例如 `&facet=category&facet=amenities`），每个 Facet 采用与 (&) 字符分隔。 要实现 Facet 导航结构，需要同时指定 Facet 和筛选器。 筛选器用于单击事件以缩小结果范围。 例如，单击“预算”可根据该条件筛选结果。

本教程扩展在[将分页添加到搜索结果](tutorial-csharp-paging.md)教程中创建的分页项目。

可以在以下项目中找到本教程中代码的完成版本：

* [4-add-facet-navigation (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/4-add-facet-navigation)

## <a name="prerequisites"></a>先决条件

* [2a-add-paging (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2a-add-paging) 解决方案。 该项目可以是你自己在上一教程中生成的版本，也可以是来自 GitHub 的副本。

本教程已更新为使用 [Azure.Search.Documents（版本 11）](https://www.nuget.org/packages/Azure.Search.Documents/)包。 有关 .NET SDK 的早期版本，请参阅 [Microsoft.Azure.Search（版本 10）代码示例](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10)。

## <a name="set-model-properties-as-isfacetable"></a>将模型属性设置为 IsFacetable

若要将某个模型属性放入分面搜索，必须使用 **IsFacetable** 对其进行标记。

1. 检查 **Hotel** 类。 例如，**Category** 和 **Tags** 已标记为 **IsFacetable**，但 **HotelName** 和 **Description** 则不是。 

    ```cs
    public partial class Hotel
    {
        [SimpleField(IsFilterable = true, IsKey = true)]
        public string HotelId { get; set; }

        [SearchableField(IsSortable = true)]
        public string HotelName { get; set; }

        [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnLucene)]
        public string Description { get; set; }

        [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrLucene)]
        [JsonPropertyName("Description_fr")]
        public string DescriptionFr { get; set; }

        [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
        public string Category { get; set; }

        [SearchableField(IsFilterable = true, IsFacetable = true)]
        public string[] Tags { get; set; }

        [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
        public bool? ParkingIncluded { get; set; }

        [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
        public DateTimeOffset? LastRenovationDate { get; set; }

        [SimpleField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
        public double? Rating { get; set; }

        public Address Address { get; set; }

        [SimpleField(IsFilterable = true, IsSortable = true)]
        public GeographyPoint Location { get; set; }

        public Room[] Rooms { get; set; }
    }
    ```

1. 我们不会在本教程中更改任何标记，因此请关闭 hotel.cs 文件并使其保留原样。

    > [!Note]
    > 如果未适当地标记中搜索中请求的字段，分面搜索将引发错误。

## <a name="add-facet-navigation-to-your-app"></a>将分面导航添加到应用

在本示例中，我们将允许用户从结果左侧显示的链接列表中选择一个酒店类别或一种设施。 用户首先输入一些搜索文本，然后通过选择类别或设施来逐渐缩小搜索结果的范围。

控制器的任务是向视图传递 Facet 列表。 在搜索过程中，若要保留用户的选择，我们使用临时存储作为保留状态的机制。

![使用分面导航缩小“池”的搜索范围](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>将筛选器字符串添加到 SearchData 模型

1. 打开 SearchData.cs 文件，将字符串属性添加到 **SearchData** 类，以保存分面筛选器字符串。

    ```cs
    public string categoryFilter { get; set; }
    public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>添加分面操作方法

主控制器需要一个新操作“Facet”，并更新其现有的“Index”和“Page”操作和“RunQueryAsync”方法   。

<!-- 1. Open the home controller file, and add the **using** statement, to enable the **List&lt;string&gt;** construct.

    ```cs
    using System.Collections.Generic; 
    ```-->

1. 替换 **Index(SearchData model)** 操作方法。

    ```cs
    public async Task<ActionResult> Index(SearchData model)
    {
        try
        {
            // Ensure the search string is valid.
            if (model.searchText == null)
            {
                model.searchText = "";
            }

            // Make the search call for the first page.
            await RunQueryAsync(model, 0, 0, "", "").ConfigureAwait(false);
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "1" });
        }

        return View(model);
    }
    ```

1. 替换“PageAsync(SearchData model)”操作方法。

    ```cs
    public async Task<ActionResult> PageAsync(SearchData model)
    {
        try
        {
            int page;

            // Calculate the page that should be displayed.
            switch (model.paging)
            {
                case "prev":
                    page = (int)TempData["page"] - 1;
                    break;

                case "next":
                    page = (int)TempData["page"] + 1;
                    break;

                default:
                    page = int.Parse(model.paging);
                    break;
            }

            // Recover the leftMostPage.
            int leftMostPage = (int)TempData["leftMostPage"];

            // Recover the filters.
            string catFilter = TempData["categoryFilter"].ToString();
            string ameFilter = TempData["amenityFilter"].ToString();

            // Recover the search text.
            model.searchText = TempData["searchfor"].ToString();

            // Search for the new page.
            await RunQueryAsync(model, page, leftMostPage, catFilter, ameFilter);
        }

        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "2" });
        }
        return View("Index", model);
    }
    ```

1. 添加当用户单击 Facet 链接时要激活的“FacetAsync(SearchData model)”操作方法。 模型将包含类别或设施搜索筛选器。 将其添加在“PageAsync”操作后面。

    ```cs
    public async Task<ActionResult> FacetAsync(SearchData model)
    {
        try
        {
            // Filters set by the model override those stored in temporary data.
            string catFilter;
            string ameFilter;
            if (model.categoryFilter != null)
            {
                catFilter = model.categoryFilter;
            } else
            {
                catFilter = TempData["categoryFilter"].ToString();
            }

            if (model.amenityFilter != null)
            {
                ameFilter = model.amenityFilter;
            } else
            {
                ameFilter = TempData["amenityFilter"].ToString();
            }

            // Recover the search text.
            model.searchText = TempData["searchfor"].ToString();

            // Initiate a new search.
            await RunQueryAsync(model, 0, 0, catFilter, ameFilter).ConfigureAwait(false);
        }
        catch
        {
            return View("Error", new ErrorViewModel { RequestId = "2" });
        }

        return View("Index", model);
    }
    ```

### <a name="set-up-the-search-filter"></a>设置搜索筛选器

当用户选择特定的分面时，例如，在单击“度假和 SPA”类别时，结果中应该只返回指定为此类别的酒店。  若要以这种方式缩小搜索范围，需要设置一个筛选器。 

1. 将 **RunQueryAsync** 方法替换为以下代码。 此代码主要采用一个类别筛选器字符串和一个设施筛选器字符串，并设置“SearchOptions”的“Filter”参数 。

    ```cs
    private async Task<ActionResult> RunQueryAsync(SearchData model, int page, int leftMostPage, string catFilter, string ameFilter)
    {
        InitSearch();

        string facetFilter = "";

        if (catFilter.Length > 0 && ameFilter.Length > 0)
        {
            // Both facets apply.
            facetFilter = $"{catFilter} and {ameFilter}"; 
        } else
        {
            // One, or zero, facets apply.
            facetFilter = $"{catFilter}{ameFilter}";
        }

        var options = new SearchOptions
        {
            Filter = facetFilter,

            SearchMode = SearchMode.All,

            // Skip past results that have already been returned.
            Skip = page * GlobalVariables.ResultsPerPage,

            // Take only the next page worth of results.
            Size = GlobalVariables.ResultsPerPage,

            // Include the total number of results.
            IncludeTotalCount = true,
        };

        // Return information on the text, and number, of facets in the data.
        options.Facets.Add("Category,count:20");
        options.Facets.Add("Tags,count:20");

        // Enter Hotel property names into this list, so only these values will be returned.
        options.Select.Add("HotelName");
        options.Select.Add("Description");
        options.Select.Add("Category");
        options.Select.Add("Tags");

        // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
        model.resultList = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);

        // This variable communicates the total number of pages to the view.
        model.pageCount = ((int)model.resultList.TotalCount + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

        // This variable communicates the page number being displayed to the view.
        model.currentPage = page;

        // Calculate the range of page numbers to display.
        if (page == 0)
        {
            leftMostPage = 0;
        }
        else if (page <= leftMostPage)
        {
            // Trigger a switch to a lower page range.
            leftMostPage = Math.Max(page - GlobalVariables.PageRangeDelta, 0);
        }
        else if (page >= leftMostPage + GlobalVariables.MaxPageRange - 1)
        {
            // Trigger a switch to a higher page range.
            leftMostPage = Math.Min(page - GlobalVariables.PageRangeDelta, model.pageCount - GlobalVariables.MaxPageRange);
        }
        model.leftMostPage = leftMostPage;

        // Calculate the number of page numbers to display.
        model.pageRange = Math.Min(model.pageCount - leftMostPage, GlobalVariables.MaxPageRange);

        // Ensure Temp data is stored for the next call.
        TempData["page"] = page;
        TempData["leftMostPage"] = model.leftMostPage;
        TempData["searchfor"] = model.searchText;
        TempData["categoryFilter"] = catFilter;
        TempData["amenityFilter"] = ameFilter;

        // Return the new view.
        return View("Index", model);
    }
    ```

    请注意，“Category”和“Tags”属性已添加到要返回的“Select”项列表  。 并非一定要添加这些属性才能让 Facet 导航正常工作，但我们使用此信息验证筛选器是否能够正常工作。

### <a name="add-lists-of-facet-links-to-the-view"></a>将分面链接列表添加到视图

需要对该视图进行一些重大更改。 

1. 首先打开 hotels.css 文件（在 wwwroot/css 文件夹中），并添加以下类。

    ```html
    .facetlist {
        list-style: none;
    }

    .facetchecks {
        width: 250px;
        display: normal;
        color: #666;
        margin: 10px;
        padding: 5px;
    }

    .facetheader {
        font-size: 10pt;
        font-weight: bold;
        color: darkgreen;
    }
    ```

1. 对于该视图，将输出组织成表，以便整齐地在左侧对齐 Facet 列表，在右侧对齐结果。 打开 index.cshtml 文件。 将 HTML &lt;body&gt; 标记的整个内容替换为以下代码。

    ```html
    <body>
        @using (Html.BeginForm("Index", "Home", FormMethod.Post))
        {
            <table>
                <tr>
                    <td></td>
                    <td>
                        <h1 class="sampleTitle">
                            <img src="~/images/azure-logo.png" width="80" />
                            Hotels Search - Facet Navigation
                        </h1>
                    </td>
                </tr>

                <tr>
                    <td></td>
                    <td>
                        <!-- Display the search text box, with the search icon to the right of it.-->
                        <div class="searchBoxForm">
                            @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input value="" class="searchBoxSubmit" type="submit">
                        </div>
                    </td>
                </tr>

                <tr>
                    <td valign="top">
                        <div id="facetplace" class="facetchecks">

                            @if (Model != null && Model.resultList != null)
                            {
                                List<string> categories = Model.resultList.Facets["Category"].Select(x => x.Value.ToString()).ToList();

                                if (categories.Count > 0)
                                {
                                    <h5 class="facetheader">Category:</h5>
                                    <ul class="facetlist">
                                        @for (var c = 0; c < categories.Count; c++)
                                        {
                                            var facetLink = $"{categories[c]} ({Model.resultList.Facets["Category"][c].Count})";
                                            <li>
                                                @Html.ActionLink(facetLink, "FacetAsync", "Home", new { categoryFilter = $"Category eq '{categories[c]}'" }, null)
                                            </li>
                                        }
                                    </ul>
                                }

                                List<string> tags = Model.resultList.Facets["Tags"].Select(x => x.Value.ToString()).ToList();

                                if (tags.Count > 0)
                                {
                                    <h5 class="facetheader">Amenities:</h5>
                                    <ul class="facetlist">
                                        @for (var c = 0; c < tags.Count; c++)
                                        {
                                            var facetLink = $"{tags[c]} ({Model.resultList.Facets["Tags"][c].Count})";
                                            <li>
                                                @Html.ActionLink(facetLink, "FacetAsync", "Home", new { amenityFilter = $"Tags/any(t: t eq '{tags[c]}')" }, null)
                                            </li>
                                        }
                                    </ul>
                                }
                            }
                        </div>
                    </td>
                    <td valign="top">
                        <div id="resultsplace">
                            @if (Model != null && Model.resultList != null)
                            {
                                // Show the result count.
                                <p class="sampleText">
                                    @Model.resultList.TotalCount Results
                                </p>

                                var results = Model.resultList.GetResults().ToList();

                                @for (var i = 0; i < results.Count; i++)
                                {
                                    string amenities = string.Join(", ", results[i].Document.Tags);

                                    string fullDescription = results[i].Document.Description;
                                    fullDescription += $"\nCategory: {results[i].Document.Category}";
                                    fullDescription += $"\nAmenities: {amenities}";


                                    // Display the hotel name and description.
                                    @Html.TextAreaFor(m => results[i].Document.HotelName, new { @class = "box1" })
                                    @Html.TextArea($"desc{i}", fullDescription, new { @class = "box2" })
                                }
                            }
                        </div>
                    </td>
                </tr>

                <tr>
                    <td></td>
                    <td valign="top">
                        @if (Model != null && Model.pageCount > 1)
                        {
                            // If there is more than one page of results, show the paging buttons.
                            <table>
                                <tr>
                                    <td class="tdPage">
                                        @if (Model.currentPage > 0)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink("|<", "PageAsync", "Home", new { paging = "0" }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">|&lt;</p>
                                        }
                                    </td>

                                    <td class="tdPage">
                                        @if (Model.currentPage > 0)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink("<", "PageAsync", "Home", new { paging = "prev" }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">&lt;</p>
                                        }
                                    </td>

                                    @for (var pn = Model.leftMostPage; pn < Model.leftMostPage + Model.pageRange; pn++)
                                    {
                                        <td class="tdPage">
                                            @if (Model.currentPage == pn)
                                            {
                                                // Convert displayed page numbers to 1-based and not 0-based.
                                                <p class="pageSelected">@(pn + 1)</p>
                                            }
                                            else
                                            {
                                                <p class="pageButton">
                                                    @Html.ActionLink((pn + 1).ToString(), "PageAsync", "Home", new { paging = @pn }, null)
                                                </p>
                                            }
                                        </td>
                                    }

                                    <td class="tdPage">
                                        @if (Model.currentPage < Model.pageCount - 1)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink(">", "PageAsync", "Home", new { paging = "next" }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">&gt;</p>
                                        }
                                    </td>

                                    <td class="tdPage">
                                        @if (Model.currentPage < Model.pageCount - 1)
                                        {
                                            <p class="pageButton">
                                                @Html.ActionLink(">|", "PageAsync", "Home", new { paging = Model.pageCount - 1 }, null)
                                            </p>
                                        }
                                        else
                                        {
                                            <p class="pageButtonDisabled">&gt;|</p>
                                        }
                                    </td>
                                </tr>
                            </table>
                        }
                    </td>
                </tr>
            </table>
        }
    </body>
    ```

    请注意使用了 **Html.ActionLink** 调用。 当用户单击分面链接时，此调用会将有效的筛选器字符串传送到控制器。 

### <a name="run-and-test-the-app"></a>运行并测试应用

对于用户而言，分面导航的优势在于，他们只需单击一下就能缩小搜索范围，以下操作顺序对此做了说明。

1. 运行应用，键入“机场”作为搜索文本。 确认分面列表是否整齐地显示在左侧。 所有这些分面适用于文本数据中包含“机场”的酒店，括号中的数字是数量。

    ![使用分面导航缩小“机场”的搜索范围](./media/tutorial-csharp-create-first-app/azure-search-facet-airport.png)

1. 单击“度假和 SPA”类别。  确认所有结果是否属于此类别。

    ![缩小“度假和 SPA”的搜索范围](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

1. 单击“欧陆式早餐”服务（设施）。  确认所有结果是否仍属于“度假和 SPA”，并包含选定的服务（设施）。

    ![缩小“欧陆式早餐”的搜索范围](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

1. 尝试选择任何其他类别，然后输入一种设施，并查看缩小了范围的结果。 然后尝试另一种搜索方式：依次输入一种设施和类别。 发送空搜索以重置页面。

    >[!Note]
    > 在分面列表中选择一项时（例如类别），该选择会替代以前在类别列表中所做的任何选择。

## <a name="takeaways"></a>要点

请考虑此项目中的以下要点：

* 必须使用“IsFacetable”属性标记每个可应用的 Facet 字段，以便将其包含在 Facet 导航中。
* 组合使用 Facet 与筛选器以减少结果。
* Facet 是累积的，每个选择都以上一个选择为基础，以进一步缩小结果范围。

## <a name="next-steps"></a>后续步骤

下一篇教程将介绍结果排序。 到目前为止，结果只是按照它们在数据库中的顺序排序。

> [!div class="nextstepaction"]
> [C# 教程：对结果排序 - Azure 认知搜索](tutorial-csharp-orders.md)
