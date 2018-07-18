---
title: 实体搜索 API 快速入门 | Microsoft Docs
description: 介绍了如何开始使用必应实体搜索 API。
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: B206A254-B7E9-49FF-AFD5-87B1E4D6D30B
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 07/06/2017
ms.author: scottwhi
ms.openlocfilehash: 12031d2447920c7e2d6180f35cf4fb29aa1b6150
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365442"
---
# <a name="making-your-first-entities-request"></a>发出首个实体请求

实体搜索 API 向必应发送搜索查询，并获取包含实体和位置的结果。 位置结果包括餐馆、酒店或其他当地企业。 对于位置，查询可以指定当地企业名称，也可以请求获取列表（例如，我附近的餐馆）。 实体结果包括人员、位置或事物。 在此上下文中，位置是旅游景点、省/自治区/直辖市、国家/地区等。 

## <a name="first-steps"></a>前几个步骤

必须先获取认知服务订阅密钥，然后才能执行首次调用。 若要获取密钥，请参阅[试用认知服务](https://azure.microsoft.com/try/cognitive-services/?api=bing-entities-search-api)。 （如果顶部未显示实体搜索 API，请单击“搜索”选项卡，并向下一直滚动到它所在的位置。）

## <a name="the-endpoint"></a>终结点

若要获得实体和位置搜索结果，请向以下终结点发送 GET 请求：  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

请求必须使用 HTTPS 协议。

建议所有请求都源自服务器。 如果将密钥作为客户端应用的一部分进行分发，会让恶意第三方有更多机会来访问密钥。 另外，从服务器执行调用还会提供未来版本 API 的单一升级点。

## <a name="specifying-query-parameters-and-headers"></a>指定查询参数和请求头

请求必须指定包含用户搜索词的 [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query) 查询参数。 请求还必须指定 [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#mkt) 查询参数，用于确定所需的结果来源市场。 有关可选查询参数列表，请参阅[查询参数](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query-parameters)。 URL 对所有查询参数进行编码。  
  
请求必须指定 [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#subscriptionkey) 请求头。 尽管可视需要添加，但仍建议还指定以下请求头：  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#clientid)  
-   [X-MSEdge-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#location)  

客户端 IP 和位置请求头对返回位置感知内容非常重要。  

有关所有请求头和响应头的列表，请参阅[头](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#headers)。

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

下面的示例展示了对上一请求的响应。 此示例还展示了必应专用响应头。 若要了解响应对象，请参阅 [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#searchresponse)。

```
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

试用 API。 请转到[实体搜索 API 测试控制台](https://dev.cognitive.microsoft.com/docs/services/7a3fb374be374859a823b79fd938cc65/)。 

若要详细了解如何使用响应对象，请参阅[在 Web 中搜索实体和位置](./search-the-web.md)。

请务必阅读[必应使用和显示要求](./use-display-requirements.md)，以免违反任何搜索结果使用规则。
