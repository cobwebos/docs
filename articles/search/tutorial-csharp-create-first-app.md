---
title: 用于创建第一个应用的 C# 教程
titleSuffix: Azure Cognitive Search
description: 了解如何逐步构建第一个 C# 搜索应用。 本教程提供了 GitHub 上工作应用的链接以及从头开始构建应用的完整过程。 了解有关 Azure 认知搜索的基本组件。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: a105766b713ce44d800391e2e943f2ac864fa0df
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780617"
---
# <a name="tutorial-create-your-first-search-app-using-the-net-sdk"></a>教程：使用 .NET SDK 创建你的第一个搜索应用

了解如何使用 Azure 认知搜索来创建查询和呈现索引搜索结果的 Web 界面。 本教程从现有的托管索引开始，以便你可以专注于构建搜索页。 索引包含虚构的酒店数据。 有了基本页面后，可以在后续课程中对其进行增强，以添加分页、facet 和提前键入体验。

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> * 设置开发环境
> * 数据结构建模
> * 创建 Web 页
> * 定义方法
> * 测试应用程序

你还将了解到搜索调用有多简单。 你将在代码中开发的关键语句封装在以下几行中。

```cs
var parameters = new SearchParameters
{
    // Enter Hotel property names into this list, so only these values will be returned.
    Select = new[] { "HotelName", "Description" }
};

DocumentSearchResult<Hotel> results  = await _indexClient.Documents.SearchAsync<Hotel>("search text", parameters);
```

这一次调用启动 Azure 数据的搜索，并返回结果。

![搜索“池”](./media/tutorial-csharp-create-first-app/azure-search-pool.png)


## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

