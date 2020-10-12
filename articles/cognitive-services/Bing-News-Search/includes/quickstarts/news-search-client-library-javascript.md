---
title: 必应新闻搜索 JavaScript 客户端库快速入门
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 3760213c7f469dfe89599c0f01afe98168efde2d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91377583"
---
通过本快速入门开始使用适用于 JavaScript 的必应新闻搜索客户端库来搜索新闻。 虽然必应新闻搜索具有与大多数编程语言兼容的 REST API，但该客户端库提供了一种简单方法来将服务集成到应用程序中。 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js) 上找到此示例的源代码。

## <a name="prerequisites"></a>先决条件

* 最新版本的 [Node.js](https://nodejs.org/en/download/)。
* [适用于 JavaScript 的必应资讯搜索 SDK](https://www.npmjs.com/package/@azure/cognitiveservices-newssearch)
     *  若要安装，请运行 `npm install @azure/cognitiveservices-newssearch`
* `@azure/ms-rest-azure-js` 包中的 `CognitiveServicesCredentials` 类，用于对客户端进行身份验证。
     * 若要安装，请运行 `npm install @azure/ms-rest-azure-js`

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>创建并初始化应用程序

1. 创建 `CognitiveServicesCredentials` 的实例： 为订阅密钥和搜索词创建变量。

    ```javascript
    const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
    let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
    let search_term = 'Winter Olympics'
    ```

2. 对客户端进行实例化：
    
    ```javascript
    const NewsSearchAPIClient = require('@azure/cognitiveservices-newssearch');
    let client = new NewsSearchAPIClient(credentials);
    ```

## <a name="send-a-search-query"></a>发送搜索查询

1. 使用客户端通过查询词进行搜索，在本例中查询词为“Winter Olympics”：
    
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
> [创建单页 Web 应用](../../tutorial-bing-news-search-single-page-app.md)
