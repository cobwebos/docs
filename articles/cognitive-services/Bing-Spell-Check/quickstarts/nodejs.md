---
title: 快速入门：使用必应拼写检查 REST API 和 Node.js 检查拼写
titlesuffix: Azure Cognitive Services
description: 开始使用必应拼写检查 REST API 检查拼写和语法。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 04/02/2019
ms.author: aahill
ms.openlocfilehash: 0a1260de6428f6ebc70757261cdcc3002820ec7b
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2019
ms.locfileid: "59547758"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-nodejs"></a>快速入门：使用必应拼写检查 REST API 和 Node.js 检查拼写

根据此快速入门中的说明对必应拼写检查 REST API 进行第一次调用。 这个简单的 Node 应用程序将向 API 发送请求并返回无法识别的单词列表，后跟建议的更正。 虽然此应用程序是使用 Node.js 编写的，但 API 是一种 RESTful Web 服务，与大多数编程语言兼容。 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingSpellCheckv7.js) 上提供了此应用程序的源代码。

## <a name="prerequisites"></a>先决条件

* [Node.js 6](https://nodejs.org/en/download/) or later.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>创建并初始化项目

1. 在你喜欢使用的 IDE 或编辑器中新建一个 Java 脚本文件。 设置严格性，并且需要 `https`。 然后为 API 终结点主机、路径和订阅密钥创建变量。

    ```javascript
    'use strict';
    let https = require ('https');

    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/spellcheck';
    let key = '<ENTER-KEY-HERE>';
    ```

2. 为搜索参数和要检查的文本创建变量。 在 `mkt=` 之后追加​​市场代码。 市场代码指示发出请求的国家/地区。 同样，请在 `&mode=` 之后追加拼写检查模式。 模式为 `proof`（捕获大部分拼写/语法错误）或者 `spell`（捕获大部分拼写错误，但是捕获的语法错误较少）。

    ```javascript
    let mkt = "en-US";
    let mode = "proof";
    let text = "Hollo, wrld!";
    let query_string = "?mkt=" + mkt + "&mode=" + mode;
    ```

## <a name="create-the-request-parameters"></a>创建请求参数

通过使用 `POST` 方法创建新对象，来创建请求参数。 通过追加终结点路径和查询字符串来添加路径。 将订阅密钥添加到 `Ocp-Apim-Subscription-Key` 标头。

```javascript
let request_params = {
   method : 'POST',
   hostname : host,
   path : path + query_string,
   headers : {
   'Content-Type' : 'application/x-www-form-urlencoded',
   'Content-Length' : text.length + 5,
      'Ocp-Apim-Subscription-Key' : key,
   }
};
```

## <a name="create-a-response-handler"></a>创建响应处理程序

创建一个名为 `response_handler` 的函数，从 API 获取 JSON 响应，并进行打印。 为响应正文创建变量。 在收到 `data` 标志时使用 `response.on()` 附加响应。 收到 `end` 标志时，将 JSON 正文打印到控制台。

```javascript
let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let body_ = JSON.parse (body);
        console.log (body_);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};
```

## <a name="send-the-request"></a>发送请求

通过将 `https.request()` 与请求参数和响应处理程序结合使用，来调用 API。 将文本写入 API，并在之后结束请求。

```javascript
let req = https.request (request_params, response_handler);
req.write ("text=" + text);
req.end ();
```

## <a name="example-json-response"></a>示例 JSON 响应

在 JSON 中返回成功的响应，如以下示例所示：

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建单页 Web 应用](../tutorials/spellcheck.md)

- [什么是必应拼写检查 API？](../overview.md)
- [必应拼写检查 API v7 参考](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference)