[安装 Visual Studio](https://visualstudio.microsoft.com/)，以用作 IDE。

### <a name="install-and-run-the-project-from-github"></a>从 GitHub 安装并运行项目

1. 定位 GitHub 上的示例：[创建第一个应用](https://github.com/Azure-Samples/azure-search-dotnet-samples)。
1. 选择“克隆或下载”以创建项目的专用本地副本  。
1. 使用 Visual Studio，导航到并打开基本搜索页上的解决方案，选择“启动但不调试”（或按 F5）  。
1. 键入某些字词（例如"wifi"、"视图"、"栏"、"停车"），并检查结果！

    ![搜索“wifi”](./media/tutorial-csharp-create-first-app/azure-search-wifi.png)

希望此项目能顺利运行，Azure 应用从而顺利运行。 这个应用中包含了较复杂搜索的许多基本组件，因此最好全部浏览完并分步重新创建。

要从头开始创建此项目，从而强化你所认识的 Azure 认知搜索的组件，请浏览以下步骤。

## <a name="set-up-a-development-environment"></a>设置开发环境

1. 在 Visual Studio 2017 或更高版本中，依次选择“新建/项目”、“ASP.NET Core Web 应用程序”   。 为项目命名，例如"FirstAzureSearchApp"。

    ![创建云项目](./media/tutorial-csharp-create-first-app/azure-search-project1.png)

2. 对于此项目类型，单击“确定”后，系统会提供另外一系列适用于此项目的选项  。 选择“Web 应用程序（模型 - 视图 - 控制器）”  。

    ![创建 MVC 项目](./media/tutorial-csharp-create-first-app/azure-search-project2.png)

3. 然后在“工具”菜单中，依次选择“NuGet 包管理器”、“管理解决方案的 NuGet 包...”    。我们需要安装一个包。 选择“浏览”选项卡，然后在搜索框中键入“Azure 认知搜索”  。 当它出现在列表（版本 9.0.1 或更高版本）中时，安装 Microsoft.Azure.Search  。 你将需要单击几个其他对话框以完成安装。

    ![使用 NuGet 添加 Azure 库](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

### <a name="initialize-azure-cognitive-search"></a>初始化 Azure 认知搜索

此示例中，我们将使用公开提供的酒店数据。 此数据是 50 个虚构的酒店名称和说明的任意集合，专门为提供演示数据而创建。 若要访问此数据，需要为它指定一个名称和密钥。

1. 打开新的项目中的 appsettings.json 文件，并用以下名称和密钥替换默认行。 此处显示的 API 密钥不是密钥示例，而是  访问酒店数据所需的密钥。 Appsettings.json 文件现在应如下所示。

    ```cs
    {
        "SearchServiceName": "azs-playground",
        "SearchServiceQueryApiKey": "EA4510A6219E14888741FCFC19BFBB82"
    }
    ```

2. 我们还没有处理完此文件，请选择此文件的属性并将“复制到输出目录”设置更改为“如果较新则复制”   。

    ![将应用程序设置复制到输出](./media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png)

## <a name="model-data-structures"></a>数据结构建模

模型（C#类）用于客户端（视图）、服务器（控制器）和使用 MVC（模型、视图、控制器）体系结构的 Azure 云之间的数据通信。 通常情况下，这些模型将反映正在访问的数据的结构。 此外，我们需要一个模型来处理视图/控制器通信。

1. 打开项目中的“模型”文件夹，使用解决方案资源管理器，你将看到默认模型  ：ErrorViewModel.cs  。

2. 右键单击“模型”文件夹，然后选择“添加”，然后选择“新项”    。 然后，在出现的对话框，选择“ASP.NET Core”，然后选择第一个选项“类”   。 将 .Cs 文件重命名为 hotel.cs，然后单击“添加”  。 将 Hotel.cs 的所有内容替换为以下代码。 请注意类的“地址”和“房间”成员，这些字段是类本身，因此我们也需要它们的模型   。

    ```cs
    using System;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Spatial;
    using Newtonsoft.Json;

    namespace FirstAzureSearchApp.Models
    {
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
    }
    ```

3. 请遵循为“地址”类创建模型的相同过程，但请将文件命名为 Address.cs  。 将内容替换为以下内容。

    ```cs
    using Microsoft.Azure.Search;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Address
        {
            [IsSearchable]
            public string StreetAddress { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string City { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string StateProvince { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string PostalCode { get; set; }

            [IsSearchable, IsFilterable, IsSortable, IsFacetable]
            public string Country { get; set; }
        }
    }
    ```

4. 同样，按照相同的过程来创建“房间”类，将文件命名为 Room.cs  。 同样，将内容替换为以下内容。

    ```cs
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Newtonsoft.Json;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Room
        {
            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnMicrosoft)]

            public string Description { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.FrMicrosoft)]
            [JsonProperty("Description_fr")]
            public string DescriptionFr { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string Type { get; set; }

            [IsFilterable, IsFacetable]
            public double? BaseRate { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string BedOptions { get; set; }

            [IsFilterable, IsFacetable]

            public int SleepsCount { get; set; }

            [IsFilterable, IsFacetable]
            public bool? SmokingAllowed { get; set; }

            [IsSearchable, IsFilterable, IsFacetable]
            public string[] Tags { get; set; }
        }
    }
    ```

5. **酒店**、**地址**和**房间**类的集合在 Azure 中称为[_复杂类型_](search-howto-complex-data-types.md)，这是 Azure 认知搜索的一项重要功能。 复杂类型可以是多级深度的类和子类，并支持比使用简单类型（仅包含基元成员的类）所表示的数据结构复杂得多的数据结构  。 我们需要另外一个模型，因此再次浏览创建新模型类的过程，但这次调用类 SearchData.cs 并将默认代码替换为以下内容。

    ```cs
    using Microsoft.Azure.Search.Models;

    namespace FirstAzureSearchApp.Models
    {
        public class SearchData
        {
            // The text to search for.
            public string searchText { get; set; }

            // The list of results.
            public DocumentSearchResult<Hotel> resultList;
        }
    }
    ```

    此类包含用户的输入 (searchText) 和搜索的输出 (resultList)   。 输出的类型 DocumentSearchResult&lt;Hotel&gt;  很重要，因为此类型与搜索结果完全匹配，我们需要将此引用传递到视图。



## <a name="create-a-web-page"></a>创建 Web 页

默认情况下，你创建的项目将创建许多客户端视图。 具体视图取决于正在使用的 Core .NET 版本（此示例中我们使用 2.1）。 它们都在项目的“视图”文件夹中  。 只需要修改 Index.cshtml 文件（位于“视图/主页”文件夹  ）。

删除 Index.cshtml 所有内容并按以下步骤重新生成文件。

1. 我们在视图中使用两个小图像。 可以使用你自己的图像，也可以从 GitHub 项目中复制图像：azure-logo.png 和 search.png。 这两个图像应置于“wwwroot/images”文件夹  。

2. Index.cshtml 的第一行应引用我们将在客户端（视图）和服务器（控制器）之间用于数据通信的模型，该模型是我们创建的 SearchData 模型  。 将此行添加到 Index.cshtml 文件。

    ```cs
    @model FirstAzureSearchApp.Models.SearchData
    ```

3. 为视图输入标题是标准做法，因此下一行应该是：

    ```cs
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

4. 在标题后，输入对 HTML 样式表的引用，我们将稍后创建它。

    ```cs
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

5. 现在我们来看看视图的内容。 需要记住的一个重点是，视图必须处理两种情况。 首先，它必须处理当应用首次启动而用户尚未输入任何搜索文本时的显示。 其次，它除了处理搜索文本框的显示，还必须处理结果的显示，以便用户重复使用。 若要处理这两种情况，我们需要检查提供给视图的模型是否为 null。 null 模型指示我们处于两种情况中的第一种（应用的初始运行）。 将以下代码添加到 Index.cshtml 文件并浏览注释。

    ```cs
    <body>
    <h1 class="sampleTitle">
        <img src="~/images/azure-logo.png" width="80" />
        Hotels Search
    </h1>

    @using (Html.BeginForm("Index", "Home", FormMethod.Post))
    {
        // Display the search text box, with the search icon to the right of it.
        <div class="searchBoxForm">
            @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox" }) <input class="searchBoxSubmit" type="submit" value="">
        </div>

        @if (Model != null)
        {
            // Show the result count.
            <p class="sampleText">
                @Html.DisplayFor(m => m.resultList.Results.Count) Results
            </p>

            @for (var i = 0; i < Model.resultList.Results.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => Model.resultList.Results[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", Model.resultList.Results[i].Document.Description, new { @class = "box2" })
            }
        }
    }
    </body>
    ```

6. 最后，我们添加样式表。 在 Visual Studio 中，在“文件”菜单中依次选择“新建/文件”和“样式表”（“常规”突出显示）     。 将默认代码替换为以下代码。 我们不会进一步详细介绍此文件，样式是标准 HTML。

    ```html
    textarea.box1 {
        width: 648px;
        height: 30px;
        border: none;
        background-color: azure;
        font-size: 14pt;
        color: blue;
        padding-left: 5px;
    }

    textarea.box2 {
        width: 648px;
        height: 100px;
        border: none;
        background-color: azure;
        font-size: 12pt;
        padding-left: 5px;
        margin-bottom: 24px;
    }

    .sampleTitle {
        font: 32px/normal 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 32px;
        text-align: left;
    }

    .sampleText {
        font: 16px/bold 'Segoe UI Light',Arial,Helvetica,Sans-Serif;
        margin: 20px 0;
        font-size: 14px;
        text-align: left;
        height: 30px;
    }

    .searchBoxForm {
        width: 648px;
        box-shadow: 0 0 0 1px rgba(0,0,0,.1), 0 2px 4px 0 rgba(0,0,0,.16);
        background-color: #fff;
        display: inline-block;
        border-collapse: collapse;
        border-spacing: 0;
        list-style: none;
        color: #666;
    }

    .searchBox {
        width: 568px;
        font-size: 16px;
        margin: 5px 0 1px 20px;
        padding: 0 10px 0 0;
        border: 0;
        max-height: 30px;
        outline: none;
        box-sizing: content-box;
        height: 35px;
        vertical-align: top;
    }

    .searchBoxSubmit {
        background-color: #fff;
        border-color: #fff;
        background-image: url(/images/search.png);
        background-repeat: no-repeat;
        height: 20px;
        width: 20px;
        text-indent: -99em;
        border-width: 0;
        border-style: solid;
        margin: 10px;
        outline: 0;
    }
    ```

7. 将样式表文件另存为 hotels.css，保存在 wwwroot/css 文件夹中（位于默认的 site.css 文件旁边）。

到此完成了我们的视图。 我们取得了良好的进展。 模型和视图已完成，只剩下通过控制器将所有项关联在一起。

## <a name="define-methods"></a>定义方法

我们需要修改默认创建的一个控制器（主页控制器）的内容  。

1. 打开 HomeController.cs 文件并将 using 语句替换为以下语句  。

    ```cs
    using System;
    using System.Diagnostics;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using FirstAzureSearchApp.Models;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    ```

### <a name="add-index-methods"></a>添加 Index 方法

我们需要两个 Index 方法：一个不采用任何参数（适用于应用首次打开的情况），另一个将模型作为参数（适用于用户已输入搜索文本的情况）  。 默认情况下创建第一种方法。 

1. 在默认“Index()”方法后，添加以下方法  。

    ```cs
        [HttpPost]
        public async Task<ActionResult> Index(SearchData model)
        {
            try
            {
                // Ensure the search string is valid.
                if (model.searchText == null)
                {
                    model.searchText = "";
                }

                // Make the Azure Cognitive Search call.
                await RunQueryAsync(model);
            }

            catch
            {
                return View("Error", new ErrorViewModel { RequestId = "1" });
            }
            return View(model);
        }
    ```

    请注意方法的 async 声明和对 RunQueryAsync 的 await 调用    。 这些关键字负责异步调用，从而避免在服务器上阻止线程。

    默认情况下，catch 块使用为我们创建的错误模型  。

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>请注意错误处理以及其他默认的视图和方法

默认情况下会创建一系列略微不同的默认视图，具体取决于你正在使用的 .NET Core 的版本。 对于.NET Core 2.1，默认视图是“索引”、“关于”、“联系方式”、“隐私”和“错误”。 例如，对于.NET Core 2.2，默认视图是“索引”、“隐私”和“错误”。 在任一情况下，运行应用时可以查看这些默认页，并检查控制器中如何对它们进行处理。

本教程稍后将测试“错误”视图。

在 GitHub 示例中，我们已删除未使用的视图以及与其关联的操作。

### <a name="add-the-runqueryasync-method"></a>添加 RunQueryAsync 方法

Azure 认知搜索调用封装在 **RunQueryAsync** 方法中。

1. 首先添加一些静态变量来设置 Azure 服务，并添加调用来启动它们。

    ```cs
        private static SearchServiceClient _serviceClient;
        private static ISearchIndexClient _indexClient;
        private static IConfigurationBuilder _builder;
        private static IConfigurationRoot _configuration;

        private void InitSearch()
        {
            // Create a configuration using the appsettings file.
            _builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            _configuration = _builder.Build();

            // Pull the values from the appsettings.json file.
            string searchServiceName = _configuration["SearchServiceName"];
            string queryApiKey = _configuration["SearchServiceQueryApiKey"];

            // Create a service and index client.
            _serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(queryApiKey));
            _indexClient = _serviceClient.Indexes.GetClient("hotels");
        }
    ```

2. 现在，添加 RunQueryAsync 方法本身  。

    ```cs
        private async Task<ActionResult> RunQueryAsync(SearchData model)
        {
            InitSearch();

            var parameters = new SearchParameters
            {
                // Enter Hotel property names into this list so only these values will be returned.
                // If Select is empty, all values will be returned, which can be inefficient.
                Select = new[] { "HotelName", "Description" }
            };

            // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
            model.resultList = await _indexClient.Documents.SearchAsync<Hotel>(model.searchText, parameters);

            // Display the results.
            return View("Index", model);
        }
    ```

    在此方法中，我们首先确保 Azure 配置已启动，然后设置某些搜索参数。 Select 参数中的字段名称与“酒店”类中属性名称完全匹配   。 可以忽略 Select 参数，在这种情况下，返回所有属性  。 但是，如果我们只对数据的子集感兴趣，不设置 Select 参数是无效的  。 通过指定我们感兴趣的属性，只返回这些属性。

    对搜索 (model.resultList = await _indexClient.Documents.SearchAsync&lt;Hotel&gt;(model.searchText, parameters);  ) 的异步调用是此教程和应用的内容。 DocumentSearchResult 类很有趣，最好（应用运行时）在此处设置断点，并使用调试器来检查 model.resultList 的内容   。 你应该发现它很直观，仅提供你请求的数据，不提供其他数据。

现在，让我们来看看实际情况。

### <a name="test-the-app"></a>测试应用程序

现在，让我们来检查应用是否正确运行。

1. 选择“调试/启动但不调试”或按 F5 键  。 如果编码正确，将出现初始索引视图。

     ![打开应用](./media/tutorial-csharp-create-first-app/azure-search-index.png)

2. 输入文本，如“沙滩”（或能想到的任何文本），然后单击搜索图标。 应该显示一些结果。

     ![搜索“沙滩”](./media/tutorial-csharp-create-first-app/azure-search-beach.png)

3. 请尝试输入“五星”。 请注意这样没有结果。 更复杂的搜索会将“五星”视为“奢华”的同义词并返回那些结果。 在 Azure 认知搜索中可以使用同义词，尽管我们不会在前几篇教程中介绍它。
 
4. 请输入“热”作为搜索文本。 它不会返回包含“酒店”这个词的条目  。 虽然会返回一些结果，但我们的搜索仅定位整个词。

5. 请尝试其他词：“池”、“阳光”、“风景”等。 你将看到 Azure 认知搜索在最简单的级别运行，但搜索结果仍具有说服力。

## <a name="test-edge-conditions-and-errors"></a>测试边缘情况和错误

即使一切工作完美进行，也请务必验证我们的错误处理功能按预期运行。 

1. 在 Index 方法中，在 try { 调用后，输入行 Throw new Exception()    。 当我们搜索文本时，此异常会强制执行错误。

2. 运行该应用，输入“栏”作为搜索文本，然后单击搜索图标。 异常应该会导致错误视图。

     ![强制执行错误](./media/tutorial-csharp-create-first-app/azure-search-error.png)

    > [!Important]
    > 在错误页面中返回内部错误编号被视为安全风险。 如果应用用于常规用途，请针对发生错误时的返回内容的安全和最佳做法进行一些调查。

3. 如果对错误处理符合预期感到满意，请删除 Throw new Exception()  。

## <a name="takeaways"></a>要点

请考虑此项目中的以下要点：

* Azure 认知搜索调用很简洁，而且可以轻松解释结果。
* 异步调用将少量复杂性添加到控制器，但如果你想开发高质量应用，这是最佳做法。
* 此应用执行简单的文本搜索，由 searchParameters 中的设置定义  。 但是，此类可以由将复杂性添加到搜索的许多成员填充。 要使此应用变得功能更强大，不需要执行许多额外工作。

## <a name="next-steps"></a>后续步骤

若要使用 Aure 认知搜索提供最佳用户体验，我们需要添加更多功能，尤其是分页（使用页码或无限滚动）和自动完成/建议。 我们还应考虑更复杂的搜索参数（例如，给定点的指定半径内对酒店的地理搜索和搜索结果排序）。

一系列教程会介绍这些后续步骤。 让我们从分页开始。

> [!div class="nextstepaction"]
> [C# 教程：搜索结果分页 - Azure 认知搜索](tutorial-csharp-paging.md)


