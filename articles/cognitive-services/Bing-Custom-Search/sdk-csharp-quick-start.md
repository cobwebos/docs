---
title: 快速入门：使用 C# SDK 调用必应自定义搜索终结点 | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 设置自定义搜索 SDK C# 控制台应用程序。
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 09/06/2018
ms.author: scottwhi
ms.openlocfilehash: 55a9982026e118e3c375908879967831a8121ede
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55178233"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-the-c-sdk"></a>快速入门：使用 C# SDK 调用必应自定义搜索终结点 

参考本快速入门开始使用 C# SDK 从必应自定义搜索实例请求搜索结果 虽然必应自定义搜索具有与大多数编程语言兼容的 REST API，但必应自定义搜索 SDK 提供了一种简单方法来将服务集成到应用程序中。 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch) 上找到此示例的源代码。

## <a name="prerequisites"></a>先决条件

- 必应自定义搜索实例。 请参阅[快速入门：创建第一个必应自定义搜索实例](quick-start.md)，了解详细信息。
- Microsoft [.Net Core](https://www.microsoft.com/net/download/core)
- 任何版本的 [Visual Studio 2017](https://www.visualstudio.com/downloads/)
- 如果使用的是 Linux/MacOS，则可使用 [Mono](http://www.mono-project.com/) 运行此应用程序。
- 已安装 [NuGet 自定义搜索](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0)包。 
    - 在 Visual Studio 中的解决方案资源管理器中，右键单击项目并从菜单中选择 `Manage NuGet Packages`。 安装 `Microsoft.Azure.CognitiveServices.Search.CustomSearch` 包。 安装 NuGet 自定义搜索包还会安装以下程序集：
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-news-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>创建并初始化应用程序

1. 在 Visual Studio 中创建新的 C# 控制台应用程序。 然后，将以下包添加到项目。

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
    ```

2. 在应用程序的 main 方法中，使用 API 密钥实例化搜索客户端。

    ```csharp
    var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-SUBSCRIPTION-KEY"));
    ```

## <a name="send-the-search-request-and-receive-a-response"></a>发送搜索请求和接收响应
    
1. 使用客户端的 `SearchAsync()` 方法发送搜索查询，并保存响应。 请务必将 `YOUR-CUSTOM-CONFIG-ID` 替换为实例的配置 ID（可在[必应自定义搜索门户](https://www.customsearch.ai/)中找到该 ID）。 此示例将搜索“Xbox”。

    ```csharp
    // This will look up a single query (Xbox).
    var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
    ```

2. `SearchAsync()` 方法将返回 `WebData` 对象。 使用该对象循环访问找到的任何 `WebPages`。 此代码可查找第一个网页结果并打印网页的 `Name` 和 `URL`。

    ```csharp
    if (webData?.WebPages?.Value?.Count > 0)
    {
        // find the first web page
        var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

        if (firstWebPagesResult != null)
        {
            Console.WriteLine("Number of webpage results {0}", webData.WebPages.Value.Count);
            Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
            Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
        }
        else
        {
            Console.WriteLine("Couldn't find web results!");
        }
    }
    else
    {
        Console.WriteLine("Didn't see any Web data..");
    }
    ```csharp

## Next steps

> [!div class="nextstepaction"]
> [Build a Custom Search web app](./tutorials/custom-search-web-page.md)
