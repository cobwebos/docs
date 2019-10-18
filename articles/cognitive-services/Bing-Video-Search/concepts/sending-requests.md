---
title: 将搜索请求发送到必应视频搜索 API
titleSuffix: Azure Cognitive Services
description: 了解如何向必应视频搜索 API 发送搜索查询。
services: cognitive-services
author: aahi
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahill
ms.openlocfilehash: 96f92f0dc1e9e0c6c409fe17680fc57412e2b7d3
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72512514"
---
# <a name="sending-search-requests-to-the-bing-video-search-api"></a>向必应视频搜索 API 发送搜索请求

本文介绍了发送到必应视频搜索 API 的请求的参数和属性，以及它返回的 JSON 响应对象。 

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="suggest-search-terms-with-the-bing-autosuggest-api"></a>使用必应自动建议 API 来提供搜索词建议

如果提供供用户输入搜索词的搜索框，请使用[必应自动推荐 API](../../bing-autosuggest/get-suggested-search-terms.md) 来改进体验。 此 API 根据用户键入的部分搜索词返回建议的查询字符串。

在用户输入搜索词以后，URL 会在设置 [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query) 查询参数之前对搜索词进行编码。 例如，如果用户输入 sailing dinghies，系统会将 `q` 设置为 `sailing+dinghies` 或 `sailing%20dinghies`。

## <a name="sending-a-request"></a>发送请求

若要获取视频搜索结果，请向以下终结点发送 GET 请求：  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/videos/search
```
   
请求必须使用 HTTPS 协议。

我们建议所有请求都源自一个服务器。 如果将密钥作为客户端应用的一部分进行分发，会让恶意第三方有更多机会来访问密钥。 另外，从服务器执行调用还会提供 API 的将来版本的单一升级点。

  
请求必须指定 [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query) 查询参数，其中包含用户的搜索词。 尽管是可选的，但请求还应该指定 [mkt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#mkt) 查询参数，该参数标识你希望结果来自的市场。 有关可选查询参数（例如 `pricing`）的列表，请参阅[查询参数](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query-parameters)。 所有查询参数值都必须是 URL 编码。  
  
请求必须指定 [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#subscriptionkey) 标头。 尽管可视需要添加，但仍建议还指定以下请求头：  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#clientid)  
-   [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#location)  

客户端 IP 和位置请求头对返回位置感知内容非常重要。  

有关所有请求头和响应头的列表，请参阅[头](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#headers)。

## <a name="example-search-request"></a>示例搜索请求

下面显示了包含所有建议的查询参数和标头的搜索请求。 如果是首次调用任何必应 API，请勿添加客户端 ID 请求头。 只有在以前调用过必应 API 且必应针对用户和设备组合返回了客户端 ID 的情况下，才应包括客户端 ID。 
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

## <a name="example-json-response"></a>示例 JSON 响应

下面显示了对上一个请求的响应。 该示例还展示了特定于必应的响应标头。

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D5694...",
    "totalEstimatedMatches" : 1000,
    "value" : [
        {
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear when...",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
            "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.DYWCvh...",
            "datePublished" : "2014-03-04T11:51:53",
            "publisher" : [
                {
                    "name" : "Fabrikam"
                }
            ],
            "creator" : {
                "name" : "Marcus Appel"
            },
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjHZ--g",
            "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56944...",
            "encodingFormat" : "h264",
            "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjBZ--g",
            "width" : 1280,
            "height" : 720,
            "duration" : "PT2M47S",
            "motionThumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OM.Y6...",
            "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"https:...><\/iframe>",
            "allowHttpsEmbed" : true,
            "viewCount" : 8743,
            "thumbnail" : {
                "width" : 300,
                "height" : 168
            },
            "videoId" : "6DB795E11A6E3CBAAD636DB795E11E3CBAAD63",
            "allowMobileEmbed" : true,
            "isSuperfresh" : false
        },
        . . .
    ],
    "nextOffset" : 0,
    "pivotSuggestions" : [
        {
            "pivot" : "sailing",
            "suggestions" : []
        },
        {
            "pivot" : "dinghies",
            "suggestions" : [
                {
                    "text" : "Sailing Cruising",
                    "displayText" : "Cruising",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF754...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Sailing..."
                    }
                },
                . . .
            ]
        }
    ]
}
```

## <a name="next-steps"></a>后续步骤

试用 API。 转到[视频搜索 API 测试控制台](https://dev.cognitive.microsoft.com/docs/services/56b43f3ccf5ff8098cef3809/operations/58113fe5e31dac0a1ce6b0a8)。 

有关使用响应对象的详细信息，请参阅[在网上搜索视频](../search-the-web.md)。

有关获取关于视频的见解（例如相关搜索）的详细信息，请参阅[视频见解](../video-insights.md)。  
  
有关社交媒体上的热门视频的详细信息，请参阅[热门视频](../trending-videos.md)。  
