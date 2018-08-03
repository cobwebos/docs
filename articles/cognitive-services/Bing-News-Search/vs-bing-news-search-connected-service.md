---
title: 必应新闻搜索 C# 教程 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 从 ASP.NET Core Web 应用程序连接到认知服务必应新闻搜索。
services: cognitive-services
author: ghogen
manager: douge
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: ghogen
ms.openlocfilehash: 5cfa82067d28b05f32bd87e0e83d55a03da8d508
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38663855"
---
# <a name="connect-to-bing-news-search-api-by-using-connected-services-in-visual-studio"></a>使用 Visual Studio 中的连接服务连接到必应新闻搜索 API

通过使用必应新闻搜索，可使应用和服务能够利用作用于 Web 的无广告搜索引擎的强大功能。 必应新闻搜索是认知服务提供的搜索服务之一。

本文详细说明了如何使用适用于必应新闻搜索的 Visual Studio 连接服务功能。 可在安装了认知服务扩展的 Visual Studio 2017 15.7 或更高版本中使用此功能。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果没有帐户，可以注册一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。
- Visual Studio 2017 版本 15.7（安装有 Web 开发工作负荷）。 [立即下载](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)。

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-bing-news-search-api"></a>为项目添加对必应新闻搜索 API 的支持

1. 创建名为 MyWebApplication 的新 ASP.NET Core Web 项目。 使用 Web 应用（模型 - 视图 - 控制器）项目模板，所有设置采用默认设置。 务必将项目命名为 MyWebApplication，以便在将代码复制到项目中时，命名空间可匹配。 

1. 在“解决方案资源管理器”中，选择“添加” > “连接服务”。
   此时会显示“连接服务”页，其中包含可添加到项目的服务。

   ![“添加连接服务”菜单项的屏幕截图](../media/vs-common/Connected-Service-Menu.PNG)

1. 在可用服务菜单中，选择“将智能搜索引入应用”。

   ![连接服务列表的屏幕截图](./media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-0.PNG)

   如果已登录到 Visual Studio，并且有与帐户关联的 Azure 订阅，则会显示一个页面，其中提供了订阅下拉列表。 选择要使用的订阅，然后选择必应新闻搜索 API 的名称。 还可以选择“编辑”，修改自动生成的名称。

   ![订阅和名称字段的屏幕截图](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-1.PNG)

1. 选择资源组和定价层。

   ![资源组和定价层字段的屏幕截图](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-2.PNG)

   如需查看有关定价层的详细信息，请选择“查看定价”。

1. 选择“添加”，添加对“连接服务”的支持。
   Visual Studio 会修改项目以添加 NuGet 包、配置文件条目和其他更改，从而支持与必应新闻搜索 API 的连接。 输出显示项目发生情况的日志。 会看到下面这样的内容：

   ```output
   [5/4/2018 12:41:21.084 PM] Adding Intelligent Search to the project.
   [5/4/2018 12:41:21.271 PM] Creating new Intelligent Search...
   [5/4/2018 12:41:24.128 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.ImageSearch' version 1.2.0...
   [5/4/2018 12:41:24.135 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.NewsSearch' version 1.2.0...
   [5/4/2018 12:41:24.154 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.WebSearch' version 1.2.0...
   [5/4/2018 12:41:24.168 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.CustomSearch' version 1.2.0...
   [5/4/2018 12:41:24.187 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.VideoSearch' version 1.2.0...
   [5/4/2018 12:42:07.287 PM] Retrieving keys...
   [5/4/2018 12:42:07.741 PM] Updating appsettings.json setting: 'ServiceKey' = 'c271412f3e4c4e1dacc7c4145fa0572a'
   [5/4/2018 12:42:07.745 PM] Updating appsettings.json setting: 'ServiceEndPoint' = 'https://api.cognitive.microsoft.com/bing/v7.0'
   [5/4/2018 12:42:07.749 PM] Updating appsettings.json setting: 'Name' = 'WebApplicationCore-Search_IntelligentSearch'
   [5/4/2018 12:42:10.217 PM] Successfully added Intelligent Search to the project.
   ```

   appsettings.json 文件现包含以下新设置：

   ```json
   "CognitiveServices": {
     "IntelligentSearch": {
       "ServiceKey": "<your service key>",
       "ServiceEndPoint": "https://api.cognitive.microsoft.com/bing/v7.0",
       "Name": "WebApplicationCore-Search_IntelligentSearch"
     }
   }
   ```
 
## <a name="use-the-bing-news-search-api-to-add-search-functionality-to-a-web-page"></a>使用必应新闻搜索 API 将搜索功能添加到网页

现已将向项目添加对必应新闻搜索 API 的支持，下面介绍如何使用 API，将智能搜索添加到网页。

1.  在 Startup.cs 的 `ConfigureServices` 方法中，添加对 `IServiceCollection.AddSingleton` 的调用。 这使得包含关键设置的配置对象可用于项目中的代码。
 
   ```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddMvc();
            services.AddSingleton<IConfiguration>(Configuration);
        }
   ```


1. 在“模型”文件夹下添加新的类文件，名为 BingNewsModel.cs。 如果以不同方式为项目命名，请使用自己的项目命名空间，而不是 MyWebApplication。 将内容替换为以下代码：
 
    ```csharp
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch.Models;
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    
    namespace MyWebApplication.Models
    {
        public class BingNewsModel
        {
            public News SearchResult { get; set; } 
            public string SearchText { get; set; }
        }
    }
    ```

   此模型用于存储对必应新闻搜索服务的调用结果。
 
