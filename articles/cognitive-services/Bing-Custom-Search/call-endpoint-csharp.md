---
title: 快速入门：使用 C# 调用必应自定义搜索终结点 | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 参考本快速入门开始从必应自定义搜索实例请求 C# 搜索结果。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: eae9565db5fd88a38343423422cfcc92a3fac33f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "88936803"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-c"></a>快速入门：使用 C# 调用必应自定义搜索终结点 

使用此快速入门了解如何从必应自定义搜索实例请求搜索结果。 虽然此应用程序是以 C# 编写的，但必应自定义搜索 API 是一种 RESTful Web 服务，与大多数编程语言兼容。 该示例的源代码可在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingCustomSearchv7.cs) 上获得。

## <a name="prerequisites"></a>先决条件

- 必应自定义搜索实例。 有关详细信息，请参阅[快速入门：创建首个必应自定义搜索实例](quick-start.md)。
- [Microsoft .NET Core](https://www.microsoft.com/net/download/core)。
- 任何版本的 [Visual Studio 2019 或更高版本](https://www.visualstudio.com/downloads/)。
- 如果使用的是 Linux/MacOS，则可使用 [Mono](https://www.mono-project.com/) 运行此应用程序。
- [必应自定义搜索](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/2.0.0) NuGet 包。 

   若要在 Visual Studio 中安装此包，请执行以下操作： 
     1. 在解决方案资源管理器中右键单击项目，然后选择“管理 NuGet 包” 。 
     2. 搜索并选择“Microsoft.Azure.CognitiveServices.Search.CustomSearch”，然后安装该包。

   当你安装必应自定义搜索 NuGet 包时，Visual Studio 还将安装以下包：
     - Microsoft.Rest.ClientRuntime
     - Microsoft.Rest.ClientRuntime.Azure
     - **Newtonsoft.Json**


[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>创建并初始化应用程序

1. 在 Visual Studio 中创建新的 C# 控制台应用程序。 然后，将以下包添加到项目：

    ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    ```

2. 创建以下类，用于存储必应自定义搜索 API 返回的搜索结果：

    ```csharp
    public class BingCustomSearchResponse {        
        public string _type{ get; set; }            
        public WebPages webPages { get; set; }
    }

    public class WebPages {
        public string webSearchUrl { get; set; }
        public int totalEstimatedMatches { get; set; }
        public WebPage[] value { get; set; }        
    }

    public class WebPage {
        public string name { get; set; }
        public string url { get; set; }
        public string displayUrl { get; set; }
        public string snippet { get; set; }
        public DateTime dateLastCrawled { get; set; }
        public string cachedPageUrl { get; set; }
    }
    ```

3. 在项目的 main 方法中，为必应自定义搜索 API 订阅密钥、搜索实例的自定义配置 ID 和搜索词创建以下变量：

    ```csharp
    var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
    var searchTerm = args.Length > 0 ? args[0]:"microsoft";
    ```

4. 构造请求 URL，方法是：将搜索词追加​​到 `q=` 查询参数后面，并将搜索实例的自定义配置 ID 追加​​到 `customconfig=` 参数后面。 使用与号 (`&`) 分隔参数。 对于 `url` 变量值，可以使用以下代码中的全局终结点，或者使用资源的 Azure 门户中显示的[自定义子域](../../cognitive-services/cognitive-services-custom-subdomains.md)终结点。

    ```csharp
    var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                "q=" + searchTerm + "&" +
                "customconfig=" + customConfigId;
    ```

## <a name="send-and-receive-a-search-request"></a>发送和接收搜索请求 

1. 创建请求客户端，并将订阅密钥添加到 `Ocp-Apim-Subscription-Key` 标头。

    ```csharp
    var client = new HttpClient();
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    ```

2. 执行搜索请求并获取 JSON 对象形式的响应。

    ```csharp
    var httpResponseMessage = client.GetAsync(url).Result;
    var responseContent = httpResponseMessage.Content.ReadAsStringAsync().Result;
    BingCustomSearchResponse response = JsonConvert.DeserializeObject<BingCustomSearchResponse>(responseContent);
    ```
## <a name="process-and-view-the-results"></a>处理和查看结果

- 循环访问响应对象以显示有关每条搜索结果的信息，包括其名称、URL 和上次网页爬网的日期。

    ```csharp
    for(int i = 0; i < response.webPages.value.Length; i++) {                
        var webPage = response.webPages.value[i];
        
        Console.WriteLine("name: " + webPage.name);
        Console.WriteLine("url: " + webPage.url);                
        Console.WriteLine("displayUrl: " + webPage.displayUrl);
        Console.WriteLine("snippet: " + webPage.snippet);
        Console.WriteLine("dateLastCrawled: " + webPage.dateLastCrawled);
        Console.WriteLine();
    }
    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
    ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [生成自定义搜索 Web 应用](./tutorials/custom-search-web-page.md)
