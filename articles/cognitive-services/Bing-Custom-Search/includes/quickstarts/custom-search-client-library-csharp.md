---
title: 必应自定义搜索 C# 客户端库快速入门
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: b722fd34a78f1e9c2f4a660c205cf4a1e163a5d7
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2020
ms.locfileid: "78253055"
---
开始使用适用于 C# 的必应自定义搜索客户端库。 请按照以下步骤安装程序包并试用基本任务的示例代码。 借助必应自定义搜索 API，可为关注的主题创建定制的无广告搜索体验。 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch) 上找到此示例的源代码。

使用适用于 C# 的必应自定义搜索客户端库，可以执行以下操作：
* 使用必应自定义搜索实例在网上查找搜索结果。

[参考文档](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingcustomsearch?view=azure-dotnet) | [库源代码](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch) | [包 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) | [示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)


## <a name="prerequisites"></a>先决条件

- 必应自定义搜索实例。 请参阅[快速入门：创建第一个必应自定义搜索实例](../../quick-start.md)，了解详细信息。
- Microsoft [.NET Core](https://www.microsoft.com/net/download/core)
- 任何版本的 [Visual Studio 2017 或更高版本](https://www.visualstudio.com/downloads/)
- 如果使用的是 Linux/MacOS，则可使用 [Mono](https://www.mono-project.com/) 运行此应用程序。
- [必应自定义搜索](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) NuGet 包。 
    - 在 Visual Studio 中的**解决方案资源管理器**内，右键单击你的项目，并从菜单中选择“管理 NuGet 包”。  安装 `Microsoft.Azure.CognitiveServices.Search.CustomSearch` 包。 安装 NuGet 自定义搜索包还会安装以下程序集：
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]


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
> [Build a Custom Search web app](../../tutorials/custom-search-web-page.md)
