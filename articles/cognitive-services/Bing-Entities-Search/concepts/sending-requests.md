---
title: 向必应实体搜索 API 发送搜索请求
titleSuffix: Azure cognitive Services
description: 了解如何向必应实体搜索 API 发送搜索请求
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: 0788b220f2612170fdea974a8bb27e8374a0b3be
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879211"
---
# <a name="sending-search-requests-to-the-bing-entity-search-api"></a>向必应实体搜索 API 发送搜索请求

必应实体搜索 API 向必应发送搜索查询，然后获得包含实体和场所的结果。 位置结果包括餐馆、酒店或其他当地企业。 对于位置，查询可以指定当地企业名称，也可以请求获取列表（例如，我附近的餐馆）。 实体结果包括人员、位置或事物。 在此上下文中，位置是旅游景点、省/自治区/直辖市、国家/地区等。

## <a name="the-endpoint"></a>终结点

若要获得实体和位置搜索结果，请向以下终结点发送 GET 请求：  

```
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

请求必须使用 HTTPS 协议。

建议所有请求都源自服务器。 如果将密钥作为客户端应用的一部分进行分发，会让恶意第三方有更多机会来访问密钥。 另外，从服务器执行调用还会提供未来版本 API 的单一升级点。

## <a name="specifying-query-parameters-and-headers"></a>指定查询参数和请求头

请求必须指定包含用户搜索词的 [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query) 查询参数。 请求还必须指定 [mkt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#mkt) 查询参数，用于确定所需的结果来源市场。 有关可选查询参数列表，请参阅[查询参数](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters)。 URL 对所有查询参数进行编码。  
  
请求必须指定 [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#subscriptionkey) 请求头。 尽管可视需要添加，但仍建议还指定以下请求头：  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#clientid)  
-   [X-MSEdge-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#location)  

客户端 IP 和位置请求头对返回位置感知内容非常重要。  

有关所有请求头和响应头的列表，请参阅[头](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers)。

## <a name="the-request"></a>请求

下面展示的实体请求包含所有建议的查询参数和请求头。 

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/entities?q=mount+rainier&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

如果是首次调用任何必应 API，请勿添加客户端 ID 请求头。 只有在以前调用过必应 API，且必应针对用户和设备组合返回了客户端 ID 的情况下，才添加客户端 ID。

## <a name="the-response"></a>响应

下面的示例展示了对上一请求的响应。 此示例还展示了必应专用响应头。 若要了解响应对象，请参阅 [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#searchresponse)。

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "mount rainier"
    },
    "entities" : {
        "queryScenario" : "DominantEntity",
        "value" : [{
            "contractualRules" : [{
                "_type" : "ContractualRules\/LicenseAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "license" : {
                    "name" : "CC-BY-SA",
                    "url" : "http:\/\/creativecommons.org\/licenses\/by-sa\/3.0\/"
                },
                "licenseNotice" : "Text under CC-BY-SA license"
            },
            {
                "_type" : "ContractualRules\/LinkAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "text" : "en.wikipedia.org",
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            },
            {
                "_type" : "ContractualRules\/MediaAttribution",
                "targetPropertyName" : "image",
                "mustBeCloseToContent" : true,
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            }],
            "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Mount%20Rainier...",
            "name" : "Mount Rainier",
            "image" : {
                "name" : "Mount Rainier",
                "thumbnailUrl" : "https:\/\/www.bing.com\/th?id=A21890c0e1f...",
                "provider" : [{
                    "_type" : "Organization",
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
                }],
                "hostPageUrl" : "http:\/\/upload.wikimedia.org\/wikipedia...",
                "width" : 110,
                "height" : 110
            },
            "description" : "Mount Rainier, Mount Tacoma, or Mount Tahoma is the highest...",
            "entityPresentationInfo" : {
                "entityScenario" : "DominantEntity",
                "entityTypeHints" : ["Attraction"],
                "entityTypeDisplayHint" : "Mountain"
            },
            "bingId" : "9ae3e6ca-81ea-6fa1-ffa0-42e1d78906"
        }]
    }
}
```


## <a name="next-steps"></a>后续步骤

* [使用必应实体 API 搜索实体](search-for-entities.md)
* [必应 API 的使用和显示要求](../use-display-requirements.md)