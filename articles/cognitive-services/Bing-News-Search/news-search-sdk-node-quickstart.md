---
title: 快速入门：执行新闻搜索 - 适用于 Node.js 的必应新闻搜索 SDK
titleSuffix: Azure Cognitive Services
description: 使用本快速入门，可以使用适用于 Node.js 的必应新闻搜索 SDK 搜索新闻，并处理响应。
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 8f45e0921748092af4702e03d557e52153a78ec0
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258656"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-nodejs"></a>快速入门：使用适用于 Node.js 的必应新闻搜索 SDK 执行新闻搜索

使用本快速入门开始使用适用于 Node.js 的必应新闻搜索 SDK 搜索新闻。 虽然必应新闻搜索具有与大多数编程语言兼容的 REST API，但该 SDK 提供了一种简单方法来将服务集成到应用程序中。 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js) 上找到此示例的源代码。

## <a name="prerequisites"></a>先决条件

* [Node.js](https://nodejs.org/en/)

若要使用必应新闻搜索 SDK 来设置控制台应用程序，请执行以下操作：
1. 在开发环境中运行 `npm install ms-rest-azure`。
2. 在开发环境中运行 `npm install azure-cognitiveservices-newssearch`。


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>创建并初始化应用程序

1. 创建 `CognitiveServicesCredentials` 的实例： 为订阅密钥和搜索词创建变量。

    ```javascript
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let search_term = 'Winter Olympics'
    ```

2. 对客户端进行实例化：
    
    ```javascript
    const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
    let client = new NewsSearchAPIClient(credentials);
    ```

## <a name="send-a-search-query"></a>发送搜索查询

3. 使用客户端通过查询词进行搜索，在本例中查询词为“Winter Olympics”：
    
    ```javascript
    client.newsOperations.search(search_term).then((result) => {
        console.log(result.value);
    }).catch((err) => {
        throw err;
    });
    ```

代码会将 `result.value` 项输出至控制台，并且不会分析任何文本。 结果（如果每个类别都有结果）将包括：

- `_type: 'NewsArticle'`
- `_type: 'WebPage'`
- `_type: 'VideoObject'`
- `_type: 'ImageObject'`

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
[创建单页 Web 应用](tutorial-bing-news-search-single-page-app.md)
