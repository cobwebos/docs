---
title: 用于创建第一个应用的 C# 教程
titleSuffix: Azure Cognitive Search
description: 了解如何逐步构建第一个 C# 搜索应用。 本教程提供了 GitHub 上工作应用的下载链接以及从头开始构建应用的完整过程。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 09/30/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ec98762ac5918437e8fdb8426b54b79b1fb5b222
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2020
ms.locfileid: "91939715"
---
# <a name="tutorial-create-your-first-search-app-using-the-net-sdk"></a>教程：使用 .NET SDK 创建你的第一个搜索应用

本教程演示如何使用 Azure 认知搜索和 Visual Studio 创建一个可查询和返回搜索索引结果的 web 应用。

在本教程中，您将学习如何执行以下操作：

> [!div class="checklist"]
> * 设置开发环境
> * 数据结构建模
> * 创建用于收集查询输入和显示结果的网页
> * 定义搜索方法
> * 测试应用程序

你还将了解到搜索调用有多简单。 你将在代码中开发的关键语句封装在以下几行中。

```csharp
var options = new SearchOptions()
{
    // The Select option specifies fields for the result set
    options.Select.Add("HotelName");
    options.Select.Add("Description");
};

var searchResult = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);
model.resultList = searchResult.Value.GetResults().ToList();
```

只有一个调用会查询索引并返回结果。

:::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-pool.png" alt-text="搜索“池”**" border="true":::

## <a name="overview"></a>概述

本教程使用现有的托管示例索引，以便你可以专注于构建可收集相关请求的查询字符串并返回结果的搜索页面。 索引包含虚构的酒店数据。 有了基本页面后，可以在后续课程中对其进行增强，以添加分页、facet 和提前键入体验。

可以在以下项目中找到本教程中代码的完成版本：

