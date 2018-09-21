---
title: 快速入门：使用用于 C# 的必应 Web 搜索 SDK
titleSuffix: Azure Cognitive Services
description: 可以使用必应 Web 搜索 SDK 轻松地将必应 Web 搜索集成到 C# 应用程序中。 本快速入门介绍如何实例化客户端、发送请求和输出响应。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: quickstart
ms.date: 08/16/2018
ms.author: erhopf
ms.openlocfilehash: cebeab562a54ec68b69eb6422685cc4b596ac223
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2018
ms.locfileid: "46127868"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-c"></a>快速入门：使用用于 C# 的必应 Web 搜索 SDK

可以使用必应 Web 搜索 SDK 轻松地将必应 Web 搜索集成到 C# 应用程序中。 本快速入门介绍如何实例化客户端、发送请求和输出响应。

想要马上查看代码？ GitHub 上提供了[适用于 C# 示例的必应 Web 搜索 SDK](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/)。

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="prerequisites"></a>先决条件

下面是在开始本快速入门之前需要准备好的项目：

* [Visual Studio](https://visualstudio.microsoft.com/downloads/) 或
* [Visual Studio Code 2017](https://code.visualstudio.com/download)
  * [适用于 Visual Studio Code 的 C#](https://visualstudio.microsoft.com/downloads/)
  * [NuGet 包管理器](https://github.com/jmrog/vscode-nuget-package-manager)
* [.Net Core SDK](https://www.microsoft.com/net/download)

## <a name="create-a-project-and-install-dependencies"></a>创建项目并安装依赖项

第一步是创建新的控制台项目。 如需控制台项目设置方面的帮助，请参阅 [Hello World -- 你的第一个程序（C# 编程指南）](https://docs.microsoft.com/dotnet/csharp/programming-guide/inside-a-program/hello-world-your-first-program)。 若要在应用程序中使用必应 Web 搜索 SDK，需使用 NuGet 包管理器来安装 `Microsoft.Azure.CognitiveServices.Search.WebSearch`。

[Web 搜索 SDK 包](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.WebSearch/1.2.0)还安装：

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="declare-dependencies"></a>声明依赖项

在 Visual Studio 或 Visual Studio Code 中打开项目，然后导入以下依赖项：

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.CognitiveServices.Search.WebSearch;
using Microsoft.Azure.CognitiveServices.Search.WebSearch.Models;
using System.Linq;
```

## <a name="create-project-scaffolding"></a>创建项目基架

创建新的控制台项目时，应该已经创建应用程序的命名空间和类。 程序应如下所示：

```csharp
namespace WebSearchSDK
{
    class YOUR_PROGRAM
    {

        // The code in the following sections goes here.

    }
}
```

在以下部分，我们会在该类中生成示例应用程序。

## <a name="construct-a-request"></a>构造请求

以下代码构造搜索查询。

```csharp
public static void WebResults(WebSearchAPI client)
{
    try
    {
        var webData = client.Web.Search(query: "Yosemite National Park");
        Console.WriteLine("Searching for \"Yosemite National Park\"");

        // Code for handling responses is provided in the next section...

    }
    catch (Exception ex)
    {
        Console.WriteLine("Encountered exception. " + ex.Message);
    }
}
```

## <a name="handle-the-response"></a>处理响应

接下来，让我们添加一些对响应进行分析并输出结果的代码。 将会输出第一个网页、图像、新闻文章和视频的 `name` 和 `url`（如果存在于响应对象中）。

```csharp
if (webData?.WebPages?.Value?.Count > 0)
{
    // find the first web page
    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

    if (firstWebPagesResult != null)
    {
        Console.WriteLine("Webpage Results # {0}", webData.WebPages.Value.Count);
        Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
        Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
    }
    else
    {
        Console.WriteLine("Didn't find any web pages...");
    }
}
else
{
    Console.WriteLine("Didn't find any web pages...");
}

/*
 * Images
 * If the search response contains images, the first result's name
 * and url are printed.
 */
if (webData?.Images?.Value?.Count > 0)
{
    // find the first image result
    var firstImageResult = webData.Images.Value.FirstOrDefault();

    if (firstImageResult != null)
    {
        Console.WriteLine("Image Results # {0}", webData.Images.Value.Count);
        Console.WriteLine("First Image result name: {0} ", firstImageResult.Name);
        Console.WriteLine("First Image result URL: {0} ", firstImageResult.ContentUrl);
    }
    else
    {
        Console.WriteLine("Didn't find any images...");
    }
}
else
{
    Console.WriteLine("Didn't find any images...");
}

/*
 * News
 * If the search response contains news articles, the first result's name
 * and url are printed.
 */
if (webData?.News?.Value?.Count > 0)
{
    // find the first news result
    var firstNewsResult = webData.News.Value.FirstOrDefault();

    if (firstNewsResult != null)
    {
        Console.WriteLine("\r\nNews Results # {0}", webData.News.Value.Count);
        Console.WriteLine("First news result name: {0} ", firstNewsResult.Name);
        Console.WriteLine("First news result URL: {0} ", firstNewsResult.Url);
    }
    else
    {
        Console.WriteLine("Didn't find any news articles...");
    }
}
else
{
    Console.WriteLine("Didn't find any news articles...");
}

/*
 * Videos
 * If the search response contains videos, the first result's name
 * and url are printed.
 */
if (webData?.Videos?.Value?.Count > 0)
{
    // find the first video result
    var firstVideoResult = webData.Videos.Value.FirstOrDefault();

    if (firstVideoResult != null)
    {
        Console.WriteLine("\r\nVideo Results # {0}", webData.Videos.Value.Count);
        Console.WriteLine("First Video result name: {0} ", firstVideoResult.Name);
        Console.WriteLine("First Video result URL: {0} ", firstVideoResult.ContentUrl);
    }
    else
    {
        Console.WriteLine("Didn't find any videos...");
    }
}
else
{
    Console.WriteLine("Didn't find any videos...");
}
```

## <a name="declare-the-main-method"></a>声明 main 方法

在此应用程序中，main 方法包括了用来实例化客户端、验证 `subscriptionKey` 和调用 `WebResults` 的代码。 请确保输入你的 Azure 帐户的有效订阅密钥，然后再继续。

```csharp
static void Main(string[] args)
{
    var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

    WebResults(client);

    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
}
```

## <a name="run-the-application"></a>运行应用程序

让我们运行应用程序！

```console
dotnet run
```

## <a name="define-functions-and-filter-results"></a>定义函数并筛选结果

对必应 Web 搜索 API 进行首次调用以后，让我们看看一些函数，这些函数突出显示了用于优化查询和筛选结果的 SDK 功能。 每个函数都可以添加到在上一部分创建的 C# 应用程序中。

### <a name="limit-the-number-of-results-returned-by-bing"></a>限制必应返回的结果数

此示例使用 `count` 和 `offset` 参数限制针对“西雅图最好的餐馆”搜索返回的结果数。 将会输出初始结果的 `name` 和 `URL`。

1. 将以下代码添加到控制台项目：
    ```csharp
    public static void WebResultsWithCountAndOffset(WebSearchAPI client)
    {
        try
        {
            var webData = client.Web.SearchAsync(query: "Best restaurants in Seattle", offset: 10, count: 20).Result;
            Console.WriteLine("\r\nSearching for \" Best restaurants in Seattle \"");

            if (webData?.WebPages?.Value?.Count > 0)
            {
                var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

                if (firstWebPagesResult != null)
                {
                    Console.WriteLine("Web Results #{0}", webData.WebPages.Value.Count);
                    Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
                    Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
                }
                else
                {
                    Console.WriteLine("Couldn't find first web result!");
                }
            }
            else
            {
                Console.WriteLine("Didn't see any Web data..");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine("Encountered exception. " + ex.Message);
        }
    }
    ```
2. 将 `WebResultsWithCountAndOffset` 添加到 `main`：
    ```csharp
    static void Main(string[] args)
    {
        var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        WebResults(client);
        // Search with count and offset...
        WebResultsWithCountAndOffset(client);  

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```
3. 运行应用程序。

### <a name="filter-for-news"></a>新闻筛选器

此示例使用 `response_filter` 参数筛选搜索结果。 返回的搜索结果仅限“Microsoft”的新闻文章。 将会输出初始结果的 `name` 和 `URL`。

1. 将以下代码添加到控制台项目：
    ```csharp
    public static void WebSearchWithResponseFilter(WebSearchAPI client)
    {
        try
        {
            IList<string> responseFilterstrings = new List<string>() { "news" };
            var webData = client.Web.SearchAsync(query: "Microsoft", responseFilter: responseFilterstrings).Result;
            Console.WriteLine("\r\nSearching for \" Microsoft \" with response filter \"news\"");

            if (webData?.News?.Value?.Count > 0)
            {
                var firstNewsResult = webData.News.Value.FirstOrDefault();

                if (firstNewsResult != null)
                {
                    Console.WriteLine("News Results #{0}", webData.News.Value.Count);
                    Console.WriteLine("First news result name: {0} ", firstNewsResult.Name);
                    Console.WriteLine("First news result URL: {0} ", firstNewsResult.Url);
                }
                else
                {
                    Console.WriteLine("Couldn't find first News results!");
                }
            }
            else
            {
                Console.WriteLine("Didn't see any News data..");
            }

        }
        catch (Exception ex)
        {
            Console.WriteLine("Encountered exception. " + ex.Message);
        }
    }
    ```
2. 将 `WebResultsWithCountAndOffset` 添加到 `main`：
    ```csharp
    static void Main(string[] args)
    {
        var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        WebResults(client);
        // Search with count and offset...
        WebResultsWithCountAndOffset(client);  
        // Search with news filter...
        WebSearchWithResponseFilter(client);

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```
3. 运行应用程序。

### <a name="use-safe-search-answer-count-and-the-promote-filter"></a>使用安全搜索、答案计数和提升筛选器

此示例使用 `answer_count`、`promote` 和 `safe_search` 参数筛选“音乐视频”的搜索结果。 将会显示初始结果的 `name` 和 `URL`。

1. 将以下代码添加到控制台项目：
    ```csharp
    public static void WebSearchWithAnswerCountPromoteAndSafeSearch(WebSearchAPI client)
    {
        try
        {
            IList<string> promoteAnswertypeStrings = new List<string>() { "videos" };
            var webData = client.Web.SearchAsync(query: "Music Videos", answerCount: 2, promote: promoteAnswertypeStrings, safeSearch: SafeSearch.Strict).Result;
            Console.WriteLine("\r\nSearching for \"Music Videos\"");

            if (webData?.Videos?.Value?.Count > 0)
            {
                var firstVideosResult = webData.Videos.Value.FirstOrDefault();

                if (firstVideosResult != null)
                {
                    Console.WriteLine("Video Results # {0}", webData.Videos.Value.Count);
                    Console.WriteLine("First Video result name: {0} ", firstVideosResult.Name);
                    Console.WriteLine("First Video result URL: {0} ", firstVideosResult.ContentUrl);
                }
                else
                {
                    Console.WriteLine("Couldn't find videos results!");
                }
            }
            else
            {
                Console.WriteLine("Didn't see any data..");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine("Encountered exception. " + ex.Message);
        }
    }
    ```
2. 将 `WebResultsWithCountAndOffset` 添加到 `main`：
    ```csharp
    static void Main(string[] args)
    {
        var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        WebResults(client);
        // Search with count and offset...
        WebResultsWithCountAndOffset(client);  
        // Search with news filter...
        WebSearchWithResponseFilter(client);
        // Search with answer count, promote, and safe search
        WebSearchWithAnswerCountPromoteAndSafeSearch(client);

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```
3. 运行应用程序。

## <a name="clean-up-resources"></a>清理资源

完成本项目以后，请务必从应用程序代码中删除订阅密钥。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [认知服务 Node.js SDK 示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/)
