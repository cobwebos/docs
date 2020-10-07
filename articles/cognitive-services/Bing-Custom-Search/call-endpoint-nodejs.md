---
title: 快速入门：使用 Node.js 调用必应自定义搜索终结点 | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 参考本快速入门，开始使用 Node.js 从必应自定义搜索实例请求搜索结果。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 43710407386995bde6d3505286e96b0737e06f08
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91309756"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-nodejs"></a>快速入门：使用 Node.js 调用必应自定义搜索终结点

使用此快速入门了解如何从必应自定义搜索实例请求搜索结果。 虽然此应用程序是以 JavaScript 编写的，但必应自定义搜索 API 是一种 RESTful Web 服务，与大多数编程语言兼容。 该示例的源代码可在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingCustomSearchv7.js) 上获得。

## <a name="prerequisites"></a>先决条件

- 必应自定义搜索实例。 有关详细信息，请参阅[快速入门：创建首个必应自定义搜索实例](quick-start.md)。

- [Node.js JavaScript 运行时](https://www.nodejs.org/)。

- [JavaScript 请求库](https://github.com/request/request)。

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>创建并初始化应用程序

- 在偏好的 IDE 或编辑器中创建新的 JavaScript 文件，然后为请求库添加 `require()` 语句。 为订阅密钥、自定义配置 ID 和搜索词创建变量。

    ```javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    ```

## <a name="send-and-receive-a-search-request"></a>发送和接收搜索请求 

1. 创建一个变量用于存储在请求中发送的信息。 构造请求 URL，方法是：将搜索词追加​​到 `q=` 查询参数后面，并将搜索实例的自定义配置 ID 追加​​到 `customconfig=` 参数后面。 使用与号 (`&`) 分隔参数。 可以为你的资源使用以下代码中的全局终结点，或者使用 Azure 门户中显示的[自定义子域](../../cognitive-services/cognitive-services-custom-subdomains.md)终结点。

    ```javascript
    var info = {
        url: 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 
            'q=' + searchTerm + "&" +
            'customconfig=' + customConfigId,
        headers: {
            'Ocp-Apim-Subscription-Key' : subscriptionKey
        }
    }
    ```

1. 使用 JavaScript 请求库将搜索请求发送到必应自定义搜索实例，并输出有关结果的信息，包括请求的名称、URL，以及上次抓取网页的日期。

    ```javascript
    request(info, function(error, response, body){
            var searchResponse = JSON.parse(body);
            for(var i = 0; i < searchResponse.webPages.value.length; ++i){
                var webPage = searchResponse.webPages.value[i];
                console.log('name: ' + webPage.name);
                console.log('url: ' + webPage.url);
                console.log('displayUrl: ' + webPage.displayUrl);
                console.log('snippet: ' + webPage.snippet);
                console.log('dateLastCrawled: ' + webPage.dateLastCrawled);
                console.log();
            }
    ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [生成自定义搜索 Web 应用](./tutorials/custom-search-web-page.md)
