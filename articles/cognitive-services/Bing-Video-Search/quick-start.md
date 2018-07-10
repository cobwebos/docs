---
title: 视频搜索 API 快速入门 | Microsoft Docs
description: 展示了如何开始使用必应视频搜索 API。
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 7E59692A-83A8-4F4C-B122-1F0EDC8E5C86
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 0bd0f067d64cac3ebac342ebadcfcc010a47af7b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366449"
---
# <a name="your-first-video-search-query"></a>你的第一个视频搜索查询

在第一次执行调用之前，需要获得必应搜索认知服务订阅密钥。 若要获取密钥，请参阅[试用认知服务](https://azure.microsoft.com/try/cognitive-services/?api=bing-video-search-api)。

若要获取视频搜索结果，请向以下终结点发送 GET 请求：  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/videos/search
```
   
请求必须使用 HTTPS 协议。

我们建议所有请求都源自一个服务器。 将密钥作为客户端应用程序的一部分进行分发会为恶意第三方提供更多访问它的机会。 另外，从服务器发出调用可以为未来版本的 API 提供一个单一升级点。

  
请求必须指定 [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query) 查询参数，其中包含用户的搜索词。 尽管是可选的，但请求还应该指定 [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#mkt) 查询参数，该参数标识你希望结果来自的市场。 有关可选查询参数（例如 `pricing`）的列表，请参阅[查询参数](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query-parameters)。 所有查询参数值都必须是 URL 编码。  
  
请求必须指定 [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#subscriptionkey) 标头。 尽管是可选的，但还是建议指定以下标头：  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#clientid)  
-   [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#location)  

客户端 IP 和位置标头对于返回位置感知内容非常重要。  

有关所有请求和响应标头的列表，请参阅[标头](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#headers)。


## <a name="the-request"></a>请求

下面显示了包含所有建议的查询参数和标头的搜索请求。 如果是第一次调用任何必应 API，请勿包括客户端 ID 标头。 只有在以前调用过必应 API 且必应针对用户和设备组合返回了客户端 ID 的情况下，才应包括客户端 ID。 
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

下面显示了对上一个请求的响应。 该示例还展示了特定于必应的响应标头。

```
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

有关使用响应对象的详细信息，请参阅[在网上搜索视频](./search-the-web.md)。

有关获取关于视频的见解（例如相关搜索）的详细信息，请参阅[视频见解](./video-insights.md)。  
  
有关社交媒体上的热门视频的详细信息，请参阅[热门视频](./trending-videos.md)。  
