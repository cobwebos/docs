---
title: 有关使用分面辅助导航的 C# 教程
titleSuffix: Azure Cognitive Search
description: 从“分页结果”继续添加分面导航。 了解如何使用分面来轻松缩小搜索范围。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: bc25ba6d95fe58a80c90881c6ad60cdb8801cce4
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780583"
---
# <a name="tutorial-add-faceted-navigation-using-the-net-sdk"></a>教程：使用 .NET SDK 添加分面导航

使用分面可为导航提供辅助，因为它能为用户提供一组链接，让他们专注于搜索。 分面是指数据的特性（例如，我们的示例数据中酒店的类别，即特定的特征）。

本教程是在 [C# 教程：搜索结果分页 - Azure 认知搜索](tutorial-csharp-paging.md)教程中创建的分页项目的基础上编写的。

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> * 将模型属性设置为 _IsFacetable_
> * 将分面导航添加到应用

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

启动并运行 [C# 教程：搜索结果分页 - Azure 认知搜索](tutorial-csharp-paging.md)项目。 此项目可以是你自己的版本，也可从 GitHub 安装：[创建第一个应用](https://github.com/Azure-Samples/azure-search-dotnet-samples)。

## <a name="set-model-properties-as-isfacetable"></a>将模型属性设置为 IsFacetable

若要将某个模型属性放入分面搜索，必须使用 **IsFacetable** 对其进行标记。

1. 检查 **Hotel** 类。 例如，**Category** 和 **Tags** 已标记为 **IsFacetable**，但 **HotelName** 和 **Description** 则不是。 

    ```cs
    public partial class Hotel
    {
        [System.ComponentModel.DataAnnotations.Key]
        [IsFilterable]
        public string HotelId { get; set; }

        [IsSearchable, IsSortable]
        public string HotelName { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnLucene)]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrLucene)]
        [JsonProperty("Description_fr")]
        public string DescriptionFr { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string Category { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string[] Tags { get; set; }

        [IsFilterable, IsSortable, IsFacetable]
        public bool? ParkingIncluded { get; set; }

        [IsFilterable, IsSortable, IsFacetable]
        public DateTimeOffset? LastRenovationDate { get; set; }

        [IsFilterable, IsSortable, IsFacetable]
        public double? Rating { get; set; }

        public Address Address { get; set; }

        [IsFilterable, IsSortable]
        public GeographyPoint Location { get; set; }

        public Room[] Rooms { get; set; }

    }
    ```

2. 我们不会在本教程中更改任何标记，因此请关闭 hotel.cs 文件并使其保留原样。

    > [!Note]
    > 如果未适当地标记中搜索中请求的字段，分面搜索将引发错误。


## <a name="add-facet-navigation-to-your-app"></a>将分面导航添加到应用

在本示例中，我们将允许用户从结果左侧显示的链接列表中选择一个酒店类别或一种设施。 用户首先输入一些搜索文本，然后可以通过选择一个类别来缩小搜索的结果范围，并可以通过选择某种设施来进一步缩小结果范围，或者，他们可以先选择设施（顺序并不重要）。

我们需要使用控制器向视图传递分面列表。 在搜索过程中，我们需要保留用户的选择，同样，我们将使用临时存储作为保留数据的机制。

![使用分面导航缩小“池”的搜索范围](./media/tutorial-csharp-create-first-app/azure-search-facet-nav.png)

### <a name="add-filter-strings-to-the-searchdata-model"></a>将筛选器字符串添加到 SearchData 模型

1. 打开 SearchData.cs 文件，将字符串属性添加到 **SearchData** 类，以保存分面筛选器字符串。

    ```cs
        public string categoryFilter { get; set; }
        public string amenityFilter { get; set; }
    ```

### <a name="add-the-facet-action-method"></a>添加分面操作方法

主控制器需要一个新操作 **Facet**、对其现有的 **Index** 和 **Page** 操作进行更新，以及对 **RunQueryAsync** 方法进行更新。

1. 打开主控制器文件，并添加 **using** 语句以启用 **List&lt;字符串&gt;** 构造。

    ```cs
    using System.Collections.Generic;
    ```

2. 替换 **Index(SearchData model)** 操作方法。

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
                await RunQueryAsync(model, 0, 0, "", "");
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

3. 替换 **Page(SearchData model)** 操作方法。

    ```cs
        public async Task<ActionResult> Page(SearchData model)
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

4. 添加当用户单击分面链接时要激活的 **Facet(SearchData model)** 操作方法。 模型将包含类别搜索筛选器或设施搜索筛选器。 也许可以在 **Page** 操作后面添加该筛选器。

    ```cs
        public async Task<ActionResult> Facet(SearchData model)
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
                await RunQueryAsync(model, 0, 0, catFilter, ameFilter);
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

1. 将 **RunQueryAsync** 方法替换为以下代码。 从根本上讲，此代码采用一个类别筛选器字符串和一个设施筛选器字符串，并设置 **SearchParameters** 的 **Filter** 参数。

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

            var parameters = new SearchParameters
            {
                Filter = facetFilter,

                // Return information on the text, and number, of facets in the data.
                Facets = new List<string> { "Category,count:20", "Tags,count:20" },

                // Enter Hotel property names into this list, so only these values will be returned.
                Select = new[] { "HotelName", "Description", "Category", "Tags" },

                SearchMode = SearchMode.All,

                // Skip past results that have already been returned.
                Skip = page * GlobalVariables.ResultsPerPage,

                // Take only the next page worth of results.
                Top = GlobalVariables.ResultsPerPage,

                // Include the total number of results.
                IncludeTotalResultCount = true,
            };

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

            // This variable communicates the total number of pages to the view.
            model.pageCount = ((int)model.resultList.Count + GlobalVariables.ResultsPerPage - 1) / GlobalVariables.ResultsPerPage;

            // This variable communicates the page number being displayed to the view.
            model.currentPage = page;

            // Calculate the range of page numbers to display.
            if (page == 0)
            {
                leftMostPage = 0;
            }
            else
               if (page <= leftMostPage)
            {
                // Trigger a switch to a lower page range.
                leftMostPage = Math.Max(page - GlobalVariables.PageRangeDelta, 0);
            }
            else
            if (page >= leftMostPage + GlobalVariables.MaxPageRange - 1)
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

    我们已将 **Category** 和 **Tags** 属性添加到要返回的 **Select** 项列表。 并非一定要添加这些属性才能让分面导航正常工作，但使用此信息可以验证是否能够正确筛选。

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

2. 对于该视图，我们会将输出组织成表，以便整齐地在左侧对齐分面列表，在右侧对齐结果。 打开 index.cshtml 文件。 将 HTML &lt;body&gt; 标记的整个内容替换为以下代码。

    ```cs
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
                                            @Html.ActionLink(facetLink, "Facet", "Home", new { categoryFilter = $"Category eq '{categories[c]}'" }, null)
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
                                            @Html.ActionLink(facetLink, "Facet", "Home", new { amenityFilter = $"Tags/any(t: t eq '{tags[c]}')" }, null)
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
                                @Html.DisplayFor(m => m.resultList.Count) Results
                            </p>

                            @for (var i = 0; i < Model.resultList.Results.Count; i++)
                            {
                                string amenities = string.Join(", ", Model.resultList.Results[i].Document.Tags);

                                string fullDescription = Model.resultList.Results[i].Document.Description;
                                fullDescription += $"\nCategory: {Model.resultList.Results[i].Document.Category}";
                                fullDescription += $"\nAmenities: {amenities}";

                                // Display the hotel name and description.
                                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
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
                                            @Html.ActionLink("|<", "Page", "Home", new { paging = "0" }, null)
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
                                            @Html.ActionLink("<", "Page", "Home", new { paging = "prev" }, null)
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
                                                @Html.ActionLink((pn + 1).ToString(), "Page", "Home", new { paging = @pn }, null)
                                            </p>
                                        }
                                    </td>
                                }

                                <td class="tdPage">
                                    @if (Model.currentPage < Model.pageCount - 1)
                                    {
                                        <p class="pageButton">
                                            @Html.ActionLink(">", "Page", "Home", new { paging = "next" }, null)
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
                                            @Html.ActionLink(">|", "Page", "Home", new { paging = Model.pageCount - 1 }, null)
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

2. 单击“度假和 SPA”类别。  确认所有结果是否属于此类别。

    ![缩小“度假和 SPA”的搜索范围](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras.png)

3. 单击“欧陆式早餐”服务（设施）。  确认所有结果是否仍属于“度假和 SPA”，并包含选定的服务（设施）。

    ![缩小“欧陆式早餐”的搜索范围](./media/tutorial-csharp-create-first-app/azure-search-facet-airport-ras-cb.png)

4. 尝试选择任何其他类别，然后输入一种设施，并查看缩小了范围的结果。 然后尝试另一种搜索方式：依次输入一种设施和类别。

    >[!Note]
    > 在分面列表中选择一项时（例如类别），该选择会替代以前在类别列表中所做的任何选择。

## <a name="takeaways"></a>要点

请考虑此项目中的以下要点：

* 如果要将每个属性包含在分面导航中，必须将其标记为 **IsFacetable**。
* 分面导航可让用户方便直观地缩小搜索范围。
* 最好是将分面导航划分为不同的部分（酒店类别、酒店设施、价格范围、评级范围等），每个部分使用相应的标题。

## <a name="next-steps"></a>后续步骤

下一篇教程将介绍结果排序。 到目前为止，结果只是按照它们在数据库中的顺序排序。

> [!div class="nextstepaction"]
> [C# 教程：对结果排序 - Azure 认知搜索](tutorial-csharp-orders.md)
