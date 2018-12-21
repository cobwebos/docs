---
title: 快速入门：搜索图像 - 适用于 C# 的必应图像搜索 SDK
description: 在本快速入门中，你将使用必应图像搜索 SDK（它是 API 的包装程序并包含相同的功能）创建你的第一个图像搜索。 这个简单的 C# 应用程序会发送图像搜索查询、分析 JSON 响应，并显示所返回的第一个图像的 URL。
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 2d81c7b22e5d7cae0beb8a549d4e44f3a1ebc132
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2018
ms.locfileid: "53261239"
---
# <a name="quickstart-search-for-images-with-the-bing-image-search-sdk-for-c"></a>快速入门：通过适用于 C# 的必应图像搜索 SDK 搜索图像

在本快速入门中，你将使用必应图像搜索 SDK（它是 API 的包装程序并包含相同的功能）创建你的第一个图像搜索。 这个简单的 C# 应用程序会发送图像搜索查询、分析 JSON 响应，并显示所返回的第一个图像的 URL。

[GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingImageSearch) 上提供了此示例的源代码以及附加的错误处理和注释。

## <a name="prerequisites"></a>先决条件
* 任何版本的 [Visual Studio 2017](https://visualstudio.microsoft.com/vs/whatsnew/)。
* [认知图像搜索 NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.ImageSearch/1.2.0)。

要在 Visual Studio 中安装必应图像搜索 SDK，请在 Visual Studio 中使用解决方案资源管理器的 `Manage NuGet Packages` 选项。

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

另请参阅[认知服务定价 - 必应搜索 API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)。

## <a name="create-and-initialize-the-application"></a>创建并初始化应用程序

首先，在 Visual Studio 中创建新的 C# 控制台应用程序。 然后，将以下包添加到项目。

```csharp
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch.Models;
```

在项目的主方法中，创建你的有效订阅密钥的变量、必应要返回的图像结果和搜索词。 然后，使用密钥实例化图像搜索客户端。

```csharp
//IMPORTANT: replace this variable with your Cognitive Services subscription key
string subscriptionKey = "ENTER YOUR KEY HERE";
//stores the image results returned by Bing
Images imageResults = null;
// the image search term to be used in the query
string searchTerm = "canadian rockies";

//initialize the client
//NOTE: If you're using version 1.2.0 or below for the Bing Image Search SDK, 
// use ImageSearchAPI() instead of ImageSearchClient() to initialize your search client.

var client = new ImageSearchClient(new ApiKeyServiceClientCredentials(subscriptionKey));
```

## <a name="send-a-search-query-using-the-client"></a>使用客户端发送搜索查询

使用客户端和查询文本进行搜索：

```csharp
// make the search request to the Bing Image API, and get the results"
imageResults = client.Images.SearchAsync(query: searchTerm).Result; //search query
```

## <a name="parse-and-view-the-first-image-result"></a>分析并查看第一个图像结果

分析响应中所返回的图像结果。
如果响应中包含搜索结果，则存储第一个结果并输出其详细信息，例如缩略图 URL、原始 URL 以及返回的图像的总数。  

```csharp
if (imageResults != null)
{
    //display the details for the first image result.
    var firstImageResult = imageResults.Value.First();
    Console.WriteLine($"\nTotal number of returned images: {imageResults.Value.Count}\n");
    Console.WriteLine($"Copy the following URLs to view these images on your browser.\n");
    Console.WriteLine($"URL to the first image:\n\n {firstImageResult.ContentUrl}\n");
    Console.WriteLine($"Thumbnail URL for the first image:\n\n {firstImageResult.ThumbnailUrl}");
    Console.ReadKey();
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [必应图像搜索单页应用教程](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app)

## <a name="see-also"></a>另请参阅

* [什么是必应图像搜索？](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [尝试在线互动演示](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [获取免费的认知服务访问密钥](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Azure 认知服务 SDK 的 .NET 示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
* [Azure 认知服务文档](https://docs.microsoft.com/azure/cognitive-services)
* [必应图像搜索 API 参考](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
