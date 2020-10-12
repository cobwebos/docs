---
title: 必应视频搜索 JavaScript 客户端库快速入门
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: fa1ecb24649d9355e6104bff8add5430fdd64c3c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91376478"
---
通过本快速入门开始使用适用于 JavaScript 的必应视频搜索客户端库来搜索新闻。 虽然必应视频搜索具有与大多数编程语言兼容的 REST API，但该客户端库提供了一种简单方法来将服务集成到应用程序中。 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js) 上找到此示例的源代码。 它包含更多注释和功能。

## <a name="prerequisites"></a>先决条件

* 最新版本的 [Node.js](https://nodejs.org/en/download/)。
* [适用于 JavaScript 的必应视频搜索 SDK](https://www.npmjs.com/package/@azure/cognitiveservices-videosearch)
     *  若要安装，请运行 `npm install @azure/cognitiveservices-videosearch`
* `@azure/ms-rest-azure-js` 包中的 `CognitiveServicesCredentials` 类，用于对客户端进行身份验证。
     * 若要安装，请运行 `npm install @azure/ms-rest-azure-js`

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>创建并初始化应用程序

1. 在你喜欢使用的 IDE 或编辑器中创建新的 JavaScript 文件，并添加针对必应视频搜索客户端库和 `CognitiveServicesCredentials` 模块的 `require()` 语句。 为你的订阅密钥创建变量。 
    
    ```javascript
    const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
    const VideoSearchAPIClient = require('@azure/cognitiveservices-videosearch');
    ```

2. 使用你的密钥创建 `CognitiveServicesCredentials` 的实例。 然后使用它来创建视频搜索客户端的实例。

    ```javascript
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let client = new VideoSearchAPIClient(credentials);
    ```

## <a name="send-the-search-request"></a>发送搜索请求

1. 使用 `client.videosOperations.search()` 向必应视频搜索 API 发送搜索请求。 当返回了搜索结果时，使用 `.then()` 来记录结果。
    
    ```javascript
    client.videosOperations.search('Interstellar Trailer').then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建单页 Web 应用](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>另请参阅 

* [什么是必应视频搜索 API？](../../overview.md)
* [认知服务 .NET SDK 示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)