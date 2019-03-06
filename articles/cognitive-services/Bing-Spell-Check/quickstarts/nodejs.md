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
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 8e3379a086eb09745142f4e3997ed195eb4d1de5
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2019
ms.locfileid: "56885901"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-nodejs"></a>快速入门：使用必应拼写检查 REST API 和 Node.js 检查拼写

根据此快速入门中的说明对必应拼写检查 REST API 进行第一次调用。 此简单 Python 应用程序将向 API 发送请求并返回无法识别的单词列表，后跟建议的更正。 虽然此应用程序是使用 Python 编写的，但 API 是一种 RESTful Web 服务，与大多数编程语言兼容。 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingSpellCheckv7.js) 上提供了此应用程序的源代码。

## <a name="prerequisites"></a>先决条件

* [Node.js 6](https://nodejs.org/en/download/) or later.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>创建并初始化项目

1. 在你喜欢使用的 IDE 或编辑器中新建一个 Java 脚本文件。 设置严格性，并且需要 https。 然后为 API 终结点主机、路径和订阅密钥创建变量。

    ```javascript
    'use strict';
    let https = require ('https');
    
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/spellcheck';
    let key = 'ENTER KEY HERE';
    ```

2. 为市场、拼写检查模式和要检查的文本创建变量。 然后创建一个字符串，它将 `?mkt=` 参数追加到市场，将 `&mode=` 追加到模式。

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
        console.log (body);
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