1. 在“控制器”文件夹中，添加名为 IntelligentSearchController.cs 的新类文件。 将内容替换为以下代码：

   ```csharp
    using System.Net.Http;
    using System.Threading.Tasks;
    using MyWebApplication.Models;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch;
    using Microsoft.Extensions.Configuration;
    
    namespace MyWebApplication.Controllers
    {
        // A controller to handle News Search requests
        public class IntelligentSearchController : Controller
        {
            private IConfiguration configuration;
  
            // Set up the configuration that contains the keys
            // (from the appsettings.json file)
            // that you will use to access the service  
            public IntelligentSearchController(IConfiguration configuration)
            {
                this.configuration = configuration;
            }

            // Call the Bing News Search API and put the result in the model object.    
            public async Task<IActionResult> BingSearchResult(BingNewsModel model)
            {
                if (!string.IsNullOrWhiteSpace(model.SearchText))
                { 
                    INewsSearchAPI client = this.GetNewsSearchClient(new MyHandler());
                    model.SearchResult = await client.News.SearchAsync(model.SearchText);
                }
                return View(model);
            }
    
            // Forward requests to the Search endpoint to the BingSearchResult method
            [HttpPost("Search")]
            public IActionResult Search(BingNewsModel model)
            {
                return RedirectToAction("BingSearchResult", model);
            }

            // Get the search client object
            private INewsSearchAPI GetNewsSearchClient(DelegatingHandler handler)
            {
                string key =
                   configuration.GetSection("CognitiveServices")["IntelligentSearch:ServiceKey"];
    
                INewsSearchAPI client = new NewsSearchAPI(
                   new ApiKeyServiceClientCredentials(key), handlers: handler);
    
                return client;
            }
        }
    }
   ```

   在此代码中，构造函数设置包含密钥的配置对象。 `Search` 路由的方法只是对 `BingSearchResult` 函数的重定向。 这将调用 `GetNewsSearchClient` 方法，获取 `NewsSearchAPI` 客户端对象。  `NewsSearchAPI` 客户端对象包含 `SearchAsync` 方法，该方法实际上调用服务并以刚刚创建的 `SearchResult` 模型返回结果。 

1. 添加在之前代码中引用过的 `MyHandler` 类。 这将对搜索服务的异步调用委托给其基类 `DelegatingHandler`。

   ```csharp
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Threading;

    class MyHandler : DelegatingHandler
    {
        protected async override Task<HttpResponseMessage> SendAsync(
        HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Call the inner handler.
            var response = await base.SendAsync(request, cancellationToken);
            
            return response;
        }
    }
   ```

1. 若要添加对提交搜索和查看结果的支持，在“视图”文件夹中创建名为 IntelligentSearch 的新文件夹。 在此新文件夹中，添加 BingSearchResult.cshtml 视图。 复制以下代码：

    ```cshtml
    @using System
    @model MyWebApplication.Models.BingNewsModel
    
    @{
        ViewData["Title"] = "BingSearchResult";
    }
    
    <h2>Search News</h2>
    
    <div class="row">
        <section>
            <form asp-controller="IntelligentSearch" asp-action="Search" method="POST"
                  class="form-horizontal" enctype="multipart/form-data">
                <table width ="90%">
                    <tr>
                        <td>
                            <input type="text" name="SearchText" class="form-control" />
                        </td>
                        <td>
                            <button type="submit" class="btn btn-default">Search</button>
                        </td>
                    </tr>
                </table>
            </form>
        </section>
    </div>
    <h2>Search Result</h2=
    <table>
    @if (!string.IsNullOrEmpty(Model.SearchText)) {
        foreach (var item in Model.SearchResult.Value) {
        <tr>
            <td rowspan="2" width="90">
               <img src=@item?.Image?.Thumbnail?.ContentUrl width="80" height="80" />
            </td>
            <td><a href=@item.Url>@item.Name</a></td>
        </tr>   
        <tr>
            <td>@item.Description</td>
        </tr>
        <tr height="10">
            <td/><td/>
         </tr>
        } }
     </table>
    <div>
        <hr />
        <p>
            <a asp-controller="Home" asp-action="Index">Return to Index</a>
        </p>
    </div>
    ```

1. 在本地启动 Web 应用程序，输入刚刚创建的页面 URL (/IntelligentSearch/BingSearchResult)，并使用“搜索”按钮发布搜索请求。

   ![必应新闻搜索结果的屏幕截图](media/vs-bing-news-search-connected-service/Cog-News-Search-Results.PNG)
           
## <a name="clean-up-resources"></a>清理资源

不再需要资源组时，可将其删除。 这会删除认知服务和相关资源。 若要通过门户删除资源组，请执行以下操作：

1. 在门户顶部的“搜索”框中输入资源组的名称。 选择要删除的资源组。
2. 选择“删除资源组”。
3. 在“键入资源组名称”框中，输入资源组的名称，然后选择“删除”。

## <a name="next-steps"></a>后续步骤

若要详细了解必应新闻搜索 API，请参阅[什么是必应新闻搜索？](index.yml)。
