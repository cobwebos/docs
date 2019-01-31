---
title: 快速入门：使用适用于 C# 的必应视觉搜索 SDK 获取图像见解
titleSuffix: Azure Cognitive Services
description: 了解如何使用必应视觉搜索 SDK 上传图像并获取其相关见解。
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 05/16/2018
ms.author: v-gedod
ms.openlocfilehash: 5884c7df37ef4bd67f85ec614bdefe87522b6bc6
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55171994"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-c"></a>快速入门：使用适用于 C# 的必应视觉搜索 SDK 获取图像见解

根据本快速入门，开始使用 C# SDK 通过必应视觉搜索服务获取图像见解。 虽然必应视觉搜索具有与大多数编程语言兼容的 REST API，但该 SDK 提供了一种简单方法来将服务集成到应用程序中。 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVisualSearch) 上找到此示例的源代码。

## <a name="prerequisites"></a>先决条件

* 任何版本的 [Visual Studio 2017](https://www.visualstudio.com/downloads/)。
* 如果使用的是 Linux/MacOS，则可使用 [Mono](http://www.mono-project.com/) 运行此应用程序。
* NuGet 视觉搜索包。 
    - 在 Visual Studio 中的解决方案资源管理器中，右键单击项目并从菜单中选择 `Manage NuGet Packages`。 安装 `Microsoft.Azure.CognitiveServices.Search.CustomSearch` 包。 安装 NuGet 包还会安装以下程序：
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json


[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>创建并初始化应用程序

1. 在 Visual Studio 中，创建新的项目。 然后，添加以下指令。
    
    ```csharp
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
    using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
    ```

2. 使用订阅密钥实例化客户端。
    
    ```csharp
    var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```
    
## <a name="send-a-search-request"></a>发送搜索请求 

1. 为图像创建 `FileStream`（在本例中为 `TestImages/image.jpg`）。 然后，使用 `client.Images.VisualSearchMethodAsync()` 通过客户端发送搜索请求。 
    
    ```csharp
     System.IO.FileStream stream = new FileStream(Path.Combine("TestImages", "image.jpg"), FileMode.Open;
     // The knowledgeRequest parameter is not required if an image binary is passed in the request body
     var visualSearchResults = client.Images.VisualSearchMethodAsync(image: stream, knowledgeRequest: (string)null).Result;
    ```
    
2. 分析以前的查询的结果：

    ```csharp
    // Visual Search results
    if (visualSearchResults.Image?.ImageInsightsToken != null)
    {
        Console.WriteLine($"Uploaded image insights token: {visualSearchResults.Image.ImageInsightsToken}");
    }
    else
    {
        Console.WriteLine("Couldn't find image insights token!");
    }
    
    // List of tags
    if (visualSearchResults.Tags.Count > 0)
    {
        var firstTagResult = visualSearchResults.Tags.First();
        Console.WriteLine($"Visual search tag count: {visualSearchResults.Tags.Count}");
    
        // List of actions in first tag
        if (firstTagResult.Actions.Count > 0)
        {
            var firstActionResult = firstTagResult.Actions.First();
            Console.WriteLine($"First tag action count: {firstTagResult.Actions.Count}");
            Console.WriteLine($"First tag action type: {firstActionResult.ActionType}");
        }
        else
        {
            Console.WriteLine("Couldn't find tag actions!");
        }
    ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [构建单页 Web 应用](tutorial-bing-visual-search-single-page-app.md)