* [1-basic-search-page (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/1-basic-search-page)

本教程已更新，以便可以使用 Azure.Search.Documents（版本 11）包。 有关 .NET SDK 的早期版本，请参阅 [Microsoft.Azure.Search（版本 10）代码示例](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10)。

## <a name="prerequisites"></a>先决条件

由于你使用的是 Microsoft 托管的公共示例搜索索引，因此在本教程中无需使用搜索服务或 Azure 帐户。

* [Visual Studio](https://visualstudio.microsoft.com/)

* [Azure 认知搜索客户端库（版本11）](https://www.nuget.org/packages/Azure.Search.Documents/)

### <a name="install-and-run-the-project-from-github"></a>从 GitHub 安装并运行项目

如果想跳转到一个正常运行的应用，请按照以下步骤下载并运行已完成的代码。

1. 定位 GitHub 上的示例：[创建第一个应用](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11)。

1. 在[根文件夹](https://github.com/Azure-Samples/azure-search-dotnet-samples)处，选择“代码”，然后选择“克隆”或“下载 ZIP”，以创建项目的私有本地副本。

1. 使用 Visual Studio，导航到并打开基本搜索页 ("1-basic-search-page") 上的解决方案，选择“启动但不调试”（或按 F5）以生成或运行该程序。

1. 这是一个有关酒店的索引，因此请键入可用于搜索酒店相关信息的字词（例如，“wifi”、“视图”、“酒吧”、“停车”），并查看结果。

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-wifi.png" alt-text="搜索“池”**" border="true":::

希望此项目能顺利运行，从而 Web 应用能顺利运行。 这个应用中包含了较复杂搜索的许多基本组件，因此最好全部浏览完并分步重新创建。 以下部分介绍了这些步骤。

## <a name="set-up-a-development-environment"></a>设置开发环境

若要从头开始创建此项目以深化对 Azure 认知搜索概念的认知，请从 Visual Studio 项目开始。

1. 在 Visual Studio 中，选择“新建 > 项目”，然后选择“ASP.NET Core Web 应用程序”  。

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-project1.png" alt-text="搜索“池”**" border="true":::

1. 为项目指定一个名称（例如“FirstSearchApp”），然后设置位置。 选择“创建”  。

1. 选择“Web 应用程序(模型-视图-控制器)”项目模板。

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-project2.png" alt-text="搜索“池”**" border="true":::

1. 安装客户端库。 在“工具” > “NuGet 包管理器” > “管理解决方案的 NuGet 包...”中，选择“浏览”，然后搜索“azure.search.documents”。 安装 Azure.Search.Documents（版本 11 或更高版本），接受许可协议和依赖项。

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png" alt-text="搜索“池”**" border="true":::

### <a name="initialize-azure-cognitive-search"></a>初始化 Azure 认知搜索

在此示例中，将使用公开可用的酒店数据。 此数据是 50 个虚构的酒店名称和说明的任意集合，专门为提供演示数据而创建。 若要访问此数据，请指定名称和 API 密钥。

1. 打开 appsettings.json，并用以下名称和密钥替换默认行。 此处显示的 API 密钥不是密钥示例，而是  访问酒店数据所需的密钥。 文件现在应如下所示。

    ```csharp
    {
        "SearchServiceName": "azs-playground",
        "SearchServiceQueryApiKey": "EA4510A6219E14888741FCFC19BFBB82"
    }
    ```

1. 在解决方案资源管理器中，选择该文件，然后在“属性”中，将“复制到输出目录”设置更改为“如果更新则复制”。

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-copy-if-newer.png" alt-text="搜索“池”**" border="true":::

## <a name="model-data-structures"></a>数据结构建模

模型（C#类）用于客户端（视图）、服务器（控制器）和使用 MVC（模型、视图、控制器）体系结构的 Azure 云之间的数据通信。 通常情况下，这些模型将反映正在访问的数据的结构。

在此步骤中，将为搜索索引的数据结构以及在视图/控制器通信中使用的搜索字符串建模。 在酒店索引中，每个酒店都有许多房间，每个酒店都有一个由多个部分构成的地址。 因此酒店的完整表示形式是一个具有层次的嵌套数据结构。 需要三个类来创建每个组件。

“酒店”、“地址”和“房间”类的集合称为[复杂类型](search-howto-complex-data-types.md)，这是 Azure 认知搜索的一项重要功能。 复杂类型可以是多级深度的类和子类，并支持比使用简单类型（仅包含基元成员的类）所表示的数据结构复杂得多的数据结构  。

1. 在解决方案资源管理器中，右键依次单击“模型” > “添加” > “新项”。

1. 选择“类”并将项目命名为 Hotel.cs。 将 Hotel.cs 的所有内容替换为以下代码。 请注意类的“地址”和“房间”成员，这些字段是类本身，因此也需要它们的模型 。

    ```csharp
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;
    using Microsoft.Spatial;
    using System;
    using System.Text.Json.Serialization;

    namespace FirstAzureSearchApp.Models
    {
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
    }
    ```

1. 请重复为“地址”类创建模型的过程，并将文件命名为 Address.cs。 将内容替换为以下内容。

    ```csharp
    using Azure.Search.Documents.Indexes;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Address
        {
            [SearchableField]
            public string StreetAddress { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string City { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string StateProvince { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string PostalCode { get; set; }

            [SearchableField(IsFilterable = true, IsSortable = true, IsFacetable = true)]
            public string Country { get; set; }
        }
    }
    ```

1. 同样，按照相同的过程来创建“房间”类，将文件命名为 Room.cs  。

    ```csharp
    using Azure.Search.Documents.Indexes;
    using Azure.Search.Documents.Indexes.Models;
    using System.Text.Json.Serialization;

    namespace FirstAzureSearchApp.Models
    {
        public partial class Room
        {
            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.EnMicrosoft)]
            public string Description { get; set; }

            [SearchableField(AnalyzerName = LexicalAnalyzerName.Values.FrMicrosoft)]
            [JsonPropertyName("Description_fr")]
            public string DescriptionFr { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string Type { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public double? BaseRate { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string BedOptions { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public int SleepsCount { get; set; }

            [SimpleField(IsFilterable = true, IsFacetable = true)]
            public bool? SmokingAllowed { get; set; }

            [SearchableField(IsFilterable = true, IsFacetable = true)]
            public string[] Tags { get; set; }
        }
    }
    ```

1. 本教程创建的最后一个模型是名为“SearchData”的类，它表示用户的输入（searchText）和搜索的输出（resultList）。 输出的类型 SearchResults&lt;Hotel&gt; 很重要，因为此类型与搜索结果完全匹配，需要将此引用传递到视图。 用以下代码替换默认模板。

    ```csharp
    using Azure.Search.Documents.Models;

    namespace FirstAzureSearchApp.Models
    {
        public class SearchData
        {
            // The text to search for.
            public string searchText { get; set; }

            // The list of results.
            public SearchResults<Hotel> resultList;
        }
    }
    ```

## <a name="create-a-web-page"></a>创建 Web 页

项目模板附带了许多客户端视图，这些视图位于“视图”文件夹中的。 具体视图取决于正在使用的 Core .NET 版本（此示例中使用 3.1）。 本教程中将修改 Index.cshtml，以包含搜索页的元素。

删除 Index.cshtml 所有内容并按以下步骤重新生成文件。

1. 本教程在视图中使用两个小图像：Azure 徽标和搜索放大镜图标（azure-logo.png 和 search.png）。 将这些图像从 GitHub 项目中复制到你的项目的 wwwroot/images 文件夹中。

1. Index.cshtml 的第一行应引用将用于实现客户端（视图）和服务器（控制器）之间数据通信的模型，该模型是之前创建的 SearchData 模型。 将此行添加到 Index.cshtml 文件。

    ```csharp
    @model FirstAzureSearchApp.Models.SearchData
    ```

1. 为视图输入标题是标准做法，因此下一行应该是：

    ```csharp
    @{
        ViewData["Title"] = "Home Page";
    }
    ```

1. 在标题后，输入对 HTML 样式表的引用，将在稍后创建它。

    ```csharp
    <head>
        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

1. 视图的主体会处理两个用例。 首先，在输入任何搜索文本之前，需要在第一次使用时提供一个空页面。 其次，除了搜索文本框外，它还需要处理结果，以便重复查询。

   为了满足这两种情况，需要检查提供给视图的模型是否为 null。 Null 模型指示第一个用例（应用的初始运行）。 将以下代码添加到 Index.cshtml 文件并浏览注释。

    ```csharp
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
                @Model.resultList.Count Results
            </p>

            @for (var i = 0; i < Model.resultList.Count; i++)
            {
                // Display the hotel name and description.
                @Html.TextAreaFor(m => m.resultList[i].Document.HotelName, new { @class = "box1" })
                @Html.TextArea($"desc{i}", Model.resultList[i].Document.Description, new { @class = "box2" })
            }
        }
    }
    </body>
    ```

1. 添加样式表。 在 Visual Studio 中，在“文件”> “新建 > 文件”中，选择“样式表”（突出显示“常规”）    。

   将默认代码替换为以下代码。 我们不会进一步详细介绍此文件，样式是标准 HTML。

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

1. 将样式表文件另存为 hotels.css，保存在 wwwroot/css 文件夹中（位于默认的 site.css 文件旁边）。

到此完成了我们的视图。 此时，模型和视图都已完成。 接下来只需要通过控制器将所有项关联在一起。

## <a name="define-methods"></a>定义方法

在此步骤中，修改为“主页控制器”的内容。

1. 打开 HomeController.cs 文件并将 using 语句替换为以下语句  。

    ```csharp
    using Azure;
    using Azure.Search.Documents;
    using Azure.Search.Documents.Indexes;
    using FirstAzureSearchApp.Models;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Configuration;
    using System;
    using System.Diagnostics;
    using System.Linq;
    using System.Threading.Tasks;
    ```

### <a name="add-index-methods"></a>添加 Index 方法

在 MVC 应用中，Index() 方法是所有控制器的默认操作方法。 它将打开索引 HTML 页。 本教程将不带参数的默认方法用于应用程序启动用例：呈现空搜索页。

在本部分中，我们将扩展该方法以支持第二个用例：在用户输入搜索文本时呈现页面。 为支持此用例，将扩展 index 方法，以便采用模型作为参数。

1. 在默认“Index()”方法后，添加以下方法  。

    ```csharp
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

    请注意方法的 async 声明和对 RunQueryAsync 的 await 调用    。 这些关键字负责异步调用，从而避免线程在服务器上受到阻止。

    Catch 块使用默认创建的错误模型。

### <a name="note-the-error-handling-and-other-default-views-and-methods"></a>请注意错误处理以及其他默认的视图和方法

默认情况下会创建一系列略微不同的默认视图，具体取决于你正在使用的 .NET Core 的版本。 对于.NET Core 3.1，默认视图是“索引”、“隐私”和“错误”。 运行应用时可以查看这些默认页，并检查控制器中如何对它们进行处理。

本教程稍后将测试“错误”视图。

在 GitHub 示例中，已删除未使用的视图以及与其关联的操作。

### <a name="add-the-runqueryasync-method"></a>添加 RunQueryAsync 方法

Azure 认知搜索调用封装在 **RunQueryAsync** 方法中。

1. 首先添加一些静态变量来设置 Azure 服务，并添加调用来启动它们。

    ```csharp
        private static SearchClient _searchClient;
        private static SearchIndexClient _indexClient;
        private static IConfigurationBuilder _builder;
        private static IConfigurationRoot _configuration;

        private void InitSearch()
        {
            // Create a configuration using appsettings.json
            _builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            _configuration = _builder.Build();

            // Read the values from appsettings.json
            string searchServiceUri = _configuration["SearchServiceUri"];
            string queryApiKey = _configuration["SearchServiceQueryApiKey"];

            // Create a service and index client.
            _indexClient = new SearchIndexClient(new Uri(searchServiceUri), new AzureKeyCredential(queryApiKey));
            _searchClient = _indexClient.GetSearchClient("hotels");
        }
    ```

2. 现在，添加 RunQueryAsync 方法本身  。

    ```csharp
    private async Task<ActionResult> RunQueryAsync(SearchData model)
    {
        InitSearch();

        var options = new SearchOptions() { };

        // Enter Hotel property names into this list so only these values will be returned.
        // If Select is empty, all values will be returned, which can be inefficient.
        options.Select.Add("HotelName");
        options.Select.Add("Description");

        // For efficiency, the search call should be asynchronous, so use SearchAsync rather than Search.
        var searchResult = await _searchClient.SearchAsync<Hotel>(model.searchText, options).ConfigureAwait(false);
        model.resultList = searchResult.Value.GetResults().ToList();

        // Display the results.
        return View("Index", model);
    }
    ```

    在此方法中，首先确保 Azure 配置已启动，然后设置某些搜索选项。 “选择”选项指定要在结果中返回的字段，从而与“宾馆”类中的属性名称匹配。 如果省略“选择”，会返回所有隐藏的字段，如果只对所有可能字段的某个子集感兴趣，这就会导致效率低下。

    搜索的异步调用会构建请求（建模为 searchText）和响应（建模为 searchResult）。 如果正在调试此代码，且需要检查 model.resultList 的内容，也可以使用 SearchResult 类来设置断点。 你应该发现它很直观，仅提供你请求的数据，不提供其他数据。

### <a name="test-the-app"></a>测试应用

现在来检查应用是否正常运行。

1. 选择“调试” > “在不调试的情况下启动”或按 F5。 如果应用按预期运行，应会获取初始索引视图。

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-index.png" alt-text="搜索“池”**" border="true":::

1. 输入查询字符串（如“海滩”）或脑海中想到的任何文字，并单击“搜索”图标以发送请求。

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-beach.png" alt-text="搜索“池”**" border="true":::

1. 请尝试输入“五星”。 请注意，此查询不返回任何结果。 更复杂的搜索会将“五星”视为“奢华”的同义词并返回那些结果。 Azure 认知搜索中提供[同义词](search-synonyms.md)支持，但此支持不在本系列教程的介绍范围内。

1. 请输入“热”作为搜索文本。 它不会返回包含“酒店”这个词的条目  。 虽然会返回一些结果，但我们的搜索仅定位整个词。

1. 请尝试其他词：“池”、“阳光”、“风景”等。 你将看到 Azure 认知搜索在最简单的级别运行，但搜索结果仍具有说服力。

## <a name="test-edge-conditions-and-errors"></a>测试边缘情况和错误

即使一切工作完美进行，也请务必验证我们的错误处理功能按预期运行。 

1. 在 Index 方法中，在 try { 调用后，输入行 Throw new Exception()    。 此异常会导致在搜索文本时发生错误。

2. 运行该应用，输入“栏”作为搜索文本，然后单击搜索图标。 异常应该会导致错误视图。

     :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-error.png" alt-text="搜索“池”**" border="true":::

    > [!Important]
    > 在错误页面中返回内部错误编号被视为安全风险。 如果应用用于常规用途，请针对发生错误时的返回内容的安全和最佳做法进行一些调查。

3. 如果对错误处理符合预期感到满意，请删除 Throw new Exception()  。

## <a name="takeaways"></a>要点

请考虑此项目中的以下要点：

* Azure 认知搜索调用很简洁，而且可以轻松解释结果。
* 异步调用将少量复杂性添加到控制器，但如果你想开发高质量应用，这是最佳做法。
* 此应用执行简单的文本搜索，由 searchOptions 中的设置定义。 但是，此类可以由将复杂性添加到搜索的许多成员填充。 要使此应用变得功能更强大，不需要执行许多额外工作。

## <a name="next-steps"></a>后续步骤

若要优化用户体验，请添加更多功能，尤其是分页（使用页码或无限滚动）和自动完成/建议。 还可以考虑更复杂的搜索选项（例如，给定点的指定半径内对酒店的地理搜索和搜索结果排序）。

接下来的教程会介绍这些后续步骤。 让我们从分页开始。

> [!div class="nextstepaction"]
> [C# 教程：搜索结果分页 - Azure 认知搜索](tutorial-csharp-paging.md)