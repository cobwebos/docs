---
title: 使用排名显示搜索结果
titleSuffix: Azure Cognitive Services
description: 介绍如何使用必应 RankingResponse 答案按排名顺序显示搜索结果。
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 2575A80C-FC74-4631-AE5D-8101CF2591D3
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: tutorial
ms.date: 12/19/2019
ms.author: aahi
ms.openlocfilehash: 1c8e0bb136fddeb84dc991e63a761378b38cc470
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75382321"
---
# <a name="build-a-console-app-search-client-in-c"></a>使用 C# 生成控制台应用搜索客户端

本教程介绍如何生成一个简单的 .NET Core 控制台应用，以允许用户查询必应 Web 搜索 API 并显示排名结果。

本教程介绍如何：

- 对必应 Web 搜索 API 进行简单查询
- 按排名顺序显示查询结果

## <a name="prerequisites"></a>必备条件

若要跟随本教程进行学习，你需要：

- Visual Studio。 如果没有，请[下载并安装免费的 Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)。
- 必应 Web 搜索 API 的订阅密钥。 如果没有订阅密钥，可以[注册免费试用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)。

## <a name="create-a-new-console-app-project"></a>创建新的控制台应用项目

在 Visual Studio 中，使用 `Ctrl`+`Shift`+`N` 创建项目。

在“新建项目”对话框中，单击“Visual C#”>“Windows 经典桌面”>“控制台应用(.NET Framework)”   。

将应用程序命名为“MyConsoleSearchApp”，然后单击“确定”   。

## <a name="add-the-jsonnet-nuget-package-to-the-project"></a>向项目添加 JSON.net Nuget 包

JSON.net 允许使用 API 返回的 JSON 响应。 将其 NuGet 包添加到项目中：

- 在“解决方案资源管理器”中，右键单击项目并选择“管理 NuGet 包...”   。
- 在“浏览”选项卡中，搜索 `Newtonsoft.Json`。 选择最新版本，然后单击“安装”  。
- 单击“查看更改”窗口中的“确定”按钮   。
- 关闭标题为“NuGet: MyConsoleSearchApp”的 Visual Studio 选项卡  。

## <a name="add-a-reference-to-systemweb"></a>添加对 System.Web 的引用

本教程依赖于 `System.Web` 程序集。 将对此程序集的引用添加到项目中：

- 在“解决方案资源管理器”中，右键单击“引用”并选择“添加引用...”   
- 选择“程序集”>“框架”，然后向下滚动并选中“System.Web”  
- 选择“确定” 

## <a name="add-some-necessary-using-statements"></a>添加一些必要的 using 语句

本教程中的代码还需要三个 using 语句。 在 **Program.cs** 顶部的现有 `using` 语句下面添加这些语句：

```csharp
using System.Web;
using System.Net.Http;
```

## <a name="ask-the-user-for-a-query"></a>要求用户发出查询

在“解决方案资源管理器”中，打开“Program.cs”   。 更新 `Main()` 方法：

```csharp
static void Main()
{
    // Get the user's query
    Console.Write("Enter Bing query: ");
    string userQuery = Console.ReadLine();
    Console.WriteLine();

    // Run the query and display the results
    RunQueryAndDisplayResults(userQuery);

    // Prevent the console window from closing immediately
    Console.WriteLine("\nHit ENTER to exit...");
    Console.ReadLine();
}
```

此方法：

- 要求用户发出查询
- 调用 `RunQueryAndDisplayResults(userQuery)` 执行查询并显示结果
- 等待用户输入，以防控制台窗口立即关闭。

## <a name="search-for-query-results-using-the-bing-web-search-api"></a>使用必应 Web 搜索 API 搜索查询结果

接下来，添加一个用于查询 API 并显示结果的方法：

```csharp
static void RunQueryAndDisplayResults(string userQuery)
{
    try
    {
        // Create a query
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "<YOUR_SUBSCRIPTION_KEY_GOES_HERE>");
        var queryString = HttpUtility.ParseQueryString(string.Empty);
        queryString["q"] = userQuery;
        var query = "https://api.cognitive.microsoft.com/bing/v7.0/search?" + queryString;

        // Run the query
        HttpResponseMessage httpResponseMessage = client.GetAsync(query).Result;

        // Deserialize the response content
        var responseContentString = httpResponseMessage.Content.ReadAsStringAsync().Result;
        Newtonsoft.Json.Linq.JObject responseObjects = Newtonsoft.Json.Linq.JObject.Parse(responseContentString);

        // Handle success and error codes
        if (httpResponseMessage.IsSuccessStatusCode)
        {
            DisplayAllRankedResults(responseObjects);
        }
        else
        {
            Console.WriteLine($"HTTP error status code: {httpResponseMessage.StatusCode.ToString()}");
        }
    }
    catch (Exception e)
    {
        Console.WriteLine(e.Message);
    }
}
```

此方法：

