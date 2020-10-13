---
title: 快速入门：使用必应自动建议 REST API 和 Node.js 建议搜索查询
titleSuffix: Azure Cognitive Services
description: 了解如何使用必应自动建议 API 和 Node.js 快速开始实时建议搜索词。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: quickstart
ms.date: 05/06/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: b61427f39f508413e33ce67f90bfecb8ac202e1f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91332638"
---
# <a name="quickstart-suggest-search-queries-with-the-bing-autosuggest-rest-api-and-nodejs"></a>快速入门：使用必应自动建议 REST API 和 Node.js 建议搜索查询

按照此快速入门的要求，学习如何调用必应自动建议 API 并读取 JSON 响应。 这个简单的 Node.js 应用程序向 API 发送部分搜索查询，并返回搜索建议。 虽然此应用程序是以 JavaScript 编写的，但 API 是一种 RESTful Web 服务，与大多数编程语言兼容。 该示例的源代码可在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingAutosuggestv7.js) 上获得

## <a name="prerequisites"></a>先决条件

* [Node.js 6](https://nodejs.org/en/download/) 或更高版本

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

## <a name="create-a-new-application"></a>创建新应用程序

1. 在最喜爱的 IDE 或编辑器中创建新的 JavaScript 文件，并设置严格性和 https 要求。
    
    ```javascript
    'use strict';
    
    let https = require ('https');
    ```

2. 为 API 终结点主机和路径、订阅密钥、[市场代码](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#market-codes)和搜索词创建变量。 使用以下代码中的全局终结点，或者使用资源的 Azure 门户中显示的[自定义子域](../../../cognitive-services/cognitive-services-custom-subdomains.md)终结点。

    ```javascript
    // Replace the subscriptionKey string value with your valid subscription key.
    let subscriptionKey = 'enter key here';
    
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/Suggestions';
    
    let mkt = 'en-US';
    let query = 'sail';
    ```

## <a name="construct-the-search-request-and-query"></a>构造搜索请求和查询。

1. 通过将市场代码追加到 `mkt=` 参数并将查询追加到 `q=` 参数来为查询创建参数字符串。

    ```javascript 
    let params = '?mkt=' + mkt + '&q=' + query;
    ```

2. 创建一个名为 `get_suggestions()` 的函数。 使用上一个步骤中的变量来设置 API 请求的搜索 URL 的格式。 将搜索词发送到 API 之前，必须进行 URL 编码。

    ```javascript
    let get_suggestions = function () {
      let request_params = {
        method : 'GET',
        hostname : host,
        path : path + params,
        headers : {
          'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
      };
    //...
    }
    ```

 1. 在同一个函数中，使用请求库将查询发送到 API。 `response_handler` 将在下部分中定义。
    
    ```javascript
        //...
        let req = https.request(request_params, response_handler);
        req.end();
        ```

## Create a search handler

1. Define a function named `response_handler` that takes an HTTP call, `response`, as a parameter. 
Do the following steps within this function:
    
    1. Define a variable to contain the body of the JSON response.  

        ```javascript
        let response_handler = function (response) {
            let body = '';
        };
        ```

    2. Store the body of the response when the `data` flag is called
        
        ```javascript
        response.on ('data', function (d) {
        body += d;
        });
        ```

    3. When an `end` flag is signaled, use `JSON.parse()` and `JSON.stringify()` to print the response.
    
        ```javascript
        response.on ('end', function () {
        let body_ = JSON.parse (body);
        let body__ = JSON.stringify (body_, null, '  ');
            console.log (body__);
        });
        response.on ('error', function (e) {
            console.log ('Error: ' + e.message);
        });
        ```

2. Call `get_suggestions()` to send the request to the Bing Autosuggest API.

## Example JSON response

A successful response is returned in JSON, as shown in the following example: 

```json
{
  "_type": "Suggestions",
  "queryContext": {
    "originalQuery": "sail"
  },
  "suggestionGroups": [
    {
      "name": "Web",
      "searchSuggestions": [
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dgvtP9TS9NwhajSapY2Se6y1eCbP2fq_GiP2n-cxi6OY\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailrite%26FORM%3dUSBAPI\u0026p\u003dDevEx,5003.1",
          "displayText": "sailrite",
          "query": "sailrite",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dBTS0G6AakxntIl9rmbDXtk1n6rQpsZZ99aQ7ClE7dTY\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsail%2bsand%2bpoint%26FORM%3dUSBAPI\u0026p\u003dDevEx,5004.1",
          "displayText": "sail sand point",
          "query": "sail sand point",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dc0QOA_j6swCZJy9FxqOwke2KslJE7ZRmMooGClAuCpY\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailboats%2bfor%2bsale%26FORM%3dUSBAPI\u0026p\u003dDevEx,5005.1",
          "displayText": "sailboats for sale",
          "query": "sailboats for sale",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dmnMdREUH20SepmHQH1zlh9Hy_w7jpOlZFm3KG2R_BoA\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailing%2banarchy%26FORM%3dUSBAPI\u0026p\u003dDevEx,5006.1",
          "displayText": "sailing anarchy",
          "query": "sailing anarchy",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dWLFO-B1GG5qtBGnoU1Bizz02YKkg5fgAQtHwhXn4z8I\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailpoint%26FORM%3dUSBAPI\u0026p\u003dDevEx,5007.1",
          "displayText": "sailpoint",
          "query": "sailpoint",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dquBMwmKlGwqC5wAU0K7n416plhWcR8zQCi7r-Fw9Y0w\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailflow%26FORM%3dUSBAPI\u0026p\u003dDevEx,5008.1",
          "displayText": "sailflow",
          "query": "sailflow",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003d0udadFl0gCTKCp0QmzQTXS3_y08iO8FpwsoKPHPS6kw\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailboatdata%26FORM%3dUSBAPI\u0026p\u003dDevEx,5009.1",
          "displayText": "sailboatdata",
          "query": "sailboatdata",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003deSSt0MRSbl2V0RFPSuVd-gC7fGOT4717pz55EBUgPec\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailor%2b2025%26FORM%3dUSBAPI\u0026p\u003dDevEx,5010.1",
          "displayText": "sailor 2025",
          "query": "sailor 2025",
          "searchKind": "WebSearch"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建单页 Web 应用](../tutorials/autosuggest.md)

- [什么是必应自动建议？](../get-suggested-search-terms.md)
- [必应自动推荐 API v7 参考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
