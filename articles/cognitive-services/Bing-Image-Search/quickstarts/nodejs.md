---
title: 快速入门：使用 REST API 和 Node.js 搜索图像 - 必应图像搜索
titleSuffix: Azure Cognitive Services
description: 使用本快速入门，通过 JavaScript 将图像搜索请求发送到必应图像搜索 REST API，并接收 JSON 响应。
services: cognitive-services
documentationcenter: ''
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 12/06/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 2aaed57c7e1d817cd892f45c441ab59d4ffba3d3
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930758"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-nodejs"></a>快速入门：使用必应图像搜索 REST API 和 Node.js 来搜索图像

使用本快速入门开始向必应图像搜索 API 发送搜索请求。 此 JavaScript 应用程序会向 API 发送搜索查询，并在结果中显示第一个图像的 URL。 虽然此应用程序是以 JavaScript 编写的，但 API 是一种 RESTful Web 服务，与大多数编程语言兼容。

[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingImageSearchv7Quickstart.js) 上提供了此示例的源代码以及附加的错误处理和注释。

## <a name="prerequisites"></a>先决条件

* 最新版本的 [Node.js](https://nodejs.org/en/download/)。

* [JavaScript 请求库](https://github.com/request/request)  

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

另请参阅[认知服务定价 - 必应搜索 API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)。

## <a name="create-and-initialize-the-application"></a>创建并初始化应用程序

1. 在最喜爱的 IDE 或编辑器中创建新的 JavaScript 文件，并设置严格性和 https 要求。

    ```javascript
    'use strict';
    let https = require('https');
    ```

2. 为 API 终结点、图像 API 搜索路径、订阅密钥和搜索词创建变量。 `host` 可以是下面的全局终结点，也可以是资源的 Azure 门户中显示的[自定义子域](../../../cognitive-services/cognitive-services-custom-subdomains.md)终结点。

    ```javascript
    let subscriptionKey = 'enter key here';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/images/search';
    let term = 'tropical ocean';
    ```

## <a name="construct-the-search-request-and-query"></a>构造搜索请求和查询。

1. 使用上一个步骤中的变量来设置 API 请求的搜索 URL 的格式。 将搜索词发送到 API 之前，必须进行 URL 编码。

    ```javascript
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path + '?q=' + encodeURIComponent(search),
        headers : {
        'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };
    ```

2. 使用请求库将查询发送到 API。 `response_handler` 将在下节中定义。
    ```javascript
    let req = https.request(request_params, response_handler);
    req.end();
    ```

## <a name="handle-and-parse-the-response"></a>处理和分析响应

1. 定义名为 `response_handler` 的函数，它将 HTTP 调用 `response` 作为参数。 在此函数内，执行以下步骤：

    1. 定义一个包含 JSON 响应的正文的变量。  
        ```javascript
        let response_handler = function (response) {
            let body = '';
        };
        ```

    2. 在调用数据  标志时存储响应的正文
        ```javascript
        response.on('data', function (d) {
            body += d;
        });
        ```

    3. 指示了 **end** 标志后，从 JSON 响应获取第一个结果。 输出第一个图像的 URL 以及返回的图像的总数。

        ```javascript
        response.on('end', function () {
            let firstImageResult = imageResults.value[0];
            console.log(`Image result count: ${imageResults.value.length}`);
            console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
            console.log(`First image web search url: ${firstImageResult.webSearchUrl}`);
         });
        ```

## <a name="example-json-response"></a>示例 JSON 响应

来自必应图像搜索 API 的响应以 JSON 形式返回。 此示例响应已截断，仅显示了单个结果。

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }]
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建单页应用](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>另请参阅

* [什么是必应图像搜索？](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [尝试在线互动演示](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) 
* 必应搜索 API 的[定价详细信息](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)。 
* [获取免费的认知服务访问密钥](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Azure 认知服务文档](https://docs.microsoft.com/azure/cognitive-services)
* [必应图像搜索 API 参考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
