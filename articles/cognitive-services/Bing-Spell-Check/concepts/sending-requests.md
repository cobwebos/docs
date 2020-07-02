---
title: 将请求发送到必应拼写检查 API
titleSuffix: Azure Cognitive Services
description: 了解必应拼写检查模式、设置和与 API 相关的其他信息。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: 4f79e3d8d8d32bb577e8c6c6f6f6c247de13c58a
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2020
ms.locfileid: "85801234"
---
# <a name="sending-requests-to-the-bing-spell-check-api"></a>将请求发送到必应拼写检查 API

要检查文本字符串是否存在拼写和语法错误，你需要向以下终结点发送 GET 请求：  

```
https://api.cognitive.microsoft.com/bing/v7.0/spellcheck
```  
  
请求必须使用 HTTPS 协议。

建议所有请求都源自服务器。 将密钥作为客户端应用程序的一部分进行分发会为恶意第三方提供更多访问它的机会。 服务器还会提供 API 的将来版本的单一升级点。

请求必须指定 [text](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#text) 查询参数，其中包含要检查的文本字符串。 尽管是可选的，但请求还应该指定 [mkt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#mkt) 查询参数，它确定你希望结果源自其中的市场。 有关可选查询参数（例如 `mode`）的列表，请参阅[查询参数](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#query-parameters)。 所有查询参数值都必须是 URL 编码。  
  
请求必须指定 [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#subscriptionkey) 请求头。 尽管是可选的，但仍建议还指定以下请求头。 这些标头有助于必应拼写检查 API 返回更准确的结果：  
  
-   [用户代理](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#clientid)  
-   [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#clientip)  
-   [X-搜索-位置](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#location)  

有关所有请求头和响应头的列表，请参阅[头](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v5-reference#headers)。

当使用 JavaScript 调用必应拼写检查 API 时，浏览器内置的安全功能可能会阻止你访问这些标头的值。

若要解决此问题，可以通过 CORS 代理发出必应拼写检查 API 请求。 来自此类代理的响应有一个 `Access-Control-Expose-Headers` 标头，此标头将响应标头列入允许列表，并将它们提供给 JavaScript。

可以轻松安装 CORS 代理，使[教程应用](../tutorials/spellcheck.md)可以访问可选的客户端标头。 首先，如果尚未安装 Node.js，请先[安装](https://nodejs.org/en/download/)。 然后，在命令提示符处输入以下命令。

```console
npm install -g cors-proxy-server
```

接下来，将 HTML 文件中的必应拼写检查 API 终结点更改为： \
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/`

最后，运行下面的命令，启动 CORS 代理：

```console
cors-proxy-server
```

使用教程应用期间，不要关闭命令窗口；关闭窗口会导致代理停止运行。 在搜索结果下的可展开 HTTP 标头部分中，现在可以看到 `X-MSEdge-ClientID` 标头（以及其他标头），并验证是否对于每个请求该标头都相同。

## <a name="example-api-request"></a>示例 API 请求

以下显示了包含所有建议查询参数和标头的请求。 如果是首次调用任何必应 API，请勿添加客户端 ID 请求头。 只有在以前调用过必应 API 且必应针对用户和设备组合返回了客户端 ID 的情况下，才包括客户端 ID。 
  
> ```  
> GET https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?text=when+its+your+turn+turn,+john,+come+runing&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE  
> X-MSEdge-ClientIP: 999.999.999.999  
> X-Search-Location: lat:47.60357;long:-122.3295;re:100  
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
> Host: api.cognitive.microsoft.com  
> ```  

下面的示例展示了对上一请求的响应。 此示例还展示了必应专用响应头。

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  

## <a name="next-steps"></a>后续步骤

- [什么是必应拼写检查 API？](../overview.md)
- [必应拼写检查 API v7 参考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