- 创建 `HttpClient` 以查询 Web 搜索 API
- 设置必应用于验证请求的 `Ocp-Apim-Subscription-Key` HTTP 标头
- 执行请求并使用 JSON.net 反序列化结果
- 调用 `DisplayAllRankedResults(responseObjects)` 以按排名顺序显示所有结果

确保将 `Ocp-Apim-Subscription-Key` 的值设置为订阅密钥。

## <a name="display-ranked-results"></a>显示排名结果

在介绍如何按排名顺序显示结果之前，请先查看 Web 搜索响应示例：

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346...",
        "totalEstimatedMatches" : 982000,
        "value" : [{
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
            "name" : "Contoso Sailing Club - Seattle",
            "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/contososailingsea...",
            "snippet" : "Come sail with Contoso in Seattle...",
            "dateLastCrawled" : "2017-04-07T02:25:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/7\/#WebPages.6",
            "name" : "Contoso Sailing Lessons - Official Site",
            "url" : "http:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/www.constososailinglessonsseat...",
            "snippet" : "Contoso sailing lessons in Seattle...",
            "dateLastCrawled" : "2017-04-09T14:30:00"
        },

        ...

        ],
        "someResultsRemoved" : true
    },
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/7\/#RelatedSearches",
        "value" : [{
            "text" : "sailing lessons",
            "displayText" : "sailing lessons",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346E..."
        }

        ...

        ]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            }

            ...

            ]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}
```

`rankingResponse` JSON 对象（[文档](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse)）描述了搜索结果的相应显示顺序。 它包括以下一个或多个按优先级排列的组：

- `pole`：要获得最明显的处理（例如，显示在主线和边栏上方）的搜索结果。
- `mainline`：要显示在主线中的搜索结果。
- `sidebar`：要显示在边栏中的搜索结果。 如果没有边栏，则在主线下方显示结果。

排名响应 JSON 可能包括一个或多个组。

在 **Program.cs** 中添加以下方法，以按正确排名顺序显示结果：

```csharp
static void DisplayAllRankedResults(Newtonsoft.Json.Linq.JObject responseObjects)
{
    string[] rankingGroups = new string[] { "pole", "mainline", "sidebar" };

    // Loop through the ranking groups in priority order
    foreach (string rankingName in rankingGroups)
    {
        Newtonsoft.Json.Linq.JToken rankingResponseItems = responseObjects.SelectToken($"rankingResponse.{rankingName}.items");
        if (rankingResponseItems != null)
        {
            foreach (Newtonsoft.Json.Linq.JObject rankingResponseItem in rankingResponseItems)
            {
                Newtonsoft.Json.Linq.JToken resultIndex;
                rankingResponseItem.TryGetValue("resultIndex", out resultIndex);
                var answerType = rankingResponseItem.Value<string>("answerType");
                switch (answerType)
                {
                    case "WebPages":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("webPages.value"), "WebPage", "name", "url", "displayUrl", "snippet");
                        break;
                    case "News":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("news.value"), "News", "name", "url", "description");
                        break;
                    case "Images":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("images.value"), "Image", "thumbnailUrl");
                        break;
                    case "Videos":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("videos.value"), "Video", "embedHtml");
                        break;
                    case "RelatedSearches":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("relatedSearches.value"), "RelatedSearch", "displayText", "webSearchUrl");
                        break;
                }
            }
        }
    }
}
```

此方法：

- 循环访问响应包含的 `rankingResponse` 组
- 通过调用 `DisplaySpecificResults(...)` 显示每个组中的项目

在 **Program.cs** 中，添加以下两种方法：

```csharp
static void DisplaySpecificResults(Newtonsoft.Json.Linq.JToken resultIndex, Newtonsoft.Json.Linq.JToken items, string title, params string[] fields)
{
    if (resultIndex == null)
    {
        foreach (Newtonsoft.Json.Linq.JToken item in items)
        {
            DisplayItem(item, title, fields);
        }
    }
    else
    {
        DisplayItem(items.ElementAt((int)resultIndex), title, fields);
    }
}

static void DisplayItem(Newtonsoft.Json.Linq.JToken item, string title, string[] fields)
{
    Console.WriteLine($"{title}: ");
    foreach( string field in fields )
    {
        Console.WriteLine($"- {field}: {item[field]}");
    }
    Console.WriteLine();
}
```

将这些方法结合使用可将搜索结果输出到控制台。

## <a name="run-the-application"></a>运行应用程序

运行应用程序。 输出应如下所示：

```
Enter Bing query: sailing lessons seattle

WebPage:
- name: Contoso Sailing Club - Seattle
- url: https://www.bing.com/cr?IG=70BE289346ED4594874FE...
- displayUrl: https://contososailingsea....
- snippet: Come sail with Contoso in Seattle...

WebPage:
- name: Contoso Sailing Lessons Seattle - Official Site
- url: http://www.bing.com/cr?IG=70BE289346ED4594874FE...
- displayUrl: https://www.constososailinglessonsseat...
- snippet: Contoso sailing lessons in Seattle...

...
```

## <a name="next-steps"></a>后续步骤

详细了解[使用排名显示结果](rank-results.md)。
